# Design System

## Apa itu Design System
Design System adalah kumpulan terpusat yang berisi komponen visual, pedoman desain (seperti palet warna, tipografi, dan *spacing*), serta standar pola interaksi UI/UX yang dapat digunakan kembali (*reusable*). Dokumen ini bertujuan untuk memastikan konsistensi tampilan dan pengalaman pengguna di seluruh bagian aplikasi.

Dalam pendekatan *Vibe Coding* yang dikerjakan oleh AI Agent, Design System berfungsi sebagai pedoman gaya (*styling ground truth*). Saat AI meng-*generate* antarmuka (UI) atau komponen baru, AI akan merujuk secara ketat pada aturan-aturan di dokumen ini. Hal ini memastikan bahwa kode UI yang dihasilkan akan secara otomatis selaras dengan identitas visual, *branding*, dan tema aplikasi, sehingga terhindar dari ketidakkonsistenan desain.

Secara rinci, sebuah dokumen Design System wajib memuat komponen-komponen berikut:
*   **Identitas Merek (*Brand Identity*):** Penjelasan mengenai filosofi desain dan nuansa visual.
*   **Palet Warna (*Color Palette*):** Daftar lengkap warna mencakup Primary, Secondary, Background, Surface, Error, Success, dan Warning (Hex/RGB).
*   **Tipografi (*Typography*):** Hierarki teks terstruktur meliputi jenis huruf, ukuran, ketebalan, dan line heights.
*   **Sistem *Spacing*, Tata Letak, & Breakpoints:** Skala margin, padding, border-radius, dan grid.
*   **Aset & Ikonografi:** Gaya visual ikon dan rasio ukuran.
*   **Komponen UI Utama:** Spesifikasi elemen *reusable* (Buttons, Cards, HUD, Badges) dengan berbagai status state.
*   **Animasi & Interaksi:** Durasi waktu dan kurva transisi.
*   **Standar Aksesibilitas (a11y):** Kontras warna dan readability.

---

## 🎨 PILOT GAME DESIGN SYSTEM (DARK MESOPOTAMIAN FANTASY)

### 1. Identitas Visual & Filosofi Merek (*Brand Identity*)
* **Tema Visual:** *Dark Ancient Mesopotamian Fantasy* — Menggabungkan estetika lempengan tanah liat kuno (*Clay Tablets*), huruf paku bercahaya (*Glowing Cuneiform*), batu obsidian gelap Menara Babel, dan aksen emas perunggu pudar (*Ancient Bronze & Gold*).
* **Nuansa Antarmuka (UI Vibe):** Gelap, taktis, elegan, dengan kontras warna neon bercahaya (*vibrant telegraphs*) agar keputusan arena mudah dibaca seketika (*high gameplay readability*).

---

### 2. Palet Warna Resmi (*Color Palette Tokens*)

| Token Name | Hex Code | Deskripsi & Peran Penggunaan |
| :--- | :--- | :--- |
| `--color-bg-base` | `#0D1117` | Latar belakang utama scene & kanvas arena gelap. |
| `--color-surface-panel` | `#161B22` | Latar panel kartu, popup modal, dan container HUD. |
| `--color-surface-card` | `#21262D` | Latar bodi kartu dan ubin netral di grid. |
| `--color-border-subtle` | `#30363D` | Garis pembatas tipis dan border ubin lantai. |
| `--color-primary-cyan` | `#58A6FF` | Aksen tombol aktif, teks cuneiform, dan glow seleksi kartu. |
| `--color-primary-glow` | `#79C0FF` | Partikel sihir, panah telegraph arah gerak. |
| `--color-danger-red` | `#DA3633` | **Enemy Intent Telegraph (Ubin Bahaya Serangan Musuh)**. |
| `--color-danger-bright` | `#F85149` | Angka pengurangan HP musuh / indikator HP kritis. |
| `--color-action-green` | `#238636` | **Target Sah Kartu Serang / Area Gerak Pemain**. |
| `--color-move-blue` | `#1F6FEB` | Sorot jangkauan langkah mobilitas Nabu. |
| `--color-gold-accent` | `#D29922` | Relic kuno, bingkai kartu langka, dan icon energi. |
| `--color-text-primary` | `#F0F6FC` | Teks judul kartu, nilai damage, dan angka status. |
| `--color-text-secondary`| `#8B949E` | Deskripsi efek sekunder, teks lore tablet, dan tooltip. |

---

### 3. Tipografi (*Typography System*)

* **Font Utama UI & HUD:** *Cinzel Decorative* / *Marcellus* / Sans-serif Stylized (Fallback: `Inter, Roboto, sans-serif`).
* **Font Angka Taktis (Damage, HP, Grid Coordinates):** Monospace tebal berangka jelas (*JetBrains Mono* / *Fira Code*).

| Tingkatan Teks | Ukuran Font | Ketebalan (*Weight*) | Tinggi Baris | Penggunaan |
| :--- | :--- | :--- | :--- | :--- |
| **Heading 1 (Hero/Victory)** | 32px | Bold (700) | 40px | Judul Chapter / Banner Menang Wave |
| **Heading 2 (Card Title)** | 18px | Semi-Bold (600) | 24px | Nama Kartu Jurus & Nama Roster Musuh |
| **Body (Card Description)** | 14px | Regular (400) | 20px | Deskripsi efek kartu & tooltip |
| **Badge Number (Damage/HP)** | 16px | Bold (700) | 18px | Angka damage popup & angka energi |
| **Caption (Sub-text)** | 11px | Regular (400) | 14px | Info giliran & tipe kartu (*Immediate/End*) |

---

### 4. Sistem Spacing & Layout (*Spacing Tokens*)

* **Satuan Dasar (*Base Unit*):** 4px.
* **Skala Spacing:**
  * `xs` : 4px (Jarak icon ke angka)
  * `sm` : 8px (Padding dalam kartu & badge)
  * `md` : 16px (Jarak antar kartu di tangan pemain)
  * `lg` : 24px (Margin HUD ke batas layar monitor)
  * `xl` : 32px (Padding modal popup hasil pertempuran)
* **Border Radius:**
  * Ubin Grid: `4px`
  * Kartu Tangan: `8px`
  * Modal Dialog: `12px`

---

### 5. Komponen UI Utama (*UI Toolkit Components*)

#### A. Kartu Taktis (*Tactical Card Component*)
* **Dimensi Rasio:** Lebar 140px × Tinggi 210px (Rasio 2:3).
* **States:**
  * *Default:* Background `#21262D`, Border `#30363D`, Opacity 1.0.
  * *Hover:* Naik ke atas 24px (*Lift Animation*), Border `#58A6FF` dengan glow bayangan biru.
  * *Dragging:* Scale 1.08x, Opacity 0.85, kursor berubah menjadi target reticle.
  * *Disabled (Energi Kurang):* Grayscale 50%, Opacity 0.4.

#### B. Badge Niat Musuh (*Enemy Intent Badges*)
* Terletak melayang di atas kepala musuh di scene:
  * ⚔️ **Serangan Fisik:** Ikon pedang merah dengan angka prediksi damage.
  * 🛡️ **Pertahanan Diri:** Ikon perisai perunggu dengan angka penambahan armor.
  * 🌀 **Efek Status/Kutukan:** Ikon spiral cuneiform ungu.

#### C. Health Bar & Shield Layer
* Bar HP berwarna hijau `#238636` / merah `#DA3633`.
* Jika memiliki Shield, lapisan biru muda `#388BFD` menutupi bagian atas Bar HP dengan angka shield di sampingnya.

---

### 6. Animasi & Transisi Mikro (*Micro-interactions*)

* **Durasi Standar:**
  * *Instant Feedback (Hover/Click):* 0.15s (`ease-out`).
  * *Card Deploy / Dissolve FX:* 0.35s (`ease-in-out`).
  * *Unit Grid Step (Lerp):* 0.18s per petak ubin.
  * *Screen Shake Damage Kritis:* Durasi 0.2s dengan magnitude 0.15 unit.

---

### 7. Standar Aksesibilitas (*a11y*)
* **Kontras Warna Ubin:** Telegraph ubin bahaya merah (`#DA3633`) selalu memiliki kontras minimal 4.5:1 terhadap ubin lantai dasar `#0D1117`.
* **Dukungan Buta Warna (*Colorblind Friendly*):** Ubin bahaya selain ditandai warna merah juga memiliki pola garis miring (*diagonal stripes overlay*) dan ikon pedang di tengah ubin.
