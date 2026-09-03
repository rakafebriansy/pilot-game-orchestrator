# Game Design Document (GDD)

## 1. Executive Summary
- **Judul Game:** Pilot Game (Working Title)
- **Genre:** Tactical Roguelike Deck-Builder
- **Platform:** Desktop (PC - Windows, macOS)
- **Distribusi Utama:** Steam
- **Target Audiens:** Penggemar game strategi, taktik posisi, pemain *roguelike*, dan pecinta *deck-builders*.
- **Konsep Inti:** Sebuah permainan strategi yang menggabungkan pertempuran taktis berbasis posisi di arena *isometric* dengan manajemen sumber daya kartu. Berfokus pada kemampuan pemain membaca niat musuh (*enemy intent*) untuk mengambil keputusan krusial: menyerang, bertahan, atau menghindar (*dodge*).

## 2. Game Overview
Pilot Game menawarkan pengalaman *turn-based* yang penuh perhitungan. Berbeda dengan *card game* tradisional di mana serangan hanya berdasar statistik, game ini sangat memperhitungkan posisi spasial pemain. Lingkungan disajikan dalam bentuk papan *grid* yang tidak kasat mata (*invisible grid*), dibalut dengan estetika *isometric* yang elegan dan bersih. Setiap aksi yang dilakukan pemain, sekecil apa pun, akan langsung memengaruhi arah dan keselamatan di akhir giliran.

## 3. Core Gameplay Loop
Siklus inti permainan dirancang untuk terus memicu ketegangan dan pemikiran strategis secara *looping* (gelombang/wave):
1. **Pre-Battle (Deck Assembly):** Pemain menyeleksi dan mempersiapkan *deck* berisi tepat 15 kartu sebelum menghadapi gelombang musuh.
2. **Exploration / Traversal:** Pemain bergerak bebas (*free movement*) di *overworld* dengan *invisible grid*, atau memilih rute melalui node/tile chapter (referensi: *Slay the Spire*). Saat menyentuh musuh atau memasuki ruangan encounter, game berpindah ke fase *Turn-Based Battle*. Transisi antar ruangan dibuat *seamless* (mulus).
3. **Combat Phase (Turn-based):**
   - **Start of Turn:** Niat musuh untuk giliran tersebut (telegraf arah serangan atau gerak) ditampilkan di layar. Pemain menarik maksimal 5 kartu dari *draw pile*.
   - **Player Phase:** Pemain mendapat **1 Aksi Utama** per giliran — pilih antara: berjalan otomatis mendekat ke target **ATAU** melancarkan serangan jarak dekat/jauh. Di samping itu, pemain mendapatkan kebebasan 1 ubin pergerakan gratis (*Free Move*), mampu menggunakan item konsumsi (*Free Action*), dan bisa menggunakan *Special Ability / Bonus Action* yang tidak memakan jatah aksi utama.
   - **Enemy Phase:** Musuh mengeksekusi niat mereka secara pasti. Jika pemain telah mengambil posisi di luar jangkauan (*dodge*), serangan musuh akan gagal secara natural.
4. **Post-Wave Progression:** Saat satu gelombang usai, pemain diberikan tiga opsi kartu baru. Pemain harus memilih satu (*drafting*) untuk memperkuat atau memodifikasi gaya main di gelombang selanjutnya.
5. **Death & Reset (Roguelike Cycle):** Jika pemain mati, run dimulai ulang dari awal. Namun, poin yang dikumpulkan dari run sebelumnya bisa digunakan untuk membeli *starter upgrade* permanen. Pertumbuhan kekuatan dibatasi agar karakter tidak *overpowered* di dalam run — kekuatan utama bertumpu pada *starter/base stat*.

## 4. Mechanics & Systems
### 4.1. Combat & Tactical System
- **Turn Actions vs Free Actions:** Mekanik manajemen poin aksi di mana kebebasan bergerak (sekali) dan pemakaian item darurat merupakan aksi gratis, memberi ruang kreativitas.
- **Enemy Intent:** Konsep telegraf visual 100% transparan. Pemain selalu diberi peringatan apa yang akan dilakukan musuh (baik itu musuh jarak jauh/*ranged* maupun jarak dekat/*melee*).
- **Skill Casting (Manual Targeting):** Kemampuan dari kartu tidak secara otomatis mengunci musuh (no auto-lock). Pemain secara manual harus mengarahkan proyektil/area efek ke target agar sukses.
- **Dodging (Evasion):** Bertahan bukan berarti menggunakan perisai, melainkan memposisikan ulang karakter (*repositioning*) dari kotak grid yang akan menjadi sasaran serangan.

### 4.2. Card & Deck System
- **Deck Limitations:** Terbatas hanya 15 kartu saat pertempuran berlangsung (maksimal 5 di tangan, 10 di *draw pile*).
- **Discard & Reshuffle:** Kartu yang telah dimainkan langsung terbuang ke tumpukan *discard*, memaksa sirkulasi penggunaan kartu.
- **Rewards:** Mekanisme "Pilih 1 dari 3" seusai pertarungan agar dek bisa dikustomisasi secara progresif (elemen roguelike).
- **Skill Combo:** Dimungkinkan adanya interaksi kombo antar skill/kartu yang saling memperkuat jika dimainkan dalam urutan atau kombinasi tertentu.
- **Blind/Cursed Abilities:** Pemain akan dihadapkan pada pilihan *ability* acak yang bersifat misteri (efeknya baru diketahui setelah diambil). Ability ini bersifat *High Risk - High Reward*, memberikan skill tambahan sekaligus memberikan handicap (kutukan/*debuff*).
- **Spell Usage Limits:** Sebagian spell/skill memiliki limitasi pemakaian — ada yang hanya bisa dipakai beberapa kali per run, sekali pakai (*consumable*), atau permanen.

### 4.3. Health & Inventory Resources
- **Player Hitpoints:** Status nyawa linear yang jika mencapai nol, akan men-trigger status *Game Over*.
- **Transient Enemy Health Bar:** Nyawa musuh atau minion tidak permanen tampil di layar. Indikator hanya muncul (*pop-up*) ketika musuh menerima kerusakan, mencegah antarmuka terlalu penuh/berantakan.
- **Consumables:** Item sekali pakai dengan inventori bertumbuh (maksimal hingga 3 slot seiring progres pemain).
- **Stash vs Wearable:** Slot inventaris terbatas. Pemain memiliki *Stash* (gudang besar di luar run) dan *Wearable Items* (slot kecil yang hanya bisa dibawa masuk ke dalam run). Jika slot penuh, item harus di-*replace*.
- **Loot & Ekonomi:** Sumber daya utama di-*loot* dari Boss. Item memiliki *Skill Tree* tersendiri yang progresnya bersifat **permanen melintas antar run**.

### 4.4. Meta-Progression & Roguelike Structure
- **Permadeath & Permanent Upgrades:** Pemain mengulang dari awal saat mati, tetapi mengumpulkan poin dari run sebelumnya untuk membeli *starter upgrade* permanen. Growth dibatasi agar tidak *overpowered* di dalam run.
- **Checkpoint System:** Pemain dibekali maksimal **3 checkpoint** yang bisa diletakkan secara bebas di level mana pun. Penempatan bersifat permanen (tidak bisa ditarik kembali) dan memiliki "harga" — mengorbankan skill yang sedang dimiliki pemain.
- **Ascension / Difficulty Scaling:** Setiap kali pemain menamatkan game, tingkat kesulitan (*difficulty*) naik dan memunculkan varian monster serta kerumitan baru. Untuk membuka kesulitan tingkat lanjut, pemain mungkin ditantang menamatkan run dengan karakter yang berbeda.

### 4.5. Procedural Generation
- **Desain Ruangan:** Ruangan menggunakan sistem template yang penempatannya diacak oleh sistem. Transisi antar ruangan dibuat *seamless*.
- **Musuh & Boss:** Penempatan musuh biasa diacak. Setiap stage/chapter memiliki mini-boss acak dari berbagai varian yang ada — pemain tidak tahu boss apa yang menanti di akhir.
- **Blind/Cursed Abilities:** (Lihat juga §4.2) Pilihan ability misteri yang diacak oleh sistem sebagai elemen kejutan.

## 5. Visual & Art Direction
### 5.1. Visual Style
- **Perspektif Isometric:** Diinspirasi oleh estetika elegan dari game "Arco", memberi kesan dimensi ruang yang indah dan taktis.
- **Invisible Grid:** Meskipun permainan berjalan ketat secara matematis di atas papan ubin catur (grid), visualisasi ubin tersebut dihilangkan untuk menjaga ilusi natural dunia game.
- **HUD & UI:** Desain antarmuka difokuskan pada fungsionalitas dan minimalisme. Deretan kartu (*deck/hand*) difiksasi pada area tengah bawah agar sudut pandang pemain ke arena tidak terhalang.
- **Color Coding & Contrast:** Mengandalkan kontras warna yang mencolok untuk *telegraph* bahaya/niat musuh dibandingkan dengan palet dunia sekitarnya (*ambient*).

## 6. Technical Specifications
### 6.1. Platform & Engine
- **Game Engine:** Unity (memadukan struktur logis Grid dan render Isometric).
- **Pemrograman:** C# dengan pedoman arsitektur yang sangat ketat (pemisahan logika vs presentasi).
- **Quality Assurance:** Berfokus pada pengetesan unit (*Unit Testing*) secara otomatis untuk mesin logika Turn Controller dan Deck Manager.

## 7. Monetization & Release Strategy
- **Bussines Model:** Premium (Buy-to-Play).
- **Target Perilisan MVP:** Scope dibatasi **sekecil mungkin** agar realistis diselesaikan oleh tim pemula. MVP hanya difokuskan pada **1 map statis dengan bentuk permainan berupa beruntun (wave/gelombang musuh)**. Fokus utamanya adalah memoles *core combat loop* di satu arena, menguji satu set kartu starter, dan 1 set boss. Fitur-fitur kompleks (seperti eksplorasi overworld, procedural generation, roguelike run utuh, dan narasi bercabang) ditarik keluar dari MVP dan ditunda untuk iterasi berikutnya.
