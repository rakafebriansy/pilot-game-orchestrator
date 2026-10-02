# 📚 Manual Implementation Guides: Pilot Game

Selamat datang di repositori **Panduan Manual Implementasi (Manual Guides)** untuk seluruh tiket pengembangan *Pilot Game*.

Dokumen ini menyajikan panduan langkah-demi-langkah (*step-by-step walkthrough*), arsitektur komponen, kode sumber C# / UXML / USS / HLSL lengkap, serta prosedur pengujian otomatis (*Unit Testing*) dan verifikasi visual untuk setiap tiket di [`tickets/`](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets).

---

## 🧭 DAFTAR ISI PANDUAN MANUAL (30 TIKET)

### 🚩 FASE 1: MVP Tactical Combat Slice (Arena 15×15 & Core Loop)

| No | Tiket | File Manual Guide | Deskripsi Utama |
| :---: | :---: | :--- | :--- |
| 1 | **TICKET-01** | [GUIDE-TICKET-01.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-01.md) | Kontrak Tipe Data, Struct Payloads & Static Event Bus (`CombatEvents.cs`) |
| 2 | **TICKET-02** | [GUIDE-TICKET-02.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-02.md) | Template ScriptableObject Dasar (`CardData.cs`, `EnemyData.cs`, `ConsumableData.cs`) |
| 3 | **TICKET-02B** | [GUIDE-TICKET-02B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-02B.md) | 14 Kartu Tempur Nabu Lengkap & Metadata Parameter (`CardData`) |
| 4 | **TICKET-02C** | [GUIDE-TICKET-02C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-02C.md) | 9 Roster Musuh SO Lengkap & 5 Item Consumable SO |
| 5 | **TICKET-03** | [GUIDE-TICKET-03.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-03.md) | Otak Logika Grid 15×15, Stealth Bush, Collision & AI Musuh Headless |
| 6 | **TICKET-03B** | [GUIDE-TICKET-03B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-03B.md) | Mesin Matematika Pertempuran (`CombatMathEngine`) & Validator Kartu (`CardPlayValidator`) |
| 7 | **TICKET-04** | [GUIDE-TICKET-04.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-04.md) | Visualisasi 3 Layer Tilemap & Sistem Highlight (`GridTilemapView.cs`) |
| 8 | **TICKET-04B** | [GUIDE-TICKET-04B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-04B.md) | Post-Processing Kamera, URP 2D Lighting, Flicker Obor & Audio Environment |
| 9 | **TICKET-04C** | [GUIDE-TICKET-04C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-04C.md) | Shader Denyut Bahaya Merah (*Pulsing Danger*) & Prefab Lingkungan Arena Final |
| 10 | **TICKET-05** | [GUIDE-TICKET-05.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-05.md) | Entitas Karakter, Pergerakan Interpolasi Grid Lerp & HealthBar Unit |
| 11 | **TICKET-05B** | [GUIDE-TICKET-05B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-05B.md) | 14 Prefab Efek Visual Skill (VFX), Floating Damage Popups & Object Pooling |
| 12 | **TICKET-05C** | [GUIDE-TICKET-05C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-05C.md) | Animator Controller State Machine, Screen Shake Traumatic & Hit Stop System |
| 13 | **TICKET-06** | [GUIDE-TICKET-06.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-06.md) | Antarmuka Kartu UI Toolkit (UXML/USS) & Interaksi Drag-and-Drop |
| 14 | **TICKET-06B** | [GUIDE-TICKET-06B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-06B.md) | Combat HUD — Counter Energi, Badge Niat Musuh & Banner Transisi Fase |
| 15 | **TICKET-06C** | [GUIDE-TICKET-06C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-06C.md) | Card Range Preview Highlight saat Hover & Animasi Dissolve Deploy Kartu |
| 16 | **TICKET-07** | [GUIDE-TICKET-07.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-07.md) | Finite State Machine (FSM) 4 Fase Giliran & Integrasi `MainBattleScene.unity` |

---

### 🚩 FASE 2: Peta Eksplorasi & Wave Drafting (Macro Loop)

| No | Tiket | File Manual Guide | Deskripsi Utama |
| :---: | :---: | :--- | :--- |
| 17 | **TICKET-08** | [GUIDE-TICKET-08.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-08.md) | Model Data Peta Bercabang Menara Babel & Prosedural Map Generator |
| 18 | **TICKET-09** | [GUIDE-TICKET-09.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-09.md) | UI Pemilihan Node Peta, Kabut Eksplorasi & Manajer Transisi Scene |
| 19 | **TICKET-09B** | [GUIDE-TICKET-09B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-09B.md) | Toko Pedagang (Merchant Shop) — Beli Kartu, Relik & Layanan Purge Deck |
| 20 | **TICKET-10** | [GUIDE-TICKET-10.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-10.md) | Sistem Drafting Hadiah Pasca-Pertempuran (*Pick 1 of 3 Cards*) |
| 21 | **TICKET-11** | [GUIDE-TICKET-11.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-11.md) | Event Naratif Misteri '?' & Node Pemulihan Api Unggun (Campfire Rest/Upgrade) |
| 22 | **TICKET-11B** | [GUIDE-TICKET-11B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-11B.md) | Sistem Checkpoint Ekspedisi (Max 3) & Mekanik Pengorbanan Skill (*Sacrifice Skill*) |

---

### 🚩 FASE 3: Meta-Progression, Boss & GDD 1.0 (Meta Loop)

| No | Tiket | File Manual Guide | Deskripsi Utama |
| :---: | :---: | :--- | :--- |
| 23 | **TICKET-12** | [GUIDE-TICKET-12.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-12.md) | Roster Boss Chapter 1 — Serangan Multi-Tile, Telegraph Khusus & Fase Enrage |
| 24 | **TICKET-12B** | [GUIDE-TICKET-12B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-12B.md) | Spawner Gelombang Musuh Bertahap & Penyesuaian Tingkat Kesulitan Lantai |
| 25 | **TICKET-12C** | [GUIDE-TICKET-12C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-12C.md) | 3 Bioma Menara Babel & Dinamika Rintangan Interaktif (*Hazard Terrain/Bush Burning*) |
| 26 | **TICKET-13** | [GUIDE-TICKET-13.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-13.md) | Konverter Poin Ekspedisi Pasca-Kematian & Sistem Penyimpanan Save Data JSON Lokal |
| 27 | **TICKET-14** | [GUIDE-TICKET-14.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-14.md) | Markas Permanen Sanctuary & UI Pohon Talenta Upgrade Meta |
| 28 | **TICKET-14B** | [GUIDE-TICKET-14B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-14B.md) | Sistem Inventaris Gudang Stash vs Loadout Wearable & Distribusi Loot Boss |
| 29 | **TICKET-15** | [GUIDE-TICKET-15.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-15.md) | Sistem Tingkat Kesulitan Ascension Tiers (Ascension 1-10 Modifiers) |
