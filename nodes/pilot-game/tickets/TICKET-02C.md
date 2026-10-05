---
id: TICKET-02C
title: Dataset SO MVP Terpilih (3 Kartu Sinergi 1, 1 Musuh, 1 Consumable)
status: Done
priority: High
labels: [Data, ScriptableObjects, Enemies, Consumables, Cards, Fase1]
---

# Deskripsi
Mengonfigurasi dan memvalidasi **Dataset ScriptableObjects MVP Terpilih** yang secara nyata telah diintegrasikan pada proyek Unity (`Tubbies Pilot Game`) untuk mendukung siklus pengujian taktis vertical slice:
1. **3 Kartu Sinergi 1:** `Card_ThrowingBlade.asset` (CARD-009), `Card_ShadowStep.asset` (CARD-034), `Card_SerratedDagger.asset` (CARD-031).
2. **1 Musuh Dasar (*Melee Minion*):** `Enemy_TatteredConscript.asset` (`enemy_tattered_conscript`).
3. **1 Item Consumable (*Instant Heal*):** `Consumable_ElixirOfLife.asset` (`consumable_elixir_of_life`).

## Acceptance Criteria
- [x] ScriptableObject musuh (`EnemyData.cs`), consumable (`ConsumableData.cs`), dan kartu (`CardData.cs`) terdefinisi di `Assets/Scripts/Cards/`.
- [x] **1 file musuh `.asset`** terkonfigurasi di `Assets/ScriptableObjects/Enemies/`:
  - `Enemy_TatteredConscript.asset` (`enemy_tattered_conscript`): Melee Minion | HP: 18 | Move: 2 | Range: 1 | Atk: 5 | Shield: 2 | Sprite: `Enemy_TatteredConscript_Sprite.jpg`.
- [x] **1 file consumable `.asset`** terkonfigurasi di `Assets/ScriptableObjects/Consumables/`:
  - `Consumable_ElixirOfLife.asset` (`consumable_elixir_of_life`): InstantHeal | Heal 10 HP | Icon: `Consumable_ElixirOfLife_Icon.jpg`.
- [x] **3 file kartu Sinergi 1 `.asset`** terkonfigurasi di `Assets/ScriptableObjects/Cards/`:
  - `Card_ThrowingBlade.asset` (`CARD-009`): Attack | Range 3 | 5 Dmg + Bleed 3 turns.
  - `Card_ShadowStep.asset` (`CARD-034`): Movement | Range 4 | Teleport behind + Bleed prep.
  - `Card_SerratedDagger.asset` (`CARD-031`): Attack | Range 1 | 6 Dmg ($2\times=12$ if Bleed) + Refresh Bleed.
- [x] Script editor generator `MVPDatasetGeneratorEditor.cs` tersedia untuk men-generate otomatis dataset MVP ini.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Cards/CardData.cs`
- `Assets/Scripts/Cards/EnemyData.cs`
- `Assets/Scripts/Cards/ConsumableData.cs`
- `Assets/ScriptableObjects/Cards/` (3 file `.asset`)
- `Assets/ScriptableObjects/Enemies/Enemy_TatteredConscript.asset`
- `Assets/ScriptableObjects/Consumables/Consumable_ElixirOfLife.asset`
- `Assets/Scripts/Editor/MVPDatasetGeneratorEditor.cs`

## Dependensi
- **Bergantung pada:** TICKET-01, TICKET-02 (EnemyData base class).
- **Digunakan oleh:** TICKET-03 (`EnemyAICalculator` menggunakan `AIPattern`), TICKET-03B (CombatMathEngine menggunakan `BaseShield`), TICKET-12 (Boss AI butuh tipe musuh).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Membuat asset ScriptableObject `Enemy_TatteredConscript.asset` di `Assets/ScriptableObjects/Enemies/`.
  2. Mengonfigurasi `Consumable_ElixirOfLife.asset` di `Assets/ScriptableObjects/Consumables/`.
  3. Memvalidasi 3 asset kartu `Card_ThrowingBlade`, `Card_ShadowStep`, dan `Card_SerratedDagger` di `Assets/ScriptableObjects/Cards/`.
  4. Menyelaraskan seluruh metadata dan file sprite art di `Assets/Art/Sprites/`.
- **Keputusan Desain & Arsitektur:**
  - Standarisasi PPU 64 dan Point Filter pada seluruh sprite MVP untuk menjaga pixel clarity.
- **Ringkasan File Terpengaruh:**
  - `Assets/ScriptableObjects/Enemies/Enemy_TatteredConscript.asset`
  - `Assets/ScriptableObjects/Consumables/Consumable_ElixirOfLife.asset`
  - `Assets/ScriptableObjects/Cards/` (3 kartu)
- **Catatan & Temuan Tak Terduga:**
  - Penamaan file icon distandarisasi dari `Item_ElixirOfLife_Icon` menjadi `Consumable_ElixirOfLife_Icon` agar konsisten dengan `ConsumableData`.
