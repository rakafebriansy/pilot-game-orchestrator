---
id: TICKET-12C
title: 3 Bioma Menara Babel & Dynamic Hazards (Terrain Interaktif)
status: Todo
priority: Low
labels: [Art, Tilemap, Biomes, DynamicHazards, Domain1, Fase3]
---

# Deskripsi
Mengimplementasikan **3 palet visual bioma Menara Babel** yang berbeda untuk setiap chapter/lantai, serta **sistem terrain interaktif dinamis** (*Dynamic Hazards*) di mana elemen arena dapat berubah akibat skill kartu (semak terbakar, pilar hancur oleh knockback, duri aktif).

Berdasarkan Domain 1 Milestone 3 dari `01_domain_1_arena_tilemap.md`.

## Acceptance Criteria

### A. 3 Bioma Tilemap
- [ ] `BiomeData.cs` (`ScriptableObject`):
  - `string BiomeId`, `string BiomeName`.
  - `TileBase[] FloorTiles` — variasi tile lantai bioma.
  - `TileBase[] ObstacleTiles` — pilar/dinding bioma.
  - `TileBase[] BushTiles` — semak bioma.
  - `Color AmbientLightColor` — warna ambient URP 2D Light sesuai bioma.
  - `AudioClip BiomeBGM` — musik background bioma.
- [ ] **3 file `.asset`** Bioma:
  1. `Biome_LowerVaults.asset` — Batu bata lumpur, semak alang-alang Euphrates, lampu obor merah-orange.
  2. `Biome_HangingGardens.asset` — Ubin marmer terawat, semak tebal hijau, air mancur, ambient biru-hijau.
  3. `Biome_ZigguratSummit.asset` — Emas kuno, pilar lapis lazuli, rune terkutuk bercahaya ungu.
- [ ] `BiomeManager.cs` (MonoBehaviour):
  - Method `void ApplyBiome(BiomeData biome)` — swap semua tile sprite di Tilemap layers, ubah ambient light color, play BGM bioma.
  - Dipanggil oleh `MapManager` saat chapter baru dimulai.

### B. Dynamic Hazards (Terrain Interaktif)
- [ ] `DynamicTileManager.cs` (MonoBehaviour):
  - Method `void BurnBush(Vector2Int coord)` — ubah tile semak menjadi tile abu (`TileType.BurnedBush`) + particle smoke.
  - Method `void DestroyPillar(Vector2Int coord)` — ubah tile pilar menjadi tile puing (`TileType.DestroyedPillar`) + particle debris + update `GridDataModel` agar tile menjadi walkable.
  - Method `void ActivateHazardSpike(Vector2Int coord)` — ubah tile lantai menjadi `TileType.HazardTrap` yang aktif (animate berduri naik-turun).
- [ ] Subscribe events:
  - `CombatEvents.OnSkillExecuted` + skill Storm/Fire → `BurnBush()` pada tile target.
  - `CombatEvents.OnUnitMoved` + knockback ke obstacle → `DestroyPillar()`.
- [ ] **Verifikasi:** Memainkan kartu `Card_Storm` di atas semak → semak menjadi abu di tilemap → tile menjadi walkable di `GridDataModel`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/BiomeData.cs`
- `Assets/Scripts/Map/BiomeManager.cs`
- `Assets/Scripts/Grid/DynamicTileManager.cs`
- `Assets/ScriptableObjects/Biomes/` (3 file `.asset`)
- `Assets/Tilemaps/Biomes/` (tile sprites per bioma)

## Dependensi
- **Bergantung pada:** TICKET-04 (Tilemap structure), TICKET-04B (Audio environment / BGM), TICKET-03 (GridDataModel harus update saat tile berubah), TICKET-09 (BiomeManager dipanggil dari MapManager).
- **Digunakan oleh:** Konten kosmetik — tidak ada tiket lain yang hard-depend, tapi penting untuk GDD 1.0 release.

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
