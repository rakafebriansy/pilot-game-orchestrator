# Prototypes Directory

Direktori `prototypes/` berfungsi sebagai ruang uji coba atau kanvas visual mandiri untuk merancang (*design*) komponen antarmuka (UI) sebelum diimplementasikan ke dalam kerangka kerja *source code* utama (*production code*).

## Konsep dan Tujuan Utama

Pendekatan esensial dari folder ini adalah **Rapid HTML-based Previewing**. Daripada membuang waktu dan *resource* untuk merangkai antarmuka secara langsung pada *framework* target yang memakan waktu kompilasi (seperti Flutter, SwiftUI, atau React Native), seluruh desain antarmuka awal **WAJIB** digambar menggunakan teknologi web dasar (**HTML/CSS/Canvas**).

Keuntungan pendekatan ini:
1. **Iterasi Visual Ekstra Cepat:** Pengguna dan AI Agent dapat terus-menerus memperbaiki *layout*, warna, dan animasi secara *real-time* di *browser*.
2. **Validasi Tanpa Hambatan:** Bentuk desain dapat diutak-atik hingga pengguna benar-benar merasa puas tanpa merusak struktur kode inti aplikasi.
3. **Translasi Bersih:** Setelah purwarupa (prototipe) HTML disetujui, desain final barulah ditranslasikan dan diintegrasikan ke dalam lingkungan bahasa pemrograman yang sebenarnya.

## Contoh Skenario Penggunaan

### 1. Skenario Game Development (Misal: SwiftUI/SpriteKit)
Jika aplikasi target Anda adalah *game* dan Anda ingin merancang desain aset objek (contoh: desain struktur Rumah atau Peta), maka komponen Rumah tersebut tidak akan langsung dikoding di dalam *SpriteKit*. Rumah tersebut akan digambar di atas *HTML Canvas* atau elemen *DOM* di folder `prototypes` ini. Setelah Anda memvalidasi tampilan *HTML Canvas* tersebut, barulah aset tersebut diterjemahkan secara matematis ke dalam logika *SwiftUI/SpriteKit*.

### 2. Skenario Mobile App (Misal: Flutter POS App)
Jika Anda sedang membangun aplikasi *Point of Sales* (POS) untuk *mobile*, Anda akan menginstruksikan AI untuk membangun rancangan layar kasir (tombol, keranjang, menu) menggunakan struktur struktur HTML/CSS di dalam folder ini. Anda memoles bentuk visualnya melalui *browser* hingga tata letaknya pas. Setelah desain akhir disetujui, AI akan melakukan proses *translation* (mengonversi `<div>` dan `flexbox` HTML tersebut menjadi kumpulan *Widget* Flutter) untuk diletakkan di repositori utama.

## Aturan Ketat Pembuatan Komponen (*Single-File Policy*)
Demi portabilitas dan kecepatan validasi saat file diklik ganda oleh pengguna, AI Agent **WAJIB** mematuhi pedoman struktur berikut untuk setiap pembuatan komponen prototipe:
1.  **Wajib Single-File (Internal CSS & JS):** Setiap rancangan satu buah komponen atau antarmuka **HARUS** disatukan ke dalam satu *file* berformat `.html` saja (contoh: `pos-dashboard.html`).
2.  **Dilarang Memecah File Eksternal:** Anda dilarang memisahkan berkas menjadi `.css` atau `.js` mandiri. Seluruh gaya elemen wajib ditulis menggunakan *Internal CSS* (di dalam blok `<style>`) dan seluruh *mockup* interaktivitas menggunakan *Internal JS* (di dalam blok `<script>`).
3.  **Hanya Kanvas Visual Statis:** Prototipe di dalam folder ini **DILARANG KERAS** menyentuh arsitektur *backend*, melakukan pemanggilan API nyata, atau terhubung ke basis data. Folder ini berfungsi mutlak murni sebagai kanvas sketsa visual semata.

## Struktur Direktori Prototipe & Reusabilitas HTML
Meskipun hasil akhirnya murni berupa perakitan berkas statis, AI Agent wajib mengorganisasikan lingkungan `prototypes` secara logis dan struktural. Patuhi hierarki pemisahan ketat berikut:

```text
📁 prototypes/
├── 📁 pages/ (atau 📁 screens/ atau folder halaman lain sesuai project)   <-- File HTML yang merender komposisi antarmuka layar penuh (full-page layout)
│   └── 📄 pos-dashboard.html
│
└── 📁 components/                 <-- File HTML independen yang murni merender satu spesifik elemen (micro-UI)
    └── 📄 item-card.html
```

*   **Thoughtful Design & Reusability:** Selaku *Frontend/DX Specialist*, Anda (AI Agent) dituntut untuk selalu berpikir selangkah lebih maju. Merancang *file* HTML di folder ini bukanlah sekadar menyusun gaya mentah. Buatlah rancangan *DOM* (struktur kode HTML) secara sangat presisi, cermat (*thoughtful*), dan modular. Pertimbangkan selalu prinsip reusabilitas konseptual (*conceptual reusability*)! Bayangkan bagaimana kelas CSS dan pengelompokan `div` Anda kelak dapat dengan mulus diekstraksi dan dipecah menjadi komponen *reusable* (seperti modul independen *Widget* di Flutter atau *View* di SwiftUI) saat pengguna memberikan instruksi tahap translasi.
