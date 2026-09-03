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
Siklus inti permainan dirancang untuk terus memicu ketegangan dan pemikiran strategis secara *looping* (gelombang/wave). Referensi utama untuk gameplay loop adalah **Arco** (bagian jeda-jeda turn-based-nya):
1. **Pre-Battle (Deck Assembly):** Pemain mempersiapkan *deck* berisi tepat **15 kartu** (5 di tangan, 10 di *draw pile*) dengan 15 kartu starter yang didapat saat pertama install.
2. **Exploration / Traversal:** Pemain bergerak bebas di *overworld*, atau memilih rute melalui node/tile chapter (referensi: *Slay the Spire*). Saat memasuki encounter, game berpindah ke fase *Turn-Based Battle*. Transisi dibuat *seamless* (mulus).
3. **Combat Phase (Turn-based):**
   - **Start of Turn (Intent Phase):** Seluruh **intensi action dari semua musuh** langsung di-generate dan ditampilkan di atas kepala masing-masing musuh. Ini terjadi **sebelum** pemain bisa menentukan action.
   - **Player Phase:** Pemain mendapat **1 kartu per giliran** sebagai aksi utama. Pemain **wajib memainkan kartu** — skip turn tidak diperbolehkan. Semua pergerakan (berpindah posisi atau dodge) juga dilakukan menggunakan kartu berlabel *movement* — **tidak ada aksi gratis di luar kartu**. Pemain bisa menggunakan item konsumsi (*Free Action*).
   - **Enemy Phase:** Musuh mengeksekusi niat mereka secara berurutan dengan urutan yang sepenuhnya acak (*totally random*). Jika pemain sudah berada di luar jangkauan (*dodge*), serangan musuh gagal secara natural.
4. **Post-Wave Progression:** Saat satu gelombang usai, pemain diberikan tiga opsi kartu baru. Pemain harus memilih satu (*drafting*) untuk memperkuat atau memodifikasi gaya main di gelombang selanjutnya.
5. **Death & Reset (Roguelike Cycle):** Jika pemain mati, run dimulai ulang dari awal. Namun, poin yang dikumpulkan dari run sebelumnya bisa digunakan untuk membeli *starter upgrade* permanen. Pertumbuhan kekuatan dibatasi agar karakter tidak *overpowered* di dalam run — kekuatan utama bertumpu pada *starter/base stat*.

## 4. Mechanics & Systems
### 4.1. Combat & Tactical System
- **Cost System (1 Turn, 1 Kartu):** Setiap giliran pemain hanya bisa memainkan **1 kartu**. Pemain **wajib** memainkan kartu dan tidak boleh skip turn.
- **Semua Aksi via Kartu:** Seluruh aksi termasuk pergerakan direpresentasikan dalam kartu. Tidak ada aksi non-kartu.
- **Enemy Intent:** Konsep telegraf visual 100% transparan. Di awal setiap giliran, **seluruh intensi action dari semua musuh** langsung di-generate sekaligus dan ditampilkan di atas kepala masing-masing musuh — **sebelum** pemain bisa menentukan action. Setiap musuh hanya memiliki **1 intent per giliran** (tidak ada multi-intent).
- **Skill Casting (Manual Targeting):** Kartu dimainkan dengan cara **klik dan drag ke target** atau dikembalikan ke deck. Tidak ada auto-lock. Pemain harus secara manual mengarahkan ke target.
- **Dodging (Evasion):** Bertahan bukan berarti menggunakan perisai, melainkan memposisikan ulang karakter (*repositioning*) dari ubin yang akan menjadi sasaran serangan musuh.
- **Bush / Stealth Mechanic:** Ada area berupa semak (*bush*) yang bisa dimasuki oleh pemain maupun musuh. Lebar bush hanya **1 tile** (selebar pemain), panjangnya bervariasi. Saat karakter berada di dalam bush, musuh tidak bisa men-*targeting* karakter tersebut saat gilirannya. Musuh harus bergerak hingga **minimal 2 tile** dari posisi bush yang ditempati untuk bisa mendapatkan target. Mekanik ini berlaku simetris — jika musuh berada di dalam bush, pemain pun tidak bisa menarget musuh tersebut dari kejauhan.

### 4.2. Card & Deck System
- **Deck Limitations:** Terbatas hanya **15 kartu** saat pertempuran berlangsung (5 di tangan, 10 di *draw pile*). Tidak ada rarity pada kartu.
- **Discard & Reshuffle:** Kartu yang telah dimainkan masuk ke *discard pile*. Jika *draw pile* habis, seluruh kartu di *discard pile* dikocok ulang dan menjadi *draw pile* baru secara otomatis, tanpa penalti.
- **Tipe-Tipe Kartu:** Ada 4 tipe kartu utama yang bersifat universal (bisa dipakai oleh pemain maupun musuh):
  - **Movement:** Memanipulasi posisi (contoh: dash, teleport). Jarak bervariasi dari 1 tile hingga teleport kemana saja. Kartu lambat vs instan juga bervariasi.
  - **Offense:** Memberikan damage atau melemahkan musuh (contoh: single attack, AoE, damage over time, serangan ranged lambat hingga instan).
  - **Defense:** Menjaga HP (contoh: shield, armor boost, regen).
  - **Status Modifier:** Modifikasi statistik karakter atau musuh yang menempel ke kartu Offense, Defense, dan Movement (contoh: attack up, stun, slow, armor break).
- **Starter Deck Pemain:** Pemain mendapat 15 kartu starter saat pertama kali install yang bersumber dari **Buku Sakti (*Grimoire*)** yang dibawanya. Variasi kartu dibuat oleh masing-masing anggota tim.
- **Rewards:** Mekanisme "Pilih 1 dari 3" seusai pertarungan agar deck bisa dikustomisasi secara progresif.
- **Blind/Cursed Abilities:** Pemain dihadapkan pada pilihan *ability* acak yang bersifat misteri. *High Risk - High Reward*, memberikan skill tambahan sekaligus handicap (kutukan/*debuff*).
- **Spell Usage Limits:** Sebagian spell/skill memiliki limitasi pemakaian — ada yang beberapa kali per run, sekali pakai, atau permanen.
- **AoE:** Dampak AoE tergantung pada kartu masing-masing. Jumlah tile maksimum tidak dibatasi untuk menjaga kreativitas.

### 4.3. Health & Inventory Resources
- **Player Hitpoints:** HP berupa **angka dan bar health (persentase)**. Jika HP mencapai nol, men-trigger status *Game Over*. HP permanen (selama run) bersifat fixed, namun tidak menutup kemungkinan bertambah menggunakan mekanik selama run (contoh: kartu yang menambah max health dalam satu stage).
- **Transient Enemy Health Bar:** Nyawa musuh atau minion tidak permanen tampil di layar. Indikator hanya muncul (*pop-up*) ketika musuh menerima kerusakan, mencegah antarmuka terlalu penuh/berantakan.
- **Consumables:** Item sekali pakai dengan maksimal **3 slot** (bisa berisi jenis yang sama atau berbeda). Didapat dari **random event** saat pemain memilih rute (seperti *Slay the Spire*).
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

### 4.6. Arena & Grid System
- **Ukuran Arena:** Arena bertarung berukuran **15×15 tile**. Setiap karakter (pemain maupun musuh) menempati **1 tile**.
- **Obstacle & Terrain:** Obstacle atau invisible wall dapat dibuat **di dalam arena** (seluas 15×15) untuk memperkecil arena aktif atau memberikan tambahan mekanik gameplay sesuai kebutuhan. Ini memungkinkan variasi layout tanpa mengganti ukuran arena.
- **Spawn Point:** Posisi awal pemain **fixed ditentukan oleh sistem**, bukan bersifat random.
- **Jumlah Musuh:** Maksimum musuh per wave ditentukan berdasarkan luas *movable tiles*. Jika arena sangat terbuka, maksimum **5 musuh** per wave (contoh referensi: lapangan futsal).
- **Pergerakan 8 Arah:** Pemain dan musuh dapat bergerak ke **8 arah** — vertikal, horizontal, dan diagonal.
- **Collision:** Jika pemain bergerak non-teleport dan jalur gerak melewati tile yang ditempati musuh, pemain akan **tertabrak dan berhenti** tepat di 1 tile sebelum tile musuh (tepat di depan musuh).

### 4.7. Enemy System
- **Deck Musuh & Classless Equipment System:**
  - Musuh menggunakan sistem kartu yang sama dengan pemain (kartu bersifat universal).
  - Musuh memiliki **deck sendiri yang ditentukan oleh senjata dan armor yang mereka pakai** (sistem *classless* ala *Albion Online*).
  - Setiap kombinasi senjata/armor musuh membawa *default card deck* bawaannya masing-masing (misal: pedang/tameng membawa kartu Melee & Defense, busur membawa kartu Ranged, jubah/tongkat membawa kartu Support & AoE).
- **Tipe Serangan Musuh:** Musuh terdiri dari **3 tipe** berdasarkan jenis serangan:
  - **Ranged:** Musuh jarak jauh.
  - **Melee:** Musuh jarak dekat.
  - **Support:** Musuh pendukung.
  Konsepnya musuh bermain secara tim (*ramean*) dengan fokus masing-masing untuk membentuk kombinasi tim yang sinergis. Pemain harus mampu menghadapi semua tipe.
- **Hierarki Musuh:** Musuh terdiri dari **3 hierarki** berdasarkan jumlah sinergi yang dimiliki:
  - **Minion (Kroco):** Hierarki terendah, jumlah sinergi paling sedikit. Tidak memiliki kemampuan mengamati kondisi sekitar (misalnya tidak merespons debuff yang terkena pemain).
  - **Regular:** Hierarki menengah, memiliki beberapa sinergi.
  - **Elite:** Hierarki tertinggi, jumlah sinergi paling banyak dan memiliki kemampuan mengamati kondisi sekitar secara penuh (merespons debuff, posisi, dan kondisi battlefield).
- **AI Musuh:** Musuh berpikir secara mandiri dan **tidak terorganisir** dalam kelompok. Urutan giliran (*turn order*) musuh bersifat **totally random**, tidak terorganisir, dan ditentukan berdasarkan jarak dan durasi serangan.
- **Intensi (Intent):** Intensi action musuh ditampilkan **di atas kepala** masing-masing karakter.

## 5. Visual & Art Direction
### 5.1. Visual Style & Aesthetics
- **Perspektif Isometric:** Diinspirasi oleh estetika elegan dari game *Arco*, memberi kesan dimensi ruang yang indah dan taktis.
- **Invisible Grid:** Meskipun permainan berjalan ketat secara matematis di atas papan ubin catur (grid), visualisasi ubin tersebut dihilangkan untuk menjaga ilusi natural dunia game.
- **HUD & UI:** Desain antarmuka difokuskan pada fungsionalitas dan minimalisme. Deretan kartu (*deck/hand*) difiksasi pada area tengah bawah agar sudut pandang pemain ke arena tidak terhalang.
- **Art Style & Referensi:** Mengusung visual bernuansa *dark retro-futuristic/anime fantasy* (referensi: *Signalis*) dipadukan dengan rendering pixel art isometric yang tajam dan atmosferik.

### 5.2. Setting, Ambience & Color Palette
- **Latar Tempat (World Setting):** Dunia yang hancur dan dipenuhi keputusasaan.
- **Tower of Babel (Archive of Knowledge):**
  - Menara Babel berfungsi sebagai tempat penyimpanan (*archive*) seluruh pengetahuan di dunia yang dipenuhi buku-buku layaknya perpustakaan raksasa.
  - **Progresi Struktur:** Lantai-lantai awal tertata rapi seperti perpustakaan besar, namun semakin naik ke lantai yang lebih tinggi, struktur bangunannya semakin kacau, terdistorsi, dan tidak beraturan.
- **Tema Visual & Arsitektur:** Mengusung tema *Medieval Dark Fantasy* pada lantai-lantai awal, kemudian secara dinamis bercampur (*hybrid*) hingga memunculkan arsitektur tema *Ancient Mesopotamia* di lantai-lantai tingkat atas.
- **Ambience:** Gelap namun memiliki sentuhan dinamis/hidup (*"gelap-gelap ceria"*, referensi: *Dead Cells*).
- **Palet Warna:** Didominasi oleh nuansa gelap/suram (*dark tones*) dengan sentuhan warna **kuning-oranye (*yellow-orange*)** sebagai *accent color* kontras untuk pencahayaan, objek interaktif, dan telegraph bahaya.

## 6. Audio Direction
### 6.1. Background Music (BGM)
- **Main Menu / Options Menu:** Tema pembuka atmosferik bernuansa misterius dan gelap.
- **Memilih Deck Kartu / Pre-Battle:** Musik persiapan taktis yang tenang dan fokus.
- **Map (Memilih Jalur Stage/Event) / Masuk Dungeon:** BGM eksplorasi bernuansa petualangan dan ketegangan eksplorasi.
- **Battle Theme (Normal) [Bervariasi]:** Musik pertempuran dinamis dengan variasi track per biome/lantai.
- **Boss Battle Theme [Bervariasi]:** Musik pertempuran boss yang intens, megah, dan memacu adrenalin.
- **Victory:** Jingle/tema perayaan singkat penanda kemenangan wave/stage.
- **Defeat / Game Over:** Tema kekalahan yang suram sekaligus memotivasi pemain untuk mencoba run baru.
- **Menu Deck Rebuilding:** Musik tenang saat menyusun dan merombak kartu deck.
- **Mysterious / Event Stage “?”:** Musik misterius dan ambigu untuk encounter/random event.
- **Shop:** BGM santai dan bernuansa transaksi/istirahat.
- **Inventory:** Musik latar minimalis saat mengelola inventory dan stash.
- **End Game Credit:** Track penutup epik setelah menyelesaikan run/ending.

### 6.2. Sound Effects (SFX)
- **UI & Navigasi:**
  - Cursor Hover
  - Cursor Click — Main Menu
  - Cursor Click — In-game / Battle / Event
  - Cursor Click — Shop (termasuk saat membeli item)
  - Cursor Click — Inventory (geser/scroll item, ganti halaman)
- **Sistem Kartu & Interaksi:**
  - Hover kartu di tangan
  - Klik & drag kartu dari tangan
  - Draw card (menarik kartu)
  - Shuffle / reshuffle card
  - Discard card
  - Efek kartu saat dimainkan (Attack, Defend, Movement, Status Effect)
- **Combat, Karakter & World:**
  - Intention serangan musuh (telegraph visual sound)
  - Karakter / musuh melancarkan serangan
  - Karakter / musuh menerima damage (hit feedback)
  - Karakter / musuh mati (death sound)
  - Status Effect (trigger buff/debuff)
  - Benturan / collision (tabrakan saat bergerak)
  - Membuka lantai / floor baru
  - SFX sebelum battle dimulai (start combat horn/cue)
  - Victory celebration sound
  - Sound bush / semak (karakter masuk/bergerak di stealth bush)
  - Suara vokal musuh (grunt/growl)
  - Dialog sound (beeps/voice text display)
  - Item usage (pemakaian consumable item)
  - Upgrade / enhance sound
  - SFX buka / tutup peti reward

## 7. Narrative & Story Delivery
- **Karakter Utama:** Karakter utama bersenjatakan **Buku Sakti (*Grimoire*)** sebagai senjata utama sekaligus elemen visual ikonik (selaras dengan latar Menara Babel sebagai arsip pengetahuan). Nama karakter final belum ditentukan (setiap anggota tim mengusulkan 1 nama).
- **Penyampaian Cerita:** Cerita di-*deliver* melalui **lingkungan** (*environmental storytelling*). Narasi muncul saat pemain berinteraksi dengan benda-benda dalam game (buku, artefak, relik perpustakaan), bukan melalui cutscene ekspositori. Cerita dirancang oleh Game Designer.

## 8. Technical Specifications
### 8.1. Platform & Engine
- **Game Engine:** Unity (memadukan struktur logis Grid dan render Isometric).
- **Pemrograman:** C# dengan pedoman arsitektur yang sangat ketat (pemisahan logika vs presentasi).
- **Quality Assurance:** Berfokus pada pengetesan unit (*Unit Testing*) secara otomatis untuk mesin logika Turn Controller dan Deck Manager.

## 9. Monetization & Release Strategy
- **Business Model:** Premium (Buy-to-Play).
- **Target Perilisan MVP:** Scope dibatasi **sekecil mungkin** agar realistis diselesaikan oleh tim pemula. MVP hanya difokuskan pada **1 map statis dengan bentuk permainan berupa beruntun (wave/gelombang musuh)**. Fokus utamanya adalah memoles *core combat loop* di satu arena, menguji satu set kartu starter, dan 1 set boss. Fitur-fitur kompleks (seperti eksplorasi overworld, procedural generation, roguelike run utuh, dan narasi bercabang) ditarik keluar dari MVP dan ditunda untuk iterasi berikutnya.

## 10. Team Assignments (Outstanding)
- **Variasi Kartu:** Dibuat oleh masing-masing anggota tim, kemudian disetor.
- **UI/UX Flow & HUD:** Didefinisikan oleh Game Designer.
- **Visual Art & Assets:** Visual Artist memproduksi aset berdasarkan panduan art style (*Arco* + *Signalis* + *Dead Cells*, dominan gelap aksen kuning-oranye, tema Babel/Mesopotamia).
- **Audio Assets Production:** Audio Designer memproduksi file audio berdasarkan daftar penempatan BGM & SFX yang telah disepakati.

---

# ITERASI BERIKUTNYA
Bagian ini memuat fitur-fitur dan mekanik tambahan yang ditarik dari cakupan MVP agar tim dapat fokus menyelesaikan *core combat loop* terlebih dahulu:

1. **Skill Combo & Bonus Action:**
   - Interaksi kombo antar kartu yang saling memperkuat jika dimainkan dalam urutan/cara tertentu.
   - Bonus action didapatkan dari eksekusi kombo (contoh: jika memakai kartu A dengan cara B, mendapat bonus action berupa C).
2. **Friendly Fire:**
   - Karakter pemain dapat menerima damage jika serangan bertipe AoE mengenai ubin tempat dirinya berdiri.
3. **Item Tambah Max Health:**
   - Pilihan consumable/item khusus yang dapat meningkatkan kapasitas maksimal HP pemain selama *run*.
4. **Shop & Ekonomi In-Run:**
   - Sistem toko tempat pemain dapat membeli kartu, consumable, atau relic menggunakan mata uang yang didapat selama run (membutuhkan perumusan sistem ekonomi game terlebih dahulu).
