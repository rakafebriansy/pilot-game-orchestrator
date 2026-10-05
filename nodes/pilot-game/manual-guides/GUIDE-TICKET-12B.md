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
    /// <summary>
    /// Mengatur penempatan musuh gelombang pertempuran secara dinamis berdasarkan lantai (GDD §4.6).
    /// </summary>
    public class CombatWaveSpawner : MonoBehaviour
    {
        [SerializeField] private List<WaveCompositionData> _wavePool = new();

        /// <summary>
        /// Men-spawn sekumpulan musuh untuk lantai tertentu:
        /// - Memilih komposisi gelombang yang sesuai dengan lantai.
        /// - Mencari koordinat acak yang walkable di paruh kanan arena.
        /// - Menandai Grid Occupant dan men-spawn GameObject musuh dengan offset +0.5f.
        /// </summary>
        public List<Vector2Int> SpawnWave(int floorNumber, GridDataModel grid, Transform unitParent)
        {
            List<Vector2Int> spawnedPositions = new List<Vector2Int>();
            var wave = SelectWaveForFloor(floorNumber);
            if (wave == null) return spawnedPositions;

            // Alokasi ID Unit: ID 1 = Pemain (Nabu), ID 2 s/d 6 = Slot 5 Musuh
            int currentUnitId = 2;

            foreach (var entry in wave.EnemySpawns)
            {
                for (int i = 0; i < entry.Count; i++)
                {
                    // Cari ubin valid yang belum ditempati dan bukan rintangan
                    Vector2Int spawnPos = FindRandomWalkableTile(grid);
                    if (spawnPos == new Vector2Int(-1, -1)) break; // Berhenti jika tidak ada ubin kosong

                    // Registrasikan okupansi unit ke dalam data model grid
                    grid.SetOccupant(spawnPos, currentUnitId);
                    spawnedPositions.Add(spawnPos);

                    // Instantiate Prefab musuh jika ada
                    if (entry.Enemy.CharacterPrefab != null)
                    {
                        // Posisi world pivot di tengah petak (coord + 0.5f)
                        Vector3 worldPos = new Vector3(spawnPos.x + 0.5f, spawnPos.y + 0.5f, 0);
                        Instantiate(entry.Enemy.CharacterPrefab, worldPos, Quaternion.identity, unitParent);
                    }

                    currentUnitId++;
                    // Batas keras kapasitas musuh per gelombang (Maksimal 5 musuh: ID 2..6)
                    if (currentUnitId > 6) return spawnedPositions;
                }
            }

            return spawnedPositions;
        }

        /// <summary>
        /// Memilih konfigurasi gelombang yang valid untuk rentang nomor lantai tertentu.
        /// </summary>
        private WaveCompositionData SelectWaveForFloor(int floor)
        {
            var validWaves = _wavePool.FindAll(w => floor >= w.MinFloor && floor <= w.MaxFloor);
            if (validWaves.Count == 0) return _wavePool.Count > 0 ? _wavePool[0] : null;
            return validWaves[Random.Range(0, validWaves.Count)];
        }

        /// <summary>
        /// Mencari petak acak yang walkable dengan batas maksimal 50 percobaan (Safety loop).
        /// Memprioritaskan penempatan di separuh kanan arena (X: 4 s/d Width-1) agar pemain memiliki ruang bernapas di awal giliran.
        /// </summary>
        private Vector2Int FindRandomWalkableTile(GridDataModel grid)
        {
            for (int attempt = 0; attempt < 50; attempt++)
            {
                int x = Random.Range(4, GridDataModel.Width); // Separuh kanan grid
                int y = Random.Range(0, GridDataModel.Height);
                Vector2Int coord = new Vector2Int(x, y);

                // Pastikan ubin berada dalam grid, bukan pilar/lubang, dan belum ada unit lain
                if (grid.IsWalkable(coord))
                {
                    return coord;
                }
            }
            return new Vector2Int(-1, -1); // Menandakan grid penuh / tidak ditemukan petak kosong
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Panggil `SpawnWave(5, gridModel, transform)` di Play Mode.
2. Musuh akan ter-spawn secara otomatis pada petak acak yang tidak bertabrakan dengan rintangan.
