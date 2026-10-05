# Analisis Kekurangan GDD-0 untuk MVP & Iterasi Berikutnya

> Dokumen ini mencatat pelacakan status gap pada Game Design Document 0.
> Dokumen ini dibagi menjadi dua bagian utama:
> - **BAGIAN 1: Gap yang Belum Selesai / Membutuhkan Keputusan** (Berada di atas, dikelompokkan per kategori & PIC).
> - **BAGIAN 2: Gap yang Sudah Selesai / Terjawab** (Berada di bawah, sebagai arsip keputusan rapat).

---

# BAGIAN 1: Gap yang Belum Selesai (Membutuhkan Keputusan & Penugasan Tim)

## 1. Combat HP Pemain & Scaling
- **Angka konkret base HP awal pemain belum dikunci.**
  - **Kondisi Saat Ini:** Formula kalkulasi damage, tipe status, dan seluruh 49 kartu sudah selesai dirumuskan, namun angka nominal Base Max HP awal Nabu (misal: 60, 80, atau 100 HP) belum dikunci secara numerik untuk mengimbangi total rata-rata damage musuh dan efek pemulihan kartu/item.
  - **Tindak Lanjut:** Kunci angka nominal Max HP pemain Nabu untuk build MVP.

---

## 2. Sistem & Statistik Musuh
- **Integrasi formal 9 Roster Musuh & Boss ke GDD.**
  - **Kondisi Saat Ini:** Tim telah merumuskan 9 Musuh Terpilih (*Selected Enemies*) di dokumen desain tim, namun detail integrasi data ke dokumen GDD pusat dan spesifikasi teknis Boss/Mini-Boss final wave belum dikunci di GDD.
  - **Tindak Lanjut:** Sinkronisasi data statistik 9 musuh dan mekanisme Boss Wave ke dalam dokumen GDD induk.
- **Detail teknis Line of Sight (LoS) & Interaksi Proyektil dengan Obstacle.**
  - **Kondisi Saat Ini:** Konsep tipe serangan Melee (1 tile), Ranged (2-5 tile, hingga 7 tile) sudah ada di desain kartu dan musuh, namun aturan baku apakah tembakan garis lurus (*Linear Line*) terhalang oleh unit lain atau pilar batu (*Earthen Bulwark*) vs proyektil lob/parabola perlu ditegaskan di engine.

---

## 3. Consumable Items
- **Finalisasi Pool Item Consumable & Drop Mechanism.**
  - **Kondisi Saat Ini:** Slot dibatasi maksimal 3 item (Free Action). Draft 5 consumable sudah dibuat (Chained Harpoon Hook, Euphrates Smoke Grenade, Scroll of Instant Blink, Bronze Caltrops, Elixir of Life), namun belum difinalisasi drop rate dan integrasinya ke dokumen GDD inti.
  - **Tindak Lanjut:** Finalisasi 3-5 item consumable untuk pool MVP dan tentukan cara perolehannya per wave.

---

## 4. UI / UX Flow & HUD
- **Screen Flow & State Diagram belum digambar.**
  - **Kondisi Saat Ini:** Alur layar dari Main Menu → Route/Map → Battle Screen → Reward Screen → Game Over / Victory belum dipetakan dalam diagram alur layar.
  - **Tindak Lanjut (PIC: Game Designer):** Merumuskan flow state diagram untuk memandu tim programmer dalam menyusun struktur scene Unity.
- **Detail skema kontrol input.**
  - **Kondisi Saat Ini:** Targeting menggunakan klik & drag mouse. Detail input shortcut keyboard (misal: angka 1-5 untuk kartu di tangan, tombol spasi untuk konfirmasi/akhiri fase, tombol Esc/drag balik untuk batal, klik kanan untuk inspect musuh/status) belum didokumentasikan secara formal.
  - **Tindak Lanjut (PIC: Game Designer):** Menyusun daftar skema kontrol lengkap.
- **Daftar spesifikasi elemen HUD (Wireframe).**
  - **Kondisi Saat Ini:** Posisi dasar kartu (tengah bawah) dan intent musuh (atas kepala) sudah ada, namun layout posisi HP bar, counter draw/discard pile, indikator buff/debuff, dan tombol aksi lainnya belum dibuat wireframe-nya.
  - **Tindak Lanjut (PIC: Game Designer):** Menyusun mockup/wireframe HUD combat.

---

## 5. Visual Art & Assets Produksi
- **Produksi Aset Visual & Sprite Sheet (Ongoing):**
  - **Kondisi Saat Ini:** Arah visual sudah jelas (Palet gelap + aksen kuning-oranye, Menara Babel perpustakaan ke Mesopotamia, referensi *Signalis* & *Dead Cells*). Yang masih perlu dibuat dan disiapkan adalah aset visual konkret:
    - Desain environment/tileset arena 15×15 (lantai perpustakaan/ruin).
    - Concept art / sprite sheet karakter Nabu, 3 jenis musuh dasar, dan boss.
    - Template grafis kartu (layout ilustrasi, border tipe kartu, teks efek).
    - Panduan aset VFX (efek serang, hit flash, dodge, floating damage number).
  - **Tindak Lanjut (PIC: Visual Artist):** Memproduksi aset-aset visual berdasarkan moodboard dan panduan yang disepakati.

---

## 6. Audio - Produksi Aset Audio
- **Produksi File Audio BGM & SFX (Ongoing):**
  - **Kondisi Saat Ini:** Daftar penempatan BGM dan SFX sudah lengkap dan terdefinisi (lihat Bagian 2 Section 11). Yang perlu dikerjakan adalah produksi file audio (*composing & sound design*).
  - **Tindak Lanjut (PIC: Audio Designer):** Memproduksi file track BGM dan SFX sesuai dengan daftar penempatan dan tema atmosfer *dark / mystical / ambient* yang telah disepakati.

---

## 7. Narasi & Interaksi Lingkungan
- **Scope konten narasi untuk MVP.**
  - **Kondisi Saat Ini:** Karakter utama telah ditetapkan bernama **Nabu** (Sang Juru Tulis). Cerita disampaikan lewat environmental storytelling (interaksi dengan objek di dalam game). Perlu diputuskan seberapa banyak teks/lore objek yang dimasukkan ke dalam MVP 1 map/wave.
  - **Tindak Lanjut (PIC: Game Designer):** Menulis teks narasi interaksi lingkungan untuk kebutuhan MVP.

---

## 8. Mekanik Drafting & Pergantian Kartu (Deck Replacement)
- **Aturan Drafting Reward saat Deck Penuh 15 Kartu.**
  - **Kondisi Saat Ini:** Reward pasca-wave memberikan pilihan 1 dari 3 kartu baru, sementara deck dibatasi tepat 15 kartu. Belum ditentukan secara teknis apakah pemain wajib membuang/menukar (*replace*) 1 kartu lama, atau kartu baru masuk ke cadangan (*sideboard/reserve*), atau pemain diperbolehkan *skip reward*.
  - **Tindak Lanjut:** Tentukan aturan pergantian deck pasca drafting.

---

## 9. Scope Eksplisit MVP
- **Batasan numerik MVP belum dikunci penuh:**
  - Jumlah tipe musuh di MVP pool (Rekomendasi: 3 minion/regular + 1 elite/boss dari 9 selected enemies).
  - Jumlah kartu unik di MVP pool (Rekomendasi: 14 kartu starter Nabu + 6-10 kartu reward draft).
  - Jumlah gelombang per sesi MVP (Rekomendasi: 3-5 wave + 1 boss fight).

---

## 10. Kebutuhan Iterasi Berikutnya (Post-MVP)
*Fitur-fitur ini telah diputuskan untuk ditunda pengerjaannya setelah MVP 1 map statis wave selesai:*
- **Variasi Layout & Bentuk Arena:** Arena non-persegi (bentuk L, lorong sempit, variasi hazard dinamis).
- **Scaling Musuh Antar Chapter/Act:** Mekanik eskalasi stat atau modifier musuh per chapter.
- **Skill Combo & Bonus Action:** Mekanik sinergi kombo antar kartu yang menghasilkan bonus action.
- **Friendly Fire:** Resiko damage terhadap diri sendiri pada serangan AoE luas.
- **Shop & Sistem Ekonomi In-Run:** Toko dalam run, mata uang koin/gold, dan relik pasif.
- **Item Penambah Max HP Permanen:** Relik/consumable khusus penambah kapasitas max HP.
- **Aturan Penggantian Deck di Atas 15 Kartu:** Mekanik replace/tukar kartu lama saat drafting reward.
- **Struktur Run Roguelike Utuh:** Map bercabang multi-node (*Slay the Spire style*) dan eksplorasi overworld penuh.
- **Meta-Progression & Checkpoint:** Checkpoint system berbayar, mata uang meta-progression, ascension difficulty level.
- **Audio Visual Naratif Mendalam:** Ilusi visual wujud malaikat di combat, Voice Over/narator, dan implementasi 3 cabang ending cerita.

---
---

# BAGIAN 2: Gap yang Sudah Selesai / Terjawab (Arsip Keputusan)

## 1. Arena & Grid
- ✅ **Dimensi Grid:** Arena berukuran **15×15 tile**. Setiap karakter (pemain & musuh) menempati **1 tile**.
- ✅ **Obstacle & Terrain:** Obstacle dan invisible wall dapat ditempatkan di dalam arena 15×15 untuk memperkecil arena aktif atau menambah variasi mekanik.
- ✅ **Spawn Point:** Posisi awal pemain **fixed ditentukan oleh sistem** (tidak diacak).
- ✅ **Mekanik Bush (Semak / Stealth):** Lebar semak 1 tile (panjang bervariasi). Karakter di dalam bush tidak bisa di-target oleh musuh kecuali musuh berada di jarak minimal 2 tile. Mekanik berlaku simetris untuk pemain dan musuh.

## 2. Sistem Pergerakan & Collision
- ✅ **Arah Pergerakan:** Mendukung **8 arah** (vertikal, horizontal, dan diagonal).
- ✅ **Sistem Gerak via Kartu:** Tidak ada gerakan gratis non-kartu; semua pergerakan (posisi/dodge) menggunakan kartu tipe *Movement*.
- ✅ **Variasi Jarak Gerak:** Jarak sangat beragam, mulai dari 1 tile (lambat) hingga berpindah bebas ke mana saja (teleport).
- ✅ **Aturan Collision (Tabrakan):** Jika bergerak non-teleport dan jalurnya melewati tile musuh, pemain akan tertabrak dan berhenti tepat 1 tile di depan musuh.

## 3. Kartu, Deck Starter & Master Card Library
- ✅ **Master Card Library (49 Kartu Lengkap):** Seluruh 49 kartu telah didefinisikan secara baku dalam 6 arketipe taktis:
  1. *Core Tactical Library (14 Kartu Inti Nabu - `CARD-001` s/d `CARD-014`):* Teleport, Decoy, Frost, Heavy Rain, Fog, Storm, Clear Weather, Skeleton Army, Throwing Blade, Sand Burial, Clone, Dash, Super Punch, Gravity Lift.
  2. *Combat, Aggro & Status Debuff (9 Kartu - `CARD-015` s/d `CARD-023`):* Thorn, Roar, Offering, Anger, Slash, Leg Sweep, Whip, Death Stare, Immolate.
  3. *Spatial, Physics & Stance Mastery (5 Kartu - `CARD-024` s/d `CARD-028`):* Brace, Scaffold Leap, Collapsing Archway, Echo of the First Tongue, Crumbling Foundation.
  4. *Tactical Weaponry & Precision (3 Kartu - `CARD-029` s/d `CARD-031`):* Tangled Overgrowth, Piercing Shoot, Serrated Dagger.
  5. *Grimoire, Occult & Combo Synergy (8 Kartu - `CARD-032` s/d `CARD-039`):* Counter Attack, Energy Slash, Shadow Step, Hex, Page Fetcher, Blood Sacrifice, Star Burst Stream, Foolish Archiver.
  6. *Elemental Magic, Hazards & Traps (10 Kartu - `CARD-040` s/d `CARD-049`):* Static Rune, Chain Lightning, Earthen Bulwark, Rolling Boulder, Machete Cleave, Heavy Crossbow, Campfire Spark, Ember Trap, Purifying Splash, Aqua Snare.
- ✅ **Tipe Aksi & Batasan Parameter:**
  - *ActionType:* `Attack`, `Defense`, `Movement`, `StatusModifier`, `Utility`.
  - *TargetArea:* `SingleTarget`, `LinearLine`, `RadiusArea`, `ConeArc`, `SelfOnly`, `GlobalAllEnemies`, `GroundTile`.
  - *PhaseRestriction:* `IntentPhase`, `PlayerPhase`, `RoundResetPhase`.
  - *Pembeda Jarak:* `CastRange` (jarak lempar caster 0 s/d 5 tile / global) vs `AoERadius` (luas sebaran efek: Single, 3x3, 5x5, Cone Arc, Linear Line, Global).
- ✅ **Cost System:** **1 Turn = 1 Kartu** (aksi utama wajib, tidak boleh skip turn). Item konsumsi berstatus *Free Action*.
- ✅ **Deck Size & Rarity:** Deck dibatasi **15 kartu** (5 di tangan, 10 di draw pile). Kartu tidak memiliki sistem rarity.
- ✅ **Reshuffle / Hand Overflow:** Jika draw pile habis, discard pile otomatis dikocok ulang menjadi draw pile baru tanpa penalti.

## 4. Sistem Musuh & AI
- ✅ **Deck Musuh:** Musuh bermain menggunakan kartu universal yang sama dengan pemain dan memiliki deck sendiri.
- ✅ **Tipe Serangan Musuh:** Terdiri dari 3 tipe (Ranged, Melee, Support) yang bermain secara tim/kombinasi sinergis.
- ✅ **Hierarki Musuh:** Terdiri dari 3 tingkatan sinergi & awareness:
  - *Minion (Kroco):* Sinergi minimal, tidak mengamati kondisi sekitar (misal tidak aware debuff pemain).
  - *Regular:* Sinergi menengah.
  - *Elite:* Sinergi tinggi, mengamati kondisi battlefield secara penuh.
- ✅ **Pola Perilaku AI Musuh:** Musuh berpikir mandiri (tidak terorganisir kelompok). Urutan gerak totally random berdasarkan jarak dan durasi serangan.
- ✅ **Jumlah Musuh per Wave:** Maksimal **5 musuh** pada arena terbuka (dihitung proporsional dengan luas movable tiles).

## 5. Enemy Intent & Telegraph
- ✅ **Format Visual Intent:** Ditampilkan secara jelas berupa ikon/indikator **di atas kepala masing-masing karakter**.
- ✅ **Waktu Generate Intent:** Seluruh niat aksi musuh di-generate dan ditampilkan **di awal turn sebelum pemain memilih aksi**.
- ✅ **Jumlah Intent:** **Tidak ada multi-intent** (1 intent per musuh per turn).

## 6. Skill Casting, Targeting & AoE
- ✅ **Mekanisme Targeting:** Manual via kontrol **klik dan drag kartu ke target** di arena (atau drag kembali ke deck untuk batal).
- ✅ **Area of Effect (AoE):** Luas area tergantung desain kartu masing-masing; jumlah maksimum tile terkena efek **tidak dibatasi**.

## 7. Turn Order & Resolusi Combat
- ✅ **Empat Fase Giliran (Combat Phases):**
  1. *`IntentPhase`:* Musuh generate intent; pemain dapat memainkan kartu instan berlabel IntentPhase.
  2. *`PlayerPhase`:* Pemain memainkan 1 kartu aksi utama (Attack, Defense, Movement, Trap, Summon).
  3. *`EnemyPhase`:* Musuh mengeksekusi niat aksinya secara individual acak.
  4. *`RoundResetPhase`:* Resolusi DoT (Burn, Bleed), pembersihan decoy, klon, reset sisa Shield, dan penyiapan ronde baru.
- ✅ **Urutan Resolusi Giliran Musuh:** Bersifat **totally random** (satu per satu secara bergiliran, bukan serentak).

## 8. Penyampaian Narasi & Karakter
- ✅ **Metode Storytelling:** Cerita di-*deliver* melalui **lingkungan** (*environmental storytelling*) saat pemain berinteraksi dengan benda-benda di dalam game.
- ✅ **Nama & Ciri Karakter Utama:** Karakter utama bernama **Nabu** (Sang Juru Tulis / The Scribe of the Archive), membawa **Buku Sakti (*Grimoire*)** sebagai senjata utama.

## 9. Visual Art Direction, Setting & Ambience
- ✅ **Latar Tempat & Ambience:** Dunia hancur penuh keputusasaan dengan atmosfer *"gelap-gelap ceria"* yang dinamis (referensi: *Dead Cells*).
- ✅ **Setting Menara Babel:** Menara sebagai arsip seluruh pengetahuan dunia berisi tumpukan buku/perpustakaan. Lantai awal rapi, semakin ke atas strukturnya semakin kacau & terdistorsi.
- ✅ **Tema Visual & Arsitektur:** Transisi dari *Medieval Dark Fantasy* (lantai awal) bercampur dan bermuara ke *Ancient Mesopotamia* (lantai atas).
- ✅ **Palet Warna:** Palet dominan gelap (*dark mood*) dengan warna kontras **kuning-oranye (*yellow-orange*)** sebagai warna aksen utama.
- ✅ **Referensi Art Style:** Menggabungkan *Isometric Pixel Art* (referensi: *Arco*) dan nuansa *dark retro-futuristic/anime fantasy* (referensi: *Signalis* & moodboard Pinterest).

## 10. Sistem Deck Musuh Berbasis Senjata & Armor
- ✅ **Sistem Classless Musuh (Referensi: *Albion Online*):** Musuh tidak memiliki *fixed class*. Komposisi deck musuh ditentukan oleh kombinasi senjata dan armor yang mereka pakai (misal: pedang/tameng untuk melee defense, busur untuk ranged, tongkat/jubah untuk support/AoE). Sedangkan Nabu menggunakan senjata **Buku Sakti (*Grimoire*)**.

## 11. Audio Direction (Daftar Penempatan BGM & SFX)
- ✅ **Daftar Penempatan BGM (12 Spot):** Main Menu, Pre-battle, Map/Dungeon, Normal Battle, Boss Battle, Victory, Defeat/Game Over, Deck Rebuilding, Mystery/Event, Shop, Inventory, End Game Credit.
- ✅ **Daftar Penempatan SFX (25 Kategori):** UI/Navigasi, Sistem Kartu (Hover, Drag, Draw, Shuffle, Discard, Use), Combat/Interaksi (Intent, Attack, Hit, Death, Status, Wall Collision, Start Horn, Bush, Grunt, Dialog, Item, Reward Chest).

## 12. Combat Math, Physics Collision & Status Effects
- ✅ **Pipeline Formula Damage Baku:**
  $$\text{Final Damage} = \Big[ (\text{Base Damage} + \text{Flat Modifiers}) \times (1 + \sum \text{Percentage Multipliers}) \Big] - \text{Target Shield}$$
  - *Flat Modifiers:* Hex ($+2\text{ Flat}$), Fracture ($+3\text{ Flat/stack}$), Empowered Buff.
  - *Percentage Multipliers (Aditif):* Strength ($+25\%$), Vulnerable Debuff ($+40\%$), Weak Debuff ($-25\%$).
  - *Shield Mitigation:* Shield menyerap damage terlebih dahulu sebelum HP. Hangus saat `RoundResetPhase`.
  - *Armor Piercing & Bleed:* Menembus Shield langsung memotong HP (*Direct to HP*).
- ✅ **Aturan Stacking & Status Effects Baku:**
  - *Refresh Duration:* Aplikasi status berulang me-refresh durasi ke durasi kartu baru.
  - *Stun & Diminishing Returns:* Stun/Freeze membatalkan 1 giliran + memberikan **1 ronde Stun Immunity Window** (anti-perma stun lock).
  - *Katalog Status Lengkap:* Freeze, Wet, Burn, Bleed, Immobilize, Vulnerable, Weak, Strength, Shielded, Stun, Resonance, Fracture, Hex.
- ✅ **Physics & Wall Slam Collision:**
  - Efek dorongan (*Knockback*) yang menabrak dinding, rintangan pilar, atau unit lain menghasilkan *Collision Halt*, **$+4\text{ Bonus Damage}$**, dan **`Stun` (1 turn)**.
