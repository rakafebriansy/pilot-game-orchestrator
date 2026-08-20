---
id: TICKET-02
title: Melatih Model AI dengan CreateML
status: Done
priority: High
labels: [Machine Learning]
---

# Deskripsi
Menggunakan dataset Javanese Script dari Kaggle untuk melatih model kecerdasan buatan berbasis `Image Classification` menggunakan alat Create ML dari Apple.

## Acceptance Criteria
- [x] Dataset dipersiapkan dengan struktur folder yang benar.
- [x] Model dilatih menggunakan `ImageFeaturePrint.Scene v2`.
- [x] Model diekspor dalam bentuk file `.mlmodel`.

---

## AI Execution Log & Output
*Tugas ini dieksekusi secara mandiri (manual) oleh pengguna sebagai bagian dari proses pembelajaran.*

- **Langkah Teknis Tereksekusi:**
  1. Melatih model menggunakan GUI Create ML dengan iterasi dasar.
  2. Menentukan bahwa dataset 79 gambar per kelas menyebabkan *overfitting* ringan (Validasi 66%), namun cukup untuk MVP.
  3. Mengekspor `AksaraJawaModel.mlmodel`.
- **Catatan & Keputusan Arsitektural:**
  - Memilih algoritma `Scene Print v2` demi akurasi ekstraksi garis karakter yang lebih tajam.
