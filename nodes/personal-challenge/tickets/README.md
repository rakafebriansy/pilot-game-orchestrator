# Local Tickets Directory (`tickets/`)

Direktori ini berfungsi sebagai pusat komando atau sumber kebenaran (*source of truth*) lokal untuk manajemen tugas proyek Anda. File *markdown* di dalam folder ini nantinya dapat disinkronisasikan ke **GitHub Projects**, sehingga Anda bisa mengontrol arus tugas secara lincah melalui AI di ranah lokal (*offline first*), sekaligus menikmati visualisasi papan *Kanban* secara nyata di antarmuka GitHub.

## Aturan Penamaan File Tiket
Setiap file tiket baru yang dibuat di dalam direktori ini **WAJIB** dikonstruksikan menggunakan pedoman nama berikut:
`TICKET-[NOMOR]-[JUDUL-SINGKAT-KEBAB-CASE].md`
**Contoh Valid:** `TICKET-01-login-form.md`, `TICKET-02-api-integration.md`

## Sumber Tiket (Backlog Source of Truth)
Anda (AI Agent) **DILARANG KERAS** mengarang, mencetuskan, atau mendesain tiket tugas baru secara sembarangan di luar kendali pengguna. Setiap instruksi untuk mendesain file tiket baru di folder ini **WAJIB MUTLAK** diimpor wujudnya dari *backlog* yang telah disetujui sebelumnya di dalam peta jalan `docs/development-planning.md`.

## Kebijakan Pemisahan Tiket UI Prototyping & Implementasi
Apabila Anda diinstruksikan untuk membuat tiket yang berkaitan dengan *UI Slicing*, Anda **WAJIB** mengonfirmasi kepada pengguna apakah desain antarmukanya sudah tersedia atau perlu dirancang melalui prototipe HTML di direktori `prototypes/`. Jika pengguna memilih mekanisme *prototyping*, Anda **WAJIB MEMISAHKAN** antara tiket pembuatan prototipe desain dengan tiket implementasi kode ke dalam sistem utama. Jangan pernah menggabungkan kedua fase ini dalam satu tiket tugas.

## Konsep "AI Execution Log & Output"
Ini adalah fitur ruang kerja reflektif mandiri bagi AI di dalam setiap *file* tiket. Ketika AI Agent menerima tugas pengerjaan atas sebuah tiket, ia dilarang hanya berfokus mengubah baris kode program saja. AI Agent **wajib** kembali ke file tiket ini dan merangkum jejak kerjanya di seksi khusus tersebut.

Pada bagian *AI Execution Log*, AI bertugas:
1.  **Mencatat jejak modifikasi teknis:** File apa saja yang dibuat/dihapus/diubah.
2.  **Menulis landasan keputusan desain:** Mengapa metode, variabel, atau kerangka kerja arsitektur A dipilih alih-alih B.
3.  **Memetakan temuan tak terduga:** Mencatat peringatan atau potensi isu keamanan (*security flaw*) jika ditemukan.
Dengan konsep log ini, tiket Anda tidak hanya mendokumentasikan apa yang *harus* dikerjakan, tetapi juga terekam jelas jejak tentang *bagaimana* itu dieksekusi. Layaknya riwayat komentar *developer* dalam tiket JIRA nyata.

---

## Boilerplate (Templat) Pembuatan Tiket Baru

Setiap AI Agent atau pengguna yang ingin membuat instruksi tiket baru **WAJIB MUTLAK** menyalin dan mematuhi struktur *markdown* di bawah ini.
*(Catatan Khusus: Apabila jenis instruksinya adalah perbaikan kegagalan sistem, Anda **wajib menggunakan format templat yang lebih spesifik** yang berlokasi di `../../global-docs/templates/bug_report_template.md` ketimbang format standar fitur di bawah).*

```markdown
---
id: TICKET-[NOMOR]
title: [Judul Singkat Tugas]
status: Todo # Siklus Transisi Valid: Todo -> In Progress -> Done
priority: High # Siklus Valid: Low, Medium, High
labels: [Frontend, Backend, Feature, Bug, dsb]
---

# Deskripsi
[Tuliskan latar belakang instruksi, masalah, atau fitur yang ingin dibangun secara mendetail. AI Agent akan menginterpretasikan dan mengeksekusi kemauan Anda berdasarkan teks di sini.]

## Acceptance Criteria (Kriteria Penerimaan)
*Daftar spesifikasi mutlak (checklists) yang harus terpenuhi agar tiket ini sah dianggap berstatus selesai (Definition of Done).*
- [ ] [Kriteria 1]
- [ ] [Kriteria 2]
- [ ] [Kriteria 3]

## Target Lingkup File (Affected Files)
*Daftar path file yang diinstruksikan atau berpotensi diubah sebagai referensi utama eksekusi AI.*
- `path/ke/file.ext`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. ...
  2. ...
- **Ringkasan File Terpengaruh:**
  - `path/ke/file/...`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - ...
```
