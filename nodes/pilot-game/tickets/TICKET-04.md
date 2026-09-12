---
id: TICKET-04
title: Visualisasi Arena & Sistem Highlight Tilemap
status: Todo
priority: High
labels: [View, Tilemap, URP, Environment]
---

# Deskripsi
Membangun susunan layer 2D Tilemap di Unity (Base Floor, Obstacles, Highlight Overlay), mengonfigurasi kamera Orthographic, serta mengimplementasikan `GridTilemapView.cs` untuk menerima event highlight dan menggambar ubin merah/hijau/biru secara dinamis.

## Acceptance Criteria
- [ ] Layer Tilemap tersusun dengan sorting layer yang benar (`Floor`, `Environment`, `Overlay`).
- [ ] `GridTilemapView.cs` berlangganan event `OnHighlightTilesRequested` dan `OnClearAllHighlights`.
- [ ] Saat event highlight aktif, sel ubin Tilemap Unity terisi dengan sprite ubin berwarna yang sesuai (`_dangerTileSprite`, `_validTileSprite`, `_moveTileSprite`).

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Grid/GridTilemapView.cs`
- `Assets/Prefabs/Arena/ArenaGrid_Prefab.prefab`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
