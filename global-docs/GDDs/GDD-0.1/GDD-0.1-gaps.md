# Analisis Kekurangan GDD-0 untuk MVP & Iterasi Berikutnya

> Dokumen ini mencatat pelacakan status gap pada Game Design Document 0.
> Dokumen ini dibagi menjadi dua bagian utama:
> - **BAGIAN 1: Gap yang Belum Selesai / Membutuhkan Keputusan** (Berada di atas, dikelompokkan per kategori & PIC).
> - **BAGIAN 2: Gap yang Sudah Selesai / Terjawab** (Berada di bawah, sebagai arsip keputusan rapat).

---

# BAGIAN 1: Gap yang Belum Selesai (Membutuhkan Keputusan & Penugasan Tim)

## 1. Kartu & Deck Starter
- **Daftar kartu starter konkret belum ada.**
  - **Kondisi Saat Ini:** Starter deck pemain berisi 15 kartu yang bersumber dari **Buku Sakti (*Grimoire*)** bawaan karakter utama, terbagi ke 4 kategori universal (Movement, Offense, Defense, Status Modifier). Belum ada daftar kartu konkret lengkap dengan nama kartu, efek spesifik, jangkauan (range), dan area efek (AoE).
  - **Tindak Lanjut (PIC: Seluruh Anggota Tim):** Masing-masing anggota tim membuat dan menyetor variasi kartu untuk kemudian dipilih/voting menjadi pool kartu dan 15 kartu starter buku sakti.
  > **Contoh Referensi:** Di *Slay the Spire*, Ironclad memulai dengan starter deck 10 kartu konkret: 5× "Strike" (6 damage), 4× "Defend" (5 block), dan 1× "Bash" (8 damage + Vulnerable). Untuk MVP ini, perlu didefinisikan 15 kartu starter konkret.

---

## 2. Sistem & Statistik Musuh
- **Statistik konkret tipe musuh belum ditentukan.**
  - **Kondisi Saat Ini:** Kategori musuh (Ranged, Melee, Support) dan hierarki (Minion, Regular, Elite) sudah diputuskan. Namun data angka spesifik per unit belum didefinisikan (HP, base damage, pola pergerakan di grid).
  - **Tindak Lanjut:** Definisikan minimal 3 tipe musuh biasa dan 1 boss/mini-boss untuk MVP lengkap dengan statistik angka dasarnya.
  > **Contoh Referensi:** Di *Slay the Spire*, musuh Act 1 memiliki angka pasti: "Jaw Worm" (44 HP, 12 damage / buff), "Cultist" (50 HP, ritual scaling damage).
- **Detail teknis jangkauan serangan Melee vs Ranged.**
  - **Kondisi Saat Ini:** Konsep tipe serangan sudah ada, namun batasan tile konkret (apakah Melee = tepat 1 tile, Ranged = 3-5 tile) serta aturan *line of sight* (apakah tembakan ranged terhalang karakter/obstacle lain) belum dirumuskan.

---

## 3. Combat Math, HP & Status Effects
- **Formula damage matematika final belum disusun.**
  - **Kondisi Saat Ini:** Formula kasar akan dibuat oleh pembuat kartu masing-masing sebelum voting. Setelah kartu terpilih, formula akan diformulasikan ke dalam model matematika baku.
  > **Contoh Referensi:** Di *Slay the Spire*, formula bakunya: `Damage Output = Base Damage + Strength - Enemy Block`.
- **Angka konkret base HP awal pemain belum ditentukan.**
  - **Kondisi Saat Ini:** HP menggunakan format angka dan health bar persentase (fixed per run, dapat bertambah via efek kartu stage). Angka nominal HP awal (misal: 50, 80, atau 100) menunggu keseimbangan rata-rata damage musuh.
- **Kategorisasi tipe damage & elemental.**
  - **Kondisi Saat Ini:** Akan ada diferensiasi tipe damage, namun pembatasan dan kategorisasinya difinalisasi setelah seluruh kartu disetor agar tidak membatasi kreativitas di awal.
- **Daftar dan durasi spesifik Status Effects.**
  - **Kondisi Saat Ini:** Status Modifier (stun, slow, armor break, attack up) akan menempel pada kartu, namun detail durasi (berapa turn) dan interaksinya terhadap intent musuh belum distandarisasi.

---

## 4. Consumable Items
- **Daftar item consumable konkret belum ada.**
  - **Kondisi Saat Ini:** Slot dibatasi maksimal 3 item (didapat dari random event di rute map). Belum ada daftar item konkret beserta nama dan efeknya.
  - **Tindak Lanjut:** Tentukan minimal 2-3 item consumable untuk MVP (contoh: Healing Potion, Damage Potion, Reposition/Smoke Potion).

---

## 5. UI / UX Flow & HUD
- **Screen Flow & State Diagram belum digambar.**
  - **Kondisi Saat Ini:** Alur layar dari Main Menu → Route/Map → Battle Screen → Reward Screen → Game Over / Victory belum dipetakan dalam diagram alur layar.
  - **Tindak Lanjut (PIC: Game Designer):** Merumuskan flow state diagram untuk memandu tim programmer dalam menyusun struktur scene Unity.
- **Detail skema kontrol input.**
  - **Kondisi Saat Ini:** Targeting menggunakan klik & drag mouse. Detail input shortcut keyboard (misal: angka 1-5 untuk kartu, tombol spasi untuk konfirmasi) belum didokumentasikan.
  - **Tindak Lanjut (PIC: Game Designer):** Menyusun daftar skema kontrol lengkap.
- **Daftar spesifikasi elemen HUD (Wireframe).**
  - **Kondisi Saat Ini:** Posisi dasar kartu (tengah bawah) dan intent musuh (atas kepala) sudah ada, namun layout posisi HP bar, counter draw/discard pile, indikator buff/debuff, dan tombol aksi lainnya belum dibuat wireframe-nya.
  - **Tindak Lanjut (PIC: Game Designer):** Menyusun mockup/wireframe HUD combat.

---

## 6. Visual Art & Assets Produksi
- **Produksi Aset Visual & Sprite Sheet (Ongoing):**
  - **Kondisi Saat Ini:** Arah visual sudah jelas (Palet gelap + aksen kuning-oranye, Menara Babel perpustakaan ke Mesopotamia, referensi *Signalis* & *Dead Cells*). Yang masih perlu dibuat dan disiapkan adalah aset visual konkret:
    - Desain environment/tileset arena 15×15 (lantai perpustakaan/ruin).
    - Concept art / sprite sheet karakter utama (pembawa buku), 3 jenis musuh, dan boss.
    - Template grafis kartu (layout ilustrasi, border tipe kartu, teks efek).
    - Panduan aset VFX (efek serang, hit flash, dodge, floating damage number).
  - **Tindak Lanjut (PIC: Visual Artist):** Memproduksi aset-aset visual berdasarkan moodboard dan panduan yang disepakati.

---

## 7. Audio - Produksi Aset Audio
- **Produksi File Audio BGM & SFX (Ongoing):**
  - **Kondisi Saat Ini:** Daftar penempatan BGM dan SFX sudah lengkap dan terdefinisi (lihat Bagian 2 Section 11). Yang perlu dikerjakan adalah produksi file audio (*composing & sound design*).
  - **Tindak Lanjut (PIC: Audio Designer):** Memproduksi file track BGM dan SFX sesuai dengan daftar penempatan dan tema atmosfer *dark / mystical / ambient* yang telah disepakati.

---

## 8. Narasi & Karakter
- **Nama final karakter utama.**
  - **Kondisi Saat Ini:** Karakter diidentifikasi sebagai pembawa buku. Nama final menunggu voting dari usulan masing-masing anggota tim (1 usulan per orang).
- **Scope konten narasi untuk MVP.**
  - **Kondisi Saat Ini:** Cerita disampaikan lewat environmental storytelling (interaksi dengan objek di dalam game). Perlu diputuskan seberapa banyak teks/lore objek yang dimasukkan ke dalam MVP 1 map/wave.
  - **Tindak Lanjut (PIC: Game Designer):** Menulis teks narasi interaksi lingkungan untuk kebutuhan MVP.

---

## 9. Scope Eksplisit MVP
- **Batasan numerik MVP belum dikunci:**
  - Jumlah tipe musuh di MVP pool (Rekomendasi: 3 kroco/regular + 1 boss).
  - Jumlah kartu unik di MVP pool (Rekomendasi: 15-20 kartu unik).
  - Jumlah gelombang per sesi MVP (Rekomendasi: 5-8 wave + 1 boss fight).

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

## 3. Kartu & Cost System
- ✅ **Tipe Kartu:** 4 tipe universal (dapat dipakai pemain maupun musuh):
  1. *Movement:* Manipulasi posisi (dash, teleport).
  2. *Offense:* Memberikan damage / melemahkan musuh (single target, AoE, DoT).
  3. *Defense:* Menjaga HP (shield, armor boost, regen).
  4. *Status Modifier:* Menempelkan buff/debuff ke kartu lain (attack up, stun, slow, armor break).
- ✅ **Cost System:** **1 Turn = 1 Kartu**.
- ✅ **Aturan Giliran:** Pemain **wajib memainkan 1 kartu** per turn (tidak boleh skip turn).
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
- ✅ **Urutan Resolusi Giliran Musuh:** Bersifat **totally random** (satu per satu secara bergiliran, bukan serentak).

## 8. Penyampaian Narasi
- ✅ **Metode Storytelling:** Cerita di-*deliver* melalui **lingkungan** (*environmental storytelling*) saat pemain berinteraksi dengan benda-benda di dalam game.
- ✅ **Ciri Visual Karakter:** Karakter utama membawa **buku** sebagai identitas visual utama.

## 9. Visual Art Direction, Setting & Ambience
- ✅ **Latar Tempat & Ambience:** Dunia hancur penuh keputusasaan dengan atmosfer *"gelap-gelap ceria"* yang dinamis (referensi: *Dead Cells*).
- ✅ **Setting Menara Babel:** Menara sebagai arsip seluruh pengetahuan dunia berisi tumpukan buku/perpustakaan. Lantai awal rapi, semakin ke atas strukturnya semakin kacau & terdistorsi.
- ✅ **Tema Visual & Arsitektur:** Transisi dari *Medieval Dark Fantasy* (lantai awal) bercampur dan bermuara ke *Ancient Mesopotamia* (lantai atas).
- ✅ **Palet Warna:** Palet dominan gelap (*dark mood*) dengan warna kontras **kuning-oranye (*yellow-orange*)** sebagai warna aksen utama.
- ✅ **Referensi Art Style:** Menggabungkan *Isometric Pixel Art* (referensi: *Arco*) dan nuansa *dark retro-futuristic/anime fantasy* (referensi: *Signalis* & moodboard Pinterest).

## 10. Sistem Deck Musuh Berbasis Senjata & Armor
- ✅ **Sistem Classless Musuh (Referensi: *Albion Online*):** Musuh tidak memiliki *fixed class*. Komposisi deck musuh ditentukan oleh kombinasi senjata dan armor yang mereka pakai (misal: pedang/tameng untuk melee defense, busur untuk ranged, tongkat/jubah untuk support/AoE). Sedangkan pemain (*Main Character*) menggunakan senjata **Buku Sakti (*Grimoire*)**.

## 11. Audio Direction (Daftar Penempatan BGM & SFX)
- ✅ **Daftar Penempatan BGM (12 Spot):**
  1. Main Menu / Options Menu
  2. Memilih Deck Kartu / Pre-battle
  3. Map (Memilih Jalur Stage/Event) / Masuk Dungeon
  4. Battle Theme (Normal) [Bervariasi]
  5. Boss Battle Theme [Bervariasi]
  6. Victory
  7. Defeat / Game Over
  8. Menu Deck Rebuilding
  9. Mysterious / Event Stage “?”
  10. Shop
  11. Inventory
  12. End Game Credit
- ✅ **Daftar Penempatan SFX (25 Kategori):**
  - *UI & Navigasi:* Cursor Hover, Cursor Click (Main Menu, In-game/Battle/Event, Shop termasuk beli item, Inventory geser/scroll/ganti halaman).
  - *Sistem Kartu:* Hover kartu, klik & drag kartu, draw card, shuffle/reshuffle, discard, efek pemakaian kartu (attack/defend/movement/status effect).
  - *Combat & Interaksi:* Intention serangan musuh, karakter/musuh menyerang, terkena damage, karakter/musuh mati, status effect, benturan/collision, buka floor baru, cue sebelum battle, victory celebration sound, sound bush/semak, dialog, suara musuh, item usage, upgrade/enhance, buka/tutup peti reward.
