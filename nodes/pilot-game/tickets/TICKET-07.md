---
id: TICKET-07
title: State Machine Giliran Tempur & Integrasi Scene Utama
status: Todo
priority: High
labels: [Core, FSM, Integration, PlayableSlice, PM-Core]
---

# Deskripsi
Mengimplementasikan **Finite State Machine (FSM) 4-fase giliran tempur** (`CombatStateMachine.cs`) dan merakit seluruh prefab hasil tiket sebelumnya (Arena Grid, Unit Nabu, Musuh, Card HUD) di dalam `MainBattleScene.unity` hingga menghasilkan **vertical slice yang dapat dimainkan penuh**.

Ini adalah tiket integrasi final Fase 1 — semua TICKET-01 hingga TICKET-06 harus selesai terlebih dahulu.

## Acceptance Criteria

### A. CombatStateMachine.cs (MonoBehaviour)
- [ ] Class mengimplementasikan interface `ICombatState` yang memiliki method `Enter()`, `Execute()`, `Exit()`.
- [ ] Empat state concrete class tersedia:
  - `IntentPhaseState` — Memanggil `EnemyAICalculator.PlanLinearAttack()` untuk setiap musuh aktif → Broadcast `OnPhaseChanged(CombatPhase.IntentPhase)` → Auto-transisi ke `PlayerPhaseState`.
  - `PlayerPhaseState` — Subscribe `OnCardPlayed`. Saat event diterima: validasi target, aplikasikan damage via `OnSkillExecuted` & `OnUnitDamaged`, lalu transisi ke `EnemyPhaseState`.
  - `EnemyPhaseState` — Kunci UI (set `CardHandController._isLocked = true`). Eksekusi serangan musuh satu per satu → Broadcast `OnUnitMoved` & `OnUnitDamaged` → Transisi ke `RoundResetPhaseState`.
  - `RoundResetPhaseState` — Broadcast `OnClearAllHighlights` → Broadcast `OnDrawCardsRequested` → Evaluasi HP (jika ada unit dengan HP ≤ 0: broadcast `OnCombatEnded`) → Jika berlanjut, transisi ke `IntentPhaseState`.
- [ ] `Transition(ICombatState nextState)` method yang memanggil `currentState.Exit()` → set state baru → `newState.Enter()`.
- [ ] Cycle 4 fase berputar terus-menerus tanpa infinite loop atau deadlock.

### B. MainBattleScene.unity — Assembly & Integration
- [ ] Hierarchy scene sesuai spesifikasi System Design:
  ```
  MainBattleScene
  ├── [CORE] GameMaster_CombatFSM       ← CombatStateMachine.cs
  ├── [D1] Arena_Environment_Prefab      ← GridTilemapView.cs, URP 2D Lights, Camera
  ├── [D2] Units_Container
  │   ├── Nabu_Player_Prefab             ← UnitId: 1
  │   ├── Enemy_Conscript_01             ← UnitId: 101
  │   └── Enemy_Conscript_02             ← UnitId: 102
  └── [D3] CardHUD_UI_Prefab             ← UIDocument + CardHandController.cs
  ```
- [ ] Seluruh referensi serialized field di Inspector terisi (tidak ada field yang null saat Play Mode).
- [ ] Event Bus tidak memiliki null subscriber yang menyebabkan NullReferenceException.

### C. Verifikasi Playable Slice
- [ ] Ronde berjalan mulus: musuh tampilkan highlight merah → pemain drag kartu ke arena → animasi serangan berjalan → musuh serang balik → ronde reset → kartu baru ditarik → kembali ke Intent Phase.
- [ ] Tidak ada Exception atau Error log di Console selama minimal 3 ronde pertempuran penuh.
- [ ] Kondisi menang (musuh semua HP ≤ 0) memunculkan log `"Combat Ended: Victory"`.
- [ ] Kondisi kalah (Nabu HP ≤ 0) memunculkan log `"Combat Ended: Defeat"`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/FSM/ICombatState.cs`
- `Assets/Scripts/Core/FSM/CombatStateMachine.cs`
- `Assets/Scripts/Core/FSM/IntentPhaseState.cs`
- `Assets/Scripts/Core/FSM/PlayerPhaseState.cs`
- `Assets/Scripts/Core/FSM/EnemyPhaseState.cs`
- `Assets/Scripts/Core/FSM/RoundResetPhaseState.cs`
- `Assets/Scenes/MainBattleScene.unity`

## Dependensi
- **Bergantung pada:** TICKET-01, TICKET-02, TICKET-03, TICKET-04, TICKET-05, TICKET-06 (semua harus Done terlebih dahulu).

## Catatan Teknis
- Gunakan pola **State Pattern** — setiap state adalah class terpisah yang mengimplementasikan `ICombatState`, bukan switch-case dalam satu method besar.
- `CombatStateMachine` TIDAK boleh mengandung logika domain spesifik (damage calculation, grid math) — ia hanya mengatur *transisi* antar state.
- Untuk validasi kartu di `PlayerPhaseState`: jika target tidak valid (di luar jangkauan, obstacle), broadcast event ditolak dan fase tidak bertransisi.

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  - *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
