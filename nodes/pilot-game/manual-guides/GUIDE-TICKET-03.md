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

        /// <summary>
        /// Mengecek apakah suatu koordinat ubin dapat dilalui/dimasuki (walkable).
        /// </summary>
        public bool IsWalkable(Vector2Int coord)
        {
            // 1. Validasi Batas Arena: Menolak koordinat jika berada di luar batas grid 15x15 (out-of-bounds/tembok terluar)
            if (!IsInsideGrid(coord)) return false;

            // 2. Validasi Rintangan Statis: Menolak ubin jika bertipe pilar/rintangan solid yang memblokir pergerakan fisik
            if (_tiles[coord.x, coord.y] == TileType.ObstaclePillar) return false;

            // 3. Validasi Okupansi Unit: Menolak ubin jika sudah ditempati oleh unit lain (pemain atau musuh)
            if (_occupants[coord.x, coord.y] != 0) return false;

            // Seluruh syarat terpenuhi -> ubin kosong, valid, dan dapat dilalui
            return true;
        }

        public bool IsStealthed(Vector2Int coord)
        {
            if (!IsInsideGrid(coord)) return false;
            return _tiles[coord.x, coord.y] == TileType.StealthBush;
        }

        /// <summary>
        /// Mengevaluasi apakah target tersamarkan dari sudut pandang pengamat (Line of Sight).
        /// Target tersamarkan HANYA jika berada di StealthBush DAN jarak Manhattan >= 2 petak (GDD §4.1).
        /// Jika pengamat berada tepat bersebelahan (jarak 1 petak), target tetap terlihat (mengembalikan false).
        /// </summary>
        public bool IsTargetStealthed(Vector2Int observerCoord, Vector2Int targetCoord)
        {
            if (!IsStealthed(targetCoord)) return false;
            int distance = Math.Abs(observerCoord.x - targetCoord.x) + Math.Abs(observerCoord.y - targetCoord.y);
            return distance >= 2;
        }

        /// <summary>
        /// Menghitung titik henti pergerakan linear bertahap per petak (Raymarch step-by-step).
        /// Jika jalur menabrak musuh, tembok batas, atau pilar rintangan di tengah jalan,
        /// unit otomatis tertabrak dan berhenti tepat 1 petak di depan rintangan (GDD §4.6 - Path Collision).
        /// </summary>
        public Vector2Int CalculateLinearMoveDestination(Vector2Int start, Vector2Int direction, int distance)
        {
            Vector2Int current = start;

            // Normalisasi arah ke unit vector step: (-1, 0, atau 1) untuk x dan y
            Vector2Int step = new Vector2Int(Math.Sign(direction.x), Math.Sign(direction.y));

            // Simulasikan langkah demi langkah sejauh 'distance'
            for (int i = 1; i <= distance; i++)
            {
                Vector2Int nextCoord = current + step;

                // Cek tabrakan: jika petak berikutnya di luar arena atau terhalang rintangan/unit lain
                if (!IsInsideGrid(nextCoord) || !IsWalkable(nextCoord))
                {
                    // Terjadi tabrakan fisik -> hentikan pergerakan di petak terakhir yang valid (current)
                    return current;
                }

                // Maju 1 langkah ke petak berikutnya
                current = nextCoord;
            }

            // Selesai melangkah tanpa tabrakan -> kembalikan posisi akhir
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
    /// Mesin kalkulasi kecerdasan buatan musuh untuk fase niat (Pure C# Non-MonoBehaviour).
    /// Menghitung targeting musuh, jarak Manhattan, dan aturan semak siluman (Stealth Bush).
    /// </summary>
    public class EnemyAICalculator
    {
        private readonly GridDataModel _grid;

        public EnemyAICalculator(GridDataModel grid)
        {
            _grid = grid ?? throw new ArgumentNullException(nameof(grid));
        }

        /// <summary>
        /// Merencanakan serangan linear (garis lurus) ke arah koordinat target pemain.
        /// Mengabaikan target jika pemain bersembunyi di semak pada jarak >= 2 petak (hanya bisa dilihat pada jarak 1 petak / bersebelahan - GDD §4.1).
        /// </summary>
        public void PlanLinearAttack(int enemyId, Vector2Int enemyCoord, Vector2Int playerCoord, int attackRange)
        {
            // 1. Evaluasi Aturan Stealth Bush (GDD §4.1 via helper terpusat):
            // Jika pemain tersamarkan di dalam semak taktis dari sudut pandang musuh (jarak >= 2), AI kehilangan Line of Sight
            if (_grid.IsTargetStealthed(enemyCoord, playerCoord))
            {
                // Pemain tersamarkan -> Batalkan niat serangan terarah ke pemain
                return;
            }

            // 2. Tentukan arah vektor normalisasi (-1, 0, atau 1) menuju pemain
            Vector2Int direction = new Vector2Int(
                Math.Sign(playerCoord.x - enemyCoord.x),
                Math.Sign(playerCoord.y - enemyCoord.y)
            );

            // 4. Bangun area bahaya linear (Danger Area) sepanjang jangkauan serangan musuh
            List<Vector2Int> dangerArea = new List<Vector2Int>();
            for (int i = 1; i <= attackRange; i++)
            {
                Vector2Int target = enemyCoord + (direction * i);
                // Jika langkah menabrak dinding batas terluar grid, hentikan raymarch seketika (break)
                if (!_grid.IsInsideGrid(target)) break;

                dangerArea.Add(target);
            }

            // 5. Publikasikan niat serangan ke Event Bus untuk visualisasi telegraf merah di HUD & Tilemap
            if (dangerArea.Count > 0)
            {
                CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, dangerArea[0]);
                CombatEvents.OnHighlightTilesRequested?.Invoke(
                    new TileHighlightRequest(dangerArea.ToArray(), HighlightType.DangerEnemyIntent)
                );
            }
        }

        /// <summary>
        /// Merencanakan serangan area multi-bentuk yang skalabel menggunakan evaluasi matematis C# Switch Expression.
        /// Menjamin keabsahan bentuk geometris dan melempar NotImplementedException secara otomatis jika ada bentuk baru yang belum terdaftar.
        /// </summary>
        public void PlanAreaAttack(int enemyId, Vector2Int targetCenter, int radius, AreaShapeType shape = AreaShapeType.Square)
        {
            List<Vector2Int> dangerArea = new List<Vector2Int>();

            // 1. Pindai area bounding box [-radius s/d +radius]
            for (int x = -radius; x <= radius; x++)
            {
                for (int y = -radius; y <= radius; y++)
                {
                    // 2. Evaluasi matematis bentuk area (otomatis throw NotImplementedException jika enum shape belum di-handle)
                    if (!AreaShapeEvaluator.IsOffsetInShape(shape, x, y, radius)) continue;

                    Vector2Int coord = new Vector2Int(targetCenter.x + x, targetCenter.y + y);

                    // 3. Pemotongan Batas Arena (Boundary Clipping)
                    if (_grid.IsInsideGrid(coord))
                    {
                        dangerArea.Add(coord);
                    }
                }
            }

            // 4. Publikasikan seluruh koordinat area bahaya ke sistem telegraf HUD & Tilemap
            if (dangerArea.Count > 0)
            {
                CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, targetCenter);
                CombatEvents.OnHighlightTilesRequested?.Invoke(
                    new TileHighlightRequest(dangerArea.ToArray(), HighlightType.DangerEnemyIntent)
                );
            }
        }
    }

    // =========================================================================
    // 📐 EVALUATOR GEOMETRIS BENTUK AOE (EXHAUSTIVE PATTERN MATCHING)
    // =========================================================================

    /// <summary>
    /// Evaluator bentuk geometris serangan area (AoE).
    /// Menggunakan C# Switch Expression modern yang ringkas, cepat, dan terpusat.
    /// Menjamin kepatuhan tipe data: melempar NotImplementedException jika ada AreaShapeType yang belum diimplementasikan.
    /// </summary>
    public static class AreaShapeEvaluator
    {
        public static bool IsOffsetInShape(AreaShapeType shape, int x, int y, int radius) => shape switch
        {
            AreaShapeType.Square    => true,                                            // Kotak penuh (Chebyshev: 3x3, 5x5)
            AreaShapeType.Diamond   => (Math.Abs(x) + Math.Abs(y)) <= radius,          // Belah ketupat (Manhattan: |x| + |y| <= r)
            AreaShapeType.Cross     => (x == 0 || y == 0),                              // Salib / Plus (+) lurus vertikal & horizontal
            AreaShapeType.Circle    => (x * x + y * y) <= (radius * radius),            // Lingkaran Euclidean halus
            AreaShapeType.DiagonalX => Math.Abs(x) == Math.Abs(y),                      // Silang diagonal (X)
            AreaShapeType.Ring      => Math.Max(Math.Abs(x), Math.Abs(y)) == radius,    // Cincin / Bingkai batas terluar
            
            // ⚠️ Melempar error jika ada nilai enum AreaShapeType baru di CombatTypes.cs yang belum diimplementasikan
            _ => throw new NotImplementedException($"[AreaShapeEvaluator] AoE Shape '{shape}' is not implemented! Please add geometric evaluation for this shape.")
        };
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
        public void IsTargetStealthed_ReturnsTrueOnlyIfInBushAndDistanceGreaterThanOrEqualToTwo()
        {
            Vector2Int observer = new Vector2Int(0, 0);
            Vector2Int target = new Vector2Int(2, 0); // Jarak 2 ubin
            _grid.SetTileType(target, TileType.StealthBush);

            // Jarak >= 2 di semak -> tersamarkan (true)
            Assert.IsTrue(_grid.IsTargetStealthed(observer, target));

            // Jarak 1 bersebelahan di semak -> tetap terlihat (false)
            Vector2Int adjacentTarget = new Vector2Int(1, 0);
            _grid.SetTileType(adjacentTarget, TileType.StealthBush);
            Assert.IsFalse(_grid.IsTargetStealthed(observer, adjacentTarget));

            // Jarak 2 di lantai normal -> tidak tersamarkan (false)
            Vector2Int normalTarget = new Vector2Int(0, 2);
            _grid.SetTileType(normalTarget, TileType.NormalFloor);
            Assert.IsFalse(_grid.IsTargetStealthed(observer, normalTarget));
        }

        [Test]
        public void EnemyAI_IgnoresPlayerInStealthBushIfDistanceGreaterThanOrEqualToTwo()
        {
            Vector2Int enemyPos = new Vector2Int(0, 0);
            Vector2Int playerPos = new Vector2Int(2, 0); // Jarak 2 ubin (>= 2)
            _grid.SetTileType(playerPos, TileType.StealthBush);

            bool intentTriggered = false;
            CombatEvents.OnEnemyIntentDecided += (id, target) => intentTriggered = true;

            _ai.PlanLinearAttack(1, enemyPos, playerPos, 5);

            // Karena pemain di dalam bush pada jarak >= 2 (jarak 2), intent tidak boleh terpicu
            Assert.IsFalse(intentTriggered);
        }

        [Test]
        public void PlanAreaAttack_SquareShape_GeneratesNineTilesForRadiusOne()
        {
            Vector2Int center = new Vector2Int(5, 5);
            TileHighlightRequest capturedRequest = default;
            CombatEvents.OnHighlightTilesRequested += req => capturedRequest = req;

            _ai.PlanAreaAttack(1, center, 1, AreaShapeType.Square);

            Assert.AreEqual(9, capturedRequest.Coordinates.Length);
        }

        [Test]
        public void PlanAreaAttack_DiamondShape_GeneratesFiveTilesForRadiusOne()
        {
            Vector2Int center = new Vector2Int(5, 5);
            TileHighlightRequest capturedRequest = default;
            CombatEvents.OnHighlightTilesRequested += req => capturedRequest = req;

            _ai.PlanAreaAttack(1, center, 1, AreaShapeType.Diamond);

            // Radius 1 Diamond membuang 4 sudut diagonal, menyisakan 5 petak
            Assert.AreEqual(5, capturedRequest.Coordinates.Length);
        }

        [Test]
        public void PlanAreaAttack_UnimplementedShape_ThrowsNotImplementedException()
        {
            Vector2Int center = new Vector2Int(5, 5);
            // Menjamin kepatuhan: melempar NotImplementedException jika tipe bentuk tidak terdaftar di AreaShapeEvaluator
            Assert.Throws<System.NotImplementedException>(() =>
            {
                _ai.PlanAreaAttack(1, center, 1, (AreaShapeType)999);
            });
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
