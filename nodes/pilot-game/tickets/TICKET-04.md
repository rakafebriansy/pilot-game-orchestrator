---
id: TICKET-04
title: Visualisasi Arena & Sistem Highlight Tilemap
status: Todo
priority: High
labels: [View, Tilemap, URP, Environment, Domain1]
---

# Deskripsi
Membangun struktur visual arena dengan **tiga layer 2D Tilemap** di Unity (Base Floor, Obstacles, Highlight Overlay), mengonfigurasi kamera Orthographic URP 2D, serta mengimplementasikan `GridTilemapView.cs` sebagai subscriber event highlight yang menggambar ubin merah/hijau/biru secara dinamis berdasarkan event dari TICKET-03.

Tiket ini adalah implementasi "View Layer" (lihat System Design §1) — tidak boleh mengandung logika pertempuran apapun.

## Acceptance Criteria
- [ ] Struktur Tilemap terbentuk di dalam scene dengan hierarchy:
  ```
  [Arena Grid Root]
  ├── BaseFloor_Tilemap     (Sorting Layer: "Floor", Order: 0)
  ├── Obstacles_Tilemap     (Sorting Layer: "Environment", Order: 1)
  └── HighlightOverlay_Tilemap (Sorting Layer: "Overlay", Order: 2)
  ```
- [ ] Tilemap dikonfigurasi dengan cell size `(1, 1, 0)` untuk tile 15×15.
- [ ] `GridTilemapView.cs` (MonoBehaviour):
  - `[SerializeField] Tilemap _highlightTilemap` — referensi ke `HighlightOverlay_Tilemap`.
  - `[SerializeField] TileBase _dangerTileSprite` — Ubin merah (enemy intent).
  - `[SerializeField] TileBase _validTileSprite` — Ubin hijau (jangkauan kartu valid).
  - `[SerializeField] TileBase _moveTileSprite` — Ubin biru (jangkauan gerak).
  - Subscribe `CombatEvents.OnHighlightTilesRequested` di `OnEnable()`.
  - Unsubscribe di `OnDisable()` (mencegah memory leak).
  - Method `RenderHighlights(TileHighlightRequest request)` — mapping `HighlightType` ke tile sprite, konversi `Vector2Int` ke `Vector3Int` untuk Tilemap.
  - Method `ClearHighlights()` — memanggil `_highlightTilemap.ClearAllTiles()`.
- [ ] Prefab `ArenaGrid_Prefab.prefab` dibuat di `Assets/Prefabs/Arena/` dengan semua komponen Tilemap terkonfigurasi.
- [ ] Kamera menggunakan `Camera.main` dengan Projection: Orthographic, ukuran disesuaikan agar seluruh grid 15×15 terlihat.
- [ ] **Verifikasi visual:** Memanggil event `OnHighlightTilesRequested` di Play Mode memunculkan ubin berwarna yang sesuai di Tilemap Overlay.
- [ ] **URP 2D Lighting:** Minimal satu `Global Light 2D` aktif di scene agar tidak ada objek yang tampil gelap total.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Grid/GridTilemapView.cs`
- `Assets/Prefabs/Arena/ArenaGrid_Prefab.prefab`

## Dependensi
- **Bergantung pada:** TICKET-01 (`CombatEvents`, `TileHighlightRequest`, `HighlightType`).
- **Digunakan oleh:** TICKET-07 (scene assembly di `MainBattleScene.unity`).

## Catatan Teknis
- `GridTilemapView` HARUS unsubscribe dari event di `OnDisable()` — ini adalah kewajiban mutlak untuk mencegah NullReferenceException saat object di-destroy.
- Sprite ubin merah/hijau/biru dapat menggunakan placeholder sprite bawaan Unity (Square) dengan material warna berbeda untuk verifikasi awal.
- Konversi: `Vector3Int cellPos = new Vector3Int(coord.x, coord.y, 0)`.

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  - *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
