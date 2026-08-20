---
id: TICKET-03
title: Integrasi MLVisionService
status: Done
priority: High
labels: [Backend, CoreML, Vision]
---

# Deskripsi
Mengimpor model `.mlmodel` yang telah dilatih ke dalam proyek Xcode dan membuat kelas Service yang membungkus *Apple Vision Framework* untuk memfasilitasi prediksi gambar.

## Acceptance Criteria
- [x] File `.mlmodel` berhasil diimpor ke dalam Xcode.
- [x] Kelas `MLVisionService.swift` berhasil dibuat.
- [x] Kode dapat di-build (*Build Succeeded*) tanpa ada error kompilasi.

---

## AI Execution Log & Output
*Tugas ini dieksekusi secara mandiri (manual) oleh pengguna sebagai bagian dari proses pembelajaran, dibantu oleh panduan `manual-guide-01.md`.*

- **Langkah Teknis Tereksekusi:**
  1. Pengguna melakukan drag-and-drop file `AksaraJawaModel.mlmodel` ke Xcode.
  2. Membuat `Services/MLVisionService.swift` yang menerapkan `VNCoreMLRequest` dan `VNImageRequestHandler`.
  3. Memastikan proyek *Build Succeeded*.
- **Ringkasan File Terpengaruh:**
  - `AksaraJawaModel.mlmodel` (Ditambahkan)
  - `Services/MLVisionService.swift` (Dibuat)
