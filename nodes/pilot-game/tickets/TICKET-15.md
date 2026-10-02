---
id: TICKET-15
title: Sistem Modifikator Tingkat Kesulitan (Ascension Tiers)
status: Todo
priority: Low
labels: [Difficulty, Ascension, Modifiers, EndgameContent, Fase3]
---

# Deskripsi
Mengimplementasikan **sistem Ascension** — tingkat kesulitan opsional yang dapat dipilih pemain sebelum memulai run. Setiap level Ascension menambahkan **modifikator negatif** yang membuat run semakin menantang, sesuai dengan pola yang ada di *Slay the Spire* dan *Hades*. Sistem ini adalah konten end-game untuk pemain yang sudah menguasai mechanics dasar.

## Acceptance Criteria

### A. Data & Definisi Ascension
- [ ] `AscensionModifierData.cs` (`ScriptableObject`):
  - `int AscensionLevel` — Level Ascension (1 hingga 10).
  - `List<AscensionEffect> Effects` — List efek yang aktif pada level ini dan seterusnya.
  - Inner class `AscensionEffect`: `AscensionEffectType Type`, `int Value`, `string DisplayDescription`.
  - Enum `AscensionEffectType`: `ReduceStartHP`, `EnemyStartWithShield`, `ReduceDraftOptions` (2 kartu bukan 3), `EliminateCampfireHeal`, `IncreaseBossDamage`, `ReduceExpeditionPointsGained`, `EnemyAttacksMoreFrequent`, `StartWithLessCards`.
- [ ] **10 level Ascension** dibuat sebagai `.asset`:
  - A1: Musuh mulai dengan 6 Shield.
  - A2: Nabu mulai run dengan HP -15%.
  - A3: Draft hanya menampilkan 2 pilihan kartu (bukan 3).
  - A4: Campfire tidak lagi memulihkan HP (hanya bisa upgrade kartu).
  - A5: Boss damage +25%.
  - A6: Poin Ekspedisi didapat -25%.
  - A7: Musuh mendapat giliran serangan 1x lebih cepat (IntentPhase lebih singkat).
  - A8: Mulai run dengan 1 kartu kutukan (`Curse_Leaden`) di deck.
  - A9: Semua musuh elite hadir di setiap encounter.
  - A10: Boss memiliki Fase Enrage aktif dari awal.

### B. AscensionManager.cs (MonoBehaviour)
- [ ] Property `int CurrentAscensionLevel` — level yang dipilih pemain (0 = no ascension).
- [ ] Method `void SetAscensionLevel(int level)` — simpan ke `MetaSaveData`.
- [ ] Method `List<AscensionEffect> GetActiveEffects()` — semua efek dari level 1 hingga `CurrentAscensionLevel` (bersifat kumulatif).
- [ ] Method `void ApplyEffectsToRun(PlayerRunState runState)` — aplikasikan semua efek aktif saat run dimulai.
- [ ] **Unlock logic:** Ascension Level N+1 unlock jika pemain berhasil menyelesaikan run (boss dikalahkan) pada Ascension Level N.

### C. UI Pemilihan Ascension (di Sanctuary Screen)
- [ ] Di `SanctuaryScreenController.cs`: tambahkan section "Pilih Tingkat Kesulitan Ascension".
- [ ] Dropdown atau radio button untuk memilih level Ascension (0–N, N = level tertinggi yang unlock).
- [ ] Panel info: menampilkan semua modifikator aktif yang akan berlaku pada level yang dipilih.
- [ ] Indikator "Current Best Ascension Level Cleared" di profil pemain.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/AscensionModifierData.cs`
- `Assets/Scripts/Meta/AscensionManager.cs`
- `Assets/ScriptableObjects/Ascension/` (10 file `.asset`)
- `Assets/Scripts/UI/SanctuaryScreenController.cs` (update dari TICKET-14)

## Dependensi
- **Bergantung pada:** TICKET-13 (`MetaSaveData` untuk menyimpan ascension level), TICKET-14 (Sanctuary UI sebagai titik pemilihan ascension).
- **Bergantung implisit pada:** TICKET-12 (boss harus ada untuk A10 enrage-from-start).

## Catatan Teknis
- Ascension bersifat **kumulatif** — A5 berarti efek A1+A2+A3+A4+A5 semua aktif sekaligus.
- Simpan `CurrentAscensionLevel` dan `MaxUnlockedAscensionLevel` di `MetaSaveData`.
- Untuk Fase 3 MVP, cukup implementasikan A1-A5 terlebih dahulu, A6-A10 bisa masuk backlog selanjutnya.

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
