# CHANGELOG

Changelog berfungsi sebagai catatan riwayat perubahan untuk proyek ini. Tujuan dari changelog ini adalah untuk melacak semua modifikasi, penambahan, dan perbaikan yang dilakukan secara terstruktur agar mudah dipahami oleh seluruh tim.

## Kategori Perubahan

Setiap entri log perubahan harus dikategorikan berdasarkan jenis file atau area yang diubah. Kategori-kategori tersebut terbagi menjadi:

*   **Guideline: <judul>**: Perubahan atau penambahan pada dokumen panduan (guideline).
*   **PRD**: Perubahan pada dokumen Product Requirements Document (PRD).
*   **Design System**: Pembaruan atau modifikasi yang berkaitan dengan Design System.
*   **System Design**: Perubahan pada arsitektur atau dokumen System Design.
*   **Development Planning: <nomor>**: Pembaruan yang terkait dengan perencanaan pengembangan (Development Planning).
*   **Ticket: <nomor>**: Perbaikan atau penambahan fitur yang merujuk pada tiket tertentu.
*   **Prototype: <judul>**: Pembaruan atau pembuatan prototipe desain/aplikasi.
*   **Implementation**: Implementasi coding pada folder yang dituju (harus menyertakan penjelasan detail mengenai apa saja yang diubah di dalam folder project).

Khusus untuk kategori **Implementation**, penjelasan pada kolom "Perubahan" **wajib** mencantumkan salah satu tag status spesifik berikut di awal kalimatnya:
*   `[Added]`: Penambahan fitur, dokumen, atau konfigurasi baru.
*   `[Changed]`: Modifikasi atau penyesuaian pada fungsionalitas, logika, atau dokumen yang sudah ada.
*   `[Fixed]`: Perbaikan atas suatu kelemahan, galat (*bug*), atau kesalahan *syntax*.
*   `[Removed]`: Penghapusan fitur, pedoman, atau kode usang (*deprecated*).

## Format Changelog

Setiap penambahan log versi terbaru **WAJIB MUTLAK** diletakkan di baris **PALING ATAS** daftar, tepat di bawah judul "Log Perubahan" (urutan *descending* / *reverse-chronological*). AI **DILARANG KERAS** menambahkan log baru di baris terbawah. Anda **WAJIB** mencatat SEMUA kategori perubahan secara disiplin, bukan hanya modifikasi kode (`Implementation`).

> ⚠️ **Satu-satunya Format yang Sah (Single Source of Truth):**
> AI Agent dilarang mengarang format changelog sendiri. Anda **WAJIB MUTLAK** menyalin dan mematuhi struktur baku yang terdapat pada berkas referensi berikut:
> `../../global-docs/templates/changelog_entry_template.md`

## Log Perubahan (Pilot Game)

*(⚠️ PERHATIAN AI AGENT: TAMBAHKAN ENTRI LOG BARU ANDA TEPAT DI BAWAH BARIS INI. JANGAN DI PALING BAWAH DOKUMEN!)*

### [2026-10-03 22:12:00] - Game Design: Adopt GDD 0.1 Cost System (1 Turn = 1 Card) & Eliminate Action Points (AP)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "opsi a, perbaiki juga cards.md dan cards.pdf serta semua document yang pakai ap"
- **Perubahan:**
  - `[Changed]` Menyelaraskan seluruh 53 kartu pada `pilot-game-team-docs/01_game_design/cards.md` dengan **Cost System GDD 0.1 (§4.1: 1 Turn = 1 Kartu)** dan menghapus sistem Action Points (AP).
  - `[Changed]` Menyeimbangkan kartu-kartu berdaya rusak tinggi (*finisher/mass AoE*) menggunakan mekanisme trade-off (*Exhaust*, *Fatigue/Recoil*, *HP Sacrifice*, *Status Prerequisite*) alih-alih biaya AP multi-poin.
  - `[Changed]` Mengganti kolom AP menjadi `Cost / Sifat Khusus` pada *Master Balance Matrix* dan mendefinisikan *1 Turn Budget Math*.
  - `[Changed]` Memperbarui berkas [cards.pdf](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-team-docs/05_deliverables/cards.pdf) dan `cards.html` di `05_deliverables` untuk merefleksikan sistem murni 1 Turn = 1 Kartu.
  - `[Changed]` Memperbarui panduan manual UI pada `GUIDE-TICKET-06.md` dan `GUIDE-TICKET-06B.md` dengan menghapus referensi teks AP.
- **Path File:**
  - `pilot-game-team-docs/01_game_design/cards.md`
  - `pilot-game-team-docs/05_deliverables/cards.html`
  - `pilot-game-team-docs/05_deliverables/cards.pdf`
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-06.md`
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-06B.md`
  - `nodes/pilot-game/CHANGELOG.md`


### [2026-10-03 21:44:00] - Game Design: Unified Master Card Library and Mathematical Balance Matrix
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "gabung cards.md dan other-cards.md , jadikan satu, dan samakan boundariesnya, berikan perhitungan sederhananya juga untuk yang belum ada. buatlah semua kartu setara"
- **Perubahan:**
  - `[Changed]` Menggabungkan seluruh 14 Kartu Inti Nabu dan seluruh set kartu tim (53 kartu unik) ke dalam `pilot-game-team-docs/01_game_design/cards.md` sebagai *Single Source of Truth (SSOT)*.
  - `[Changed]` Menyelaraskan seluruh batas (boundaries) dan skema parameter kartu mengikuti properti runtime C# `CardData.cs` (`Id`, `Name`, `ActionType`, `TargetArea`, `PhaseRestriction`, `EnergyCost`, `BaseDamage`, `BaseShield`, `Range`, `AreaRadius`, `InflictedStatus`, `StatusDuration`).
  - `[Added]` Menetapkan rumus anggaran nilai baku matematis (*AP Value Budget Math*) untuk kartu 0 AP, 1 AP, 2 AP, dan 3 AP agar seluruh 53 kartu memiliki rasio kekuatan yang setara dan adil. Mengganti semua variabel prototipe ($X, Y, Z$) dengan nilai numerik konkret, formula scaling, serta tabel matriks keseimbangan master (*Master Balance Matrix*).
  - `[Changed]` Memperbarui `other-cards.md` menjadi penunjuk arsip yang merujuk ke `cards.md`.
- **Path File:**
  - `pilot-game-team-docs/01_game_design/cards.md`
  - `pilot-game-team-docs/01_game_design/other-cards.md`
  - `nodes/pilot-game/CHANGELOG.md`


### [2026-10-03 21:38:00] - Game Design: Consolidation and De-duplication of Team Card Submissions
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "berikut pdf berisi seluruh kartu dari anggota anggota lain. buatlah other-cards.md di 01_game_design" & keputusan de-duplikasi kartu
- **Perubahan:**
  - `[Added]` Membuat dokumen `pilot-game-team-docs/01_game_design/other-cards.md` yang merangkum seluruh 44 ide kartu dari 4 dokumen submission anggota tim.
  - `[Changed]` Menyelaraskan dan mengeliminasi kartu duplikat: membedakan `Decoy` (taunt bait + reposition 3 tile) dan `Clone` (klon peniru serangan), menghapus `Duplicate` & `Foothold`, menggabungkan `Shield Up` ke `Brace`, merevisi `Serrated Dagger` sebagai finisher vs Bleed, `Machete Cleave` sebagai AoE Arc 3 ubin, `Immobilize Root` sebagai area hazard 2x2 (*Tangled Overgrowth*), dan menggabungkan `Conjure Cover` + `Earthen Wall` menjadi `Earthen Bulwark`.
  - `[Changed]` Memperbarui `cards.md` dan `GUIDE-TICKET-02B.md` sesuai spesifikasi baru `Decoy` dan `Clone`.
- **Path File:**
  - `pilot-game-team-docs/01_game_design/cards.md`
  - `pilot-game-team-docs/01_game_design/other-cards.md`
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-02B.md`
  - `nodes/pilot-game/CHANGELOG.md`

### [2026-10-03 19:02:00] - Guideline: English Localization for All Asset Description Fields in Manual Guides
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "ganti bahasa inggris untuk deskripsi assetnya @[/Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides]"
- **Perubahan:**
  - `[Changed]` Menerjemahkan seluruh teks field `Description` pada ScriptableObject kartu, musuh, dan item consumable di dalam `GUIDE-TICKET-02.md`, `GUIDE-TICKET-02B.md`, `GUIDE-TICKET-02C.md`, dan `GUIDE-TICKET-06.md` menjadi 100% Bahasa Inggris, memastikan seluruh data teks runtime game seragam dalam Bahasa Inggris.
- **Path File:**
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-02B.md`
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-02C.md`
  - `nodes/pilot-game/manual-guides/GUIDE-TICKET-06.md`
  - `nodes/pilot-game/CHANGELOG.md`

### [2026-10-03 18:55:00] - Guideline: Standardize Sample Entities in Manual Guides to Throwing Blade, Tattered Conscript, and Elixir of Life
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `Tubbies Pilot Game` & `pilot-game-ai-orchestrator`
- **Konteks:** "ganti contoh@[/Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides] ke throwing blade dan tattered conscript dan elixir of life"
- **Perubahan:**
  - `[Changed]` Mengganti contoh pembuatan kartu sample di `GUIDE-TICKET-02.md`, kriteria `TICKET-02.md`, dan aset sprite `Assets/Art/Sprites/` dari Page Cutter menjadi `Card_ThrowingBlade.asset` (*Throwing Blade*), bersanding dengan `Enemy_TatteredConscript.asset` (*Tattered Conscript*) dan `Item_ElixirOfLife.asset` (*Elixir of Life*).
  - `[Added]` Menghasilkan dan menyertakan aset pixel art 64 PPU `Card_ThrowingBlade_Art.jpg` di folder `Assets/Art/Sprites/`.
- **Path File:**
  - `pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/CHANGELOG.md`
  - `Tubbies Pilot Game/Assets/Art/Sprites/Card_ThrowingBlade_Art.jpg`

### [2026-10-03 18:46:00] - Implementation: ScriptableObject Data Templates for Cards, Enemies, and Consumables
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `Tubbies Pilot Game` & `pilot-game-ai-orchestrator`
- **Konteks:** "commit push @[/Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/Tubbies Pilot Game] , yang berbeda, timpa milik @[/Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir] dengan @[/Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/Tubbies Pilot Game] jika ada yang berbeda."
- **Perubahan:**
  - `[Added]` Mengimplementasikan ScriptableObject data templates `CardData.cs`, `EnemyData.cs`, dan `ConsumableData.cs` di namespace `PilotGame.Cards`.
  - `[Added]` Menyiapkan struktur folder `Assets/ScriptableObjects/Cards/`, `Enemies/`, `Consumables/` serta aset placeholder sprite pixel art 64 PPU di `Assets/Art/Sprites/`.
  - `[Changed]` Menyelaraskan seluruh spesifikasi field C# pada `GUIDE-TICKET-02.md`, `GUIDE-TICKET-02B.md`, `TICKET-02.md`, dan `development-planning.md` dengan implementasi C# di `Tubbies Pilot Game` (`TargetArea`, `energyCost`, `CardDeck`, `Boss` hierarchy, `UX & VFX` headers).
- **Path File:**
  - `Tubbies Pilot Game/Assets/Scripts/Cards/CardData.cs`
  - `Tubbies Pilot Game/Assets/Scripts/Cards/EnemyData.cs`
  - `Tubbies Pilot Game/Assets/Scripts/Cards/ConsumableData.cs`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-02B.md`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/docs/development-planning.md`
  - `pilot-game-ai-orchestrator/nodes/pilot-game/CHANGELOG.md`
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "public GameObject VFXPrefab; public AudioClip SFX; tidak ada di enemy?"
- **Perubahan:** `[Added]` Menambahkan field `GameObject VFXPrefab` dan `AudioClip SFX` ke dalam `EnemyData.cs` serta menyeragamkannya pada `ConsumableData.cs` dan `CardData.cs` di bawah header `[Header("UX & Visual FX")]` pada `GUIDE-TICKET-02.md` dan kriteria penerimaan `TICKET-02.md`.
- **Path File:** `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`, `nodes/pilot-game/tickets/TICKET-02.md`

### [2026-10-03 18:29:00] - Guideline: Standardize Clean OOP Field Names (Id, Name, Description) across All Data Templates
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "okelah paramsnya, tapi kan ini field class, pasti ya CardName.id pemakaiannya bukan?"
- **Perubahan:** `[Changed]` Menghapus redundansi nama kelas pada variabel (*class stuttering*) dan menstandardisasi seluruh ScriptableObject data (`CardData`, `EnemyData`, `ConsumableData`, `BossData`, `EquipmentData`) menjadi field yang bersih dan ringkas (`Id`, `Name`, `Description`, `Art`/`Sprite`/`Icon`/`Portrait`) di seluruh manual guides (`GUIDE-TICKET-02.md`, `02B`, `02C`, `06`, `09B`, `10`, `11`, `11B`, `12`, `14B`) dan tiket (`TICKET-02.md`).
- **Path File:** `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-02B.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-02C.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-06.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-09B.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-10.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-11.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-11B.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-12.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-14B.md`, `nodes/pilot-game/tickets/TICKET-02.md`

### [2026-10-03 18:23:00] - Guideline: Add Description Field to EnemyData ScriptableObject
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "tambah enemy desc"
- **Perubahan:** `[Added]` Menambahkan field `[TextArea(2, 4)] public string Description;` pada kelas `EnemyData.cs` di `GUIDE-TICKET-02.md`, memperkaya panduan langkah pembuatan aset musuh di Unity Editor (`Enemy_TatteredConscript.asset`), serta menyinkronkan kriteria penerimaan pada `TICKET-02.md`.
- **Path File:** `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`, `nodes/pilot-game/tickets/TICKET-02.md`

### [2026-10-03 18:15:00] - Guideline: Add None to StatusEffectType Enum Definition across Orchestrator & Manual Guides
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "saya menambahkan status effect none"
- **Perubahan:** `[Added]` Menambahkan nilai `None` (serta sinkronisasi `Poison` dan `Burn`) pada definisi enum `StatusEffectType` di `GUIDE-TICKET-01.md`, `TICKET-01.md`, `TICKET-03B.md`, serta memperbarui default field `InflictedStatus = StatusEffectType.None` pada `GUIDE-TICKET-02.md` dan kartu netral di `GUIDE-TICKET-02B.md`.
- **Path File:** `nodes/pilot-game/manual-guides/GUIDE-TICKET-01.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-02B.md`, `nodes/pilot-game/tickets/TICKET-01.md`, `nodes/pilot-game/tickets/TICKET-03B.md`

### [2026-10-03 18:04:00] - Guideline: Standardize English Code Syntax & Indonesian Comments across Manual Guides
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "seluruh kode bahasa inggris, yang bahasa indonesia hanya comments"
- **Perubahan:** `[Changed]` Memperbarui seluruh string atribut kode C# (seperti `[Header(...)]`, tipe data, identifier, class, method) di semua manual guides (`GUIDE-TICKET-02.md`, `GUIDE-TICKET-04B.md`, `GUIDE-TICKET-12.md`) menjadi 100% Bahasa Inggris murni, dengan mempertahankan seluruh komentar penjelasan (`//`, `///`, `/* */`) dalam Bahasa Indonesia.
- **Path File:** `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-04B.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-12.md`

### [2026-10-03 17:48:00] - Guideline: Standardize 64 PPU & 64x64 Pixel Art Spec across GDD & Manual Guides
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `pilot-game-ai-orchestrator`
- **Konteks:** "ubah ke 64 semua manual guides dan dokumen"
- **Perubahan:** `[Changed]` Menetapkan dan membakukan resolusi kanvas aset $64 \times 64\text{ px}$ per ubin/karakter serta pengaturan import Unity Pixels Per Unit (PPU) = **64** (*Filter Mode: Point, Compression: None*) pada seluruh dokumen GDD (`GDD-0.1.md`), aturan visual seni (`visual-art-rules.md`), dan panduan manual editor Unity (`GUIDE-TICKET-02.md`, `GUIDE-TICKET-04.md`, `GUIDE-TICKET-05.md`).
- **Path File:** `global-docs/GDDs/GDD-0.1/GDD-0.1.md`, `global-docs/visual-art-rules.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-04.md`, `nodes/pilot-game/manual-guides/GUIDE-TICKET-05.md`

### [2026-10-03 16:54:00] - Implementation: TICKET-01 Shared Data Contracts & Event Bus
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `Tubbies Pilot Game`
- **Konteks:** "Tubbies Pilot Game commit push, saya sudah manual guides. untuk commit message sesuaikan pilot-game-ai-orchestrator dan jangan lupa update changelog"
- **Perubahan:** `[Added]` Mengimplementasikan kontrak tipe data pertempuran (`CombatTypes.cs`), payload struct zero-allocation (`CombatPayloads.cs`), static event bus terpusat (`CombatEvents.cs`), serta Assembly Definition (`PilotGame.Core.asmdef`) pada codebase Unity `Tubbies Pilot Game`. `[Removed]` Membersihkan aset sprite usang (`variant.png`) dan memperbarui scene dasar (`SampleScene.unity`).
- **Path File:** `Assets/Scripts/Core/Data/CombatTypes.cs`, `Assets/Scripts/Core/Data/CombatPayloads.cs`, `Assets/Scripts/Core/Events/CombatEvents.cs`, `Assets/Scripts/Core/PilotGame.Core.asmdef`, `Assets/Scenes/SampleScene.unity`

### [2026-10-02 07:59:00] - Enhancement: Template Synchronization from ai-orchestrator-template
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Konteks:** "sebelum itu, saya ada update di ai-orchestrator-template sebagai template dari orchestrator, tambahkan seluruh updatenya ke pilot-game-ai-orchestrator tanpa merusak kemajuan yang sudah ada"
- **Perubahan:** `[Added]` Menambahkan pedoman universal `database.md` (standar datetime Epoch Millis vs Timestamp UTC) dan `global-docs/LEARN.md` (knowledge base Q&A implementasi). `[Changed]` Memperbarui SOP Graphify di `main.md` (kewajiban kueri, generasi graf otomatis di Path Codebase, larangan folder .graphify di repo orchestrator), memperbarui `version-control.md` dan `commit_message_template.md` untuk menegakkan kebersihan pesan commit dari istilah orchestrator pada node, serta menambahkan FASE 4 Implementation Q&A Prompt pada `README.md`. Seluruh dokumen spesifik Pilot Game (PRD, GDD, Tiket Fase 1, Design System, Naratif) tetap terlindungi 100% tanpa regresi.
- **Path File:** `global-guidelines/database.md`, `global-docs/LEARN.md`, `global-guidelines/README.md`, `global-guidelines/coding.md`, `global-guidelines/version-control.md`, `global-docs/templates/commit_message_template.md`, `README.md`, `nodes/_template/docs/system-design.md`, `nodes/_template/main.md`, `nodes/_template/CHANGELOG.md`, `nodes/pilot-game/main.md`, `nodes/pilot-game/CHANGELOG.md`

### [2026-09-11 10:48:00] - Initialization: Pilot Game Node Setup
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Konteks:** "Saya ingin menginisialisasi Single-Project Environment menggunakan kerangka kerja AI Orchestrator ini. Definisi: Pilot Game, Unity, URP 2D, Path Codebase: Tubbies Pilot Game."
- **Perubahan:** `[Added]` Menginisialisasi Node utama `nodes/pilot-game/`, mengonfigurasi Mode Operasional ke `MODE 2: PROMPT-DRIVEN`, memindai codebase Unity URP 2D di `Tubbies Pilot Game`, menyusun dokumen mandatory (`prd.md`, `design-system.md`, `system-design.md`, `development-planning.md`), merancang diagram arsitektur PlantUML (`user-journey.puml`, `macro-state.puml`, `system-architecture.puml`, `data-class-erd.puml`), menyusun 7 tiket backlog Fase 1 (`TICKET-01` s/d `TICKET-07`), serta mengonfigurasi Knowledge Graph Graphify.
- **Path File:** `global-docs/prd.md`, `global-docs/design-system.md`, `global-docs/diagrams/user-journey.puml`, `global-docs/diagrams/macro-state.puml`, `nodes/pilot-game/main.md`, `nodes/pilot-game/guidelines/project-context.md`, `nodes/pilot-game/docs/system-design.md`, `nodes/pilot-game/docs/development-planning.md`, `nodes/pilot-game/docs/diagrams/system-architecture.puml`, `nodes/pilot-game/docs/diagrams/data-class-erd.puml`, `nodes/pilot-game/tickets/TICKET-01.md`, `nodes/pilot-game/tickets/TICKET-02.md`, `nodes/pilot-game/tickets/TICKET-03.md`, `nodes/pilot-game/tickets/TICKET-04.md`, `nodes/pilot-game/tickets/TICKET-05.md`, `nodes/pilot-game/tickets/TICKET-06.md`, `nodes/pilot-game/tickets/TICKET-07.md`, `nodes/pilot-game/CHANGELOG.md`

### [2026-09-02 08:14:00] - Guideline: Safe File Operations Policy
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Konteks:** "saya seringkali kehilangan kesempatan me-review ketika mengiyakan ai agent untuk menggunakan: 1. scripts (perubahan massal) dengan js, python, dll 2. sed -i ... 3. git checkout <file> 4. git restore <file> berikan batasan untuk penggunaan hal tersebut"
- **Perubahan:** `[Added]` Menambahkan pedoman `safe-file-operations.md` yang melarang operasi file destruktif (mass change scripts, in-place edit seperti `sed -i`, `git checkout <file>`, `git restore <file>`, `git clean`, `git stash drop`, `write_to_file` overwrite) serta mewajibkan alternatif reversible. Memperbarui `version-control.md`, `error-handling.md`, dan `nodes/_template/main.md` dengan cross-reference ke pedoman baru tersebut.
- **Path File:** `global-guidelines/safe-file-operations.md`, `global-guidelines/error-handling.md`, `global-guidelines/version-control.md`, `nodes/_template/main.md`, `nodes/_template/CHANGELOG.md`

### [2026-07-30 13:10] - Guideline: Comprehensive Audit Resolution
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "periksalah seluruh ai-orchestrator-template untuk potensi halu atau inefisiensi oleh AI"
- **Perubahan:** `[Fixed]` Menyelesaikan 16 temuan audit mencakup perbaikan halusinasi (H-01 s/d H-05), optimasi token (I-01 s/d I-05), penjernihan kontradiksi (A-01 s/d A-04), dan penutupan celah kepatuhan (C-01 s/d C-02, M-01 s/d M-03).
- **Path File:** `global-docs/prd.md`, `global-docs/design-system.md`, `global-docs/templates/README.md`, `global-guidelines/coding.md`, `global-guidelines/testing.md`, `global-guidelines/ui-and-assets.md`, `global-guidelines/version-control.md`, `nodes/_template/CHANGELOG.md`, `nodes/_template/main.md`, `nodes/_template/retrospectives/README.md`, `nodes/_template/docs/diagrams/README.md`

### [2026-07-20 18:48] - Enhancement: Node-Level Graphify Integration
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "ubahlah pendekatan graphify, jangan dibuat di directory orchestrator melainkan dibuat di directory milik project/node... mintalah cek terlebih dahulu apakah ada graphify di dalam project/node... sebelum melakukan eksekusi/scanning manual"
- **Perubahan:** Mengubah referensi `graphify build` dan `graphify update` pada root `README.md` dan `nodes/_template/main.md` agar menargetkan direktori spesifik *Path Codebase*. Menambahkan langkah wajib baru di SOP `main.md` (Poin E.2) untuk mengecek ketersediaan `.graphify` demi menghemat *token* sebelum pemindaian manual.
- **Path File:** `README.md`, `nodes/_template/main.md`

### [2026-07-19 22:43] - Guideline: Master Entrypoint & SOP
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "masukkan aturan ke `nodes/_template/main.md` dan refine lah main md buatlah lebih terstruktur, dan refine lagi terkait hierarki supaya tidak tumpang tindih. berlakukan aturan ke seluruh file di orchestrator template"
- **Perubahan:** Restrukturisasi total `main.md` menjadi hierarki A-E yang lebih jelas, menambahkan Protokol Pemulihan Konteks (Bagian B), memindahkan aturan Git & Inisiatif Liar ke file pedoman global (`version-control.md` & `error-handling.md`), serta menambahkan keterangan SSoT pada `global-guidelines/README.md`.
- **Path File:** `nodes/_template/main.md`, `global-guidelines/error-handling.md`, `global-guidelines/version-control.md`, `global-guidelines/README.md`

### [2026-07-02 16:58] - Guideline: Main SOP
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "harus tetap auto commit dan setiap auto commit harus berdasarkan ticket, jika instruksi tak memiliki ticket maka harus dibuatkan ticket! tambahkan di @[nodes/_template/main.md]"
- **Perubahan:** `[SUPERSEDED]` Menambahkan poin ke-7 pada SOP di `main.md` yang mewajibkan auto-commit berbasis tiket. *(Catatan: Aturan ini telah digugurkan. Berdasarkan revisi terbaru di `version-control.md`, AI dilarang keras melakukan auto-commit).*
- **Path File:** `nodes/_template/main.md`

