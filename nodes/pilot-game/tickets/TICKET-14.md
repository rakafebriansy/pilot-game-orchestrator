---
id: TICKET-14
title: UI Markas Sanctuary & Pohon Talenta Upgrade Permanen
status: Todo
priority: Medium
labels: [UI, Sanctuary, MetaProgression, TalentTree, Fase3]
---

# Deskripsi
Membangun **layar Markas Sanctuary** — hub meta-progression yang dapat diakses pemain sebelum memulai run baru. Di sini pemain menggunakan **Poin Ekspedisi** (dari TICKET-13) untuk membuka **node talenta upgrade permanen** yang meningkatkan statistik dasar Nabu atau mengubah aturan game di semua run berikutnya.

## Acceptance Criteria

### A. Data Talenta
- [ ] `TalentData.cs` (`ScriptableObject`):
  - `string TalentId`, `string TalentName`, `string TalentDescription`.
  - `int Cost` — Poin Ekspedisi yang dibutuhkan untuk membuka.
  - `List<string> PrerequisiteTalentIds` — Talenta yang harus dibuka lebih dulu (dependensi pohon).
  - `TalentEffectType EffectType` — Enum: `IncreaseMaxHP`, `StartWithExtraCard`, `ReduceEnemyStartHP`, `IncreaseCardDamage`, `StartWithGold`, `ExtraRestEffect`.
  - `int EffectValue` — Besaran efek.
  - `[CreateAssetMenu]` dengan menuName: `"PilotGame/Talent Data"`.
- [ ] Minimal **12 node talenta** dibuat sebagai `.asset`:
  - Tier 1 (200 pts): `Vital Constitution` (+10 MaxHP), `Quick Learner` (mulai run dengan 1 kartu extra), `Forager` (Campfire heal +15%).
  - Tier 2 (400 pts, butuh 1 Tier 1): `Iron Will` (+20 MaxHP), `Battle Hardened` (musuh mulai -10% HP), `Efficient Strikes` (+1 damage semua kartu Attack).
  - Tier 3 (700 pts, butuh 2 Tier 2): `Apex Expedition` (+100 pts dari setiap run), `Legacy of Nabu` (unlock 5 kartu legendary di draft pool).

### B. TalentTreeManager.cs (MonoBehaviour)
- [ ] Method `bool CanUnlockTalent(TalentData talent)` — cek points cukup & prerequisites terpenuhi.
- [ ] Method `void UnlockTalent(TalentData talent)` — kurangi poin, tambahkan ke `MetaSaveData.UnlockedTalentIds`, simpan via `SaveDataManager`.
- [ ] Method `void ApplyAllUnlockedTalents()` — dipanggil saat run baru dimulai; aplikasikan semua efek talenta ke `PlayerRunState`.

### C. SanctuaryScreenController.cs (MonoBehaviour + UXML)
- [ ] Visual pohon talenta: node sebagai lingkaran/card, garis koneksi antar node.
- [ ] Node status:
  - **Locked** (abu-abu, tidak bisa diklik jika prerequisites belum terpenuhi).
  - **Available** (bersinar/highlight, bisa diklik untuk membuka).
  - **Unlocked** (warna emas, efek permanen aktif).
- [ ] Panel info di samping: nama talenta, deskripsi efek, biaya, status prerequisites.
- [ ] Tombol "Mulai Ekspedisi Baru" — memulai run baru dengan semua talenta yang sudah dibuka.
- [ ] Tombol "Kembali ke Menu Utama" — navigasi ke main menu.
- [ ] Display "Poin Ekspedisi" saat ini di sudut atas.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/TalentData.cs`
- `Assets/Scripts/Meta/TalentTreeManager.cs`
- `Assets/Scripts/UI/SanctuaryScreenController.cs`
- `Assets/ScriptableObjects/Talents/` (minimal 12 file `.asset`)
- `Assets/UI/UXML/SanctuaryScreenUI.uxml`
- `Assets/UI/USS/SanctuaryScreen.uss`
- `Assets/Scenes/SanctuaryScene.unity`

## Dependensi
- **Bergantung pada:** TICKET-13 (`ExpeditionPointsManager`, `SaveDataManager`, `MetaSaveData`).
- **Digunakan oleh:** Titik akhir meta-loop — tidak ada tiket lain yang bergantung pada ini di Fase 3.

## Catatan Teknis
- Sanctuary diakses HANYA sebelum run dimulai (bukan di tengah run).
- Simpan `UnlockedTalentIds` di `MetaSaveData` (persisten antar run), bukan `RunSaveData` (yang di-reset per run).
- Visual pohon talenta: gunakan UI Toolkit dengan custom drawing atau pre-built layout UXML yang merepresentasikan koneksi node.

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
