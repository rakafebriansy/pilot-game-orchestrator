# 🤖 Unity & C# Architecture Rules (AI Agent Specification)

Dokumen ini berisi aturan teknis ringkas, padat, dan mengikat (*binding constraints*) untuk seluruh AI Agent saat memproduksi, memodifikasi, atau merefaktor kode C# dan aset Unity di repositori *Pilot Game*.

> [!NOTE]
> Untuk panduan penjelasan naratif, kamus mental model Web-to-Unity, dan resep onboarding programmer junior, rujuk ke [unity-readme.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/global-guidelines/unity-readme.md).

---

## 1. ATURAN PARADIGMA & ARSITEKTUR

1. **LARANGAN DOTS/ECS:** DILARANG MENGGUNAKAN `Unity.Entities`, `Unity.Dots`, atau Pure ECS. Seluruh kode wajib menggunakan **OOP Klasik + ScriptableObjects**.
2. **TRIAS PEMISAHAN KODE (Separation of Concerns):**
   * **Data Layer:** `ScriptableObject` murni untuk data konfigurasi statis (kartu, musuh, item). Tidak boleh memuat logika giliran atau memanipulasi GameObject.
   * **Logic Layer:** Pure C# Class (Non-MonoBehaviour) untuk matematika pertempuran, grid 15×15, dan status giliran.
   * **Presentation/View Layer:** `MonoBehaviour` murni untuk rendering visual, animasi, VFX, dan audio SFX.
3. **EVENT-DRIVEN DECOUPLING:**
   * DILARANG membuat *hard reference* antar modul berbeda (misal: `CardView` langsung memanggil `GridManager`).
   * Seluruh komunikasi wajib melalui static event hub terpusat: `CombatEvents.cs`.
   * **Wajib Unsubscribe:** Setiap *event listener* yang didaftarkan pada `OnEnable()` **WAJIB** di-unsubscribe pada `OnDisable()` (`CombatEvents.OnEvent -= Handler`).

---

## 2. STANDAR KODE & PENAMAAN C#

1. **Konvensi Penamaan:**
   * `PascalCase`: Class, Struct, Interface (`IPascalCase`), Enum & Member, Method, Public Property, Konstanta (`MaxCards`).
   * `_camelCase`: Private field (`_currentHealth`), Serialized Inspector field (`[SerializeField] private int _baseDamage`).
   * `OnPascalCase`: Action delegate / Event (`public static Action<CardData> OnCardPlayed;`).
   * `PascalCase_Deskriptif`: File aset ScriptableObject (`Card_PageCutter.asset`).
2. **Enkapsulasi Inspector:**
   * DILARANG menggunakan `public int health;` untuk menampilkan field di Inspector.
   * Gunakan `[SerializeField] private int _health;` dipadukan dengan *read-only property* `public int Health => _health;`.
3. **ZERO-COMMENT POLICY:**
   * DILARANG MENYISIPKAN komentar non-fungsional apa pun (`//`, `/* */`, `///`) di dalam file kode C#. Kode harus *self-documenting*.

---

## 3. FINITE STATE MACHINE (FSM) GILIRAN

1. **Pola State Murni:** Gunakan interface `ICombatState` (`Enter()`, `Update()`, `Exit()`) dan `CombatStateMachine`.
2. **Empat Fase Wajib:**
   * `IntentPhaseState`: Menghitung niat aksi seluruh musuh aktif & menampilkan telegraph pada grid 15×15.
   * `PlayerPhaseState`: Membuka interaksi kartu tangan pemain (Pemain wajib memainkan 1 kartu per giliran, tanpa *pass turn*).
   * `EnemyPhaseState`: Mengeksekusi niat musuh dalam urutan acak (*Totally Random Order*).
   * `RoundResetState`: Menarik kartu hingga maksimal 5 di tangan, mengecek kondisi Menang/Kalah.

---

## 4. SISTEM UI (UI TOOLKIT)

1. **Framework Wajib:** Gunakan **Unity UI Toolkit** (bukan Canvas / UGUI legacy).
2. **Pemisahan File:** Struktur di `.uxml`, penataan gaya di `.uss`, dan binding logika di skrip C# `MonoBehaviour` melalui `rootVisualElement.Q<T>("element-name")`.
3. **Standar Styling:** Terapkan konvensi BEM pada kelas USS (`.card-element`, `.card-element__title`, `.card-element--selected`).
4. **Interaksi Drag & Drop:** Tangani via *Pointer Events* (`RegisterCallback<PointerDownEvent>`, `PointerMoveEvent`, `PointerUpEvent`) dan selalu *unregister* pada `OnDisable()`.

---

## 5. UNITY NEW INPUT SYSTEM

1. **LARANGAN INPUT LEGACY:** DILARANG menggunakan `Input.GetMouseButtonDown`, `Input.GetKey`, atau `Input.GetAxis`.
2. **Konfigurasi Terpusat:** Gunakan Action Map pada `PlayerControls.inputactions`.
3. **Event Callbacks:** Tangani input melalui event callback (`.performed`, `.canceled`) di `InputReader.cs`, bukan melalui polling frame di `Update()`.

---

## 6. OPTIMASI MEMORI & ANTI-GC SPIKE

1. **ZERO ALLOCATION DI UPDATE LOOP:**
   * DILARANG menggunakan kata kunci `new` (misal: `new List<T>()`) di dalam method `Update()` atau *hot paths*.
   * Alokasikan collection sekali saat inisialisasi (`Awake`), gunakan `.Clear()` saat digunakan ulang.
2. **LARANGAN STRING CONCATENATION DI UPDATE:**
   * DILARANG menggabungkan string (`"HP: " + hp`) setiap frame. Perbarui teks UI hanya saat event data berubah.
3. **LARANGAN PENCARIAN SLOW FRAME:**
   * Cache referensi `GetComponent<T>()` di `Awake()`.
   * DILARANG KERAS menggunakan `GameObject.Find()` atau `FindObjectOfType()`.
4. **OBJECT POOLING WAJIB:**
   * DILARANG melakukan `Instantiate` dan `Destroy` berulang untuk pop-up damage, proyektil, dan efek tebasan visual.
   * Gunakan `UnityEngine.Pool.ObjectPool<T>`.

---

## 7. ISOLASI DIREKTORI & WORKFLOW SANDBOX

1. **Peta Folder Kerja:**
   * `Assets/00_Core/`: Milik PM (Data SO, Event Hub, Input Actions, Scene Utama `MainBattleScene.unity`).
   * `Assets/01_Sandbox_Logic/`: Lingkup Junior Dev 1 (Grid 15×15, Turn FSM, Enemy AI).
   * `Assets/02_Sandbox_CardUI/`: Lingkup Junior Dev 2 (Card View, UXML/USS, Hand HUD).
   * `Assets/03_Sandbox_Data/`: Lingkup Member 3 (Aset `.asset` Card, Enemy, Consumables, Sprites).
   * `Assets/04_Sandbox_Environment/`: Lingkup Member 4 (Tilemaps, Audio SFX/BGM, Arena Lighting).
2. **Aturan Karantina:**
   * AI Agent dilarang memodifikasi file di luar sandbox yang sedang ditugaskan.
   * Dilarang menyunting `MainBattleScene.unity` kecuali atas tugas integrasi Project Manager.
   * Seluruh unit/fitur wajib dikemas dalam bentuk **Prefab** mandiri.

---

## 8. INTEGRASI GIT & PENGATURAN EDITOR

1. **Sinkronisasi Berkas `.meta`:** Setiap penambahan/modifikasi file wajib menyertakan file `.meta` terkait.
2. **Editor Settings:**
   * `Asset Serialization Mode` = **Force Text**
   * `Version Control Mode` = **Visible Meta Files**
