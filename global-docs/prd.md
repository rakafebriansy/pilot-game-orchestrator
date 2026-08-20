# Product Requirements Document (PRD)

## Aksara Jawa Translator

### Tujuan & Latar Belakang (*Objective & Background*)
Aplikasi ini dikembangkan sebagai *personal challenge* di ekosistem Apple untuk menerjemahkan Aksara Jawa (Hanacaraka) menjadi teks Latin. Tujuan utamanya adalah pembelajaran mendalam terkait *Machine Learning* di perangkat Apple, khususnya **CoreML**, **CreateML**, dan **Vision Framework**. Aplikasi ini dirancang agar dapat berjalan secara **offline** di perangkat iOS.
Target penggunanya adalah **pelajar** yang sedang mempelajari bahasa atau budaya Jawa, serta butuh alat bantu untuk membaca aksara tersebut dengan cepat.
Dataset yang digunakan untuk melatih model diambil dari Kaggle: [vzrenggamani/hanacaraka](https://www.kaggle.com/datasets/vzrenggamani/hanacaraka/data).

### Alur Pengguna (*User Flow / User Journey*)
Pengguna membuka aplikasi dan dapat memilih dua metode input utama:
1. **Mode Kamera (Real-time):** Mengarahkan kamera langsung ke teks Aksara Jawa dan melihat terjemahannya di layar.
2. **Mode Galeri:** Memilih foto berisi Aksara Jawa yang sudah ada di perangkat.
Sistem kemudian menggunakan CoreML dan Vision untuk memproses gambar/frame dan menampilkan teks Latin. Pengguna dapat menyalin teks hasil terjemahan tersebut.

Visualisasi alur: [User Journey](file:///Users/rakafebriansyahputra/Developer/repositories/projects/personal-challenge-project/personal-challenge-ai-orchestrator/global-docs/diagrams/user-journey.puml)

### Interaksi Sistem Global (Use Case & Activity Diagram)
Aplikasi iOS interaktif tunggal yang mendukung pemrosesan gambar offline. 
Visualisasi Use Case: [Use Case Diagram](file:///Users/rakafebriansyahputra/Developer/repositories/projects/personal-challenge-project/personal-challenge-ai-orchestrator/global-docs/diagrams/use-case.puml)

### Kebutuhan Fungsional (*Functional Requirements*)
*   Aplikasi harus memiliki fitur menerjemahkan Aksara Jawa melalui bidikan kamera secara real-time.
*   Aplikasi harus bisa memuat gambar dari galeri pengguna (Photo Library) untuk diterjemahkan.
*   Aplikasi harus menampilkan hasil teks dalam huruf Latin dengan tingkat akurasi model ML yang akan ditingkatkan seiring berjalannya proyek.
*   Sistem harus bisa berjalan sepenuhnya secara *offline* tanpa panggilan ke API atau internet, menggunakan model `.mlmodel` / `.mlpackage` bawaan.
*   Aplikasi memiliki fitur *Copy to Clipboard* untuk menyalin hasil terjemahan.

### Kebutuhan Non-Fungsional (*Non-Functional Requirements*)
*   **Platform:** Minimum iOS 16.0 (atau menyesuaikan ketersediaan API CreateML/Vision).
*   **Performa ML:** Inferensi CoreML harus berjalan cukup cepat agar *real-time camera feed* tidak *lag* secara signifikan.
*   **Aksesibilitas (Accessibility):** Diimplementasikan sebanyak mungkin sebagai prioritas terakhir (termasuk *VoiceOver* untuk membacakan hasil terjemahan, *Dynamic Type* untuk ukuran font teks hasil).
*   **Pengujian (Testing):** *Unit Test* dan *UI Test* wajib dibuat menggunakan **XCTest**.

### Kriteria Penerimaan (*Acceptance Criteria*)
*   Skenario MVP 1: Model CreateML berhasil dilatih dari dataset Kaggle (di luar aplikasi) dan siap di-import.
*   Skenario MVP 2: Aplikasi dapat meng-import dan mengeksekusi inferensi pada frame kamera menggunakan framework Vision dan CoreML.
*   Skenario MVP 3: Pengguna mendapatkan *string* hasil konversi teks Latin dan bisa disalin.
*   Skenario Aksesibilitas: Teks hasil terjemahan bisa dibaca oleh *VoiceOver*.

### Asumsi & Keterbatasan (*Assumptions & Constraints*)
*   **Asumsi:** Dataset Kaggle memiliki kualitas gambar yang cukup untuk membuat model OCR sederhana.
*   **Keterbatasan:** Karena murni berbasis ML perangkat (*on-device ML*), akurasi model dibatasi oleh kemampuan *device* dan kualitas model awal yang dilatih (CreateML).

### Di Luar Cakupan (*Out of Scope*)
*   Server backend atau database eksternal.
*   Sistem login atau pembuatan akun.
*   Menerjemahkan bahasa (misal: Jawa ke Indonesia) — aplikasi **hanya mengonversi huruf/aksara** (Aksara Jawa ke huruf Latin).

### Peta Jalan Fase (Milestone/Phase Breakdown)
*   **Fase 1 (Proof of Concept):** Eksperimen dengan dataset Kaggle dan CreateML untuk membuahkan sebuah model valid.
*   **Fase 2 (Core App MVP):** Membangun UI SwiftUI sederhana yang mengintegrasikan model ke galeri dan kamera statis.
*   **Fase 3 (Real-Time Vision):** Implementasi *live-feed* dari kamera untuk inferensi secara *real-time*.
*   **Fase 4 (Polish & Accessibility):** Menyempurnakan UI/UX, optimasi inferensi, fitur aksesibilitas, dan XCTest.

### Aturan Khusus Ekosistem Multi-Project (Multi-Node)
Saat ini proyek berjalan sebagai **Single-Project Environment** dalam node `aksara-jawa-translator`. Semua fungsionalitas dan arsitektur dienkapsulasi di dalam node tersebut.

---
*Digenerate oleh AI Agent. Mode Operasional saat ini: Mode 2 (Prompt-Driven).*
