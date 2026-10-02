# 📖 Manual Guide: TICKET-03 — Otak Logika Grid 15×15, Stealth Bush & AI Musuh (Headless C#)

> **Referensi Tiket:** [TICKET-03.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-03.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini membangun mesin logika spasial murni (**Pure C# Non-MonoBehaviour**) untuk representasi arena 15×15:
1. **`GridDataModel.cs`**: Mengelola matriks ubin, status walkability, rintangan pilar, semak taktis (*Stealth Bush*), dan kalkulasi tabrakan rute (*Movement Collision*).
2. **`EnemyAICalculator.cs`**: Menghitung niat serangan musuh (*Intent Phase*) dengan memperhitungkan aturan persembunyian semak (GDD §4.1).
3. **`GridLogicTests.cs`**: Unit test EditMode otomatis untuk memastikan kebenaran kalkulasi tanpa membuka scene.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── Grid/
│   │   └── GridDataModel.cs
│   └── Units/
│       └── EnemyAICalculator.cs
└── Tests/
    └── EditMode/
        └── GridLogicTests.cs
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Grid/GridDataModel.cs`
```csharp
using System;
using UnityEngine;
using PilotGame.Core.Data;

namespace PilotGame.Grid
{
    /// <summary>
    /// Model data representasi spasial arena 15x15 secara murni (Pure C#).
    /// </summary>
    public class GridDataModel
    {
        public const int Width = 15;
        public const int Height = 15;

        private readonly TileType[,] _tiles;
        private readonly int[,] _occupants; // 0 = kosong, >0 = unitId

        public GridDataModel()
        {
            _tiles = new TileType[Width, Height];
            _occupants = new int[Width, Height];

            // Inisialisasi lantai ubin normal
            for (int x = 0; x < Width; x++)
            {
                for (int y = 0; y < Height; y++)
                {
                    _tiles[x, y] = TileType.NormalFloor;
                    _occupants[x, y] = 0;
                }
            }
        }

        public bool IsInsideGrid(Vector2Int coord)
        {
            return coord.x >= 0 && coord.x < Width && coord.y >= 0 && coord.y < Height;
        }

        public bool IsWalkable(Vector2Int coord)
        {
            if (!IsInsideGrid(coord)) return false;
            if (_tiles[coord.x, coord.y] == TileType.ObstaclePillar) return false;
            if (_occupants[coord.x, coord.y] != 0) return false;
            return true;
        }

        public bool IsStealthed(Vector2Int coord)
        {
            if (!IsInsideGrid(coord)) return false;
            return _tiles[coord.x, coord.y] == TileType.StealthBush;
        }

        /// <summary>
        /// Menghitung titik henti pergerakan linear. Jika jalur menabrak musuh/rintangan,
        /// unit akan tertabrak dan berhenti tepat 1 petak di depan rintangan (GDD §4.6).
        /// </summary>
        public Vector2Int CalculateLinearMoveDestination(Vector2Int start, Vector2Int direction, int distance)
        {
            Vector2Int current = start;
            Vector2Int step = new Vector2Int(Math.Sign(direction.x), Math.Sign(direction.y));

            for (int i = 1; i <= distance; i++)
            {
                Vector2Int nextCoord = current + step;
                if (!IsInsideGrid(nextCoord) || !IsWalkable(nextCoord))
                {
                    // Tertabrak rintangan atau unit lain -> berhenti di posisi saat ini
                    return current;
                }
                current = nextCoord;
            }
            return current;
        }

        public void SetOccupant(Vector2Int coord, int unitId)
        {
            if (IsInsideGrid(coord))
            {
                _occupants[coord.x, coord.y] = unitId;
            }
        }

        public int GetOccupant(Vector2Int coord)
        {
            if (!IsInsideGrid(coord)) return 0;
            return _occupants[coord.x, coord.y];
        }

        public void SetTileType(Vector2Int coord, TileType type)
        {
            if (IsInsideGrid(coord))
            {
                _tiles[coord.x, coord.y] = type;
            }
        }

        public TileType GetTileType(Vector2Int coord)
        {
            if (!IsInsideGrid(coord)) return TileType.ObstaclePillar;
            return _tiles[coord.x, coord.y];
        }
    }
}
```

---

### B. `Assets/Scripts/Units/EnemyAICalculator.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;

namespace PilotGame.Units
{
    /// <summary>
    /// Mesin kalkulasi kecerdasan buatan musuh untuk fase niat (Pure C#).
    /// </summary>
    public class EnemyAICalculator
    {
        private readonly GridDataModel _grid;

        public EnemyAICalculator(GridDataModel grid)
        {
            _grid = grid ?? throw new ArgumentNullException(nameof(grid));
        }

        /// <summary>
        /// Merencanakan serangan linear ke arah pemain.
        /// </summary>
        public void PlanLinearAttack(int enemyId, Vector2Int enemyCoord, Vector2Int playerCoord, int attackRange)
        {
            // Periksa aturan Stealth Bush (GDD §4.1)
            int distanceToPlayer = Math.Abs(enemyCoord.x - playerCoord.x) + Math.Abs(enemyCoord.y - playerCoord.y);
            if (_grid.IsStealthed(playerCoord) && distanceToPlayer > 2)
            {
                // Pemain sembunyi di dalam semak dan jarak > 2 -> AI tidak bisa menarget pemain!
                return;
            }

            Vector2Int direction = new Vector2Int(
                Math.Sign(playerCoord.x - enemyCoord.x),
                Math.Sign(playerCoord.y - enemyCoord.y)
            );

            List<Vector2Int> dangerArea = new List<Vector2Int>();
            for (int i = 1; i <= attackRange; i++)
            {
                Vector2Int target = enemyCoord + (direction * i);
                if (_grid.IsInsideGrid(target))
                {
                    dangerArea.Add(target);
                }
            }

            if (dangerArea.Count > 0)
            {
                CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, dangerArea[0]);
                CombatEvents.OnHighlightTilesRequested?.Invoke(
                    new TileHighlightRequest(dangerArea.ToArray(), HighlightType.DangerEnemyIntent)
                );
            }
        }

        /// <summary>
        /// Merencanakan serangan area (Radius Area).
        /// </summary>
        public void PlanAreaAttack(int enemyId, Vector2Int targetCenter, int radius)
        {
            List<Vector2Int> dangerArea = new List<Vector2Int>();

            for (int x = -radius; x <= radius; x++)
            {
                for (int y = -radius; y <= radius; y++)
                {
                    Vector2Int coord = new Vector2Int(targetCenter.x + x, targetCenter.y + y);
                    if (_grid.IsInsideGrid(coord))
                    {
                        dangerArea.Add(coord);
                    }
                }
            }

            if (dangerArea.Count > 0)
            {
                CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, targetCenter);
                CombatEvents.OnHighlightTilesRequested?.Invoke(
                    new TileHighlightRequest(dangerArea.ToArray(), HighlightType.DangerEnemyIntent)
                );
            }
        }
    }
}
```

---

### C. `Assets/Tests/EditMode/GridLogicTests.cs`
```csharp
using NUnit.Framework;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;
using PilotGame.Units;

namespace PilotGame.Tests.EditMode
{
    [TestFixture]
    public class GridLogicTests
    {
        private GridDataModel _grid;
        private EnemyAICalculator _ai;

        [SetUp]
        public void Setup()
        {
            _grid = new GridDataModel();
            _ai = new EnemyAICalculator(_grid);
        }

        [Test]
        public void IsInsideGrid_CorrectlyValidatesBoundaries()
        {
            Assert.IsTrue(_grid.IsInsideGrid(new Vector2Int(0, 0)));
            Assert.IsTrue(_grid.IsInsideGrid(new Vector2Int(14, 14)));
            Assert.IsFalse(_grid.IsInsideGrid(new Vector2Int(-1, 0)));
            Assert.IsFalse(_grid.IsInsideGrid(new Vector2Int(15, 0)));
            Assert.IsFalse(_grid.IsInsideGrid(new Vector2Int(5, 15)));
        }

        [Test]
        public void IsWalkable_ReturnsFalseOnObstacleOrOccupant()
        {
            Vector2Int pillarPos = new Vector2Int(5, 5);
            _grid.SetTileType(pillarPos, TileType.ObstaclePillar);
            Assert.IsFalse(_grid.IsWalkable(pillarPos));

            Vector2Int unitPos = new Vector2Int(3, 3);
            _grid.SetOccupant(unitPos, 99);
            Assert.IsFalse(_grid.IsWalkable(unitPos));
        }

        [Test]
        public void CalculateLinearMoveDestination_StopsBeforeCollision()
        {
            Vector2Int startPos = new Vector2Int(2, 2);
            Vector2Int obstaclePos = new Vector2Int(4, 2);
            _grid.SetTileType(obstaclePos, TileType.ObstaclePillar);

            // Coba jalan 3 petak ke kanan (x + 3)
            Vector2Int destination = _grid.CalculateLinearMoveDestination(startPos, Vector2Int.right, 3);

            // Harusnya terhenti di (3, 2) tepat 1 petak di depan pilar di (4, 2)
            Assert.AreEqual(new Vector2Int(3, 2), destination);
        }

        [Test]
        public void EnemyAI_IgnoresPlayerInStealthBushIfDistanceGreaterThanTwo()
        {
            Vector2Int enemyPos = new Vector2Int(0, 0);
            Vector2Int playerPos = new Vector2Int(5, 0); // Jarak 5 ubin
            _grid.SetTileType(playerPos, TileType.StealthBush);

            bool intentTriggered = false;
            CombatEvents.OnEnemyIntentDecided += (id, target) => intentTriggered = true;

            _ai.PlanLinearAttack(1, enemyPos, playerPos, 5);

            // Karena pemain di dalam bush pada jarak 5, intent tidak boleh terpicu
            Assert.IsFalse(intentTriggered);
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Di Unity Editor, buka jendela **Test Runner** via menu `Window > General > Test Runner`.
2. Pilih tab **EditMode**.
3. Klik tombol **Run All**.
4. Pastikan semua tes di `GridLogicTests` berstatus hijau (Passed) ✅.
