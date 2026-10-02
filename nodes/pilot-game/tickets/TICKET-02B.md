---
id: TICKET-02B
title: 14 Kartu Tempur Nabu — ScriptableObjects Lengkap
status: Todo
priority: High
labels: [Data, ScriptableObjects, Cards, Fase1]
---

# Deskripsi
Melengkapi katalog **14 kartu tempur penuh Nabu** sebagai file `.asset` ScriptableObjects dengan data lengkap sesuai spesifikasi desain dari `pilot-game-team-docs/01_game_design/cards.md`. Tiket ini adalah breakdown dari TICKET-02 yang hanya membuat 5 kartu sample.

Kartu-kartu ini dibagi ke dalam 3 **Fase Eksekusi** (mechanic baru dari cards.md): `PreCombat` (fase sebelum), `MainPhase` (fase utama), dan `PostPhase` (fase akhir). Field `PhaseRestriction` harus ditambahkan ke `CardData.cs`.

## Acceptance Criteria
- [ ] `CardData.cs` diperbarui dengan field tambahan:
  - `CardPhaseRestriction PhaseRestriction` — Enum baru: `AnyPhase`, `PreCombatOnly`, `MainPhaseOnly`, `PostPhaseOnly`, `Immediate`.
  - `bool HasStatusEffect`, `StatusEffectType StatusEffect`, `int StatusEffectDuration`.
  - `bool PushesUnit`, `int PushDistance`.
  - `CardAreaType AreaType` — Enum: `SingleTarget`, `LinearLine`, `Radial`, `CrossShape`, `WholeRow`, `WholeColumn`, `FreeSelect`.
- [ ] 14 file `.asset` kartu dibuat di `Assets/ScriptableObjects/Cards/`:
  1. `Card_Teleport.asset` — Movement | Fase: PreCombat | AreaType: FreeSelect | Range: seluruh map.
  2. `Card_Decoy.asset` — Utility | Fase: PostPhase | Spawn tiruan + teleport pemain.
  3. `Card_Frost.asset` — Utility | Fase: MainPhase | AreaType: Radial | Status: Freeze area.
  4. `Card_HeavyRain.asset` — Utility | Immediate | Duration: 3 rounds | Efek: seluruh unit -1 movement.
  5. `Card_Fog.asset` — Utility | MainPhase | Duration: 3 rounds | Efek: random hit chance di area fog.
  6. `Card_Storm.asset` — Utility | MainPhase | Duration: 3 rounds | Auto damage musuh tiap round.
  7. `Card_ClearWeather.asset` — Utility | Immediate | Hapus semua weather effect aktif.
  8. `Card_SkeletonArmy.asset` — Utility | MainPhase | Summon skeleton ring sekeliling pemain.
  9. `Card_ThrowingBlade.asset` — Attack | MainPhase | Range: 3 | Status: Bleed 2 turns.
  10. `Card_SandBurial.asset` — Utility | PreCombat | Status: Immobilize 1 round (target).
  11. `Card_Clone.asset` — Utility | PostPhase | Identik Clone mechanic dengan Decoy.
  12. `Card_Dash.asset` — Movement | MainPhase | Range: 3 | PushesUnit: true, PushDistance: 1.
  13. `Card_SuperPunch.asset` — Attack | MainPhase | Range: 1 | Damage: 15 | Self-push: 2 tile.
  14. `Card_GravityLift.asset` — Utility | PreCombat | Range: 3 radius | Target -1 movement.
- [ ] Enum baru `CardPhaseRestriction`, `CardAreaType` ditambahkan ke `CombatTypes.cs` (update TICKET-01 artifact).
- [ ] Semua field aset terisi di Unity Inspector dan dapat dibaca oleh C#.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/CardData.cs` (update)
- `Assets/Scripts/Core/Data/CombatTypes.cs` (update — tambah enum baru)
- `Assets/ScriptableObjects/Cards/` (14 file `.asset` baru)

## Dependensi
- **Bergantung pada:** TICKET-01, TICKET-02 (CardData base class).
- **Digunakan oleh:** TICKET-03B (CardPlayValidator butuh field AreaType & PhaseRestriction), TICKET-06 (DeckManager).

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
