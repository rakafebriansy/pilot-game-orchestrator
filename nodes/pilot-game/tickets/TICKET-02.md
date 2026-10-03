---
id: TICKET-02
title: Katalog Data ScriptableObjects (Kartu, Musuh, Consumable)
status: Todo
priority: High
labels: [Data, ScriptableObjects, Cards, Enemies, Consumables]
---

# Deskripsi
Menyusun **template data konfigurasi statis** menggunakan ScriptableObjects untuk tiga entitas utama: kartu jurus (`CardData.cs`), musuh (`EnemyData.cs`), dan item consumable (`ConsumableData.cs`). Kemudian membuat sample file `.asset` awal berdasarkan data desain dari `pilot-game-team-docs/01_game_design/`.

Tiket ini bergantung pada TICKET-01 (enum `CardActionType` diperlukan oleh `CardData.cs`).

## Acceptance Criteria
- [ ] `CardData.cs` (`ScriptableObject`) mengimplementasikan properti enkapsulasi read-only:
  - `string CardId` — ID unik kartu (contoh: `"card_page_cutter"`).
  - `string CardName` — Nama tampilan kartu.
  - `CardActionType ActionType` — Tipe aksi (`Attack`, `Defense`, `Movement`, `Utility`).
  - `int BaseValue` — Nilai damage / shield / langkah.
  - `int Range` — Jangkauan dalam satuan ubin (tile).
  - `Sprite CardIllustration` — Sprite visual kartu.
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Card Data"`.
- [ ] `EnemyData.cs` (`ScriptableObject`) mengimplementasikan:
  - `string EnemyId` — ID unik musuh.
  - `string EnemyName` — Nama musuh.
  - `string Description` — Deskripsi latar naratif musuh.
  - `int MaxHealth` — HP maksimum musuh.
  - `int BaseAttackDamage` — Damage serangan dasar.
  - `int MoveRange` — Jangkauan gerak per giliran (dalam tile).
  - `int AttackRange` — Jangkauan serangan (dalam tile).
  - `Sprite EnemySprite` — Sprite visual musuh.
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Enemy Data"`.
- [ ] `ConsumableData.cs` (`ScriptableObject`) mengimplementasikan:
  - `string ItemId`, `string ItemName`, `int HealAmount`, `Sprite ItemIcon`.
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Consumable Data"`.
- [ ] Minimal **5 file aset kartu** dibuat di `Assets/ScriptableObjects/Cards/` berdasarkan data desain:
  - `Card_PageCutter.asset` — Attack | Damage: 6 | Range: 1
  - `Card_TumbleDodge.asset` — Movement | Step: 2 | Range: 2
  - `Card_ClayShield.asset` — Defense | Shield: 5 | Range: 0
  - `Card_ScorchingBrand.asset` — Attack | Damage: 4 | Range: 3
  - `Card_EnkiSurge.asset` — Utility | Value: 0 | Range: 0 (draw 2 cards)
- [ ] Minimal **3 file aset musuh** dibuat di `Assets/ScriptableObjects/Enemies/` berdasarkan data dari `enemies.md`:
  - `Enemy_TatteredConscript.asset` — HP: 18 | Gerak: 2 | Serangan: 1 | Damage: 5
  - `Enemy_DustboundSkeleton.asset` — HP: 14 | Gerak: 2 | Serangan: 1 | Damage: 4
  - `Enemy_ArchiveScavenger.asset` — HP: 16 | Gerak: 2 | Serangan: 1 | Damage: 5
- [ ] Seluruh kode kompilasi bersih di Unity Editor.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/CardData.cs`
- `Assets/Scripts/Core/Data/EnemyData.cs`
- `Assets/Scripts/Core/Data/ConsumableData.cs`
- `Assets/ScriptableObjects/Cards/Card_PageCutter.asset`
- `Assets/ScriptableObjects/Cards/Card_TumbleDodge.asset`
- `Assets/ScriptableObjects/Cards/Card_ClayShield.asset`
- `Assets/ScriptableObjects/Cards/Card_ScorchingBrand.asset`
- `Assets/ScriptableObjects/Cards/Card_EnkiSurge.asset`
- `Assets/ScriptableObjects/Enemies/Enemy_TatteredConscript.asset`
- `Assets/ScriptableObjects/Enemies/Enemy_DustboundSkeleton.asset`
- `Assets/ScriptableObjects/Enemies/Enemy_ArchiveScavenger.asset`

## Dependensi
- **Bergantung pada:** TICKET-01 (membutuhkan `CardActionType` dari `CombatTypes.cs`).

## Catatan Teknis
- Gunakan **properti enkapsulasi C# (`=>`)** — JANGAN menggunakan field publik. Ini menjaga prinsip immutability data ScriptableObject.
- File `.asset` di Unity dibuat via menu Assets > Create > PilotGame setelah script di-compile.

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
