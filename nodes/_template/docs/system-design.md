# System Design

## Apa itu System Design
System Design adalah dokumen teknis komprehensif yang menjelaskan arsitektur perangkat lunak, tumpukan teknologi (*tech stack*), pola desain (*design patterns*), struktur *database*, serta cara berbagai komponen sistem saling berintegrasi. Jika PRD berfokus pada "apa" yang akan dibangun untuk pengguna, System Design berfokus secara mendalam pada "bagaimana" sistem tersebut akan dibangun dari sisi rekayasa perangkat lunak (*software engineering*) agar efisien, aman, dan dapat diskalakan (*scalable*).

Dalam pendekatan *Vibe Coding*, System Design memegang peran krusial sebagai "cetak biru arsitektur" bagi AI Agent. Dokumen ini menjadi batasan teknis (*technical constraints*) agar AI tidak membuat keputusan struktural secara sembarangan saat menulis kode. Dengan adanya acuan ini, seluruh modul, komponen, dan fungsi yang di-*generate* oleh AI akan selalu mematuhi satu standar arsitektur yang sama (misalnya, *Clean Architecture*, *MVC*, atau *MVVM*), sehingga basis kode tetap rapi dan terstruktur.

Secara rinci, sebuah dokumen System Design wajib memuat komponen-komponen berikut:
*   **Arsitektur Perangkat Lunak (*Software Architecture*):** Pola dasar yang akan diterapkan secara konsisten pada seluruh proyek (contoh: *Clean Architecture*, *Microservices*, *MVVM*).
*   **Struktur Direktori (*Folder Structure*):** Pemetaan standar untuk pengorganisasian *folder* dan aturan penempatan jenis-jenis *file* (seperti *models, views, controllers, services*).
*   **Tumpukan Teknologi (*Tech Stack* & Dependensi):** Bahasa pemrograman, *framework*, *library*, versi SDK, serta alat pendukung (*tools*) lain yang menjadi prasyarat proyek.
*   **Logika & Alur Komponen (Flowchart):** Diagram alur kerja algoritma spesifik *node* ini. Anda **WAJIB MUTLAK** memvisualisasikan alur kerja komponen utamanya menggunakan PlantUML. Simpan file sebagai `docs/diagrams/flowchart.puml` secara eksplisit lalu tautkan ke sini. Jika *node* ini adalah aplikasi bertipe *Game Engine*, Anda **WAJIB** menggantinya dengan *Micro State Diagram* (seperti alur *Player State*) menggunakan PlantUML dan simpan di `docs/diagrams/state-diagram.puml`.
*   **Skema Data (*Data Modeling*):** Rancangan dan representasi struktur data yang digunakan. Bagian ini wajib dirincikan ke dalam sub-komponen berikut:
    *   **ERD (*Entity-Relationship Diagram*):** Jika aplikasi ini memuat penyimpanan basis data (*Backend/Fullstack*), Anda **WAJIB** membuat rancangan tabel, relasi, dan tipe data menggunakan PlantUML. Simpan file sebagai `docs/diagrams/erd.puml` secara eksplisit dan tautkan ke sini.
    *   **Standar Datetime & Basis Data:** Saat mendefinisikan kolom tanggal/waktu pada skema basis data, Anda **WAJIB** memilih dan mendokumentasikan salah satu opsi baku (Opsi 1: Epoch Millis / `BIGINT` atau Opsi 2: Timestamp with Timezone UTC / `TIMESTAMPTZ`) secara konsisten dengan merujuk pada `../../global-guidelines/database.md`.
    *   **Model:** (Jika aplikasi berupa *fullstack* atau *frontend*) mendefinisikan representasi struktur *object* atau entitas secara internal di dalam kode (*state*).
    *   **DTO (*Data Transfer Object*):** (Jika aplikasi berbasis API) menjelaskan format struktur data (*payload*) yang dikirim atau diterima dalam proses komunikasi *client-server*.
*   **Desain API (*API Contracts*):** Spesifikasi alur komunikasi antar layanan atau antara *client* dan *server*, mencakup spesifikasi *endpoint*, format *request/response payload*, metode autentikasi, serta wajib menyertakan **contoh konkret dari *Header*, *Request*, dan *Response***.
*   **Arsitektur Penyebaran (Deployment Architecture):** Rincian mengenai infrastruktur *hosting*, skema penyebaran peladen (*server deployment*), penyedia awan (*cloud provider*), dan sistem *containerization* (seperti Docker) yang digunakan.
*   **Integrasi Eksternal (*Third-Party Integrations*):** Daftar dan mekanisme integrasi sistem dengan penyedia layanan pihak ketiga (seperti *payment gateway*, analitik, penyimpanan cloud).
*   **Konfigurasi Lingkungan (Environment Variables):** Daftar nama variabel lingkungan rahasia (.env) yang dibutuhkan oleh arsitektur ini beserta deskripsi utilitasnya.
*   **Keamanan & Penanganan Eror (*Security & Error Handling*):** Protokol keamanan (*encryption*), pengelolaan autentikasi/otorisasi pengguna, serta standar untuk *error logging* dan validasi data.

## Cara Melakukan Generate System Design
Pastikan dokumen PRD (`../../global-docs/prd.md`) sudah diselesaikan terlebih dahulu. Berikan draf PRD tersebut kepada AI Agent dan instruksikan untuk mengusulkan arsitektur sistem yang paling cocok. Mintalah AI Agent untuk mengajukan pertanyaan-pertanyaan spesifik terkait batasan infrastruktur, preferensi *tech stack*, target performa, skema keamanan, serta struktur data yang Anda inginkan. Berdasarkan diskusi tersebut, AI akan merumuskan dokumen teknis yang menjadi acuan penulisan kode selanjutnya.

> **Kewajiban Referensi Desain:** Sebelum AI Agent mengeksekusi penyusunan antarmuka (UI) berdasarkan arsitektur ini, ia **WAJIB** merujuk pada pedoman visual global di `../../global-docs/design-system.md` agar implementasi logika sesuai dengan pedoman estetika ekosistem.
## (_Judul Proyek_)

*(Rincian arsitektur, struktur folder, skema data, serta panduan System Design spesifik untuk aplikasi ini akan ditambahkan di bawah batas ini)*
