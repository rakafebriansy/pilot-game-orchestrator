---
id: TICKET-07
title: State Machine Giliran Tempur & Integrasi Scene Utama
status: Todo
priority: High
labels: [Core, FSM, Integration, PlayableSlice]
---

# Deskripsi
Mengimplementasikan Finite State Machine (FSM) 4 fase pertempuran (`CombatStateMachine.cs`) dan merakit seluruh komponen (Arena Grid, Unit Nabu, Musuh, Card HUD UI) di dalam scene utama `MainBattleScene.unity` hingga menghasilkan *vertical slice* yang dapat dimainkan penuh.

## Acceptance Criteria
- [ ] `CombatStateMachine.cs` mengeksekusi siklus 4 fase secara berurutan: `IntentPhase` ➡️ `PlayerPhase` ➡️ `EnemyPhase` ➡️ `RoundResetPhase`.
- [ ] Di `MainBattleScene.unity`, ronde pertempuran berjalan mulus: musuh pasang telegraph merah ➡️ pemain memainkan kartu ➡️ animasi tebasan/gerak berjalan ➡️ musuh serang ➡️ ronde di-reset dan kartu baru ditarik.
- [ ] Tidak ada exception atau error log saat pertarungan berlangsung.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/FSM/CombatStateMachine.cs`
- `Assets/Scenes/MainBattleScene.unity`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
