# Analisis Kekurangan GDD-0 untuk MVP & Iterasi Berikutnya

> Dokumen ini mencatat hal-hal yang **belum terdefinisi atau kurang detail** di Game Design Document 0.
>
> Mengingat MVP akan dibatasi sebagai **1 map statis dengan sistem wave (gelombang)**, dokumen ini dibagi menjadi dua bagian:
> - **BAGIAN 1: Kebutuhan MVP** (Harus diputuskan segera untuk testing loop dasar).
> - **BAGIAN 2: Kebutuhan Iterasi Berikutnya** (Ide-ide lanjutan yang ditunda dari MVP).
>
> Setiap gap dilengkapi dengan **contoh dari game sejenis** (roguelike / deck-builder) sebagai referensi.
> Tema narasi sementara dirujuk dari Dokumen Narasi.

---

# BAGIAN 1: Kebutuhan MVP (1 Map Statis, Gelombang Musuh)

## 1. Arena & Grid

- **Dimensi grid belum ditentukan.** Berapa lebar dan tinggi arena dalam satuan ubin (tile)? Ukuran ini akan menentukan seberapa luas ruang gerak pemain dan musuh, serta seberapa penting mekanik dodge/reposisi.
  > **Contoh:** Di *Into the Breach* (tactical roguelike), arena berukuran 8×8 ubin. Ukuran ini cukup kecil agar setiap langkah terasa bermakna, tapi cukup luas agar pemain punya ruang bermanuver. Di *Dungeon of the Endless* (roguelike), tiap ruangan berukuran lebih kecil lagi (sekitar 5×5 hingga 7×7) karena fokusnya pada pertarungan jarak dekat. Untuk game ini yang mengandalkan dodge dan posisi, ukuran sekitar **6×6 hingga 10×10** bisa menjadi titik awal yang masuk akal.

- **Obstacle dan terrain belum disebutkan sama sekali.** Apakah arena selalu kosong dan datar, atau bisa ada penghalang seperti batu, dinding, lubang, atau terrain khusus? Ini penting karena memengaruhi bagaimana pemain merencanakan dodge dan serangan.
  > **Contoh:** Di *Into the Breach*, ada gedung yang tidak bisa dilewati, danau yang memberi damage, dan gunung yang menghalangi gerakan. Obstacle seperti ini membuat setiap pertarungan terasa berbeda meski arena ukurannya sama. Untuk MVP, minimal perlu didefinisikan apakah ada setidaknya **1 tipe obstacle** (misalnya dinding/pilar yang menghalangi jalan dan proyektil).

- **Spawn point (titik kemunculan) pemain dan musuh belum didefinisikan.** Di mana pemain muncul saat pertarungan dimulai? Di mana musuh-musuh muncul? Apakah posisi awal ini selalu tetap atau diacak?
  > **Contoh:** Di *Into the Breach*, pemain memilih sendiri posisi awal unit mereka dari beberapa ubin yang tersedia sebelum giliran pertama dimulai. Di *Slay the Spire*, posisi tidak relevan karena tidak berbasis grid. Untuk game ini, mekanik spawn point perlu didefinisikan karena posisi awal langsung memengaruhi strategi dodge di giliran pertama.

---

## 2. Sistem Pergerakan

- **Arah pergerakan Free Move belum dispesifikasi.** GDD menyebut pemain mendapat "1 ubin pergerakan gratis" setiap giliran, tapi belum dijelaskan ke arah mana saja pemain bisa bergerak. Apakah hanya bisa bergerak ke 4 arah (atas, bawah, kiri, kanan, yang disebut *orthogonal*)? Atau bisa juga bergerak diagonal sehingga ada 8 arah?
  > **Contoh:** Di *Into the Breach*, unit hanya bisa bergerak secara orthogonal (4 arah: atas, bawah, kiri, kanan), tidak bisa diagonal. Ini menyederhanakan perhitungan jarak dan membuat setiap langkah lebih mudah diprediksi. Di *Fire Emblem* (tactical RPG), unit bisa bergerak ke segala arah di grid termasuk diagonal. Untuk game pemula, **4 arah orthogonal** lebih mudah diimplementasikan dan dimengerti pemain.

- **Belum ada penjelasan apakah kartu bisa memberikan perpindahan lebih dari 1 ubin.** Apakah ada kartu yang memungkinkan pemain bergerak 2 atau 3 ubin sekaligus (misalnya kartu "Sprint" atau "Dash")?
  > **Contoh:** Di *Slay the Spire*, tidak ada perpindahan karena tidak ada grid. Namun di *Into the Breach*, beberapa kemampuan bisa mendorong unit beberapa ubin sekaligus. Perlu diputuskan apakah di MVP ada kartu yang memberikan perpindahan ekstra, atau Free Move 1 ubin sudah cukup.

- **Belum ada aturan collision (tabrakan).** Apa yang terjadi jika pemain mencoba bergerak ke ubin yang sudah ditempati musuh? Atau sebaliknya, jika musuh ingin bergerak ke ubin pemain? Apakah perpindahan tersebut diblokir, atau ada efek khusus seperti saling dorong?
  > **Contoh:** Di *Into the Breach*, unit tidak bisa menempati ubin yang sudah ada unit lain. Jika sebuah kemampuan mendorong unit ke ubin yang sudah ditempati, yang terjadi adalah keduanya menerima damage (efek "bumping"). Aturan ini perlu didefinisikan karena arena yang kecil berarti tabrakan akan sering terjadi.

---

## 3. Kartu: Detail Implementasi

- **Tidak ada daftar kartu starter sama sekali.** Bahkan untuk MVP, dibutuhkan setidaknya satu set kartu konkret yang bisa dimainkan, lengkap dengan: nama kartu, efek yang terjadi saat dimainkan, jangkauan serangannya (berapa ubin), dan area efeknya (mengenai 1 ubin saja atau beberapa ubin sekaligus).
  > **Contoh:** Di *Slay the Spire*, karakter Ironclad memulai dengan starter deck berisi 10 kartu: 5× "Strike" (6 damage ke 1 musuh), 4× "Defend" (5 block/pertahanan), dan 1× "Bash" (8 damage + efek Vulnerable). Setiap kartu memiliki *cost* (biaya energi) yang jelas. Untuk MVP game ini, **definisikan minimal 8-15 kartu starter** dengan nama, cost, damage/efek, range, dan AoE yang spesifik.

- **Tipe-tipe kartu belum dikategorikan secara eksplisit.** GDD menyebut ada "kartu serangan" dan "kartu pertahanan", tapi belum ada penjelasan mekanik apa yang membedakan keduanya. Apakah ada tipe lain seperti kartu utilitas (misalnya untuk perpindahan posisi) atau kartu buff (memperkuat diri sendiri)?
  > **Contoh:** Di *Slay the Spire*, kartu dikategorikan menjadi 3 tipe utama: Attack (merah, memberikan damage), Skill (hijau, memberikan block/efek utilitas), dan Power (biru, memberikan buff permanen selama pertarungan berlangsung). Kategori ini membantu pemain memahami fungsi kartu dengan cepat.

- **Cost system (sistem biaya aksi) belum didefinisikan.** GDD menyebut "1 Aksi Utama per giliran" dan bahwa penggunaan kartu memakan Turn Action, tapi belum jelas: apakah pemain hanya bisa memainkan 1 kartu per giliran? Atau ada kartu yang "gratis" (0 cost)? Apakah ada cara mendapatkan aksi tambahan?
  > **Contoh:** Di *Slay the Spire*, pemain mendapat 3 energi per giliran dan setiap kartu memakan 0-5 energi. Jadi pemain bisa memainkan banyak kartu murah atau sedikit kartu mahal. Di *Into the Breach*, setiap unit hanya bisa bertindak 1 kali per giliran (1 aksi). Karena GDD menyebut "1 Aksi Utama", perlu diperjelas apakah ini berarti benar-benar hanya 1 kartu per giliran, atau ada sistem energi yang lebih fleksibel.

- **Belum ada aturan hand overflow.** *Hand overflow* adalah kondisi ketika semua kartu di *draw pile* (tumpukan tarik) sudah habis ditarik, sementara pemain masih perlu menarik kartu. Biasanya, kartu-kartu di *discard pile* (tumpukan buang) akan dikocok ulang (*reshuffle*) untuk menjadi draw pile baru. Tapi bagaimana detailnya di game ini? Apakah reshuffle terjadi otomatis saat draw pile kosong? Apakah ada penalti saat reshuffle?
  > **Contoh:** Di *Slay the Spire*, ketika draw pile habis, seluruh kartu di discard pile otomatis dikocok dan menjadi draw pile baru, tanpa penalti apa pun. Proses ini terjadi secara instan dan mulus. Untuk game ini yang hanya punya 15 kartu total, siklus reshuffle akan terjadi cukup sering, sehingga aturannya harus jelas.

- **Belum ada penjelasan apakah pemain boleh melewatkan (skip) giliran tanpa memainkan kartu.** Apakah pemain wajib memainkan setidaknya 1 kartu setiap giliran, atau boleh tidak melakukan apa-apa dan langsung mengakhiri giliran?
  > **Contoh:** Di *Slay the Spire*, pemain bisa kapan saja menekan tombol "End Turn" tanpa memainkan kartu apa pun. Ini adalah keputusan strategis, misalnya saat tangan penuh kartu serangan tapi musuh sedang menyiapkan serangan besar dan pemain lebih memilih bertahan (block). Aturan ini memengaruhi strategi secara signifikan.

---

## 4. Sistem Musuh: Detail Implementasi

- **Tidak ada definisi musuh satu pun.** Bahkan untuk MVP paling minimal, dibutuhkan setidaknya 2-3 tipe musuh dengan statistik yang konkret: berapa HP-nya, berapa damage serangan mereka, bagaimana pola gerak mereka di grid, dan berapa jangkauan serangan mereka.
  > **Contoh:** Di *Slay the Spire*, musuh-musuh di Act 1 (chapter pertama) sudah terdefinisi dengan sangat rinci. Misalnya: "Jaw Worm" memiliki 44 HP, bisa menyerang 12 damage, atau memberi diri sendiri buff, atau bertahan. "Cultist" punya 50 HP, selalu buff di giliran pertama lalu menyerang dengan damage yang meningkat setiap giliran. Untuk MVP, **definisikan minimal 3 tipe musuh biasa dan 1 mini-boss** dengan semua angka yang jelas.

- **Pola AI (perilaku otomatis) musuh belum didefinisikan.** Pola AI adalah cara musuh "berpikir" dan memutuskan apa yang akan dilakukan setiap giliran. Ada beberapa pendekatan umum:
  - **Random (acak):** musuh memilih aksi secara acak dari daftar kemampuan yang dimilikinya. Mudah diimplementasikan tapi bisa terasa tidak masuk akal.
  - **Proximity-based (berdasarkan jarak):** musuh memprioritaskan aksi berdasarkan seberapa dekat pemain. Misalnya, jika pemain dekat maka musuh menyerang, jika jauh maka musuh mendekat dulu. Terasa lebih natural.
  - **Scripted (pola tetap berurutan):** musuh mengikuti urutan aksi yang sudah ditentukan sebelumnya. Misalnya: giliran 1 selalu buff, giliran 2 selalu menyerang, giliran 3 selalu bertahan, lalu berulang. Paling mudah diprediksi oleh pemain.
  - **Hybrid (campuran):** gabungan beberapa pendekatan di atas. Misalnya, pola dasar scripted tapi bisa berubah jika HP musuh sudah rendah.
  > **Contoh:** Di *Slay the Spire*, setiap musuh punya tabel probabilitas yang sudah ditentukan. Misalnya: "Jaw Worm" punya 75% kemungkinan menyerang dan 25% kemungkinan buff, tapi tidak pernah melakukan aksi yang sama 3 kali berturut-turut. Pola ini sudah diperhitungkan desainernya agar terasa adil. Di *Into the Breach*, musuh menggunakan sistem scripted yang 100% transparan sehingga pemain bisa memprediksi dengan pasti.

- **Perbedaan mekanik melee (jarak dekat) dan ranged (jarak jauh) belum dijabarkan.** GDD menyebut ada musuh melee dan ranged, tapi belum dijelaskan secara teknis: musuh melee bisa menyerang dari jarak berapa ubin (hanya 1 ubin tepat di sebelahnya? atau 1-2 ubin?)? Musuh ranged bisa menyerang dari jarak berapa ubin? Apakah serangan ranged bisa terhalang oleh obstacle atau unit lain di antara penembak dan target?
  > **Contoh:** Di *Into the Breach*, serangan ranged berupa garis lurus yang bisa menembus beberapa ubin, tapi akan mengenai unit pertama yang ada di jalurnya (termasuk sekutu sendiri). Di *Fire Emblem*, senjata jarak dekat jangkauannya 1 ubin, sementara senjata jarak jauh 2-3 ubin. Untuk game ini, **definisikan jangkauan angka konkret** (misal: melee = 1 ubin, ranged = 3-5 ubin, dan apakah ada *line of sight* atau tidak).

- **Jumlah musuh per gelombang belum ditentukan.** Berapa musuh yang muncul di setiap gelombang? Apakah jumlahnya bertambah seiring gelombang berikutnya?
  > **Contoh:** Di *Slay the Spire*, encounter biasa bisa berisi 1-5 musuh sekaligus (misalnya 2 Louse + 1 Gremlin). Elite encounter berisi 1 musuh yang kuat. Boss encounter berisi 1 boss besar. Untuk MVP, **tentukan kisaran jumlah musuh** (misalnya: gelombang awal 2 musuh, gelombang akhir 4-5 musuh).

---

## 5. Enemy Intent & Telegraph

- **Format visual telegraf belum didefinisikan.** GDD menyebut intent musuh "100% transparan", tapi belum menjelaskan bentuk visualnya. Apakah berupa ubin-ubin yang berubah warna menjadi merah sebagai tanda area serangan? Apakah berupa ikon gambar di atas kepala musuh (pedang untuk menyerang, perisai untuk bertahan)? Atau berupa panah arah yang menunjukkan ke mana musuh akan bergerak?
  > **Contoh:** Di *Slay the Spire*, intent musuh ditampilkan sebagai ikon di atas kepala musuh (pedang merah = serangan + angka damage, perisai biru = bertahan, panah hijau = buff). Di *Into the Breach*, intent ditampilkan lebih detail: ubin target berwarna merah, arah serangan ditunjukkan panah, dan damage tertulis di atas ubin. Karena game ini berbasis posisi, pendekatan *Into the Breach* (highlight ubin + arah) lebih relevan untuk ditiru.

- **Belum ada aturan kapan intent di-generate (dibuat).** Apakah niat musuh sudah ditentukan di awal giliran pemain sehingga pemain bisa merencanakan strategi, atau baru terlihat di akhir giliran pemain? Hal ini sangat memengaruhi tingkat "keadilan" dan kenyamanan bermain.
  > **Contoh:** Di *Slay the Spire*, intent musuh sudah terlihat sejak awal giliran pemain, sehingga pemain punya waktu penuh untuk merencanakan respons. Di *Into the Breach*, semua intent musuh juga sudah ditampilkan sebelum pemain bertindak. Kedua game ini menjadikan transparansi sebagai pilar desain utama, sama seperti yang disebut di GDD.

- **Belum ada definisi multi-intent.** Apakah satu musuh hanya bisa punya 1 rencana aksi per giliran, atau bisa lebih? Misalnya, bisakah satu musuh berencana "bergerak 2 ubin ke kanan, lalu menyerang ke depan" dalam satu giliran?
  > **Contoh:** Di *Slay the Spire*, setiap musuh hanya punya 1 intent per giliran (entah menyerang, bertahan, atau buff, tidak pernah dua sekaligus). Ini menyederhanakan informasi yang harus diproses pemain. Di *Into the Breach*, musuh juga hanya punya 1 aksi, tapi aksi itu bisa berupa gerakan + serangan sebagai satu paket. Perlu diputuskan mana yang digunakan.

---

## 6. Skill Casting / Manual Targeting

- **Mekanisme targeting belum didefinisikan secara teknis.** GDD menyebut pemain harus "manual mengarahkan" serangan, tapi belum jelas caranya. Apakah pemain mengklik ubin target di grid? Apakah pemain memilih arah (utara/selatan/timur/barat) lalu serangan terbang ke arah itu? Atau apakah pemain men-drag (menyeret) kartu ke posisi di arena?
  > **Contoh:** Di *Into the Breach*, pemain mengklik unit, memilih skill, lalu mengklik ubin target. Sistem langsung menampilkan preview (pratinjau) efek serangan sebelum dikonfirmasi. Di *Arco* (referensi visual game ini), targeting dilakukan dengan mengklik posisi target di arena isometric. Untuk MVP, pendekatan **klik ubin target + preview** adalah yang paling umum dan mudah dipahami pemain.

- **Area of Effect (AoE) belum ada contoh bentuk dan ukurannya.** AoE adalah area yang terkena efek dari sebuah skill. GDD menyebut ada "area efek", tapi belum menjelaskan bentuk apa saja yang mungkin. Apakah ada serangan yang mengenai garis lurus 3 ubin ke depan? Atau area 3×3 ubin di sekitar target? Atau bentuk silang (+)?
  > **Contoh:** Di *Into the Breach*, ada beberapa bentuk AoE: garis lurus (menyerang semua ubin dalam satu baris/kolom), titik tunggal (1 ubin), push/knockback (mendorong ke segala arah). Di *Fire Emblem*, AoE bisa berbentuk berlian (diamond) dengan radius tertentu. Untuk MVP, **definisikan minimal 2-3 bentuk AoE dasar** (misalnya: single target, garis lurus, area 3×3).

- **Belum ada aturan friendly fire.** Apakah skill atau serangan pemain bisa mengenai diri sendiri jika pemain berdiri di area efek? Atau apakah serangan musuh bisa mengenai musuh lain?
  > **Contoh:** Di *Into the Breach*, ini menjadi mekanik inti: serangan musuh bisa mengenai musuh lain jika pemain mendorong musuh ke jalur serangan musuh lain. Ini menciptakan gameplay yang sangat taktis. Di *Slay the Spire*, tidak ada friendly fire karena tidak ada posisi spasial. Keputusan ini akan sangat memengaruhi kedalaman taktis game.

---

## 7. Damage & Combat Math

- **Formula damage belum ada.** Belum jelas bagaimana damage dihitung saat serangan mengenai target. Apakah damage bersifat flat (langsung, misalnya serangan 10 damage selalu memberikan 10 damage)? Apakah ada modifier (pengali atau penambah, misalnya buff "kekuatan +3" menambah semua serangan 3 damage)? Apakah ada defense reduction (pengurangan karena pertahanan, misalnya musuh punya 5 armor sehingga damage 10 menjadi 5)?
  > **Contoh:** Di *Slay the Spire*, formula damage-nya sederhana: **Damage Output = Base Damage + Strength - Enemy Block**. Block (pertahanan) mengurangi damage langsung dan hilang di akhir giliran. Strength (kekuatan) menambah semua serangan secara permanen selama pertarungan. Formula sederhana seperti ini mudah diimplementasikan dan mudah dipahami pemain.

- **HP awal pemain belum ditentukan.** Berapa nyawa pemain saat memulai run?
  > **Contoh:** Di *Slay the Spire*, Ironclad memulai dengan 80 HP (max 80). Di *Into the Breach*, setiap mech punya 3-4 HP. Angka ini harus seimbang dengan damage rata-rata musuh. Jika musuh menyerang 5-10 damage per giliran, HP 50-80 masuk akal. Jika musuh menyerang 1-2 damage, HP 10-20 sudah cukup.

- **Belum ada penjelasan apakah ada jenis damage yang berbeda.** Apakah semua serangan memberikan damage yang sama jenisnya (homogen, cuma "damage" saja), atau ada tipe-tipe berbeda seperti damage fisik, damage sihir, damage api, dan lain-lain? Tipe damage biasanya berkaitan dengan resistensi (misalnya: musuh tipe es lemah terhadap damage api).
  > **Contoh:** Di *Slay the Spire*, semua damage bersifat homogen (tidak ada tipe elemen). Di *Darkest Dungeon* (roguelike RPG), ada damage biasa dan damage stress, serta ada resistensi musuh terhadap tipe serangan tertentu (bleed, blight, stun, move). Untuk MVP, **damage homogen** lebih sederhana dan disarankan.

---

## 8. Consumable Items

- **Tidak ada daftar consumable item satu pun.** Bahkan untuk MVP, dibutuhkan setidaknya 2-3 item konkret dengan efek yang jelas agar sistem inventory bisa diuji coba.
  > **Contoh:** Di *Slay the Spire*, ada potion (ramuan) sebagai consumable. Contoh: "Fire Potion" (memberikan 20 damage ke 1 musuh), "Block Potion" (mendapat 12 block), "Fairy in a Bottle" (otomatis menghidupkan pemain dengan 30% HP jika mati). Untuk MVP, **definisikan minimal 3 item** dengan nama dan efek yang jelas, misalnya: potion heal, potion damage, dan potion dodge/reposisi.

---

## 9. Turn Order & Fase Detail

- **Urutan resolusi dalam Enemy Phase belum didefinisikan.** Jika ada beberapa musuh di arena, siapa yang bergerak dan menyerang duluan? Apakah berdasarkan posisi di grid (misalnya dari kiri ke kanan, atas ke bawah)? Berdasarkan tipe musuh (melee duluan baru ranged)? Atau berdasarkan urutan kemunculan di arena?
  > **Contoh:** Di *Into the Breach*, urutan musuh sudah ditampilkan secara visual dengan nomor urut, sehingga pemain bisa merencanakan strategi berdasarkan urutan itu. Di *Slay the Spire*, urutan tidak terlalu penting karena tidak ada posisi spasial. Karena game ini berbasis posisi, urutan musuh bisa sangat memengaruhi hasil. Misalnya: jika musuh A mendorong pemain ke kiri, lalu musuh B menyerang posisi di kiri, urutan itu krusial.

- **Belum ada aturan simultanitas.** Apakah semua musuh bertindak serentak (semua serangan dihitung bersamaan) atau satu per satu secara bergiliran (musuh A selesai bertindak dulu, baru musuh B)?
  > **Contoh:** Di *Into the Breach*, musuh bertindak secara berurutan (satu per satu), dan urutan ini ditampilkan di UI. Di *Slay the Spire*, musuh bertindak berurutan dari kiri ke kanan. Keputusan ini memengaruhi apakah efek dari aksi satu musuh bisa mengubah hasil aksi musuh lain (misalnya: musuh pertama membunuh unit yang harusnya diserang musuh kedua).

- **Belum ada definisi efek status (status effects).** Apakah ada efek-efek yang bertahan lebih dari 1 giliran, seperti: *burn* (terbakar, menerima damage tambahan setiap giliran), *poison* (keracunan, menerima damage setiap giliran), *buff* (penguatan sementara), *debuff* (pelemahan sementara), *stun* (tidak bisa bertindak 1 giliran)? Jika ada, bagaimana interaksinya dengan sistem intent musuh?
  > **Contoh:** Di *Slay the Spire*, ada beberapa status effect utama: Vulnerable (menerima 50% lebih banyak damage), Weak (memberikan 25% lebih sedikit damage), Poison (menerima damage sebesar jumlah poison di akhir giliran, lalu poison berkurang 1), dan Strength (menambah damage serangan). Untuk MVP, **minimal definisikan apakah ada status effect atau tidak**. Jika ada, tentukan minimal 2-3 jenis beserta durasi dan efeknya.

---

## 10. UI/UX Flow & State

- **Screen flow (alur layar) atau state diagram belum ada.** Belum tergambar urutan layar yang dilihat pemain dari awal membuka game sampai selesai bermain. Misalnya: Main Menu → Character Select → Overworld/Map → Pre-Battle (Deck Assembly) → Combat → Post-Wave Reward → ... → Boss Fight → Victory/Game Over → Summary → Main Menu. Alur ini sangat penting untuk programmer agar tahu berapa banyak "layar" yang harus dibuat.
  > **Contoh:** Di *Slay the Spire*, alur layarnya kira-kira: Main Menu → Character Select → Map Screen (pilih node) → Combat Screen → Reward Screen → Map Screen → ... → Boss → Act Clear → Map Screen Act baru → ... → Victory/Defeat → Summary → Main Menu. Setiap "layar" ini adalah scene atau state yang perlu diprogram secara terpisah di Unity.

- **Kontrol input belum didefinisikan.** Bagaimana pemain berinteraksi dengan game? Apakah sepenuhnya menggunakan mouse saja (klik kiri untuk pilih, klik kanan untuk batal)? Apakah ada shortcut keyboard (misalnya tombol 1-5 untuk memilih kartu di tangan, spasi untuk end turn)? Apakah ada dukungan controller/gamepad?
  > **Contoh:** Di *Slay the Spire*, kontrol utama adalah mouse, tapi ada juga keyboard shortcut dan dukungan controller. Di *Into the Breach*, semua bisa dilakukan dengan mouse saja, tapi ada shortcut keyboard untuk undo. Untuk MVP, **mouse-only** sudah cukup, tapi perlu didefinisikan secara eksplisit agar programmer tahu.

- **Elemen HUD (Heads-Up Display) konkret belum didaftarkan.** GDD menyebut "desain minimalis" dan "kartu di tengah bawah", tapi belum mendaftar semua elemen yang perlu tampil di layar saat pertarungan. Misalnya: di mana HP bar pemain ditampilkan? Di mana tombol "End Turn"? Di mana indikator jumlah aksi tersisa? Di mana draw pile counter (jumlah kartu di tumpukan tarik)? Di mana discard pile counter?
  > **Contoh:** Di *Slay the Spire*, layout HUD combat-nya: HP bar di pojok kiri bawah, energi di kiri bawah (dekat kartu), kartu di tangan di tengah bawah, draw pile di pojok kiri bawah layar, discard pile di pojok kanan bawah, tombol End Turn di pojok kanan tengah layar, info musuh (HP + intent) di atas kepala setiap musuh. **Buat wireframe atau daftar lengkap** setiap elemen HUD yang perlu ada di layar.

---

## 11. Visual Art & Ambience

> Berdasarkan tema narasi di Dokumen Narasi, dunia game ini berlatar **dunia yang hancur dan dipenuhi keputusasaan**, dengan konflik antara manusia, malaikat, dan malaikat jatuh. Menara raksasa yang menembus langit menjadi simbol sentral, dan ada dualitas antara **cahaya suci** dan **kegelapan sihir**. Visual art harus mencerminkan atmosfer ini.

- **Palet warna (color palette) belum didefinisikan secara konkret.** GDD menyebut "kontras warna mencolok" dan "perspektif isometric elegan", tapi belum ada definisi palet warna spesifik. Warna apa yang mendominasi dunia game? Warna apa untuk elemen UI? Warna apa untuk membedakan elemen suci vs elemen gelap (sesuai narasi)?
  > **Contoh:** Di *Hades* (roguelike dengan narasi mitologi), setiap biome punya palet warna yang khas: Tartarus berwarna hijau gelap, Asphodel berwarna oranye/lava, Elysium berwarna biru/emas, Styx berwarna merah/hitam. Untuk game ini yang bertema pemberontakan terhadap surga, pertimbangkan: palet gelap (abu-abu, hitam, biru tua) untuk dunia dasar yang hancur. Aksen emas/putih untuk elemen suci (malaikat, darah suci). Aksen ungu/merah gelap untuk kekuatan Y (malaikat jatuh). Warna merah terang untuk telegraph bahaya.

- **Art style belum ditentukan secara detail.** GDD menyebut "diinspirasi oleh Arco" dan "isometric elegan", tapi belum menjelaskan apakah art style-nya pixel art, hand-drawn, 3D rendered ke 2D, low-poly, atau gaya lain. Keputusan ini memengaruhi seluruh pipeline produksi aset visual.
  > **Contoh:** *Arco* (referensi game ini) menggunakan gaya pixel art dengan palet warna terbatas dan animasi sprite yang halus. *Hades* menggunakan hand-drawn 2D art dengan animasi frame-by-frame yang sangat fluid. *Into the Breach* menggunakan pixel art isometric yang clean dan readable. Untuk MVP, **pixel art isometric** (seperti Arco) kemungkinan paling realistis untuk tim kecil karena lebih cepat diproduksi.

- **Desain environment (lingkungan) belum ada.** Berdasarkan narasi, ada beberapa lokasi potensial: reruntuhan dunia yang hancur, bagian-bagian menara raksasa, dataran pertempuran antara manusia dan malaikat, dan akhirnya puncak menara yang mendekati surga. Tapi belum ada yang mendefinisikan seperti apa penampilan visual lokasi-lokasi ini.
  > **Contoh:** Di *Hades*, setiap biome punya tileset (kumpulan gambar ubin) dan background art yang berbeda total. Tartarus punya lantai batu gelap dan dinding kuil Yunani. Asphodel punya lantai lava dan tulang raksasa. Untuk MVP, **minimal definisikan 1-2 tileset environment** (misalnya: "reruntuhan kota" untuk gelombang awal dan "interior menara" untuk gelombang lanjut).

- **Desain karakter belum ada.** Belum ada concept art atau deskripsi visual untuk: pemain (X), musuh-musuh biasa, mini-boss, dan Y (Malaikat Jatuh). Ini termasuk siluet, proporsi tubuh, warna dominan, dan elemen desain khas masing-masing.
  > **Contoh:** Di *Slay the Spire*, setiap karakter pemain punya siluet yang sangat berbeda dan mudah dikenali: Ironclad berbaju besi merah, Silent berkerudung hijau, Defect robot biru, Watcher berjubah ungu. Musuh-musuh juga punya desain khas per Act. Untuk MVP, **minimal butuh concept art atau sprite sheet untuk: 1 pemain, 3 tipe musuh biasa, dan 1 boss**.

- **Desain kartu belum ada.** Belum ada template visual untuk tampilan kartu di tangan pemain. Bagaimana layout kartu? Di mana nama kartu ditampilkan? Di mana cost? Di mana ilustrasi kartu? Apakah ada border berwarna untuk membedakan tipe kartu?
  > **Contoh:** Di *Slay the Spire*, desain kartu terdiri dari: ilustrasi di bagian atas, nama di tengah, cost (angka energi) di pojok kiri atas, deskripsi efek di bawah, dan border yang berbeda warna per tipe (merah untuk Attack, hijau untuk Skill, biru untuk Power). Upgrade kartu ditandai dengan efek visual "shiny" di border. Untuk MVP, **buat template kartu minimal** dengan elemen: ilustrasi (boleh placeholder), nama, cost, dan deskripsi efek.

- **Efek visual (VFX) untuk serangan, dodge, dan damage belum didefinisikan.** Apa yang terlihat di layar saat pemain menyerang? Saat musuh menyerang? Saat pemain berhasil dodge? Saat pemain atau musuh menerima damage? Efek visual ini sangat penting untuk memberikan *game feel* (sensasi bermain) yang memuaskan.
  > **Contoh:** Di *Into the Breach*, serangan ditampilkan sebagai animasi pendek (proyektil terbang, ledakan di ubin target, angka damage muncul). Dodge terasa karena unit berpindah posisi dan serangan musuh menghantam ubin kosong. Di *Hades*, setiap serangan punya *hit flash* (kilatan saat mengenai musuh) dan *screen shake* (layar bergetar). Untuk MVP, **definisikan minimal: animasi serangan sederhana, efek damage angka (floating number), dan indikator dodge berhasil**.

---

## 12. Audio

> Audio harus merujuk ke tema narasi di Dokumen Narasi: dunia hancur yang gelap, pemberontakan terhadap surga, dualitas suci vs gelap, menara raksasa, dan ilusi yang menutupi kebenaran.

**Tidak ada pembahasan audio sama sekali di GDD.** Berikut adalah daftar kebutuhan audio yang harus didefinisikan dan disiapkan untuk working MVP:

### 12.1. Musik Latar (Background Music / BGM)

Musik adalah elemen paling krusial untuk membangun atmosfer. Minimal dibutuhkan track-track berikut:

- **BGM Main Menu:** Musik yang didengar pemain pertama kali saat membuka game. Harus mencerminkan kesan pertama dunia game: gelap, penuh misteri, tapi ada secercah keagungan (menara yang menjulang ke langit).
  > **Contoh:** Di *Darkest Dungeon*, BGM main menu langsung menyajikan paduan suara (*choir*) yang suram dan organ gereja, langsung membangun suasana "kegelapan religius". Di *Slay the Spire*, BGM main menu tenang dan misterius. Untuk game ini yang bertema pemberontakan terhadap surga, campuran **orkestra suram + choir sayup** bisa sangat cocok.

- **BGM Combat (pertarungan biasa):** Musik yang meningkatkan ketegangan saat pertarungan berlangsung. Harus mendukung ritme turn-based (tidak terlalu cepat, tapi intens).
  > **Contoh:** Di *Into the Breach*, BGM combat bergaya ambient elektronik yang menekan. Di *Darkest Dungeon*, BGM combat berat dan menekan.

- **BGM Boss Fight:** Musik yang lebih intens dari combat biasa untuk pertarungan melawan boss atau mini-boss. Harus membuat pemain merasa sedang menghadapi ancaman besar.
  > **Contoh:** Di *Hades*, setiap boss punya tema musik unik yang ikonik (seperti tema Megaera atau tema Hades). Di *Slay the Spire*, BGM boss fight lebih intens dari BGM combat biasa.

- **BGM Game Over / Death:** Musik singkat yang menemani layar Game Over. Harus menyampaikan rasa "kalah" tapi juga mendorong pemain untuk mencoba lagi.
  > **Contoh:** Di *Hades*, setelah mati ada transisi musik yang halus dari "kekalahan" ke musik House of Hades yang lebih tenang.

- **BGM Victory / Post-Run:** Musik yang menemani layar kemenangan atau ringkasan run.

### 12.2. Sound Effects (SFX) - Combat

Efek suara yang diperlukan saat pertarungan:

- **SFX memainkan kartu:** Suara saat pemain memilih dan memainkan kartu dari tangan (misalnya suara "swish" kertas atau energi yang dilepaskan).
- **SFX serangan mengenai target (hit):** Suara saat serangan berhasil mengenai musuh atau pemain. Harus terasa "berdampak" (*impactful*).
- **SFX serangan meleset / dodge berhasil:** Suara saat pemain berhasil menghindar dari serangan musuh, memberikan feedback positif bahwa dodge berhasil.
- **SFX menerima damage:** Suara saat pemain atau musuh menerima damage.
- **SFX kematian musuh:** Suara saat musuh dikalahkan.
- **SFX kematian pemain / game over:** Suara saat pemain kehabisan HP.
- **SFX gerakan/langkah kaki:** Suara saat pemain atau musuh bergerak di grid.
- **SFX menggunakan consumable item:** Suara saat pemain menggunakan item (misalnya suara minum potion, suara kristal pecah).

### 12.3. Sound Effects (SFX) - UI

Efek suara untuk interaksi antarmuka:

- **SFX hover tombol:** Suara saat kursor melewati tombol/elemen interaktif.
- **SFX klik tombol:** Suara saat pemain mengklik tombol.
- **SFX memilih kartu di tangan:** Suara saat pemain meng-highlight/memilih kartu.
- **SFX membuka/menutup menu:** Suara transisi menu.
- **SFX end turn:** Suara saat pemain mengakhiri giliran.
- **SFX menarik kartu (draw):** Suara saat kartu baru masuk ke tangan dari draw pile.
- **SFX discard:** Suara saat kartu dibuang ke discard pile.
- **SFX reward / kartu baru didapat:** Suara saat pemain mendapatkan kartu baru setelah pertarungan.

### 12.4. Sound Effects (SFX) - Ambience

Suara lingkungan yang mengisi latar belakang untuk memperkuat atmosfer:

- **Ambience dunia hancur:** Suara angin bertiup di reruntuhan, suara puing jatuh jauh, suara api menyala kecil. Mendukung narasi dunia yang penuh keputusasaan.
- **Ambience menara:** Suara angin di ketinggian, suara batu bergemuruh, suara mesin/sihir yang menjaga menara. Semakin tinggi menara, semakin intens ambience-nya.
- **Ambience mendekati surga:** Suara choir (paduan suara) sayup yang semakin keras, suara bel, suara energi suci. Untuk area yang dekat gerbang surga.
  > **Contoh:** Di *Darkest Dungeon*, setiap area punya ambience unik: Ruins punya suara angin dan batu runtuh, Weald punya suara hutan gelap dan serangga, Cove punya suara air dan makhluk laut. Di *Hades*, ambience Tartarus punya suara api dan rintihan jauh, berbeda dengan Elysium yang punya suara angin dan ketenangan palsu.

---

## 13. Kekurangan dari Dokumen Narasi Sementara untuk MVP

> Berdasarkan Dokumen Narasi, cerita sudah punya kerangka yang kuat (X sebagai pemimpin, Y sebagai malaikat jatuh, ilusi gelap, 3 ending). Namun untuk **working MVP**, ada beberapa hal yang masih perlu didefinisikan:

- **Nama karakter belum ada.** Karakter utama hanya disebut "X" dan antagonis disebut "Y". Untuk MVP, setidaknya perlu nama sementara (*placeholder*) agar bisa dipakai di dialog, UI, dan aset.
  > **Contoh:** Di *Hades*, protagonis bernama Zagreus dan ini langsung memberi identitas. Di *Darkest Dungeon*, pemain disebut "Heir" (pewaris). Bahkan nama sementara seperti "The Commander" atau "The Rebel" sudah jauh lebih baik dari "X".

- **Bagaimana narasi disampaikan di dalam game belum didefinisikan.** Apakah lewat cutscene sebelum/sesudah run? Lewat teks dialog di antara gelombang? Lewat narasi environment (pemain membaca lore dari objek di dunia game)? Atau lewat narrator yang berbicara saat bermain?
  > **Contoh:** Di *Slay the Spire*, narasi sangat minim dan tersampaikan hanya lewat deskripsi kartu dan event. Di *Hades*, narasi tersampaikan lewat dialog interaktif di hub area dan selama gameplay. Di *Darkest Dungeon*, narasi tersampaikan lewat narrator dan jurnal. Perlu diputuskan metode penyampaian narasi untuk MVP.

- **Konten narasi untuk MVP scope belum ditentukan.** Dari 3 ending yang ada (Kebutaan Mutlak, Penebusan yang Terlambat, Jalan Manusia), apakah MVP hanya mengimplementasikan 1 ending atau ketiganya? Berapa banyak dialog/cutscene yang dibutuhkan?
  > **Contoh:** Di *Hades*, early access dimulai dengan hanya sebagian cerita yang tersedia (ending belum ada). Narasi ditambahkan secara bertahap. Untuk MVP, **cukup fokus pada 1 jalur cerita** (misalnya "Jalan Manusia" sebagai default ending MVP) dan tambahkan jalur lain di iterasi berikutnya.

---

## 14. Scope MVP Eksplisit

- **GDD menyebut "memoles fungsionalitas Combat Loop dan satu set komplit gelombang musuh" sebagai target MVP, tapi batasan konkretnya belum diperjelas.** Tanpa batasan yang spesifik, tim akan kesulitan menentukan kapan MVP "sudah selesai". Berikut hal-hal yang harus diputuskan:

  - **Berapa tipe musuh minimum yang harus ada di MVP?**
    > **Contoh:** *Slay the Spire* early access sudah memiliki puluhan musuh. Tapi untuk MVP internal, **3-5 tipe musuh biasa + 1-2 mini-boss + 1 boss** sudah cukup untuk menguji combat loop.

  - **Berapa jumlah kartu minimum di pool (kumpulan kartu yang tersedia)?**
    > **Contoh:** *Slay the Spire* Ironclad punya 77 kartu unik di pool. Untuk MVP, **15-20 kartu unik** sudah cukup: ~8 serangan, ~5 pertahanan/utilitas, ~2-3 buff/debuff, dan ~2 kartu spesial.

  - **Berapa gelombang minimum per sesi bermain?**
    > **Contoh:** Untuk MVP, **5-8 gelombang + 1 boss fight** sudah cukup untuk memberi pengalaman loop yang utuh (sekitar 15-30 menit per sesi).

  - **Fitur mana yang masuk MVP dan mana yang ditunda?** Perlu didaftarkan secara eksplisit. Misalnya:
    - ✅ MVP: combat loop, 1 set kartu, beberapa tipe musuh, 1 boss, basic HUD, basic SFX
    - ⏳ Ditunda: ascension system, checkpoint system, blind/cursed abilities, voice over, multiple endings
    > **Contoh:** *Hades* early access (versi pertama yang dirilis) sudah punya 1 biome lengkap, 1 boss, beberapa senjata, dan narasi dasar. Fitur seperti biome tambahan, boss baru, dan ending ditambahkan secara bertahap selama early access.

---

# BAGIAN 2: Kebutuhan Iterasi Berikutnya (Eksplorasi, Roguelike Loop, Meta-Progression)

> Ide-ide di bawah ini adalah fitur tingkat lanjut yang ditarik keluar dari scope MVP agar MVP bisa diselesaikan dengan cepat oleh tim pemula. Jangan diabaikan, namun simpan untuk dikerjakan setelah MVP 1 map statis dan wave selesai.

## A. Variasi Arena
- **Bentuk arena belum dijelaskan.** Apakah arena selalu berbentuk persegi, persegi panjang, atau bisa berubah-ubah di setiap gelombang? Apakah ada ruangan yang berbentuk unik (misalnya huruf L atau lorong sempit)?
  > **Contoh:** Di *Slay the Spire* (deck-builder roguelike), tidak ada arena spasial karena pertarungannya tidak berbasis posisi. Namun di *Into the Breach*, arena selalu 8×8 persegi, tapi konten di dalamnya (gedung, gunung, air) yang berubah-ubah. Di *Arco* (referensi visual game ini), arena bervariasi bentuknya per encounter.

## B. Scaling Musuh Lanjutan
- **Jumlah gelombang per encounter atau per run belum ditentukan.** Berapa gelombang yang harus dilewati pemain sebelum run dianggap selesai?
  > **Contoh:** Di *Slay the Spire*, 1 run penuh terdiri dari 3 Act, masing-masing Act berisi sekitar 15 node (campuran pertarungan biasa, elite, event, toko, dan istirahat), ditutup oleh 1 boss. Di *Into the Breach*, 1 run terdiri dari 4 pulau dengan masing-masing 2-5 misi. Untuk MVP, **cukup definisikan 1 chapter/act pendek** (misalnya 5-8 gelombang + 1 boss fight) agar bisa diuji coba sebagai loop lengkap.

- **Scaling (peningkatan kekuatan musuh) belum dibahas.** Apakah musuh di gelombang berikutnya menjadi lebih kuat? Jika iya, dengan cara apa? Apakah HP dan damage mereka naik? Apakah muncul tipe musuh baru yang lebih sulit?
  > **Contoh:** Di *Slay the Spire*, scaling dilakukan melalui Act: musuh di Act 2 punya HP dan damage lebih tinggi dari Act 1, plus kemampuan yang lebih kompleks. Di *Hades* (action roguelike), musuh di biome berikutnya punya HP lebih tinggi dan pola serangan baru. Perlu ditentukan apakah scaling bersifat **linear** (HP/damage naik bertahap) atau **stepped** (naik signifikan di chapter/stage tertentu).

## C. Roguelike Loop & Ekonomi
- **Cara mendapatkan consumable item belum dijelaskan.** Apakah item didapat sebagai drop (jatuh) dari musuh yang dikalahkan? Apakah item muncul sebagai reward setelah gelombang selesai? Atau apakah pemain sudah membawa item sejak awal run?
  > **Contoh:** Di *Slay the Spire*, potion bisa didapat sebagai reward setelah mengalahkan musuh biasa (reward acak), dibeli di toko, atau didapat dari event acak. Di *Hades*, item (*boon*) didapat dari interaksi dengan dewa di setiap ruangan. Perlu diputuskan dari mana pemain mendapat consumable dalam game ini.

- **Mata uang dan toko belum disebut.** Apakah ada sistem mata uang dalam run (untuk membeli kartu/item di toko), atau hanya mengandalkan reward drop?
  > **Contoh:** Di *Slay the Spire*, ada Gold yang didapat dari setiap pertarungan dan digunakan untuk membeli kartu, relic, atau potion di toko. Di *Hades*, ada Charon's Obol untuk beli item di toko dalam run, dan Darkness sebagai mata uang permanen. Apakah game ini punya sistem toko dalam run?

- **Aturan deck setelah reward "Pilih 1 dari 3" belum jelas.** GDD menyebut pemain memilih 1 kartu baru dari 3 opsi setelah gelombang selesai, tapi deck dibatasi 15 kartu. Yang belum jelas: apakah kartu baru **menambah** deck (melebihi 15)? Atau pemain harus **menggantikan** salah satu kartu lama dengan kartu baru agar tetap 15?
  > **Contoh:** Di *Slay the Spire*, deck tidak punya batas atas. Kartu baru selalu menambah deck (bisa membengkak sampai 30-40 kartu). Pemain bisa menghapus kartu di event atau toko untuk mengecilkan deck. Tapi game ini membatasi deck di 15 kartu, sehingga harus ada mekanik penggantian yang jelas.

- **Struktur satu "run" belum didefinisikan secara rinci.** Berapa encounter (pertarungan) yang harus dilewati dalam satu run penuh? Apakah perjalanannya linear (lurus, pertarungan demi pertarungan) atau ada percabangan (pemain memilih jalur)?
  > **Contoh:** Di *Slay the Spire*, satu run terdiri dari 3 Act. Setiap Act memiliki peta bercabang dengan ~15 node. Pemain memilih jalur mana yang diambil, setiap jalur punya komposisi node yang berbeda (pertarungan, elite, toko, istirahat, event). Di *Hades*, satu run terdiri dari 4 biome (Tartarus → Asphodel → Elysium → Styx), masing-masing berisi sekitar 10-15 kamar yang dilalui secara linear dengan sesekali pilihan jalur. Perlu diputuskan apakah game ini menggunakan peta bercabang atau jalur linear.

## D. Meta-Progression
- **Kondisi menang satu run belum didefinisikan.** Apa yang terjadi setelah pemain berhasil melewati gelombang terakhir? Apakah ada cutscene? Apakah langsung kembali ke menu utama? Apakah ada layar ringkasan statistik (berapa damage total, berapa kartu dimainkan, berapa lama run berlangsung)?
  > **Contoh:** Di *Slay the Spire*, setelah mengalahkan boss akhir di Act 3, muncul layar kemenangan dengan ringkasan run (skor, kartu yang dikumpulkan, relic, waktu bermain). Setelah itu pemain kembali ke menu utama dan tingkat Ascension naik. Di *Hades*, setiap kali pemain berhasil keluar dari Underworld, ada *dialogue* cerita yang berkembang. Perlu didefinisikan apa yang terjadi setelah kemenangan.

- **Detail kondisi kalah masih minim.** GDD hanya menyebut "HP = 0 → Game Over". Tapi setelah Game Over, apa yang terjadi? GDD sudah menyebut adanya checkpoint system (3 checkpoint), tapi belum jelas bagaimana interaksinya: apakah pemain respawn di checkpoint terakhir? Jika tidak ada checkpoint, apakah langsung kembali ke menu utama? Apakah ada layar "Game Over" yang menampilkan progress run yang telah dilakukan?
  > **Contoh:** Di *Slay the Spire*, setelah mati muncul layar ringkasan run (kartu, relic, lantai terakhir, skor), lalu pemain kembali ke menu untuk memulai run baru. Di *Hades*, setelah mati pemain kembali ke House of Hades (hub area) dan bisa berinteraksi dengan NPC sebelum memulai run baru. Perlu didefinisikan flow setelah mati, termasuk interaksi dengan sistem checkpoint.

- **Belum ada penjelasan apa yang "dibawa pulang" setelah mati.** GDD sudah menyebut poin permanen dan starter upgrade. Tapi poin apa yang dikumpulkan? Bagaimana cara menghitungnya? Apakah berdasarkan gelombang yang berhasil dilewati, jumlah musuh yang dikalahkan, atau kombinasi keduanya?
  > **Contoh:** Di *Hades*, setelah mati pemain membawa pulang "Darkness" (mata uang untuk upgrade permanen) dan item cerita. Jumlah Darkness didapat dari kamar-kamar yang berhasil diselesaikan selama run. Di *Slay the Spire*, tidak ada mata uang permanen yang dibawa pulang (hanya membuka kartu/relic baru untuk pool). Perlu diputuskan sistem "reward kematian" yang spesifik.

- **Trigger pertumbuhan slot inventori belum didefinisikan.** GDD menyebut "slot bertumbuh hingga 3", tapi tidak menjelaskan kapan slot baru terbuka. Apakah terbuka setelah mengalahkan boss tertentu? Setelah mencapai gelombang tertentu? Atau bisa dibeli dengan poin dari run sebelumnya (meta-progression)?
  > **Contoh:** Di *Slay the Spire*, pemain selalu punya 3 slot potion dari awal (bisa ditambah menjadi 5 dengan relic tertentu). Di *Hades*, slot untuk keepsake bertambah seiring pemain membuka konten. Perlu diputuskan mekanisme pertumbuhan slot ini.

## E. Audio Visual Naratif Mendalam
- **Efek visual untuk ilusi narasi belum dibahas.** Berdasarkan narasi, ada elemen penting: malaikat terlihat sebagai iblis di mata pemain karena sihir gelap Y, dan ada "petunjuk" kebenaran (cahaya keemasan, darah suci yang menyuburkan tanah). Apakah elemen visual ini muncul di gameplay atau hanya di cutscene?
  > **Contoh:** Di *Hades*, narasi tersampaikan melalui campuran gameplay dan dialogue interaktif, bukan hanya cutscene. Di *Darkest Dungeon*, efek visual kegilaan dan stress tersampaikan langsung di combat. Perlu diputuskan apakah elemen ilusi narasi ini **terlihat di combat** (misalnya: musuh malaikat kadang "berkedip" menunjukkan wujud asli suci mereka) atau hanya dikisahkan di cutscene/dialog.

- **BGM Exploration / Overworld:** Musik saat pemain menjelajahi map atau memilih rute. Lebih tenang dari musik combat, tapi tetap mempertahankan ketegangan.
  > **Contoh:** Di *Hades*, setiap biome punya BGM eksplorasi yang berbeda tapi semuanya bersemangat. Di *Slay the Spire*, BGM map screen tenang dan kontemplatif.

- **Voice over atau narrator (Narasi Audio):** Apakah ada suara narrator yang mengomentari aksi pemain atau menceritakan lore? Ini sangat memperkuat pengalaman narasi.
  > **Contoh:** Di *Darkest Dungeon*, narrator (Wayne June) mengomentari hampir setiap aksi pemain dan ini menjadi salah satu elemen paling ikonik dari game tersebut. Di *Hades*, narrator (Zagreus) dan karakter lain terus-menerus berbicara, membuat dunia terasa hidup. Untuk MVP, ini bersifat opsional, tapi jika diimplementasikan bisa menjadi **pembeda yang sangat kuat**.

- **Hubungan narasi dengan gameplay loop belum terhubung.** Cerita menyebut 3 ending yang berbeda berdasarkan pilihan pemain, tapi belum jelas bagaimana pilihan ini direpresentasikan di gameplay. Apakah ada moral choice system (sistem pilihan moral)? Apakah ending bergantung pada seberapa sering pemain menggunakan kekuatan Y vs kekuatan sendiri?
  > **Contoh:** Di *Hades*, setiap run yang berhasil membuka progres cerita baru. Di *Undertale*, ending ditentukan oleh apakah pemain membunuh atau mengampuni musuh. Mekanisme ini harus dihubungkan ke gameplay agar cerita tidak terasa terpisah.

- **"Petunjuk kebenaran" dalam gameplay belum dihubungkan ke mekanik.** Narasi menyebut bahwa prajurit bisa melihat "cahaya keemasan" dari wujud malaikat dan darah suci yang menyuburkan tanah. Apakah elemen-elemen ini muncul sebagai mekanik gameplay (misalnya: darah suci sebagai item heal, cahaya keemasan sebagai event yang memberi buff), atau hanya sebagai lore/flavor text?
  > **Contoh:** Di *Hades*, elemen cerita langsung menjadi mekanik: Boon dari dewa Olympus = pilihan skill. Hubungan Zagreus dengan NPC = bonus buff. Di *Darkest Dungeon*, elemen narasi seperti "kegilaan" langsung menjadi mekanik (sistem stress). Menghubungkan narasi ke mekanik membuat cerita terasa bermakna dan bukan sekadar hiasan.
