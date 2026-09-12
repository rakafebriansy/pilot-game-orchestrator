---
id: TICKET-05
title: Entitas Karakter, Pergerakan Grid Lerp & Animasi
status: Todo
priority: High
labels: [View, Units, Animation, Movement]
---

# Deskripsi
Membangun prefab karakter protagonis (Nabu) dan musuh dasar, mengimplementasikan controller pergerakan grid halus (`UnitMovementView.cs`) berbasis interpolasi posisi (*Lerp*), dan mengintegrasikan respons animasi tebasan/damage.

## Acceptance Criteria
- [ ] Prefab Unit memiliki komponen `SpriteRenderer`, `Animator`, dan `UnitMovementView`.
- [ ] `UnitMovementView.cs` menangani event `CombatEvents.OnUnitMoved` dengan pergerakan `Vector3.MoveTowards` / `Lerp` ke posisi tengah petak target.
- [ ] Tersedia visualisasi HealthBar dan damage popup sederhana di atas unit.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Units/UnitMovementView.cs`
- `Assets/Scripts/Units/UnitAnimatorPresenter.cs`
- `Assets/Prefabs/Units/Nabu_Player_Prefab.prefab`
- `Assets/Prefabs/Units/Enemy_Conscript_Prefab.prefab`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
