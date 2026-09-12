---
id: TICKET-03
title: Otak Logika Grid 15x15 & AI Musuh
status: Todo
priority: High
labels: [Logic, Grid, AI, UnitTests]
---

# Deskripsi
Mengembangkan logika matematika murni (Pure C# Non-MonoBehaviour) untuk representasi matriks petak 15×15, validasi batas arena, pemeriksaan rintangan pilar, dan algoritma penentuan niat serang musuh (*Intent Calculator*).

## Acceptance Criteria
- [ ] `GridDataModel.cs` mengelola matriks 15×15 dan menyediakan method `IsInsideGrid`, `IsWalkable`, `SetOccupant`, dan `GetOccupant`.
- [ ] `EnemyAICalculator.cs` mampu menghitung koordinat target serangan dan menembakkan event `CombatEvents.OnEnemyIntentDecided` serta `CombatEvents.OnHighlightTilesRequested`.
- [ ] Tersedia test suite C# EditMode NUnit yang memverifikasi matematika grid dan kalkulasi arah serangan.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Grid/GridDataModel.cs`
- `Assets/Scripts/Units/EnemyAICalculator.cs`
- `Assets/Tests/EditMode/GridLogicTests.cs`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
