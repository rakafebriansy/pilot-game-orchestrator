---
id: TICKET-01
title: Pondasi Tipe Data, Payloads & Pusat Event
status: Done
priority: High
labels: [Core, Architecture, Events, Bridge]
---

# Deskripsi
Membangun lapisan **kontrak data bersama (*Shared Data Contracts*)** dan **event bus statis terpusat** (`CombatEvents.cs`) sebagai fondasi komunikasi seluruh sistem pertempuran taktis. Ini adalah Fase 0 dari sequential implementation guide — tanpa tiket ini, semua domain lain tidak bisa saling berkomunikasi.

Seluruh file di tiket ini adalah Pure C# (bukan MonoBehaviour) dan harus diletakkan di dalam `Assets/Scripts/Core/` sesuai dengan System Design.

## Acceptance Criteria
- [x] `CombatTypes.cs` memuat:
  - Enum `CombatPhase` dengan nilai: `IntentPhase`, `PlayerPhase`, `EnemyPhase`, `RoundResetPhase`.
  - Enum `HighlightType` dengan nilai: `None`, `DangerEnemyIntent`, `ValidCardTarget`, `MovementRange`, `HoverPreview`.
  - Enum `TileType` dengan nilai: `NormalFloor`, `StealthBush`, `ObstaclePillar`, `HazardTrap`, `BurnedBush`.
  - Enum `CardActionType` dengan nilai: `Attack`, `Defense`, `Movement`, `StatusModifier`, `Utility`.
  - Enum `StatusEffectType` dengan nilai: `None`, `Bleed`, `Freeze`, `Immobilize`, `Stun`, `Vulnerable`, `Shielded`, `Poison`, `Burn`.
  - Struct `ActiveStatusEffect` untuk pelacakan durasi status.
- [x] `CombatPayloads.cs` memuat:
  - `readonly struct TileHighlightRequest` dengan field `Vector2Int[] Coordinates` dan `HighlightType Style`.
  - `readonly struct UnitMovePayload` dengan field `int UnitId`, `Vector2Int FromCoord`, `Vector2Int ToCoord`.
  - `readonly struct DamagePayload` dengan field `int TargetUnitId`, `int DamageAmount`, `int ShieldRemaining`.
  - Setiap struct memiliki constructor eksplisit.
- [x] `CombatEvents.cs` mendefinisikan `static Action` delegates:
  - `OnEnemyIntentDecided` (param: `int enemyId`, `Vector2Int target`)
  - `OnHighlightTilesRequested` (param: `TileHighlightRequest`)
  - `OnClearAllHighlights` (tanpa param)
  - `OnCardPlayed` (param: `object card`, `Vector2Int targetCoord`)
  - `OnUnitMoved` (param: `UnitMovePayload`)
  - `OnSkillExecuted` (param: `int casterId`, `int skillId`, `Vector2Int target`)
  - `OnUnitDamaged` (param: `DamagePayload`)
  - `OnStatusEffectApplied` (param: `int unitId`, `StatusEffectType`, `int duration`)
  - `OnStatusEffectExpired` (param: `int unitId`, `StatusEffectType`)
  - `OnPhaseChanged` (param: `CombatPhase`)
  - `OnDrawCardsRequested` (tanpa param)
  - `OnCombatEnded` (param: `bool isVictory`)
  - `OnCheckpointPlaced` (param: `string nodeId`)
  - Method `ResetAllEvents()` untuk pembersihan delegate.
- [x] Seluruh kode C# bersih dari error compile di Unity Editor (proyek berhasil build).
- [x] Tidak ada dependency ke MonoBehaviour, Unity Object, atau namespace `UnityEngine` di file ini (Pure C# — kecuali `Vector2Int` dari `UnityEngine`).

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/CombatTypes.cs`
- `Assets/Scripts/Core/Data/CombatPayloads.cs`
- `Assets/Scripts/Core/Events/CombatEvents.cs`
- `Assets/Scripts/Core/PilotGame.Core.asmdef`

## Catatan Teknis
- Seluruh payload komunikasi **WAJIB** menggunakan `readonly struct` agar teralokasi di Stack memory (Zero GC Spike — lihat System Design §6).
- `CombatEvents.cs` adalah **static class**. AI Agent harus memastikan tidak ada instance state di dalamnya.
- Urutan pengerjaan: `CombatTypes.cs` → `CombatPayloads.cs` → `CombatEvents.cs` (karena masing-masing bergantung pada yang sebelumnya).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Menulis `CombatTypes.cs` di namespace `PilotGame.Core.Data` memuat seluruh enum pertempuran dan struct `ActiveStatusEffect`.
  2. Menulis `CombatPayloads.cs` memuat `readonly struct` untuk event highlight, unit move, dan damage calculation.
  3. Menulis `CombatEvents.cs` di namespace `PilotGame.Core.Events` memuat static event bus delegates dan `ResetAllEvents()`.
  4. Menambahkan `PilotGame.Core.asmdef` untuk modularitas arsitektur enterprise.
- **Keputusan Desain & Arsitektur:**
  - Menggunakan `readonly struct` untuk zero-allocation memory overhead di stack.
  - Memisahkan event delegate ke static class murni tanpa MonoBehaviour agar decoupled 100%.
- **Ringkasan File Terpengaruh:**
  - `Assets/Scripts/Core/Data/CombatTypes.cs`
  - `Assets/Scripts/Core/Data/CombatPayloads.cs`
  - `Assets/Scripts/Core/Events/CombatEvents.cs`
  - `Assets/Scripts/Core/PilotGame.Core.asmdef`
- **Catatan & Temuan Tak Terduga:**
  - Kode terverifikasi bersih dari error kompilasi dan siap digunakan oleh Domain 1, 2, 3, 4.
