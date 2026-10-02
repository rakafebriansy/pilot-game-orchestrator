---
id: TICKET-05C
title: Animator Controller, Hit Impact System (Screen Shake & Hit Stop)
status: Todo
priority: Medium
labels: [Animation, GameFeel, HitImpact, ScreenShake, Domain2, Fase1]
---

# Deskripsi
Membangun **Animator Controller** lengkap untuk unit Nabu dan musuh, serta mengimplementasikan **sistem game feel** berupa Screen Shake dan Hit Stop micro-freeze yang membuat impact serangan terasa berbobot. Ini adalah deliverable Milestone 3 Domain 2 yang diperlukan bahkan untuk Fase 1 agar playtest terasa memuaskan.

## Acceptance Criteria

### A. Animator Controller
- [ ] `NabuAnimatorController.asset` (Unity Animator Controller):
  - State Machine: `Idle` → `Walk` → `Attack` → kembali ke `Idle`.
  - State `HitReact` — dapat diakses dari state manapun via `AnyState transition`.
  - State `Death` — transition dari AnyState saat trigger `Die` aktif.
  - Parameter: `bool IsMoving`, `Trigger Attack`, `Trigger HitReact`, `Trigger Die`.
- [ ] `EnemyAnimatorController.asset` (template serupa untuk semua musuh).
- [ ] `UnitAnimatorPresenter.cs` (update dari TICKET-05, pisahkan concern ini):
  - Subscribe `CombatEvents.OnSkillExecuted` → trigger `Attack` parameter di Animator.
  - Subscribe `CombatEvents.OnUnitDamaged` → jika targetId match: trigger `HitReact` + spawn DamagePopup via Pool.
  - Subscribe `CombatEvents.OnUnitMoved` → set `IsMoving = true`, reset saat coroutine selesai.
  - Jika HP unit = 0 setelah damage: trigger `Die`, disable collider, delay 1 detik lalu destroy/return to pool.

### B. Screen Shake System
- [ ] `CameraShaker.cs` (MonoBehaviour) di-attach ke Main Camera:
  - Method `void Shake(float duration, float magnitude)`:
    - Coroutine yang offset posisi kamera secara random dalam magnitude selama duration.
    - Gunakan `Random.insideUnitSphere * magnitude`, projected ke XY plane.
    - Lerp kembali ke posisi asal setelah selesai.
  - Subscribe `CombatEvents.OnUnitDamaged` → Shake(0.15f, 0.08f) untuk hit biasa.
  - Subscribe `CombatEvents.OnSkillExecuted` dengan skill `SuperPunch`: Shake(0.3f, 0.2f).

### C. Hit Stop (Micro-Freeze)
- [ ] `HitStopManager.cs` (MonoBehaviour, Singleton):
  - Method `void TriggerHitStop(float duration = 0.05f)`:
    - Set `Time.timeScale = 0f`.
    - Gunakan `WaitForSecondsRealtime(duration)` (tidak terpengaruh timeScale).
    - Reset `Time.timeScale = 1f`.
  - Subscribe `CombatEvents.OnUnitDamaged` → trigger hit stop 0.05 detik.
  - Subscribe damage event yang menandai kematian unit → hit stop lebih panjang (0.12f).

## Target Lingkup File (Affected Files)
- `Assets/Animations/NabuAnimatorController.asset`
- `Assets/Animations/EnemyAnimatorController.asset`
- `Assets/Scripts/Units/UnitAnimatorPresenter.cs` (update dari TICKET-05)
- `Assets/Scripts/Core/CameraShaker.cs`
- `Assets/Scripts/Core/HitStopManager.cs`

## Dependensi
- **Bergantung pada:** TICKET-05 (prefab unit harus ada), TICKET-05B (DamagePopup pool harus ready), TICKET-01 (CombatEvents).
- **Digunakan oleh:** TICKET-07 (scene assembly final).

## Catatan Teknis
- Hit Stop menggunakan `WaitForSecondsRealtime` — jangan gunakan `WaitForSeconds` karena terpengaruh `Time.timeScale`.
- Screen shake magnitude harus dikalibrasi: terlalu keras terasa nauseous, terlalu lemah tidak terasa.
- Untuk Fase 1, animator menggunakan placeholder animation clips (single frame) — art animation final masuk sprint terpisah.

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
