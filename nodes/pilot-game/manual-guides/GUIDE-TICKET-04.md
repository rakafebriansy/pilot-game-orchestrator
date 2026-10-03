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

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Konfigurasi Sorting Layers di Project Settings
Sebelum membuat Tilemap di Scene, kita wajib mendaftarkan Sorting Layers standar:
1. Di menu bar atas Unity Editor, pilih **Edit > Project Settings**.
2. Di panel kiri, pilih **Tags and Layers**.
3. Buka dropdown **Sorting Layers**, lalu klik tombol **`+`** untuk menambahkan layer berurutan dari atas ke bawah:
   * `Default` (Bawaan Unity)
   * `Floor` (Untuk lantai arena dasar)
   * `Environment` (Untuk pilar rintangan & semak)
   * `Units` (Untuk karakter Nabu & musuh)
   * `Overlay` (Untuk visual highlight merah/hijau/biru)
   * `UI` (Untuk HUD dan kartu)
4. Tutup jendela Project Settings.

---

### Langkah 2.2: Pembuatan Hierarchy Tilemap di Scene
1. Buka Scene baru atau scene kerja (`MainBattleScene.unity`).
2. Di panel **Hierarchy**, klik kanan pada area kosong > pilih **2D Object > Tilemap > Rectangular**.
3. Sebuah GameObject bernama `Grid` akan otomatis terbuat dengan child `Tilemap`.
4. Ganti nama GameObject `Grid` menjadi `ArenaGridRoot`.
5. Klik `ArenaGridRoot` di Hierarchy, periksa panel **Inspector**:
   * Komponen **Grid**:
     * **Cell Size:** `X: 1, Y: 1, Z: 0`
     * **Cell Layout:** `Rectangle`
     * **Cell Swizzle:** `XYZ`
6. Buat 3 child Tilemap di dalam `ArenaGridRoot`:
   * **Child 1:** Ganti nama menjadi `BaseFloor_Tilemap`
     * Di Inspector komponen **Tilemap Renderer**:
       * **Sorting Layer:** Pilih `Floor`
       * **Order in Layer:** `0`
   * **Child 2:** Klik kanan `ArenaGridRoot` > **2D Object > Tilemap > Rectangular**, beri nama `Obstacles_Tilemap`
     * Di Inspector komponen **Tilemap Renderer**:
       * **Sorting Layer:** Pilih `Environment`
       * **Order in Layer:** `1`
   * **Child 3:** Klik kanan `ArenaGridRoot` > **2D Object > Tilemap > Rectangular**, beri nama `HighlightOverlay_Tilemap`
     * Di Inspector komponen **Tilemap Renderer**:
       * **Sorting Layer:** Pilih `Overlay`
       * **Order in Layer:** `2`

Struktur akhir di panel Hierarchy:
```text
[Hierarchy]
└── ArenaGridRoot                 [Grid, GridTilemapView]
    ├── BaseFloor_Tilemap         [Tilemap, Tilemap Renderer -> Layer: Floor]
    ├── Obstacles_Tilemap         [Tilemap, Tilemap Renderer -> Layer: Environment]
    └── HighlightOverlay_Tilemap  [Tilemap, Tilemap Renderer -> Layer: Overlay]
```

---

### Langkah 2.3: Pembuatan Aset Tile (Sprites to Tiles)
1. Di Project Window, buat folder `Assets/Art/Tiles/HighlightTiles/`.
2. Siapkan 4 sprite ubin berukuran **64x64 px** (*Pixels Per Unit / PPU: 64, Filter: Point, Compression: None*):
   * `Sprite_RedSquare.png` (Merah semi-transparan untuk Intent Musuh)
   * `Sprite_GreenSquare.png` (Hijau semi-transparan untuk Jangkauan Kartu)
   * `Sprite_BlueSquare.png` (Biru semi-transparan untuk Jangkauan Gerak)
   * `Sprite_YellowSquare.png` (Kuning semi-transparan untuk Kursor Hover)
3. Klik kanan di folder `HighlightTiles/` > **Create > 2D > Tiles > Tile**.
4. Beri nama: `Tile_DangerRed.asset`.
5. Di Inspector `Tile_DangerRed.asset`, seret `Sprite_RedSquare` ke slot field **Sprite**.
6. Ulangi untuk `Tile_ValidGreen.asset`, `Tile_MoveBlue.asset`, dan `Tile_HoverYellow.asset`.

---

### Langkah 2.4: Memasang Komponen & Wiring di Inspector
1. Klik GameObject `ArenaGridRoot` di Hierarchy.
2. Di panel **Inspector**, klik tombol **Add Component** di bagian bawah > ketik `GridTilemapView` > tekan Enter.
3. Hubungkan referensi slot (*Drag-and-Drop Wiring*):
   * Seret child `HighlightOverlay_Tilemap` dari Hierarchy ke slot field **Highlight Tilemap**.
   * Seret `Tile_DangerRed.asset` dari Project View ke slot **Danger Tile Sprite**.
   * Seret `Tile_ValidGreen.asset` dari Project View ke slot **Valid Tile Sprite**.
   * Seret `Tile_MoveBlue.asset` dari Project View ke slot **Move Tile Sprite**.
   * Seret `Tile_HoverYellow.asset` dari Project View ke slot **Hover Tile Sprite**.
4. Tarik GameObject `ArenaGridRoot` dari Hierarchy ke folder `Assets/Prefabs/Arena/` di Project Window untuk menyimpannya sebagai `ArenaGrid_Prefab.prefab`.

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

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Klik tombol **Play** di toolbar atas Unity Editor.
2. Buat script sementara atau panggil via Inspector debug:
   ```csharp
   Vector2Int[] sampleArea = new Vector2Int[] { new Vector2Int(4, 4), new Vector2Int(4, 5), new Vector2Int(4, 6) };
   CombatEvents.OnHighlightTilesRequested?.Invoke(new TileHighlightRequest(sampleArea, HighlightType.DangerEnemyIntent));
   ```
3. Lihat panel **Game View**: Tiga ubin merah akan muncul tepat di posisi grid (4,4), (4,5), dan (4,6) di atas lapisan lantai tanpa tertutup latar belakang.
