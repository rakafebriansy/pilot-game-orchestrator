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

