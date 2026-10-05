---
id: TICKET-02B
title: "Kartu Sinergi 1: Bleed & Assassination Archetype — ScriptableObjects (CardData)"
status: Done
priority: High
labels: [Data, ScriptableObjects, Cards, BleedSynergy, Fase1]
---

# Deskripsi
Mengimplementasikan set kartu **Sinergi 1: Bleed & Assassination Archetype** dari `cards.md` (§11) sebagai file `.asset` ScriptableObjects (`CardData.cs`) lengkap dengan metadata parameter baku (`PhaseRestriction`, `CastRange`, `AoERadius`, status abnormal `Bleed`, dan mekanik multiplier sinergi).

Iterasi ini secara eksklusif memfokuskan implementasi pada 3 kartu kombo pembunuh:
1. **Throwing Blade (`CARD-009`):** Pembuka kombo jarak jauh (Range 3) yang menginfeksi target dengan status `Bleed` (2 Direct Dmg/turn selama 3 turn).
2. **Shadow Step (`CARD-034`):** Reposisi instan (*Teleport*) ke petak belakang musuh target (Range 4) + mempersiapkan bonus `Bleed` untuk serangan berikutnya.
3. **Serrated Dagger (`CARD-031`):** Eksekutor melee (*Finisher*) yang menggandakan damage menjadi **$2\times\text{ Damage}$ (12 Damage)** jika target berstatus `Bleed` sekaligus me-refresh durasi DoT `Bleed`.

## Acceptance Criteria
- [x] `CardData.cs` mengimplementasikan parameter baku sesuai skema `cards.md`:
  - `string Id`, `string Name`, `string Description`.
  - `CardActionType ActionType` (`Attack`, `Defense`, `Movement`, `StatusModifier`, `Utility`).
  - `TargetAreaType TargetArea` (`SingleTarget`, `LinearLine`, `RadiusArea`, `ConeArc`, `SelfOnly`, `GlobalAllEnemies`, `GroundTile`).
  - `CombatPhase PhaseRestriction` (`IntentPhase`, `PlayerPhase`, `RoundResetPhase`).
  - `int BaseDamage`, `int BaseShield`, `int Range`, `int AreaRadius`.
  - `StatusEffectType InflictedStatus`, `int StatusDuration`.
  - `bool RequiresBleedSynergy` (atau flag multiplier kondisi Bleed).
- [x] 3 file `.asset` kartu Sinergi 1 dibuat di `Assets/ScriptableObjects/Cards/`:
  1. `Card_ThrowingBlade.asset` (`CARD-009`): Attack | PlayerPhase | CastRange: 3 | BaseDamage: 5 | InflictedStatus: Bleed | StatusDuration: 3.
  2. `Card_ShadowStep.asset` (`CARD-034`): Movement | PlayerPhase | CastRange: 4 | Teleport behind target + apply Bleed next hit.
  3. `Card_SerratedDagger.asset` (`CARD-031`): Attack | PlayerPhase | CastRange: 1 | BaseDamage: 6 ($2\times = 12$ Dmg jika target Bleed) + Refresh Bleed duration.
- [x] Deck pertempuran starter Nabu untuk iterasi ini dikonstruksi berisi komposisi 15 kartu dari arketipe ini (e.g., 5× Throwing Blade, 5× Shadow Step, 5× Serrated Dagger).
- [x] Script generator otomatis `CardGeneratorEditor.cs` diperbarui untuk men-generate 3 ScriptableObject kartu ini dan menyusun starter deck 15 kartu Sinergi 1.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Cards/CardData.cs` (update skema parameter baku)
- `Assets/Scripts/Core/Data/CombatTypes.cs` (update enum tipe)
- `Assets/ScriptableObjects/Cards/Card_ThrowingBlade.asset`
- `Assets/ScriptableObjects/Cards/Card_ShadowStep.asset`
- `Assets/ScriptableObjects/Cards/Card_SerratedDagger.asset`
- `Assets/Scripts/Editor/CardGeneratorEditor.cs`

## Dependensi
- **Bergantung pada:** TICKET-01 (CombatTypes, StatusEffectType.Bleed), TICKET-02 (CardData base).
- **Digunakan oleh:** TICKET-03B (CardPlayValidator & CombatMathEngine), TICKET-05B (VFX Sinergi 1), TICKET-06 (DeckManager).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Merestrukturisasi `CardData.cs` sesuai parameter 1 Turn = 1 Card (tanpa energi).
  2. Mengonfigurasi aset gambar `.jpg` dan `.meta` untuk `Card_ThrowingBlade_Art.jpg`, `Card_ShadowStep_Art.jpg`, dan `Card_SerratedDagger_Art.jpg` (PPU 64, Point Filter).
  3. Membuat file ScriptableObject `.asset` untuk ketiga kartu Sinergi 1 di `Assets/ScriptableObjects/Cards/`.
- **Keputusan Desain & Arsitektur:**
  - Menstandarisasi penamaan field C# menjadi clean PascalCase (`Id`, `Name`, `Description`, `Art`, `Sprite`).
- **Ringkasan File Terpengaruh:**
  - `Assets/Scripts/Cards/CardData.cs`
  - `Assets/ScriptableObjects/Cards/Card_ThrowingBlade.asset`
  - `Assets/ScriptableObjects/Cards/Card_ShadowStep.asset`
  - `Assets/ScriptableObjects/Cards/Card_SerratedDagger.asset`
- **Catatan & Temuan Tak Terduga:**
  - Git LFS digunakan untuk aset biner gambar sesuai spesifikasi `.gitattributes`.
