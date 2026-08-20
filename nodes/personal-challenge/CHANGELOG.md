# Changelog

### [2026-08-20 14:07:00] - TICKET-01, 02, 03: Setup MVVM, CoreML, dan MLVisionService
> **Trigger:** Prompt Driven (Manual Learning) | **Branch:** `main` | **Repo:** `personal-challenge`
- **Konteks:** Pengguna mengeksekusi penulisan kode awal secara mandiri (belajar manual) untuk mengintegrasikan model hasil Create ML ke Xcode.
- **Perubahan:** 
  - `[Removed]` Membuang boilerplate SwiftData dari `personal_challengeApp.swift` dan menghapus `Item.swift`.
  - `[Added]` Menyiapkan struktur arsitektur folder `Views`, `ViewModels`, dan `Services`.
  - `[Added]` Membuat `MLVisionService.swift` yang menerapkan `VNCoreMLRequest` untuk mendeteksi `AksaraJawaModel`.
- **Path File:** `tickets/TICKET-01-setup-xcode.md`, `tickets/TICKET-02-train-model.md`, `tickets/TICKET-03-mlvision-service.md`, `docs/manual-guide-01.md`
### [2026-08-20 08:59:00] - Inisialisasi Single-Project Environment & Dokumen Arsitektur Dasar
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `personal-challenge-ai-orchestrator`
- **Konteks:** Pembuatan inisialisasi awal ekosistem AI Orchestrator untuk tantangan aplikasi penerjemah Aksara Jawa (iOS/CreateML). 
- **Perubahan:** 
  - `[Added]` Global PRD dan Design System.
  - `[Added]` Node environment `personal-challenge` dibuat dari template.
  - `[Added]` Penetapan Mode Operasional ke Mode 2.
  - `[Added]` Penyusunan diagram UML untuk User Journey, Use Case, dan System Architecture.
  - `[Added]` Dokumen System Design dan Development Planning untuk Node.
- **Path File:** `global-docs/prd.md`, `global-docs/design-system.md`, `global-docs/diagrams/*.puml`, `nodes/personal-challenge/docs/*.md`, `nodes/personal-challenge/docs/diagrams/system-architecture.puml`
