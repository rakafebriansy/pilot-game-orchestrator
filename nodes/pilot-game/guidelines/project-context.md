# Panduan Spesifik Proyek: Pilot Game (Tubbies Studio)

Dokumen ini merupakan pedoman *custom* yang mengikat aturan, peringatan eksklusif, dan penyesuaian khusus yang **hanya relevan** pada proyek **Pilot Game**.

---

## 🔍 Hasil Pemindaian Codebase Asli (*Codebase Scan*)

* **Path Codebase:** `/Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/Tubbies Pilot Game`
* **Engine & Render Pipeline:** Unity 2022 LTS / Unity 6 dengan **Universal Render Pipeline 2D (URP 2D)** (`com.unity.render-pipelines.universal: 17.3.0`).
* **Sistem UI Terpasang:** **Unity UI Toolkit** (`com.unity.modules.uielements: 1.0.0`).
* **Sistem Input:** **Unity New Input System** (`com.unity.inputsystem: 1.20.0`).
* **Sistem Ubin Arena:** **2D Tilemap & Tilemap Extras (Rule Tiles)** (`com.unity.2d.tilemap: 1.0.0`, `com.unity.2d.tilemap.extras: 6.0.3`).
* **Framework Pengujian:** **Unity Test Framework (NUnit)** (`com.unity.test-framework: 1.6.0`).
* **Status Arsitektur Awal:** Proyek bersih (*clean slate*) siap untuk implementasi Fase 0 s/d Fase 6.

---

## 🏛️ Aturan Fundamental Proyek

1. **LARANGAN PURE ECS / DOTS:**
   * Dilarang menggunakan `Unity.Entities`, `Unity.Dots`, atau Pure ECS. Seluruh kode gameplay wajib dibangun menggunakan **OOP Klasik + ScriptableObjects**.
2. **TRIAS PEMISAHAN KODE (Separation of Concerns):**
   * **Data Layer:** `ScriptableObject` murni untuk data konfigurasi statis (kartu, musuh, item).
   * **Logic Layer:** Pure C# Class (Non-MonoBehaviour) untuk matematika pertempuran, grid 15×15, dan status giliran.
   * **View/Presentation Layer:** `MonoBehaviour` murni untuk visualisasi Sprite, Tilemap, animasi, partikel VFX, dan audio SFX.
3. **EVENT-DRIVEN DECOUPLING VIA COMBATEVENTS:**
   * Dilarang membuat *hard reference* atau `GetComponent` langsung antar modul berbeda.
   * Seluruh komunikasi wajib melalui *event hub* terpusat: `CombatEvents.cs`.
   * **Wajib Unsubscribe:** Setiap *event listener* yang didaftarkan pada `OnEnable()` **WAJIB** di-unsubscribe pada `OnDisable()`.
4. **STANDAR SISTEM KOORDINAT 15×15:**
   * Koordinat logika wajib integer `Vector2Int(x, y)` dari `(0, 0)` (Pojok Kiri Bawah) hingga `(14, 14)` (Pojok Kanan Atas).
   * Cell Size pada Grid Unity diatur `(1, 1, 0)` dengan pivot `(0.5, 0.5)`.
5. **ZERO-COMMENT POLICY:**
   * Dilarang menyisipkan komentar non-fungsional di dalam file kode C#. Kode harus *self-documenting* melalui penamaan method dan variabel yang deskriptif.
6. **ANTI-GC ALLOCATION PADA HOT PATHS:**
   * Dilarang menggunakan kata kunci `new` (seperti `new List<T>()`) di dalam loop `Update()`.
   * Wajib menerapkan *Object Pooling* (`UnityEngine.Pool.ObjectPool<T>`) untuk damage pop-up, proyektil, dan partikel tebasan.
7. **PREFERENSI BAHASA KODE & STRING:**
   * **English (Default):** Seluruh penamaan variabel, method, event, exception message, UI text, dan log wajib dalam bahasa Inggris. *(Jika pengguna meminta bahasa non-Inggris secara eksplisit, catat instruksi tersebut di sini)*.

