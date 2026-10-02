---
id: TICKET-03B
title: Mesin Matematika Pertempuran & Validator Kartu (CombatMathEngine + CardPlayValidator)
status: Todo
priority: High
labels: [Logic, CombatMath, Validation, PureCS, Domain4, Fase1]
---

# Deskripsi
Komponen **kritis yang hilang dari Fase 1** — tanpa ini, kartu tidak bisa dieksekusi secara sah. Tiket ini mengimplementasikan dua kelas logika murni:

1. **`CombatMathEngine.cs`** — mesin hitung damage, mitigasi shield, dan hasil final combat action (terlihat di diagram pada node `Combat Math Engine`).
2. **`CardPlayValidator.cs`** — memvalidasi apakah suatu kartu boleh dimainkan ke koordinat target berdasarkan range, area type, phase restriction, dan status unit.

Kedua class ini adalah **Logic Layer** — tidak boleh mengandung MonoBehaviour atau visual.

## Acceptance Criteria

### A. `CombatMathEngine.cs` (Pure C#)
- [ ] Method `DamagePayload CalculateDamage(int targetUnitId, int rawDamage, int targetShield)`:
  - Formula: `finalDamage = Max(0, rawDamage - targetShield)`, `remainingShield = Max(0, targetShield - rawDamage)`.
  - Return `DamagePayload` struct dengan field `TargetUnitId`, `DamageAmount`, `ShieldRemaining`.
- [ ] Method `int CalculateManhattanDistance(Vector2Int a, Vector2Int b)`:
  - Formula: `Abs(a.x - b.x) + Abs(a.y - b.y)`.
- [ ] Method `bool IsInRange(Vector2Int attacker, Vector2Int target, int range)`:
  - Menggunakan Manhattan distance. Return true jika `distance <= range`.
- [ ] Method `void ApplyStatusEffect(int unitId, StatusEffectType type, int duration)`:
  - Menyimpan status aktif ke `Dictionary<int, List<ActiveStatusEffect>>`.
  - Broadcast `CombatEvents.OnStatusEffectApplied?.Invoke(unitId, type, duration)` (event baru).
- [ ] Method `void TickStatusEffects()`:
  - Dipanggil setiap `RoundResetPhase` — kurangi duration semua status, broadcast `OnStatusEffectTick`, hapus yang expired.
- [ ] Unit test `CombatMathTests.cs` (NUnit EditMode):
  - Test `CalculateDamage(0 shield)` → damage penuh.
  - Test `CalculateDamage(lebih besar dari damage)` → final damage = 0, shield berkurang.
  - Test `IsInRange` true/false dengan berbagai jarak.

### B. `CardPlayValidator.cs` (Pure C#)
- [ ] Method `bool CanPlayCard(CardData card, Vector2Int playerPos, Vector2Int targetCoord, GridDataModel grid, CombatPhase currentPhase)`:
  - Cek `card.PhaseRestriction` vs `currentPhase`.
  - Cek `IsInRange(playerPos, targetCoord, card.Range)`.
  - Cek target tile sesuai `card.AreaType` (misal `SingleTarget` harus ada occupant jika attack).
  - Return false + alasan jika tidak valid.
- [ ] Method `List<Vector2Int> GetValidTargetTiles(CardData card, Vector2Int playerPos, GridDataModel grid)`:
  - Berdasarkan `card.AreaType` dan `card.Range`, hitung semua koordinat valid yang bisa dijadikan target.
  - Digunakan oleh `CardHandController` saat hover kartu untuk preview highlight.
- [ ] Integrasi: `CardPlayValidator` dipanggil oleh `PlayerPhaseState` sebelum broadcast `OnCardPlayed`.

### C. `StatusEffectType` Enum & Data
- [ ] Enum `StatusEffectType` di `CombatTypes.cs`: `Bleed`, `Freeze`, `Immobilize`, `Stun`, `Vulnerable`, `Shielded`.
- [ ] Struct `ActiveStatusEffect`: `StatusEffectType Type`, `int RemainingDuration`, `int UnitId`.
- [ ] `CombatEvents.cs` update: tambah `OnStatusEffectApplied(int unitId, StatusEffectType, int duration)` dan `OnStatusEffectExpired(int unitId, StatusEffectType)`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/CombatMathEngine.cs`
- `Assets/Scripts/Cards/CardPlayValidator.cs`
- `Assets/Scripts/Core/Data/CombatTypes.cs` (update — tambah StatusEffectType, ActiveStatusEffect)
- `Assets/Scripts/Core/Events/CombatEvents.cs` (update — tambah 2 event baru)
- `Assets/Tests/EditMode/CombatMathTests.cs`

## Dependensi
- **Bergantung pada:** TICKET-01 (CombatEvents, Payloads), TICKET-02B (CardData dengan AreaType, PhaseRestriction), TICKET-03 (GridDataModel).
- **Digunakan oleh:** TICKET-07 (FSM memanggil Validator sebelum execute kartu), TICKET-06C (CardHandController menggunakan `GetValidTargetTiles`).

## Catatan Teknis
- `CombatMathEngine` menyimpan status effects di `Dictionary` — ini state yang perlu di-reset setiap run baru.
- `CardPlayValidator` harus stateless (no fields) — semua state diinjeksikan via parameter.
- Formula damage mengikuti prioritas: `rawDamage - currentShield` (shield mengabsorb terlebih dahulu).

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
