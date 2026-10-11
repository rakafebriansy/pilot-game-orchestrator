# 📖 Manual Guide: TICKET-04 — Visualisasi Arena & Sistem Highlight Tilemap

> **Referensi Tiket:** [TICKET-04.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04.md)  
> **Domain:** `[🗺️ DOMAIN 1: ARENA & TILEMAP]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 📁 PETA STRUKTUR DIREKTORI (LOKASI SELURUH BERKAS)

Berikut adalah denah letak absolut dan relatif dari seluruh berkas yang dibuat dan digunakan pada tiket ini:

```text
Tubbies Pilot Game/
└── Assets/
    ├── Scenes/
    │   └── MainBattleScene.unity                    <-- Scene pertempuran utama (bukan di root Assets)
    │
    ├── Art/
    │   └── Tiles/
    │       └── HighlightTiles/                      <-- Direktori seluruh aset visual highlight
    │           ├── Sprite_RedSquare.png             <-- Sprite sumber (64x64)
    │           ├── Sprite_GreenSquare.png           <-- Sprite sumber (64x64)
    │           ├── Sprite_BlueSquare.png            <-- Sprite sumber (64x64)
    │           ├── Sprite_YellowSquare.png          <-- Sprite sumber (64x64)
    │           ├── Tile_DangerRed.asset             <-- Tile asset merah (di-assign ke script)
    │           ├── Tile_ValidGreen.asset            <-- Tile asset hijau (di-assign ke script)
    │           ├── Tile_MoveBlue.asset              <-- Tile asset biru (di-assign ke script)
    │           ├── Tile_HoverYellow.asset           <-- Tile asset kuning (di-assign ke script)
    │           └── HighlightPalette.prefab          <-- (Opsional) Prefab palette kuas jika via Tile Palette GUI
    │
    ├── Scripts/
    │   └── Grid/
    │       ├── GridTilemapView.cs                   <-- Script View Subscriber Event Highlight
    │       ├── GridDataModel.cs                     <-- Model Grid (TICKET-03)
    │       └── PilotGame.Grid.asmdef                <-- Assembly definition modul Grid
    │
    └── Prefabs/
        └── Arena/
            └── ArenaGrid_Prefab.prefab              <-- Prefab hasil ekspor GameObject ArenaGridRoot
```

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan lapisan visual arena menggunakan sistem **3 Layer 2D Tilemap** di Unity:
1. `BaseFloor_Tilemap` (Sorting Layer: Floor, Order: 0)
2. `Obstacles_Tilemap` (Sorting Layer: Environment, Order: 1)
3. `HighlightOverlay_Tilemap` (Sorting Layer: Overlay, Order: 2)

Serta script `GridTilemapView.cs` yang berlangganan event `CombatEvents.OnHighlightTilesRequested` untuk mewarnai ubin secara dinamis (Merah, Hijau, Biru, Kuning).

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
   * `Overlay` (Untuk visual highlight merah/hijau/biru/kuning)
   * `UI` (Untuk HUD dan kartu)
4. Tutup jendela Project Settings.

---

### Langkah 2.2: Pembuatan Hierarchy Tilemap di Scene
1. Buka scene: **`Assets/Scenes/MainBattleScene.unity`**.
2. Di panel **Hierarchy**, klik kanan pada area kosong > pilih **2D Object > Tilemap > Rectangular**.
3. Sebuah GameObject bernama `Grid` akan otomatis terbuat dengan child `Tilemap`.
4. Ganti nama GameObject `Grid` induk menjadi: **`ArenaGridRoot`**.
5. Klik `ArenaGridRoot` di Hierarchy, periksa panel **Inspector**:
   * Komponen **Grid**:
     * **Cell Size:** `X: 1, Y: 1, Z: 0`
     * **Cell Layout:** `Rectangle`
     * **Cell Swizzle:** `XYZ`
6. Buat 3 child Tilemap di dalam `ArenaGridRoot`:
   * **Child 1:** Ganti nama `Tilemap` bawaan menjadi **`BaseFloor_Tilemap`**
     * Di Inspector komponen **Tilemap Renderer**:
       * **Sorting Layer:** Pilih `Floor`
       * **Order in Layer:** `0`
   * **Child 2:** Klik kanan pada `ArenaGridRoot` > **2D Object > Tilemap > Rectangular**, beri nama **`Obstacles_Tilemap`**
     * Di Inspector komponen **Tilemap Renderer**:
       * **Sorting Layer:** Pilih `Environment`
       * **Order in Layer:** `1`
   * **Child 3:** Klik kanan pada `ArenaGridRoot` > **2D Object > Tilemap > Rectangular**, beri nama **`HighlightOverlay_Tilemap`**
     * Di Inspector komponen **Tilemap Renderer**:
       * **Sorting Layer:** Pilih `Overlay`
       * **Order in Layer:** `2`

Struktur akhir di panel Hierarchy:
```text
[Hierarchy - MainBattleScene]
└── ArenaGridRoot                 [Grid, GridTilemapView]
    ├── BaseFloor_Tilemap         [Tilemap, Tilemap Renderer -> Layer: Floor, Order: 0]
    ├── Obstacles_Tilemap         [Tilemap, Tilemap Renderer -> Layer: Environment, Order: 1]
    └── HighlightOverlay_Tilemap  [Tilemap, Tilemap Renderer -> Layer: Overlay, Order: 2]
```

---

### Langkah 2.3: Pembuatan Aset Tile (Alur Resmi via Tile Palette GUI)

> ⚠️ **PENTING: Memahami Perbedaan `.prefab` vs `.asset` di Unity 6:**  
> * **Tile Palette (`.prefab`):** Adalah palet kuas lukis Unity. Formatnya memang disimpan sebagai file prefab.
> * **Tile Asset (`.asset`):** Adalah ubin individu (`Tile_DangerRed.asset`, dll.). File inilah yang dibutuhkan untuk Inspector script `GridTilemapView.cs`.
> * Unity 6 tidak memiliki tombol klik-kanan "Create > Tile" tunggal; cara resminya adalah mengubah sprite menjadi ubin melalui jendela Tile Palette.

#### Langkah-langkah Pembuatan Resmi:
1. Pastikan 4 file sprite sudah ada di `Assets/Art/Tiles/HighlightTiles/`:
   * `Sprite_RedSquare.png` (PPU: 64, Point, None)
   * `Sprite_GreenSquare.png` (PPU: 64, Point, None)
   * `Sprite_BlueSquare.png` (PPU: 64, Point, None)
   * `Sprite_YellowSquare.png` (PPU: 64, Point, None)
2. **Buka Jendela Tile Palette:** Di menu bar paling atas Unity, pilih **Window > 2D > Tile Palette**.
3. **Buat Wadah Palet Baru (Sekali Saja):**
   * Di tab Tile Palette, klik dropdown palet > pilih **Create New Palette**.
   * Name: `HighlightPalette`
   * Grid: **Rectangle**
   * Klik **Create** > pilih folder penyimpanan: `Assets/Art/Tiles/HighlightTiles/` > klik **Save** (file `HighlightPalette.prefab` akan terbuat).
4. **Mengubah Sprite Menjadi File `.asset` (Langkah Inti):**
   * Di panel Project, buka folder `Assets/Art/Tiles/HighlightTiles/`.
   * **Seret (Drag & Drop)** file `Sprite_RedSquare` ke dalam area kotak-kotak grid di jendela **Tile Palette**.
   * Unity akan langsung membuka pop-up file dialog kedua untuk menyimpan Tile.
   * Pilih folder tujuan: `Assets/Art/Tiles/HighlightTiles/`.
   * Beri nama: **`Tile_DangerRed`** > klik **Save** (Unity otomatis menyimpannya sebagai **`Tile_DangerRed.asset`**).
5. **Ulangi Proses Seret untuk 3 Sprite Lainnya:**
   * Seret `Sprite_GreenSquare` ke grid Tile Palette ➡️ Simpan sebagai **`Tile_ValidGreen.asset`** di folder `HighlightTiles/`.
   * Seret `Sprite_BlueSquare` ke grid Tile Palette ➡️ Simpan sebagai **`Tile_MoveBlue.asset`** di folder `HighlightTiles/`.
   * Seret `Sprite_YellowSquare` ke grid Tile Palette ➡️ Simpan sebagai **`Tile_HoverYellow.asset`** di folder `HighlightTiles/`.

Hasil akhir di `Assets/Art/Tiles/HighlightTiles/`:
```text
HighlightTiles/
├── Sprite_RedSquare.png
├── Sprite_GreenSquare.png
├── Sprite_BlueSquare.png
├── Sprite_YellowSquare.png
├── Tile_DangerRed.asset          <-- Siap dimasukkan ke Inspector
├── Tile_ValidGreen.asset         <-- Siap dimasukkan ke Inspector
├── Tile_MoveBlue.asset           <-- Siap dimasukkan ke Inspector
├── Tile_HoverYellow.asset        <-- Siap dimasukkan ke Inspector
└── HighlightPalette.prefab       <-- Wadah palet
```

---

### Langkah 2.4: Memasang Komponen & Wiring di Inspector
1. Pastikan script `GridTilemapView.cs` sudah berada di `Assets/Scripts/Grid/GridTilemapView.cs`.
2. Klik GameObject **`ArenaGridRoot`** di panel **Hierarchy**.
3. Di panel **Inspector**, klik tombol **Add Component** > ketik `GridTilemapView` > tekan Enter.
4. Hubungkan referensi slot (*Drag-and-Drop Wiring*):
   * **Highlight Tilemap:** Seret child `HighlightOverlay_Tilemap` dari panel Hierarchy ke slot ini.
   * **Danger Tile Sprite:** Seret `Tile_DangerRed.asset` dari Project Window ke slot ini.
   * **Valid Tile Sprite:** Seret `Tile_ValidGreen.asset` dari Project Window ke slot ini.
   * **Move Tile Sprite:** Seret `Tile_MoveBlue.asset` dari Project Window ke slot ini.
   * **Hover Tile Sprite:** Seret `Tile_HoverYellow.asset` dari Project Window ke slot ini.

---

### Langkah 2.5: Menyimpan Prefab Arena
1. Buka folder `Assets/Prefabs/Arena/` di panel Project (buat folder `Arena` jika belum ada).
2. Tarik (Drag & Drop) GameObject **`ArenaGridRoot`** dari Hierarchy ke dalam folder `Assets/Prefabs/Arena/`.
3. GameObject di Hierarchy akan berubah warna menjadi biru, menandakan ia telah berhasil menjadi prefab:  
   📂 **`Assets/Prefabs/Arena/ArenaGrid_Prefab.prefab`**.

---

## 💻 3. Kode Sumber Lengkap

### `Assets/Scripts/Grid/GridTilemapView.cs`
Simpan kode berikut di `Assets/Scripts/Grid/GridTilemapView.cs`:

```csharp
using UnityEngine;
using UnityEngine.Tilemaps;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Grid
{
    /// <summary>
    /// Mengelola representasi visual highlight pada Tilemap Overlay di arena.
    /// Berperan murni sebagai View Layer yang merespons event dari CombatEvents bus.
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

        // Berlangganan (Subscribe) ke event bus saat komponen aktif di scene
        private void OnEnable()
        {
            CombatEvents.OnHighlightTilesRequested += RenderHighlights;
            CombatEvents.OnClearAllHighlights += ClearHighlights;
        }

        // Melepas langganan (Unsubscribe) saat non-aktif untuk mencegah memory leak
        private void OnDisable()
        {
            CombatEvents.OnHighlightTilesRequested -= RenderHighlights;
            CombatEvents.OnClearAllHighlights -= ClearHighlights;
        }

        /// <summary>
        /// Menggambar ubin warna (Highlight Overlay) secara batch pada koordinat yang diminta.
        /// Mengonversi koordinat logika 2D (Vector2Int) ke koordinat sel Tilemap Unity (Vector3Int).
        /// </summary>
        public void RenderHighlights(TileHighlightRequest request)
        {
            if (_highlightTilemap == null || request.Coordinates == null) return;

            // Dapatkan aset TileBase yang sesuai dengan gaya highlight yang diminta
            TileBase selectedTile = GetTileSpriteByStyle(request.Style);
            if (selectedTile == null) return;

            // Gambar ubin satu per satu ke dalam lapisan Tilemap Overlay
            foreach (var coord in request.Coordinates)
            {
                // Konversi koordinat grid (x, y) ke koordinat sel Tilemap Unity (x, y, z=0)
                Vector3Int tilemapCoord = new Vector3Int(coord.x, coord.y, 0);
                _highlightTilemap.SetTile(tilemapCoord, selectedTile);
            }
        }

        /// <summary>
        /// Menghapus seluruh visual highlight di arena dengan mengosongkan ubin pada Tilemap Overlay.
        /// </summary>
        public void ClearHighlights()
        {
            if (_highlightTilemap != null)
            {
                _highlightTilemap.ClearAllTiles();
            }
        }

        /// <summary>
        /// Memetakan tipe enum HighlightType ke aset sprite TileBase yang sesuai.
        /// </summary>
        private TileBase GetTileSpriteByStyle(HighlightType style)
        {
            return style switch
            {
                HighlightType.DangerEnemyIntent => _dangerTileSprite, // Merah: Indikator bahaya intent musuh
                HighlightType.ValidCardTarget => _validTileSprite,    // Hijau: Target kartu yang valid
                HighlightType.MovementRange => _moveTileSprite,       // Biru: Jangkauan langkah/gerak
                HighlightType.HoverPreview => _hoverTileSprite,       // Oranye/Kuning: Ubin yang sedang disorot kursor
                _ => null
            };
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Buka scene `Assets/Scenes/MainBattleScene.unity`.
2. Klik tombol **Play** di toolbar atas Unity Editor.
3. Buat script penguji sederhana atau panggil via kode pengujian:
   ```csharp
   Vector2Int[] sampleArea = new Vector2Int[] { new Vector2Int(4, 4), new Vector2Int(4, 5), new Vector2Int(4, 6) };
   CombatEvents.OnHighlightTilesRequested?.Invoke(new TileHighlightRequest(sampleArea, HighlightType.DangerEnemyIntent));
   ```
4. Lihat panel **Game View**: Tiga ubin merah semi-transparan akan muncul tepat di posisi sel grid (4,4), (4,5), dan (4,6) di atas lapisan lantai tanpa tertutup latar belakang.
5. Panggil `CombatEvents.OnClearAllHighlights?.Invoke();` untuk memvalidasi bahwa seluruh ubin highlight terhapus seketika.
