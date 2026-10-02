---
id: TICKET-11
title: Event Naratif Misteri '?' & Rest Campfire Recovery System
status: Todo
priority: Medium
labels: [Narrative, EventSystem, Recovery, Campfire, Fase2]
---

# Deskripsi
Mengimplementasikan dua jenis node non-combat dari peta eksplorasi:
1. **Node Misteri '?'** — Event naratif acak dari kumpulan pilihan dengan konsekuensi positif/negatif (berdasarkan data dari `pilot-game-team-docs/01_game_design/narrative.md`).
2. **Node Api Unggun (Campfire Rest)** — Layar istirahat di mana pemain memilih antara memulihkan HP atau meningkatkan kartu.

## Acceptance Criteria

### A. Sistem Event Naratif ('?')
- [ ] `NarrativeEventData.cs` (`ScriptableObject`):
  - `string EventId`, `string EventTitle`, `string EventDescription` (lore teks panjang).
  - `List<EventChoice> Choices` — daftar pilihan yang tersedia (minimal 2 pilihan per event).
  - Inner class `EventChoice`: `string ChoiceLabel`, `string ResultDescription`, `EventOutcomeType OutcomeType`, `int OutcomeValue`.
  - Enum `EventOutcomeType`: `HealHP`, `LoseHP`, `GainGold`, `LoseGold`, `AddCard`, `RemoveCard`, `Nothing`.
- [ ] `NarrativeEventManager.cs` (MonoBehaviour):
  - Method `NarrativeEventData GetRandomEvent(int chapterIndex)` — ambil event random sesuai chapter.
  - Method `void ApplyOutcome(EventOutcome outcome)` — aplikasikan efek pilihan ke state run saat ini.
  - Minimal **5 event unik** dibuat sebagai ScriptableObject `.asset` di `Assets/ScriptableObjects/Events/`.
- [ ] `EventScreenController.cs` (MonoBehaviour + UXML):
  - Menampilkan judul event, ilustrasi, dan paragraf deskripsi.
  - Render `EventChoice` sebagai tombol-tombol pilihan.
  - Saat pilihan diklik: tampilkan `ResultDescription` → animasi efek (heal flash merah/hijau) → tombol "Continue" muncul → kembali ke Map Screen.

### B. Sistem Api Unggun (Campfire Rest)
- [ ] `CampfireManager.cs` (MonoBehaviour):
  - Method `void HealPlayer(int amount)` — pulihkan HP Nabu (default: 30% dari MaxHP, tidak melebihi MaxHP).
  - Method `void UpgradeCard(CardData card, CardData upgradedVersion)` — ganti kartu dalam deck dengan versi yang di-upgrade.
- [ ] `CampfireScreenController.cs` (MonoBehaviour + UXML):
  - Dua pilihan utama: "Istirahat (Pulihkan HP)" dan "Tingkatkan Kartu".
  - Jika "Tingkatkan Kartu": tampilkan daftar kartu di deck → pemain pilih 1 → pilih versi upgrade-nya → konfirmasi.
  - Jika "Istirahat": animasi heal → HP bar bertambah → tombol "Lanjutkan" aktif.
- [ ] Minimal **3 pasang kartu upgrade** didefinisikan (contoh: `Card_PageCutter` → `Card_PageCutter_Plus` dengan +2 damage).

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Map/NarrativeEventManager.cs`
- `Assets/Scripts/UI/EventScreenController.cs`
- `Assets/Scripts/Map/CampfireManager.cs`
- `Assets/Scripts/UI/CampfireScreenController.cs`
- `Assets/ScriptableObjects/Events/` (minimal 5 file `.asset`)
- `Assets/UI/UXML/EventScreenUI.uxml`
- `Assets/UI/UXML/CampfireScreenUI.uxml`

## Dependensi
- **Bergantung pada:** TICKET-09 (`MapManager` untuk navigasi), TICKET-02 (`CardData` untuk system kartu upgrade).
- **Digunakan oleh:** TICKET-13 (save data harus menyimpan HP dan deck state setelah event).

## Catatan Teknis
- Event naratif sebaiknya ada flag `isOneTimePerRun` — event yang sudah muncul tidak muncul lagi dalam run yang sama.
- HP Nabu harus persisten antar encounter (simpan di `MapManager` atau `PlayerRunState` singleton).

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
