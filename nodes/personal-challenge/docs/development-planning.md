# Development Planning & Roadmap

## Fase 1: Inisialisasi Proyek & Model (MVP 1)

Fase ini berfokus pada pengaturan awal lingkungan SwiftUI dan mendapatkan model `.mlmodel` fungsional yang sudah dilatih dengan dataset Kaggle.
*Karena mode operasional proyek ini adalah Mode 2 (Prompt-Driven), eksekusi tidak dilakukan secara berurutan secara otonom melainkan dari instruksi-instruksi per tiket dari pengembang.*

### Daftar Backlog Tiket:
- [ ] **TICKET-01:** Setup proyek Xcode awal untuk iOS App SwiftUI. Konfigurasi bundle ID, target deployment (iOS 16.0+), dan struktur folder MVVM dasar.
- [ ] **TICKET-02:** Persiapan dataset Kaggle dan script/Project CreateML untuk melatih model Object Detection / Image Classification.
- [ ] **TICKET-03:** Import `.mlmodel` yang sudah dilatih ke dalam proyek Xcode dan buat class wrapper `MLVisionService`.
- [ ] **TICKET-04:** Buat UI sederhana (MainView) dan integrasikan fitur unggah gambar dari Galeri (PhotoLibraryService) untuk di-inferensi statis oleh model.
- [ ] **TICKET-05:** Tampilkan hasil teks terjemahan ke UI statis.

*(Fase selanjutnya akan direncanakan setelah Fase 1 tercapai secara keseluruhan)*
