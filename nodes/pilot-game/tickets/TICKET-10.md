---
id: TICKET-10
title: Sistem Drafting Hadiah Pasca-Pertempuran (Pick 1 of 3 Cards)
status: Todo
priority: High
labels: [UI, Drafting, Reward, Cards, Domain3, Fase2]
---

# Deskripsi
Mengimplementasikan **layar pemilihan hadiah kartu** (*card draft screen*) yang muncul setelah pemain berhasil menyelesaikan sebuah encounter pertempuran. Pemain disajikan **3 pilihan kartu acak** dari kumpulan kartu yang tersedia, dan harus memilih **1 kartu** untuk ditambahkan permanen ke deck mereka selama sesi run berlangsung.

Sistem ini adalah pilar utama meta-progression loop Fase 2.

## Acceptance Criteria
- [ ] `DraftRewardData.cs` — Class/ScriptableObject yang menyimpan pool kartu tersedia per chapter.
  - `List<CardData> AvailableCardPool` — semua kartu yang bisa muncul sebagai hadiah.
  - Kartu yang sudah ada di deck pemain **bisa** muncul kembali sebagai opsi (duplikat diperbolehkan).
- [ ] `DraftManager.cs` (MonoBehaviour):
  - Method `List<CardData> GenerateDraftOptions(int count = 3)` — memilih `count` kartu acak dari pool tanpa pengulangan dalam satu draft.
  - Method `void ConfirmDraftChoice(CardData chosen)` — tambahkan kartu ke `DeckManager._fullDeck` dan simpan ke run state.
  - Method `void SkipDraft()` — pemain dapat melewati tanpa mengambil kartu (dengan konfirmasi "Are you sure?").
- [ ] `DraftScreenController.cs` (MonoBehaviour):
  - Merender 3 card option visual di UI menggunakan `CardHandHUD.uxml` component cards.
  - Hover card: menampilkan tooltip detail (nama, tipe, nilai, jangkauan, deskripsi efek).
  - Klik satu kartu: highlight pilihan tersebut, tombol "Take This Card" aktif.
  - Klik "Take This Card": panggil `DraftManager.ConfirmDraftChoice(selected)` → animasi card fly ke deck → kembali ke Map Screen.
  - Klik "Skip": tampilkan dialog konfirmasi → jika Ya, panggil `DraftManager.SkipDraft()` → kembali ke Map Screen.
- [ ] `DraftScreenUI.uxml` — Layout layar draft:
  - Judul: "Pilih Hadiah Expedisimu".
  - 3 card option dengan spacing merata horizontal.
  - Footer: tombol "Take This Card" (awalnya disabled) dan "Skip Reward".
- [ ] **Verifikasi:**
  - Setelah combat selesai (kondisi victory di `CombatStateMachine`) → layar draft muncul.
  - Setelah memilih kartu → deck di `DeckManager` bertambah 1 kartu.
  - Kembali ke Map Screen dengan state yang ter-update.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Cards/DraftManager.cs`
- `Assets/Scripts/UI/DraftScreenController.cs`
- `Assets/UI/UXML/DraftScreenUI.uxml`
- `Assets/UI/USS/DraftScreen.uss`
- `Assets/Scenes/DraftScene.unity` (atau sebagai overlay di MapScene)

## Dependensi
- **Bergantung pada:** TICKET-02 (`CardData`), TICKET-06 (`DeckManager`), TICKET-09 (`MapManager` untuk navigasi kembali).
- **Digunakan oleh:** TICKET-14 (Shop juga menawarkan kartu dengan mekanisme serupa).

## Catatan Teknis
- Jika diimplementasikan sebagai overlay, gunakan `UIDocument` dengan prioriti render tinggi (sort order > main battle UI).
- Untuk animasi "card fly to deck", gunakan DOTween atau `UnityEngine.UI` Lerp coroutine sederhana.
- Pool kartu draft sebaiknya berbeda per chapter untuk menjaga kurva kesulitan (kartu lemah di chapter awal, kuat di akhir).

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
