# User Interface (UI) & Asset Integration Standards

*(Pedoman teknis ini berlaku krusial untuk fase pengerjaan atas entitas proyek apa pun yang mengelola subsistem antarmuka visual)*

## Standar & Aturan Seni Visual (Visual & Concept Art Rules)
Untuk seluruh produksi aset visual game, *concept art*, ilustrasi kartu, *sprite* isometrik, hierarki visual medan laga, dan alur kerja pembuatan aset, Anda **WAJIB MUTLAK** mematuhi pedoman utama yang tertulis di dalam **[`global-docs/visual-art-rules.md`](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/global-docs/visual-art-rules.md)** (mencakup 3 Pilar Utama: *Clarity, Satisfaction, Style*; Hierarki Visual: *Value, Shape/Size, Detail Placement*; dan Alur Concept Art: *Project Prep, Generating Ideas, Navigating Feedback*).

## Kewajiban Penggunaan Prototipe (Pre-Coding)
Sebelum Anda (AI Agent) secara langsung menulis *source code* antarmuka (*UI*) menggunakan *framework* utama aplikasi (seperti Flutter, SwiftUI, Next.js, dsb.), Anda **DIWAJIBKAN** merancang sketsa draf visualnya di dalam direktori spesifik *node* yaitu `nodes/[nama-node]/prototypes/`. 
Anda **WAJIB MUTLAK** membaca dan mematuhi seluruh arsitektur *Rapid HTML-based Previewing* dan *Single-File Policy* yang tertulis di dalam file referensi **`nodes/[nama-node]/prototypes/README.md`** sebelum menyusun kode UI apa pun.
## Standar Konversi Aset Visual ke Kode Implementatif
Setiap kali mengeksekusi konversi dari objek aset visual mentah (contoh: templat *raw* SVG, berkas HTML orisinal, XML tata letak struktural) menjadi *codebase* berbasis *framework* fungsional:
1.  Patuhi secara saksama kaidah penamaan konvensi internal dari target *framework* tersebut. Misalnya, atribut kode yang semula berbasis sintaks *kebab-case* **wajib dikonversi** ke dalam format sintaksis *camelCase* apabila tata kelola bahasa memandatkannya.
2.  Gugurkan segala *value default* dari sumber asli visual yang sudah tidak relevan di lingkungan eksekusi, format ulang penulisan sintaksis pelapisan desain (*styling parameters*), serta tertibkan penutup *tag* agar sepenuhnya memenuhi prasyarat kompiler atau penata dokumen terkait.

## Prosedur Autorisasi Sumber Aset Lintas Domain
1.  Di saat lapisan visual dituntut untuk merender media statis pendukung (*placeholder image/stream resource*) langsung dari sumber infrastruktur (*domain*) pihak luar secara jarak jauh (*remote fetching*):
2.  Aturan tata batas mutlak mewajibkan Anda untuk meregistrasikan alamat domain eksternal secara sadar ke dalam konfigurasi daftar aman (*whitelist* / *safe origins setting*) kerangka kerja proyek, guna mencegah terblokirnya materi media akibat *policy restriction* seperti batasan CORS atau *invalid internal runtime exceptions*.

## Metrik Skalabilitas Satuan Antarmuka (Aksesibilitas Mutlak)
1.  **Tinggalkan Absolutisme Unit Piksel (Khusus Web/CSS):** Sangat dihindari dalam spesifikasi pengembangan web menggunakan pendefinisian jarak absolut dan kaku melalui piksel (`px`) untuk menetapkan hierarki ukuran area pandang (*margins/spacing*), parameter kerangka blok, maupun topografi *font* tulisan secara *hardcoded*.
2.  **Inisiatif Satuan Dimensi Responsif:** Sistem harus dibangun dengan berorientasi pada nilai porsi rasio proporsional—seperti `rem`, `em`, atau persentase basis (`%`). Regulasi ini esensial bukan sebatas aspek *layouting responsif*, melainkan agar aplikasi secara mendasar mampu bersanding mulus terhadap adaptasi pengaturan aksesibilitas skala *font default* secara sistemik di OS (*Operating System*) atau peramban dari sisi preferensi klien akhir.
3.  **Pengecualian Native Framework:** Aturan larangan `px` ini **TIDAK BERLAKU** untuk *framework native* (seperti Flutter, SwiftUI, React Native) yang mana secara baku sudah menggunakan satuan responsif abstrak (seperti *logical pixels*, *points*, atau *density-independent pixels*). Pada *framework* tersebut, silakan gunakan satuan baku bawaannya.

## Kepatuhan Aksesibilitas (Accessibility Compliance)
Jika *project/node* yang sedang dikerjakan merupakan aplikasi antarmuka pengguna seperti **Web (Frontend)** atau **Mobile App**, Anda **WAJIB** memastikan bahwa hasil pekerjaan memenuhi standar aksesibilitas (*accessibility* / a11y).
1. **Adaptasi Berdasarkan Teknologi:** Pendekatan aksesibilitas harus disesuaikan dengan jenis teknologi yang digunakan. Jika menggunakan teknologi *native*, manfaatkan kapabilitas dan API aksesibilitas bawaan dari *platform native* tersebut secara maksimal.
2. **Prosedur Implementasi & Konfirmasi:** Anda harus mendaftar dan memberikan seluruh opsi fitur aksesibilitas yang relevan dan dapat diimplementasikan sesuai dengan *stack* teknologi yang dipakai. Setelah memberikan daftar opsi tersebut, **wajib** tanyakan kepada *developer* (pengguna) mengenai fitur mana saja yang ingin diimplementasikan.

## Evaluasi Heuristik (Heuristic Evaluation)
Anda **WAJIB** menggunakan metode *Heuristic Evaluation* untuk meninjau dan mengevaluasi kualitas usabilitas antarmuka pengguna (UI). Pengecekan ini mutlak dilakukan untuk memastikan seluruh desain dan interaksi telah sejalan dengan prinsip dasar kemudahan penggunaan.

Berikut adalah 10 prinsip Heuristik Jakob Nielsen yang wajib dicek beserta contoh *approach* (pendekatan) penerapannya pada tahap pengembangan antarmuka:

1. **Visibilitas Status Sistem (Visibility of system status)**
   - *Approach:* Selalu berikan *feedback* instan kepada pengguna. Gunakan *loading spinner* atau *skeleton screen* saat mengambil/mengirim data via API. Jika ada proses latar belakang, sediakan *progress bar* atau teks status (seperti "Menyimpan...").
2. **Kecocokan Sistem dengan Dunia Nyata (Match between system and the real world)**
   - *Approach:* Gunakan ikon universal (misal: ikon tong sampah untuk "Hapus", ikon disket untuk "Simpan"). Jangan tampilkan pesan *error* teknis (contoh: "Error 500" atau "Null Reference"), ubahlah menjadi bahasa natural ("Maaf, koneksi gagal. Silakan coba lagi").
3. **Kendali dan Kebebasan Pengguna (User control and freedom)**
   - *Approach:* Pastikan setiap alur memiliki jalan keluar. Selalu sediakan tombol "Batal" (*Cancel*), "Kembali" (*Back*), atau opsi "Urungkan" (*Undo*) jika pengguna tidak sengaja mengeklik atau menghapus suatu *item*.
4. **Konsistensi dan Standar (Consistency and standards)**
   - *Approach:* Terapkan penamaan, warna, tipografi, dan gaya komponen (seperti tombol *Primary/Secondary*) yang konsisten di seluruh halaman aplikasi. Pastikan implementasinya mematuhi standar *Design System* dari *framework* atau desain awal.
5. **Pencegahan Kesalahan (Error prevention)**
   - *Approach:* Terapkan validasi form secara *real-time* saat pengetikan (contoh: memvalidasi format email sebelum ditekan *Submit*). Gunakan *state disabled* pada tombol kirim jika input masih kosong/salah. Tampilkan modal konfirmasi untuk tindakan yang merusak/destruktif ("Apakah Anda yakin ingin menghapus ini?").
6. **Pengenalan Daripada Mengingat (Recognition rather than recall)**
   - *Approach:* Jangan memaksa pengguna mengingat informasi di luar kepala. Gunakan *dropdown autocomplete*, *placeholder* yang deskriptif, dan fitur "Pencarian Terakhir" (*Recent Searches*) agar pengguna dapat sekadar memilih objek yang pernah ditangani.
7. **Fleksibilitas dan Efisiensi Penggunaan (Flexibility and efficiency of use)**
   - *Approach:* Rancang UI agar bisa digunakan secara cepat oleh pemakai tingkat lanjut (*expert user*). Sediakan jalan pintas *keyboard* (*shortcuts/hotkeys*), fitur aksi jamak (*bulk actions*), atau gestur sapuan (*swipe actions* pada versi *mobile*).
8. **Desain Estetis dan Minimalis (Aesthetic and minimalist design)**
   - *Approach:* Hindari *clutter* atau tata letak elemen yang terlalu padat. Jaga *white space* atau jarak margin/padding yang cukup untuk menonjolkan fokus utama layar. Hapus semua elemen dekoratif yang tidak menunjang fungsionalitas.
9. **Membantu Pengguna Mengenali, Mendiagnosis, dan Memulihkan Kesalahan (Help users recognize, diagnose, and recover from errors)**
   - *Approach:* Jika validasi *form* gagal, *highlight* warna merah khusus pada kolom/bidang yang bermasalah. Jangan hanya menulis "Input Invalid", melainkan beri tahu cara memperbaikinya ("Password harus memiliki minimal 8 karakter dan huruf kapital").
10. **Bantuan dan Dokumentasi (Help and documentation)**
    - *Approach:* Berikan *tooltip* bantuan (?) pada label yang mungkin membingungkan. Apabila UI melibatkan proses langkah-langkah rumit, berikan layar pengantar interaktif (*onboarding walkthrough*) atau tautan "Pelajari Lebih Lanjut".
