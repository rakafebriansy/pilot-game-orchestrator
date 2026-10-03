---
id: TICKET-02
title: Katalog Data ScriptableObjects (Kartu, Musuh, Consumable)
status: Done
priority: High
labels: [Data, ScriptableObjects, Cards, Enemies, Consumables]
---

# Deskripsi
Menyusun **template data konfigurasi statis** menggunakan ScriptableObjects untuk tiga entitas utama: kartu jurus (`CardData.cs`), musuh (`EnemyData.cs`), dan item consumable (`ConsumableData.cs`). Kemudian membuat sample file `.asset` awal berdasarkan data desain dari `pilot-game-team-docs/01_game_design/`.

Tiket ini bergantung pada TICKET-01 (enum `CardActionType` diperlukan oleh `CardData.cs`).

## Acceptance Criteria
- [x] `CardData.cs` (`ScriptableObject`) mengimplementasikan properti data:
  - `string Id` — ID unik kartu (contoh: `"card_throwing_blade"`).
  - `string Name` — Nama tampilan kartu.
  - `string Description` — Deskripsi efek jurus kartu.
  - `CardActionType ActionType` — Tipe aksi (`Attack`, `Skill`, `Power`, `Movement`, `Utility`).
  - `TargetAreaType TargetArea`, `CombatPhase PhaseRestriction`, `int energyCost`.
  - `int BaseDamage`, `int BaseShield`, `int Range`, `int AreaRadius`.
  - `StatusEffectType InflictedStatus`, `int StatusDuration`.
  - `Sprite Art` — Sprite visual kartu.
  - `GameObject VFXPrefab`, `AudioClip SFX`.
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Data/Card Data"`.
- [x] `EnemyData.cs` (`ScriptableObject`) mengimplementasikan:
  - `string Id` — ID unik musuh.
  - `string Name` — Nama musuh.
  - `string Description` — Deskripsi latar naratif musuh.
  - `EnemyArchetype Archetype`, `EnemyHierarchy Hierarchy`.
  - `int MaxHealth` — HP maksimum musuh.
  - `int AttackPower` — Damage serangan dasar.
  - `int AttackRange` — Jangkauan serangan (dalam tile).
  - `int BaseShield` — Shield dasar musuh.
  - `int MoveSpeedTiles` — Jangkauan gerak per giliran (dalam tile).
  - `StatusEffectType InflictedStatus`, `int StatusDuration`.
  - `Sprite Sprite` — Sprite visual musuh.
  - `GameObject CharacterPrefab`, `GameObject VFXPrefab`, `AudioClip SFX`.
  - `List<CardData> CardDeck` — Deck musuh classless.
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Data/Enemy Data"`.
- [x] `ConsumableData.cs` (`ScriptableObject`) mengimplementasikan:
  - `string Id`, `string Name`, `string Description`, `Sprite Icon`.
  - `ConsumableEffectType EffectType`, `int EffectValue`.
  - `GameObject VFXPrefab`, `AudioClip SFX`.
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Data/Consumable Data"`.
- [x] Struktur folder `Assets/ScriptableObjects/Cards/`, `Assets/ScriptableObjects/Enemies/`, `Assets/ScriptableObjects/Consumables/` dan sprite placeholder dibuat.
- [x] Seluruh kode kompilasi bersih di Unity Editor.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Cards/CardData.cs`
- `Assets/Scripts/Cards/EnemyData.cs`
- `Assets/Scripts/Cards/ConsumableData.cs`
- `Assets/Art/Sprites/Card_ThrowingBlade_Art.jpg`
- `Assets/Art/Sprites/Enemy_TatteredConscript_Sprite.jpg`
- `Assets/Art/Sprites/Item_ElixirOfLife_Icon.jpg`
- `Assets/ScriptableObjects/Cards/`
- `Assets/ScriptableObjects/Enemies/`
- `Assets/ScriptableObjects/Consumables/`

## Dependensi
- **Bergantung pada:** TICKET-01 (membutuhkan `CardActionType` dari `CombatTypes.cs`).

## Catatan Teknis
- Standar penamaan field menggunakan sintaks C# PascalCase/camelCase yang bersih tanpa stuttering (`Id`, `Name`, `Description`, `Art`, `Sprite`, `Icon`).
- PPU aset sprite distandarisasi ke 64 PPU (Point Filter, No Compression).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Membuat template ScriptableObject `CardData.cs`, `EnemyData.cs`, dan `ConsumableData.cs` di bawah namespace `PilotGame.Cards`.
  2. Menyediakan metadata pendukung (`TargetAreaType`, `EnemyArchetype`, `EnemyHierarchy`, `ConsumableEffectType`).
  3. Mengonfigurasi folder target aset `Assets/ScriptableObjects/` dan aset sprite visual `Assets/Art/Sprites/`.
- **Keputusan Desain & Arsitektur:**
  - Mengadopsi konvensi penamaan field clean (`Id`, `Name`, `Description`) tanpa redundant prefix.
  - Menyertakan slot visual & audio (`VFXPrefab`, `SFX`) pada seluruh ScriptableObject entitas untuk keseragaman feedback audio-visual.
- **Ringkasan File Terpengaruh:**
  - `Assets/Scripts/Cards/CardData.cs`
  - `Assets/Scripts/Cards/EnemyData.cs`
  - `Assets/Scripts/Cards/ConsumableData.cs`
- **Catatan & Temuan Tak Terduga:**
  - Telah disinkronkan langsung dengan repositori Unity `Tubbies Pilot Game`.
