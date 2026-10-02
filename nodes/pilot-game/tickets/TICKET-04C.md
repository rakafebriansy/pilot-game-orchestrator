---
id: TICKET-04C
title: Pulsing Danger Shader & ArenaEnvironment_Prefab Final
status: Todo
priority: Medium
labels: [View, Shader, VFX, Tilemap, Domain1, Fase1]
---

# Deskripsi
Mengimplementasikan **shader denyut merah berkedip** (*Pulsing Danger Shader*) untuk mempertegas visual telegraph serangan musuh, lalu mengemas seluruh setup arena (Tilemap layers, Camera, Lights, Shader) menjadi **`ArenaEnvironment_Prefab.prefab`** yang self-contained dan siap di-instantiate di `MainBattleScene.unity`.

## Acceptance Criteria

### A. Pulsing Danger Shader
- [ ] Shader Graph atau URP Lit 2D Shader `PulsingDanger.shader`:
  - Property: `_BaseColor`, `_PulseColor` (default merah #FF2244), `_PulseSpeed` (default 2.0).
  - Output: lerp antara `_BaseColor` dan `_PulseColor` menggunakan `sin(Time * _PulseSpeed)` normalized 0-1.
- [ ] Material `DangerTile_Material.mat` menggunakan shader ini, diterapkan pada `_dangerTileSprite` di `GridTilemapView.cs`.
- [ ] Script `DangerTileAnimator.cs` (optional alternative jika Shader Graph tidak available): coroutine yang toggle tile color antara merah dan merah-gelap setiap 0.25 detik saat highlight type = `DangerEnemyIntent`.

### B. ArenaEnvironment_Prefab Final Assembly
- [ ] `ArenaEnvironment_Prefab.prefab` mengandung:
  ```
  ArenaEnvironment_Prefab
  ├── [Grid]                    ← Komponen Grid Unity
  │   ├── BaseFloor_Tilemap     ← Sorting Layer: Floor, Order: 0
  │   ├── Obstacles_Tilemap     ← Sorting Layer: Environment, Order: 1, ShadowCaster2D
  │   └── HighlightOverlay_Tilemap ← Sorting Layer: Overlay, Order: 2
  ├── [Camera]
  │   └── Main Camera           ← Orthographic, Post-Processing Volume
  ├── [Lighting]
  │   ├── GlobalLight2D         ← Ambient malam
  │   ├── TorchLight_NW         ← Point Light 2D + TorchFlicker
  │   ├── TorchLight_NE
  │   ├── TorchLight_SW
  │   └── TorchLight_SE
  └── GridTilemapView           ← Script subscriber event
  ```
- [ ] Prefab bisa di-drag ke scene baru dan langsung berfungsi tanpa konfigurasi tambahan.
- [ ] Semua field serialized (sprite tiles, material shader) sudah terisi di Inspector.

## Target Lingkup File (Affected Files)
- `Assets/Shaders/PulsingDanger.shader` (atau `PulsingDanger.shadergraph`)
- `Assets/Materials/DangerTile_Material.mat`
- `Assets/Scripts/Grid/DangerTileAnimator.cs`
- `Assets/Prefabs/Arena/ArenaEnvironment_Prefab.prefab` (update dari TICKET-04)

## Dependensi
- **Bergantung pada:** TICKET-04 (base tilemap structure), TICKET-04B (URP Lighting setup dan Camera config).
- **Digunakan oleh:** TICKET-07 (scene assembly di MainBattleScene.unity).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
