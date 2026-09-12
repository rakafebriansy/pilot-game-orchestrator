---
id: TICKET-02
title: Katalog Data ScriptableObjects
status: Todo
priority: High
labels: [Data, ScriptableObjects, Cards, Enemies]
---

# Deskripsi
Menyusun template data konfigurasi statis menggunakan ScriptableObjects untuk kartu jurus (`CardData.cs`), musuh (`EnemyData.cs`), dan item consumable (`ConsumableData.cs`), serta membuat sampel file `.asset` awal di Unity Inspector.

## Acceptance Criteria
- [ ] `CardData.cs` mengimplementasikan properti enkapsulasi `CardId`, `CardName`, `ActionType`, `BaseValue`, `Range`, dan `CardIllustration`.
- [ ] `EnemyData.cs` mengimplementasikan properti stat dasar musuh (`MaxHealth`, `BaseAttackDamage`, `MoveSpeed`, `EnemySprite`).
- [ ] Minimal 3 file aset kartu (`Card_PageCutter.asset`, `Card_TumbleDodge.asset`, `Card_ClayShield.asset`) berhasil dibuat di `Assets/ScriptableObjects/Cards/`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/CardData.cs`
- `Assets/Scripts/Core/Data/EnemyData.cs`
- `Assets/ScriptableObjects/Cards/Card_PageCutter.asset`
- `Assets/ScriptableObjects/Cards/Card_TumbleDodge.asset`
- `Assets/ScriptableObjects/Cards/Card_ClayShield.asset`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
