# Design System & UI Guidelines

## Aksara Jawa Translator

Mengingat belum ada Vibe/Estetika UI yang secara spesifik didefinisikan oleh pengguna, *design system* ini akan bersandar pada **Human Interface Guidelines (HIG)** dari Apple, dengan tujuan memberikan pengalaman *native* yang kuat dan bersih, serta memaksimalkan fitur aksesibilitas sistem.

### 1. Prinsip Desain
*   **Native & Familiar:** Menggunakan komponen SwiftUI standar (*NavigationStack*, *Buttons*, *Forms*, *Sheets*) agar pengguna iOS merasa familier.
*   **Aksesibel (Accessible First):** Dukungan penuh untuk *Dynamic Type* (ukuran teks menyesuaikan pengaturan iOS), label *VoiceOver* yang deskriptif, dan rasio kontras warna tinggi.
*   **Fokus pada Kamera:** Karena aplikasi berbasis kamera/OCR, UI harus minimalis di layar pemindaian untuk memaksimalkan area tangkapan *Viewfinder*.

### 2. Palet Warna (Color Palette)
Mengandalkan warna semantik bawaan iOS yang otomatis mendukung Dark Mode dan Light Mode:
*   **Primary Accent:** `Color.accentColor` (Bisa disetel ke *Custom System Orange* atau *Brown* untuk memberikan nuansa klasik khas budaya Jawa, namun tetap terlihat modern).
*   **Background:** `Color(uiColor: .systemBackground)` dan `Color(uiColor: .secondarySystemBackground)`.
*   **Text:** `Color.primary` untuk teks utama, `Color.secondary` untuk subteks dan *captions*.

### 3. Tipografi (Typography)
*   **Font Utama:** Apple System Font (San Francisco).
*   **Ukuran Font:** 
    *   Judul Halaman: `.largeTitle` atau `.title` dengan *weight bold*.
    *   Teks Hasil Terjemahan: `.title2` atau `.title3` (agar mudah dibaca).
    *   Instruksi UI: `.body`.
*   **Dynamic Type:** Semua teks wajib menggunakan `.font(...)` *modifiers* semantik, bukan *fixed size*, agar bisa membesar sesuai *accessibility settings*.

### 4. Ikonografi (Iconography)
Menggunakan **SF Symbols** secara eksklusif:
*   Mode Kamera: `camera.fill` atau `camera.viewfinder`.
*   Mode Galeri: `photo.fill.on.rectangle.fill`.
*   Tombol Salin (Copy): `doc.on.doc`.
*   Feedback Berhasil: `checkmark.circle.fill`.

### 5. Komponen UI Inti
*   **Viewfinder (Layar Kamera):** Membutuhkan *overlay* transparan dengan kotak pemandu batas teks (*bounding box guide*) agar pengguna tahu area mana yang diproses.
*   **Bottom Sheet (Hasil Terjemahan):** Saat hasil OCR keluar, teks ditampilkan melalui `.sheet` atau panel di bawah agar tidak sepenuhnya menutupi kamera.
*   **Floating Action Button (FAB) / Kamera Toolbar:** Tombol bundar besar di bagian bawah untuk men-trigger capture/galeri.

### 6. Animasi (Micro-Interactions)
*   *Pulse Animation* pada area *viewfinder* saat ML sedang memproses *live-feed*.
*   *Haptic Feedback* (`UIImpactFeedbackGenerator`) setiap kali teks Aksara Jawa terdeteksi dan hasil terjemahan berhasil diperbarui.

---
*Digenerate oleh AI Agent. Spesifikasi desain di atas bersifat draf awal dan dapat diperluas setelah ideasi UI lebih spesifik.*
