# 📖 Manual Guide: TICKET-12C — 3 Bioma Menara Babel & Dynamic Hazards

> **Referensi Tiket:** [TICKET-12C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12C.md)  
> **Domain:** `[🗺️ DOMAIN 1: ARENA & TILEMAP]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan keragaman visual tema 3 Bioma Menara Babel (GDD §5.2) serta interaksi ubin dinamis (*Dynamic Hazards*):
1. **3 Bioma Menara Babel**:
   * *Bioma 1 (Lantai 1-5)*: Medieval Dark Fantasy Library (Rak buku megah, ubin batu tua).
   * *Bioma 2 (Lantai 6-10)*: Distorted Arcane Archive (Buku melayang, arsitektur terdistorsi).
   * *Bioma 3 (Lantai 11-15)*: Ancient Mesopotamian Ziggurat (Aksara paku emas, pasir gurun, obelisk kuno).
2. **Dinamika Semak Terbakar (*Bush Burning*)**: Kartu berelemen petir/api (misal: *Storm*) membakar `StealthBush` menjadi abu (`BurnedBush`), menghilangkan fungsi semak siluman seketika.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Arena/
│       ├── BiomeType.cs
│       ├── BiomeManager.cs
│       └── DynamicTileManager.cs
└── ScriptableObjects/
    └── Biomes/
        ├── Biome_MedievalLibrary.asset
        ├── Biome_ArcaneDistortion.asset
        └── Biome_MesopotamianZiggurat.asset
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Arena/BiomeManager.cs`
```csharp
using UnityEngine;
using UnityEngine.Tilemaps;

namespace PilotGame.Arena
{
    public enum BiomeType
    {
        MedievalLibrary,      // Lantai 1-5
        ArcaneDistortion,     // Lantai 6-10
        MesopotamianZiggurat  // Lantai 11-15
    }

    [CreateAssetMenu(fileName = "NewBiome", menuName = "PilotGame/Data/Biome Data")]
    public class BiomeData : ScriptableObject
    {
        public BiomeType Type;
        public string BiomeName;
        public TileBase FloorTile;
        public TileBase PillarObstacleTile;
        public TileBase BushTile;
        public Color AmbientLightColor;
        public AudioClip BiomeBGM;
    }

    public class BiomeManager : MonoBehaviour
    {
        [SerializeField] private Tilemap _floorTilemap;
        [SerializeField] private BiomeData[] _biomes;

        public void ApplyBiome(int floorNumber)
        {
            BiomeData selected = GetBiomeForFloor(floorNumber);
            if (selected == null || _floorTilemap == null) return;

            // Warnai ubin lantai sesuai bioma
            for (int x = 0; x < 15; x++)
            {
                for (int y = 0; y < 15; y++)
                {
                    _floorTilemap.SetTile(new Vector3Int(x, y, 0), selected.FloorTile);
                }
            }

            Debug.Log($"[BiomeManager] Memasang Bioma: {selected.BiomeName} untuk Lantai {floorNumber}");
        }

        private BiomeData GetBiomeForFloor(int floor)
        {
            if (floor <= 5) return _biomes[0];
            if (floor <= 10) return _biomes[1];
            return _biomes[2];
        }
    }
}
```

---

### B. `Assets/Scripts/Arena/DynamicTileManager.cs`
```csharp
using UnityEngine;
using UnityEngine.Tilemaps;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;

namespace PilotGame.Arena
{
    public class DynamicTileManager : MonoBehaviour
    {
        [SerializeField] private Tilemap _floorTilemap;
        [SerializeField] private TileBase _burnedBushTile;
        [SerializeField] private GameObject _smokeParticlePrefab;

        private GridDataModel _grid;

        public void Initialize(GridDataModel grid)
        {
            _grid = grid;
        }

        private void OnEnable()
        {
            CombatEvents.OnSkillExecuted += HandleSkillExecuted;
        }

        private void OnDisable()
        {
            CombatEvents.OnSkillExecuted -= HandleSkillExecuted;
        }

        private void HandleSkillExecuted(int casterId, int skillId, Vector2Int targetCoord)
        {
            // Skill 6 = Storm / Fire: membakar semak jika mengenai target
            if (skillId == 6 && _grid != null && _grid.GetTileType(targetCoord) == TileType.StealthBush)
            {
                BurnBush(targetCoord);
            }
        }

        public void BurnBush(Vector2Int coord)
        {
            _grid.SetTileType(coord, TileType.BurnedBush);
            if (_floorTilemap != null && _burnedBushTile != null)
            {
                _floorTilemap.SetTile(new Vector3Int(coord.x, coord.y, 0), _burnedBushTile);
            }

            if (_smokeParticlePrefab != null)
            {
                Vector3 worldPos = new Vector3(coord.x + 0.5f, coord.y + 0.5f, 0);
                Instantiate(_smokeParticlePrefab, worldPos, Quaternion.identity);
            }

            Debug.Log($"[DynamicTile] Semak di koordinat ({coord.x}, {coord.y}) telah terbakar!");
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Ubah nomor lantai ke 12: lantai arena otomatis beralih ke tekstur pasir Ziggurat Mesopotamia dan pencahayaan bernuansa kuning emas.
2. Gunakan skill Storm di atas semak: semak terbakar menjadi ubin abu hitam dan partikel asap muncul.
