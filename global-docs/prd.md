# Product Requirements Document (PRD)

## Apa itu PRD
Product Requirements Document (PRD) adalah dokumen komprehensif yang menjadi panduan mutlak bagi **AI Agent** dalam membangun aplikasi atau proyek perangkat lunak ini. Dokumen ini menjelaskan secara rinci fungsionalitas, fitur, tujuan, dan batasan dari aplikasi yang akan dikembangkan.

Dalam pendekatan *Vibe Coding* dimana penulisan kode dan pengembangan dilakukan secara otonom atau semi-otonom oleh AI, PRD ini berfungsi sebagai instruksi utama (*master prompt*) dan sumber kebenaran tunggal (*single source of truth*). Dokumen ini mendefinisikan dengan jelas **apa** aplikasi yang harus dibangun oleh AI, **bagaimana perilaku yang diharapkan** dari aplikasi tersebut, serta **apa saja kriteria kesuksesannya** (*acceptance criteria*). Melalui dokumen ini, AI Agent dapat memahami *big picture* dan spesifikasi produk secara menyeluruh sebelum menyusun rancangan teknis (*System Design*) maupun mengeksekusi penulisan kode.

Secara rinci, sebuah dokumen PRD wajib memuat komponen-komponen berikut:
*   **Tujuan & Latar Belakang (*Objective & Background*):** Penjelasan mengenai masalah utama yang ingin dipecahkan, deskripsi target pengguna (*user personas*), dan alasan logis mengapa produk/fitur ini penting untuk dikembangkan.
*   **Alur Pengguna (*User Flow / User Journey*):** Narasi atau urutan langkah-langkah *end-to-end* yang menggambarkan cara pengguna berinteraksi dengan ekosistem (contoh: alur dari registrasi hingga menyelesaikan sebuah transaksi).
    *   **Kewajiban Visualisasi PlantUML:** Anda **WAJIB** membuat visualisasi alur pengalaman pengguna ini menggunakan PlantUML. Simpan kodenya di `global-docs/diagrams/user-journey.puml` lalu buat tautan rujukannya di sini.
*   **Interaksi Sistem Global (Use Case & Activity Diagram):** Khusus jika sistem ini memuat aplikasi bertipe *Web*, *Mobile*, atau *Desktop*, Anda **WAJIB** membuat *Use Case Diagram* atau *Activity Diagram* berbasis PlantUML. Jika aplikasi ini berupa *Game*, Anda **WAJIB** membuat *Macro State Diagram* (misal: *Menu State*, *Play State*) menggunakan PlantUML. Simpan kode diagram tersebut sebagai `.puml` di dalam folder `global-docs/diagrams/` secara eksplisit dan cantumkan tautannya di bagian ini.
*   **Kebutuhan Fungsional (*Functional Requirements*):** Daftar rinci berisi aksi-aksi dan fungsionalitas sistem yang wajib ada.
*   **Kebutuhan Non-Fungsional (*Non-Functional Requirements*):** Ekspektasi yang mengatur performa, keamanan, stabilitas, waktu respons sistem, hingga *support* *platform*.
*   **Kriteria Penerimaan (*Acceptance Criteria*):** Syarat dan batasan mutlak yang harus terpenuhi agar sebuah fitur divalidasi dan dianggap selesai.
*   **Asumsi & Keterbatasan (*Assumptions & Constraints*):** Prediksi kondisi yang mendasari pengembangan dan batasan sistem/bisnis.
*   **Di Luar Cakupan (*Out of Scope*):** Daftar eksplisit mengenai fungsi atau fitur yang tidak dikerjakan pada fase/iterasi saat ini.
*   **Peta Jalan Fase (Milestone/Phase Breakdown):** Pembagian target rilis fitur ke dalam beberapa fase berurut.

---

## 🏛️ PILOT GAME (TUBBIES STUDIO)

### 1. Tujuan & Latar Belakang (*Objective & Background*)
* **Visi Produk:** *Pilot Game* adalah game *Roguelike Deckbuilder* taktis berbasis petak arena 15×15 bertema mitologi Mesopotamia kuno (*Dark Ancient Mesopotamian Fantasy*).
* **Core Fantasy:** Pemain berperan sebagai **Nabu**, seorang juru tulis pemberontak yang membawa Buku Sakti (*Grimoire*) berisi lempengan huruf paku kuno (*Cuneiform*), menembus lantai-lantai terkutuk Menara Babel untuk meruntuhkan kekuasaan para penguasa tiran.
* **Target Pemain (*User Persona*):**
  * Penggemar game *turn-based strategy* dan *deckbuilder* (*Slay the Spire*, *Into the Breach*, *Arco*).
  * Pemain yang menyukai teka-teki taktis berisiko tinggi dengan mekanisme penghindaran posisi (*positioning & dodge*).

---

### 2. Alur Pengguna & Visualisasi Arsitektur
* **Alur Perjalanan Pengguna:** Pemain memulai ekspedisi di Markas (*Sanctuary*), menyusun 15 kartu starter, memilih rute peta Babel, memasuki arena taktis 15×15, mengalahkan musuh melalui 4 fase ronde, melakukan *drafting* 1 dari 3 kartu baru, hingga mengalahkan Boss Chapter atau gugur (*permadeath*) untuk menukar poin upgrade permanen.
* **Visualisasi User Journey:** [user-journey.puml](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/global-docs/diagrams/user-journey.puml)
* **Visualisasi Macro Game State:** [macro-state.puml](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/global-docs/diagrams/macro-state.puml)

---

### 3. Kebutuhan Fungsional (*Functional Requirements*)

#### A. Sistem Pertempuran Taktis 4-Fase (Micro Loop)
1. **Fase 1 (Intent Phase):** Sistem AI musuh secara serentak menghitung aksi dan menampilkan *danger telegraph* (ubin merah menyala) serta badge niat di atas kepala unit.
2. **Fase 2 (Player Action Phase):** Pemain wajib memainkan tepat 1 kartu per ronde via *Drag-and-Drop* ke arena (tanpa opsi *skip turn*). Pemain juga dapat menggunakan 1 *consumable item* sebagai aksi bebas (*free action*).
3. **Fase 3 (Enemy Action Phase):** Seluruh musuh aktif mengeksekusi serangan dalam urutan acak (*Totally Random Order*). Jika pemain telah berpindah dari ubin target sebelum musuh menyerang, serangan dinyatakan meleset (*Natural Dodge*).
4. **Fase 4 (Round Reset Phase):** Sistem menarik kartu baru ke tangan hingga berjumlah 5 kartu, membersihkan seluruh ubin highlight, dan mengecek kondisi kemenangan/kekalahan wave.

#### B. Sistem Arena & Interaksi Grid 15×15
1. **Grid Matrix:** Arena berdimensi tetap 15×15 petak dengan koordinat integer `(0, 0)` hingga `(14, 14)`.
2. **Elemen Lingkungan Khusus:**
   * **Semak Mesopotamia (*Stealth Bush*):** Petak semak 1-ubin yang memberikan status tak terlihat (*Stealth*) selama 1 giliran jika pemain berdiri di dalamnya.
   * **Pilar Batu (*Obstacle Pillar*):** Menghalangi langkah unit dan memblokir lintasan tembakan proyektil garis lurus.
   * **Jebakan Lantai (*Hazard Spike*):** Memberikan damage langsung jika unit didorong atau melangkah ke atasnya.

#### C. Sistem Kartu & Deckbuilding (Macro Loop)
1. **Struktur Buku Sakti (*Deck Assembly*):** Total 15 kartu (5 di tangan, 10 di *draw pile*). Jika *draw pile* habis, *discard pile* otomatis di-kocok ulang (*reshuffle*).
2. **Post-Wave Drafting:** Setelah membersihkan satu node pertarungan, pemain memilih 1 dari 3 kartu baru yang ditawarkan untuk dimasukkan ke dalam deck ekspedisi.

#### D. Sistem Persistensi Roguelike (Meta Loop)
1. **Permadeath & Essence Conversion:** Jika HP Nabu mencapai 0, ekspedisi berakhir. Seluruh poin yang dikumpulkan selama run dikonversi menjadi *Sanctuary Currency*.
2. **Sanctuary Upgrades:** Pemain dapat membelanjakan mata uang untuk membuka stat permanen, slot relic, dan kartu baru di pohon talenta markas.

---

### 4. Kebutuhan Non-Fungsional (*Non-Functional Requirements*)
* **Platform Target:** PC Standalone (Windows & macOS via Steam).
* **Performa Rendering:** Minimum 60 FPS stabil pada resolusi standar 1080p (1920×1080) menggunakan Universal Render Pipeline 2D (URP 2D).
* **Zero GC Allocation di Hot Paths:** Tidak ada alokasi memori `new` di dalam loop `Update()` pertempuran untuk mencegah *GC spike/stutter*.
* **Penyimpanan Data Lokal:** Serialisasi data progres menggunakan format JSON terenkripsi lokal.

---

### 5. Kriteria Penerimaan (*Acceptance Criteria - MVP Phase*)
* [x] Siklus 4-fase pertempuran (Intent ➡️ Player ➡️ Enemy ➡️ Reset) berjalan tanpa deadlock atau desinkronisasi visual.
* [x] Protagonis Nabu dapat memainkan kartu Attack (damage ke musuh) dan Mobility (pindah ubin) secara presisi.
* [x] AI musuh sukses menampilkan telegraph merah dan mengeksekusi serangan sesuai pola kotak ubin.
* [x] Mekanik *Natural Dodge* terbukti berfungsi (Pemain yang geser keluar ubin merah tidak terkena damage).
* [x] Hand UI kartu responsif terhadap input mouse drag-and-drop.

---

### 6. Asumsi & Keterbatasan (*Assumptions & Constraints*)
* **Arsitektur Wajib:** OOP Klasik + ScriptableObjects + UI Toolkit (Dilarang menggunakan Pure ECS / DOTS).
* **Format Input:** Mouse & Keyboard dengan New Input System Unity.
* **Bahasa Kode:** C# dengan Zero-Comment Policy pada source code fungsional.

---

### 7. Di Luar Cakupan (*Out of Scope - Iterasi Pertama*)
* Fitur Multiplayer / Online Co-op / PvP.
* Dukungan platform Mobile (Android/iOS) dan Konsol.
* Mode cerita penuh dengan cutscene animasi 3D sinematik.

---

### 8. Peta Jalan Rilis (*Milestone Breakdown*)
* **Fase 1 (MVP Vertical Slice):** Single Arena 15×15, Protagonis Nabu, 3 Tipe Musuh Humanoid, 14 Kartu Aksi Tempur, 4-Fase Turn FSM.
* **Fase 2 (Exploration Expansion):** Peta Cabang Menara Babel (Combat, '?' Events, Campfire), 3 Checkpoints, Relic System.
* **Fase 3 (Meta Progression & Boss Climax):** Chapter Boss Encounter, Sanctuary Talent Tree, Ascension Difficulty Tiers.
