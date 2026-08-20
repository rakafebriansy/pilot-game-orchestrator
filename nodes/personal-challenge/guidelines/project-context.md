# Project Context & Guidelines

## 1. Analisis Codebase Saat Ini
Saat ini repositori berupa proyek kosong (*greenfield project*). Pengembangan akan difokuskan pada pembuatan aplikasi iOS secara native menggunakan **SwiftUI** dan **CreateML**. Belum ada *legacy code* maupun dependensi pihak ketiga.

## 2. Batasan Spesifik Proyek
*   **Lingkungan & Bahasa:** Native Apple Environment (Swift, SwiftUI, iOS 16.0+).
*   **Machine Learning:** Model *on-device* menggunakan CoreML dan Vision. Pelatihan model dilakukan via CreateML dengan dataset Kaggle `vzrenggamani/hanacaraka/data`.
*   **Konektivitas:** 100% *Offline* (Tidak menggunakan *backend* atau layanan *cloud* untuk *inference*).
*   **Aksesibilitas:** Menjadi pertimbangan penting, namun diimplementasikan dengan prioritas fitur di akhir setelah *core functionality* (terjemahan kamera dan galeri) berjalan dengan baik.
*   **Pengujian:** Mengandalkan **XCTest** untuk memvalidasi *pipeline* ML dan unit-unit UI utama.
*   **Mode Operasional:** Menggunakan Mode 2 (*Prompt-Driven*) sehingga AI mengeksekusi secara mikro instruksi pengguna, alih-alih merencanakan semuanya di awal.

## 3. Pustaka Eksisting
*(Belum ada pustaka eksternal yang di-install. Pods/SPM belum ada.)*
