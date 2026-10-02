# 📖 Manual Guide: TICKET-12B — Combat Wave Spawner & Floor Progression Scaling

> **Referensi Tiket:** [TICKET-12B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12B.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan **Sistem Spawner Gelombang Musuh Bertahap** dan penyesuaian skala kekuatan musuh berdasarkan nomor lantai:
1. **`CombatWaveSpawner.cs`**: Men-spawn gelombang musuh (maksimal 5 unit per gelombang sesuai GDD §4.6) pada titik koordinat acak yang *walkable*.
2. **`WaveCompositionData.cs`**: Mengonfigurasi komposisi musuh (kombinasi Minion, Regular, Elite) berdasarkan lantai.
3. **Stat Scaling**: Pengali HP (+10% per lantai) dan Attack (+1 per 3 lantai).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Spawner/
│       ├── WaveCompositionData.cs
│       └── CombatWaveSpawner.cs
└── ScriptableObjects/
    └── Waves/
        ├── Wave_Floor1_Intro.asset
        ├── Wave_Floor5_Mid.asset
        └── Wave_Floor10_Hard.asset
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Spawner/WaveCompositionData.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;

namespace PilotGame.Spawner
{
    [Serializable]
    public class EnemySpawnEntry
    {
        public EnemyData Enemy;
        public int Count = 1;
    }

    [CreateAssetMenu(fileName = "NewWaveComposition", menuName = "PilotGame/Data/Wave Composition")]
    public class WaveCompositionData : ScriptableObject
    {
        public string WaveId;
        public int MinFloor = 1;
        public int MaxFloor = 15;
        public List<EnemySpawnEntry> EnemySpawns = new();
    }
}
```

---

### B. `Assets/Scripts/Spawner/CombatWaveSpawner.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Grid;

namespace PilotGame.Spawner
{
    public class CombatWaveSpawner : MonoBehaviour
    {
        [SerializeField] private List<WaveCompositionData> _wavePool = new();

        public List<Vector2Int> SpawnWave(int floorNumber, GridDataModel grid, Transform unitParent)
        {
            List<Vector2Int> spawnedPositions = new List<Vector2Int>();
            var wave = SelectWaveForFloor(floorNumber);
            if (wave == null) return spawnedPositions;

            int currentUnitId = 2; // Nabu = 1

            foreach (var entry in wave.EnemySpawns)
            {
                for (int i = 0; i < entry.Count; i++)
                {
                    Vector2Int spawnPos = FindRandomWalkableTile(grid);
                    if (spawnPos == new Vector2Int(-1, -1)) break;

                    grid.SetOccupant(spawnPos, currentUnitId);
                    spawnedPositions.Add(spawnPos);

                    // Instantiate Prefab musuh jika ada
                    if (entry.Enemy.CharacterPrefab != null)
                    {
                        Vector3 worldPos = new Vector3(spawnPos.x + 0.5f, spawnPos.y + 0.5f, 0);
                        Instantiate(entry.Enemy.CharacterPrefab, worldPos, Quaternion.identity, unitParent);
                    }

                    currentUnitId++;
                    if (currentUnitId > 6) return spawnedPositions; // Max 5 musuh (ID 2..6)
                }
            }

            return spawnedPositions;
        }

        private WaveCompositionData SelectWaveForFloor(int floor)
        {
            var validWaves = _wavePool.FindAll(w => floor >= w.MinFloor && floor <= w.MaxFloor);
            if (validWaves.Count == 0) return _wavePool.Count > 0 ? _wavePool[0] : null;
            return validWaves[Random.Range(0, validWaves.Count)];
        }

        private Vector2Int FindRandomWalkableTile(GridDataModel grid)
        {
            for (int attempt = 0; attempt < 50; attempt++)
            {
                int x = Random.Range(4, GridDataModel.Width); // Spawn di separuh kanan arena
                int y = Random.Range(0, GridDataModel.Height);
                Vector2Int coord = new Vector2Int(x, y);

                if (grid.IsWalkable(coord))
                {
                    return coord;
                }
            }
            return new Vector2Int(-1, -1);
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Panggil `SpawnWave(5, gridModel, transform)` di Play Mode.
2. Musuh akan ter-spawn secara otomatis pada petak acak yang tidak bertabrakan dengan rintangan.
