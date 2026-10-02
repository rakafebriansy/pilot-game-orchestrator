---
id: TICKET-02C
title: 9 Musuh SO Lengkap & 5 Item Consumable SO
status: Todo
priority: High
labels: [Data, ScriptableObjects, Enemies, Items, Fase1]
---

# Deskripsi
Membuat **9 file `.asset` ScriptableObjects musuh lengkap** berdasarkan `selected_enemies.md` (9 musuh terpilih dari matriks 3×3: Melee/Ranged/Support × Minion/Regular/Elite), beserta **5 item consumable** dengan data statistik yang sesuai.

Tiket ini juga menambahkan field tambahan pada `EnemyData.cs` yang dibutuhkan oleh `EnemyAICalculator.cs` (TICKET-03) untuk mengeksekusi pola AI yang benar.

## Acceptance Criteria
- [ ] `EnemyData.cs` diperbarui dengan field tambahan:
  - `EnemyCategory Category` — Enum: `Melee`, `Ranged`, `Support`.
  - `EnemyTier Tier` — Enum: `Minion`, `Regular`, `Elite`.
  - `EnemyAIPattern AIPattern` — Enum: `ChasePlayer`, `FlankPlayer`, `HoldPosition`, `SupportAllies`, `HybridAggressive`.
  - `int AttackPattern` — bitmask untuk pola serangan (forward, diagonal, area).
  - `int BaseShield` — Shield awal yang dimiliki musuh (0 untuk minion).
  - Enum `EnemyCategory`, `EnemyTier`, `EnemyAIPattern` ditambahkan ke `CombatTypes.cs`.
- [ ] **9 file musuh `.asset`** di `Assets/ScriptableObjects/Enemies/`:
  1. `Enemy_TatteredConscript.asset` — Melee Minion | HP: 18 | Move: 2 | Range: 1 | Damage: 5 | AI: ChasePlayer
  2. `Enemy_CuneiformSlicer.asset` — Melee Regular | HP: 32 | Move: 2 | Range: 1 | Damage: 8 | AI: FlankPlayer
  3. `Enemy_ExecutionerArchive.asset` — Melee Elite | HP: 85 | Move: 2 | Range: 2 | Damage: 16 (AoE) | AI: HybridAggressive
  4. `Enemy_ArchiveSlingBoy.asset` — Ranged Minion | HP: 14 | Move: 2 | Range: 4 | Damage: 4 | AI: HoldPosition
  5. `Enemy_ScrollPyromancer.asset` — Ranged Regular | HP: 28 | Move: 1 | Range: 3 | Damage: 10 (AoE) | AI: HoldPosition
  6. `Enemy_GrandMarksman.asset` — Ranged Elite | HP: 65 | Move: 1 | Range: 6 | Damage: 18 | AI: HybridAggressive
  7. `Enemy_TombBellRinger.asset` — Support Minion | HP: 20 | Move: 2 | Range: 2 | Damage: 2 | AI: SupportAllies
  8. `Enemy_BabelWardTemplar.asset` — Support Regular | HP: 45 | Move: 1 | Range: 0 | BaseShield: 12 | AI: SupportAllies
  9. `Enemy_HighOracleEnki.asset` — Support Elite | HP: 70 | Move: 1 | Range: 3 | Damage: 6 | AI: SupportAllies
- [ ] **5 file consumable `.asset`** di `Assets/ScriptableObjects/Consumables/`:
  1. `Item_HealingPotion.asset` — Pulihkan 15 HP.
  2. `Item_ShieldRune.asset` — Berikan 10 Shield untuk 1 ronde.
  3. `Item_ScrollOfHaste.asset` — +1 aksi kartu untuk ronde ini.
  4. `Item_PoisonVial.asset` — Terapkan Bleed 3 turns ke target.
  5. `Item_AncientFragment.asset` — +50 Expedition Points saat digunakan.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/EnemyData.cs` (update)
- `Assets/Scripts/Core/Data/CombatTypes.cs` (update — tambah enum EnemyCategory, EnemyTier, AIPattern)
- `Assets/ScriptableObjects/Enemies/` (9 file `.asset`)
- `Assets/ScriptableObjects/Consumables/` (5 file `.asset`)

## Dependensi
- **Bergantung pada:** TICKET-01, TICKET-02 (EnemyData base class).
- **Digunakan oleh:** TICKET-03 (`EnemyAICalculator` menggunakan `AIPattern`), TICKET-03B (CombatMathEngine menggunakan `BaseShield`), TICKET-12 (Boss AI butuh tipe musuh).

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
