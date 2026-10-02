# 📖 Manual Guide: TICKET-04 — Visualisasi Arena & Sistem Highlight Tilemap

> **Referensi Tiket:** [TICKET-04.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04.md)  
> **Domain:** `[🗺️ DOMAIN 1: ARENA & TILEMAP]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan lapisan visual arena menggunakan sistem **3 Layer 2D Tilemap** di Unity:
1. `BaseFloor_Tilemap` (Sorting Layer: Floor)
2. `Obstacles_Tilemap` (Sorting Layer: Environment)
3. `HighlightOverlay_Tilemap` (Sorting Layer: Overlay)

Serta script `GridTilemapView.cs` yang berlangganan event `CombatEvents.OnHighlightTilesRequested` untuk mewarnai ubin secara dinamis (Merah, Hijau, Biru).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Arena/
│       └── GridTilemapView.cs
└── Prefabs/
    └── Arena/
        └── ArenaGrid_Prefab.prefab
```

---

## 💻 3. Kode Sumber Lengkap

### `Assets/Scripts/Arena/GridTilemapView.cs`
```csharp
using UnityEngine;
using UnityEngine.Tilemaps;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Arena
{
    /// <summary>
    /// Mengelola representasi visual highlight pada Tilemap Overlay di arena.
    /// </summary>
    public class GridTilemapView : MonoBehaviour
    {
        [Header("Tilemap References")]
        [SerializeField] private Tilemap _highlightTilemap;

        [Header("Highlight Tile Sprites")]
        [SerializeField] private TileBase _dangerTileSprite;  // Merah: Intent musuh
        [SerializeField] private TileBase _validTileSprite;   // Hijau: Target kartu sah
        [SerializeField] private TileBase _moveTileSprite;    // Biru: Jangkauan gerak
        [SerializeField] private TileBase _hoverTileSprite;   // Oranye/Kuning: Preview kursor

        private void OnEnable()
        {
            CombatEvents.OnHighlightTilesRequested += RenderHighlights;
            CombatEvents.OnClearAllHighlights += ClearHighlights;
        }

        private void OnDisable()
        {
            CombatEvents.OnHighlightTilesRequested -= RenderHighlights;
            CombatEvents.OnClearAllHighlights -= ClearHighlights;
        }

        /// <summary>
        /// Menggambar ubin warna pada koordinat yang diminta.
        /// </summary>
        public void RenderHighlights(TileHighlightRequest request)
        {
            if (_highlightTilemap == null || request.Coordinates == null) return;

            TileBase selectedTile = GetTileSpriteByStyle(request.Style);
            if (selectedTile == null) return;

            foreach (var coord in request.Coordinates)
            {
                Vector3Int tilemapCoord = new Vector3Int(coord.x, coord.y, 0);
                _highlightTilemap.SetTile(tilemapCoord, selectedTile);
            }
        }

        /// <summary>
        /// Menghapus seluruh visual highlight di arena.
        /// </summary>
        public void ClearHighlights()
        {
            if (_highlightTilemap != null)
            {
                _highlightTilemap.ClearAllTiles();
            }
        }

        private TileBase GetTileSpriteByStyle(HighlightType style)
        {
            return style switch
            {
                HighlightType.DangerEnemyIntent => _dangerTileSprite,
                HighlightType.ValidCardTarget => _validTileSprite,
                HighlightType.MovementRange => _moveTileSprite,
                HighlightType.HoverPreview => _hoverTileSprite,
                _ => null
            };
        }
    }
}
```

---

## 🛠️ 4. Langkah Setup Hierarchy di Unity Editor
1. Di Hierarchy Scene, buat GameObject baru bernama `ArenaGridRoot`.
2. Tambahkan komponen **Grid** (Cell Size: `1, 1, 0`).
3. Di bawah `ArenaGridRoot`, buat 3 child GameObject dengan komponen **Tilemap** dan **TilemapRenderer**:
   * `BaseFloor_Tilemap` → Sorting Layer: `Floor`, Order: 0
   * `Obstacles_Tilemap` → Sorting Layer: `Environment`, Order: 1
   * `HighlightOverlay_Tilemap` → Sorting Layer: `Overlay`, Order: 2
4. Pasang script `GridTilemapView.cs` pada `ArenaGridRoot`.
5. Seret `HighlightOverlay_Tilemap` ke slot field `_highlightTilemap`.
6. Tarik `ArenaGridRoot` ke Project View folder `Assets/Prefabs/Arena/` untuk menjadikannya Prefab `ArenaGrid_Prefab.prefab`.

---

## 🧪 5. Langkah Verifikasi
1. Buka Play Mode di Unity Editor.
2. Buat script pengujian sementara untuk memicu highlight:
   ```csharp
   Vector2Int[] area = new Vector2Int[] { new Vector2Int(5, 5), new Vector2Int(5, 6), new Vector2Int(5, 7) };
   CombatEvents.OnHighlightTilesRequested?.Invoke(new TileHighlightRequest(area, HighlightType.DangerEnemyIntent));
   ```
3. Periksa secara visual di Game View bahwa 3 ubin merah muncul tepat di koordinat (5,5), (5,6), dan (5,7).
