---
id: TICKET-01
title: Pondasi Tipe Data, Payloads & Pusat Event
status: Todo
priority: High
labels: [Core, Architecture, Events]
---

# Deskripsi
Membangun lapisan kontrak data bersama (*Shared Data Contracts*) dan event bus statis terpusat (`CombatEvents.cs`) sebagai fondasi komunikasi seluruh sistem pertempuran taktis.

## Acceptance Criteria
- [ ] `CombatTypes.cs` memuat enum `CombatPhase`, `HighlightType`, `TileType`, dan `CardActionType`.
- [ ] `CombatPayloads.cs` memuat struct DTO `TileHighlightRequest`, `UnitMovePayload`, dan `DamagePayload`.
- [ ] `CombatEvents.cs` mendefinisikan static Action delegates untuk semua fase pertempuran.
- [ ] Seluruh kode C# bersih dari error compile di Unity Editor.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/CombatTypes.cs`
- `Assets/Scripts/Core/Data/CombatPayloads.cs`
- `Assets/Scripts/Core/Events/CombatEvents.cs`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
