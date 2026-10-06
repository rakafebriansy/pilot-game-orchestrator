---
id: TICKET-03B
title: Mesin Matematika Pertempuran & Validator Kartu (CombatMathEngine + CardPlayValidator)
status: Done
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
- [x] Method `DamagePayload CalculateDamage(int targetUnitId, int rawDamage, int targetShield)`:
  - Formula: `finalDamage = Max(0, rawDamage - targetShield)`, `remainingShield = Max(0, targetShield - rawDamage)`.
  - Return `DamagePayload` struct dengan field `TargetUnitId`, `DamageAmount`, `ShieldRemaining`.
- [x] Method `int CalculateManhattanDistance(Vector2Int a, Vector2Int b)`:
  - Formula: `Abs(a.x - b.x) + Abs(a.y - b.y)`.
- [x] Method `bool IsInRange(Vector2Int attacker, Vector2Int target, int range)`:
  - Menggunakan Manhattan distance. Return true jika `distance <= range`.
- [x] Method `void ApplyStatusEffect(int unitId, StatusEffectType type, int duration)`:
  - Menyimpan status aktif ke `Dictionary<int, List<ActiveStatusEffect>>`.
  - Broadcast `CombatEvents.OnStatusEffectApplied?.Invoke(unitId, type, duration)` (event baru).
- [x] Method `void TickStatusEffects()`:
  - Dipanggil setiap `RoundResetPhase` — kurangi duration semua status, broadcast `OnStatusEffectTick`, hapus yang expired.
- [x] Unit test `CombatMathTests.cs` (NUnit EditMode):
  - Test `CalculateDamage(0 shield)` → damage penuh.
  - Test `CalculateDamage(lebih besar dari damage)` → final damage = 0, shield berkurang.
  - Test `IsInRange` true/false dengan berbagai jarak.

### B. `CardPlayValidator.cs` (Pure C#)
- [x] Method `bool CanPlayCard(CardData card, Vector2Int playerPos, Vector2Int targetCoord, GridDataModel grid, CombatPhase currentPhase)`:
  - Cek `card.PhaseRestriction` vs `currentPhase`.
  - Cek `IsInRange(playerPos, targetCoord, card.Range)`.
  - Cek target tile sesuai `card.AreaType` (misal `SingleTarget` harus ada occupant jika attack).
  - Cek StealthBush rule (GDD §4.1): Jika unit target berada di `TileType.StealthBush`, tidak boleh ditarget kecuali `CalculateManhattanDistance(playerPos, targetCoord) <= 1` (jarak 1 petak bersebelahan) via `grid.IsTargetStealthed(playerPos, targetCoord)`.
  - Return false + alasan jika tidak valid.
- [x] Method `List<Vector2Int> GetValidTargetTiles(CardData card, Vector2Int playerPos, GridDataModel grid)`:
  - Berdasarkan `card.AreaType` dan `card.Range`, hitung semua koordinat valid yang bisa dijadikan target.
  - Saring target yang berada di `StealthBush` di luar jarak 1 petak (jarak >= 2 petak tidak terlihat).
  - Untuk kartu bertipe Movement non-teleport: gunakan kalkulasi rute collision (berhenti sebelum rintangan).
  - Digunakan oleh `CardHandController` saat hover kartu untuk preview highlight.
- [x] Integrasi: `CardPlayValidator` dipanggil oleh `PlayerPhaseState` sebelum broadcast `OnCardPlayed`.

### C. `StatusEffectType` Enum & Data
- [x] Enum `StatusEffectType` di `CombatTypes.cs`: `None`, `Bleed`, `Freeze`, `Immobilize`, `Stun`, `Vulnerable`, `Shielded`, `Poison`, `Burn`.
- [x] Struct `ActiveStatusEffect`: `StatusEffectType Type`, `int RemainingDuration`, `int UnitId`.
- [x] `CombatEvents.cs` update: tambah `OnStatusEffectApplied(int unitId, StatusEffectType, int duration)` dan `OnStatusEffectExpired(int unitId, StatusEffectType)`.

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
  1. Mengimplementasikan `CombatMathEngine.cs` (Pure C#) yang menangani mitigasi shield pada `CalculateDamage`, kalkulasi jarak Manhattan `CalculateManhattanDistance`, pengecekan jangkauan `IsInRange`, serta siklus hidup efek status (`ApplyStatusEffect`, `TickStatusEffects`, `ResetEngine`).
  2. Mengimplementasikan `CardPlayValidator.cs` (Pure C#) statis untuk validasi kelayakan memainkan kartu (`CanPlayCard`) dengan pengecekan batas grid, pembatasan fase, jangkauan kartu, deteksi semak siluman (`IsTargetStealthed`), keberadaan okupansi target serang tunggal, dan kelayakan ubin pergerakan (`IsWalkable`).
  3. Menyediakan metode `GetValidTargetTiles` untuk menyaring petak target yang sah dalam radius range kartu dengan memfilter semak siluman tak terlihat.
  4. Menulis rangkaian unit test `CombatMathTests.cs` pada EditMode testing suite yang memverifikasi penyerapan damage oleh shield dan akurasi kalkulasi Manhattan distance.
  5. Memastikan seluruh *in-code string literals* (alasan kegagalan, log) menggunakan bahasa Inggris baku dan mengikuti seluruh konvensi kode proyek.
- **Keputusan Desain & Arsitektur:**
  - `CardPlayValidator` dirancang sebagai static helper murni (stateless) guna memastikan kemudahan pengujian dan isolasi dari siklus hidup runtime MonoBehaviour.
  - Memanfaatkan fungsi relasional `GridDataModel.IsTargetStealthed(casterCoordinate, targetCoordinate)` untuk abstraksi logika semak siluman (jarak >= 2 tidak terlihat, jarak <= 1 terlihat).
  - `CombatMathEngine` mengelola status effect secara terisolasi dengan tuple deconstruction pada iterasi dictionary dan iterasi mundur untuk manipulasi list yang aman dari exception modifikasi koleksi.
- **Ringkasan File Terpengaruh:**
  - `Assets/Scripts/Cards/CardPlayValidator.cs` (File Baru)
  - `Assets/Scripts/Core/Data/CombatMathEngine.cs` (File Baru)
  - `Assets/Tests/EditMode/CombatMathTests.cs` (File Baru)
  - `Assets/Scripts/Grid/GridDataModel.cs` (Integrasi `IsTargetStealthed`)
  - `Assets/Scripts/Units/EnemyAICalculator.cs` (Integrasi `IsTargetStealthed`)
  - `Assets/Tests/EditMode/GridLogicTests.cs` (Test untuk `IsTargetStealthed`)
- **Catatan & Temuan Tak Terduga:**
  - Memperbaiki potensi bug pada validasi fase `CardPlayValidator` agar menggunakan negasi (`card.PhaseRestriction != currentPhase`).
  - Menginisialisasi `_unitStatusEffects = new()` pada `CombatMathEngine` untuk mencegah `NullReferenceException` saat unit pertama kali menerima status effect.
