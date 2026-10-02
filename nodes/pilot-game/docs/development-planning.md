# Development Planning (Roadmap): Pilot Game

## Apa itu Development Planning
*Development Planning* adalah dokumen peta jalan (*roadmap*) strategis yang menjembatani dokumen makro (seperti PRD dan System Design) dengan tugas-tugas mikro (berupa Tiket). Dokumen ini berfungsi untuk memecah keseluruhan ruang lingkup proyek ke dalam beberapa fase pengerjaan (*Milestones*) yang dapat dikelola secara bertahap.

Dalam pendekatan *Vibe Coding* dengan AI Agent, dokumen ini sangat krusial sebagai "Gudang Antrean Tiket" (*Ticket Backlog*).

---

## 🏛️ PETA JALAN PENGEMBANGAN PILOT GAME

---

### 🚩 FASE 1: MVP Tactical Combat Slice (Target Utama Iterasi 1)
* **Tujuan Utama:** Membangun *vertical slice* pertempuran taktis yang dapat dimainkan penuh (*playable slice*) pada grid 15×15 dengan sistem 4-fase giliran, protagonis Nabu, 3 musuh dasar, 14 kartu tempur, dan interaksi drag-and-drop UI Toolkit.
* **Referensi Arsitektur:** `docs/system-design.md` §1-5, `../../pilot-game-team-docs/02_engineering/sequential_implementation_guide.md`

* **Daftar Backlog Tiket (Fase 1):**
  * `[ ]` **[TICKET-01](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-01.md):** Pondasi Tipe Data, Payloads & Pusat Event (`CombatTypes.cs`, `CombatPayloads.cs`, `CombatEvents.cs`).
    * *Sub-tasks: Enum CombatPhase, HighlightType, TileType, CardActionType; Struct TileHighlightRequest, UnitMovePayload, DamagePayload; Static CombatEvents delegate hub.*
  * `[ ]` **[TICKET-02](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md):** Katalog Data ScriptableObjects (`CardData.cs`, `EnemyData.cs`, `ConsumableData.cs`, Template `.asset`).
    * *Sub-tasks: 5 kartu SO asset, 3 musuh SO asset (Tattered Conscript, Dustbound Skeleton, Archive Scavenger).*
    * *Dependensi: TICKET-01 selesai.*
  * `[ ]` **[TICKET-03](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-03.md):** Otak Logika Grid 15×15 & AI Musuh (`GridDataModel.cs`, `EnemyAICalculator.cs` + Unit Tests).
    * *Sub-tasks: GridDataModel dengan 6 method; EnemyAICalculator PlanLinearAttack & PlanAreaAttack; NUnit EditMode test suite GridLogicTests.cs.*
    * *Dependensi: TICKET-01 selesai.*
  * `[ ]` **[TICKET-04](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04.md):** Visualisasi Arena & Sistem Highlight Tilemap (`GridTilemapView.cs`, Tilemap Layers, URP 2D Lights).
    * *Sub-tasks: 3 Tilemap layers (Floor/Environment/Overlay), GridTilemapView subscriber, ArenaGrid_Prefab, URP Global Light 2D.*
    * *Dependensi: TICKET-01 selesai.*
  * `[ ]` **[TICKET-05](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05.md):** Entitas Karakter, Pergerakan Grid Lerp & Animasi (`UnitMovementView.cs`, `UnitAnimatorPresenter.cs`, Prefabs).
    * *Sub-tasks: UnitMovementView dengan MoveTowards coroutine, UnitAnimatorPresenter event-driven, HealthBar, Nabu_Player_Prefab, Enemy_Conscript_Prefab.*
    * *Dependensi: TICKET-01, TICKET-02 selesai.*
  * `[ ]` **[TICKET-06](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06.md):** Antarmuka Kartu UI Toolkit & Drag-and-Drop (`CardHandController.cs`, `DeckManager.cs`, UXML/USS).
    * *Sub-tasks: CardHandHUD.uxml dengan BEM, Cards.uss dengan design tokens, DeckManager draw/discard/reshuffle, drag-to-grid pointer events.*
    * *Dependensi: TICKET-01, TICKET-02 selesai.*
  * `[ ]` **[TICKET-07](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-07.md):** State Machine Giliran Tempur & Integrasi Scene Utama (`ICombatState.cs`, `CombatStateMachine.cs`, `MainBattleScene.unity`).
    * *Sub-tasks: Interface ICombatState, 4 state classes (Intent/Player/Enemy/RoundReset), scene assembly dengan seluruh prefab, verifikasi playable slice 3 ronde.*
    * *Dependensi: TICKET-01 s/d TICKET-06 semua selesai.*

---

### 🚩 FASE 2: Peta Eksplorasi & Wave Drafting (Macro Loop)
* **Tujuan Utama:** Menghubungkan arena pertempuran ke peta eksplorasi Menara Babel bercabang (*Node Traversal*), event misteri '?', istirahat api unggun, dan pemilihan hadiah *drafting* 1 dari 3 kartu pasca-wave.
* **Prasyarat:** Seluruh tiket Fase 1 (TICKET-01 s/d TICKET-07) berstatus Done.

* **Daftar Backlog Tiket (Fase 2):**
  * `[ ]` **[TICKET-08](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-08.md):** Model Data Peta Rute Bercabang Menara Babel (`MapNodeData.cs`, `MapGenerator.cs`, `MapLayout.cs` + Unit Tests).
    * *Sub-tasks: MapNodeData SO dengan 5 MapNodeType; MapGenerator procedural dengan weighted probability; NUnit test MapGeneratorTests.cs.*
    * *Dependensi: TICKET-01 (pattern struct/enum).*
  * `[ ]` **[TICKET-09](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09.md):** Sistem UI Pemilihan Node Peta & Transisi Scene (`MapScreenController.cs`, `MapManager.cs`, `SceneTransitionManager.cs`).
    * *Sub-tasks: MapScene UXML dengan node visual, MapManager DontDestroyOnLoad singleton, fade in/out scene transition menggunakan AsyncOperation.*
    * *Dependensi: TICKET-08, TICKET-07 (MainBattleScene target).*
  * `[ ]` **[TICKET-10](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-10.md):** Sistem Drafting Hadiah Pasca-Pertempuran Pick 1 of 3 Cards (`DraftManager.cs`, `DraftScreenController.cs`).
    * *Sub-tasks: DraftManager generate 3 random cards, DraftScreenUI.uxml, skip dengan konfirmasi, deck update setelah pilih.*
    * *Dependensi: TICKET-02 (CardData), TICKET-06 (DeckManager), TICKET-09 (MapManager).*
  * `[ ]` **[TICKET-11](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-11.md):** Event Naratif Misteri '?' & Rest Campfire Recovery System (`NarrativeEventManager.cs`, `CampfireManager.cs`).
    * *Sub-tasks: NarrativeEventData SO (min 5 events), EventScreenController.cs, CampfireManager heal/upgrade, 3 pasang kartu upgrade.*
    * *Dependensi: TICKET-09 (MapManager), TICKET-02 (CardData).*

---

### 🚩 FASE 3: Meta-Progression & Chapter Boss (Meta Loop)
* **Tujuan Utama:** Sistem persistensi roguelike markas (*Sanctuary*), pohon talenta upgrade permanen, pertarungan Boss Lantai 1, dan tingkat kesulitan *Ascension*.
* **Prasyarat:** Seluruh tiket Fase 2 (TICKET-08 s/d TICKET-11) berstatus Done.

* **Daftar Backlog Tiket (Fase 3):**
  * `[ ]` **[TICKET-12](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12.md):** Roster Boss Chapter 1 — Mekanik Serangan Multi-Tile & Fase Enrage (`BossData.cs`, `BossAIController.cs`, Prefab Archivist Sentinel).
    * *Sub-tasks: BossData SO dengan AttackPatterns, BossAIController dengan scripted rotation + enrage trigger, multi-tile highlight coroutine, BossHealthBarPresenter.*
    * *Dependensi: TICKET-01, TICKET-03, TICKET-07.*
  * `[ ]` **[TICKET-13](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-13.md):** Sistem Konversi Poin Ekspedisi & Penyimpanan Save Data Lokal (`RunSaveData.cs`, `MetaSaveData.cs`, `SaveDataManager.cs`, `ExpeditionPointsManager.cs`).
    * *Sub-tasks: JSON serialize/deserialize ke persistentDataPath, timestamp Epoch Millis, auto-save per node, NUnit SaveDataTests.cs.*
    * *Dependensi: TICKET-08 (MapLayout), TICKET-02 (CardData IDs).*
  * `[ ]` **[TICKET-14](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-14.md):** UI Markas Sanctuary & Pohon Talenta Upgrade Permanen (`TalentData.cs`, `TalentTreeManager.cs`, `SanctuaryScreenController.cs`).
    * *Sub-tasks: 12 TalentData SO asset (3 tier), visual talent tree UXML, unlock logic dengan prerequisites, apply talents on run start.*
    * *Dependensi: TICKET-13 (ExpeditionPoints, MetaSaveData).*
  * `[ ]` **[TICKET-15](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-15.md):** Sistem Modifikator Tingkat Kesulitan Ascension Tiers (`AscensionModifierData.cs`, `AscensionManager.cs`).
    * *Sub-tasks: 10 AscensionModifierData SO (A1-A10 kumulatif), AscensionManager apply effects, Sanctuary UI section untuk pilih Ascension level.*
    * *Dependensi: TICKET-13 (MetaSaveData), TICKET-14 (SanctuaryScreenController).*

---

## 📊 STATUS SUMMARY

| Tiket | Judul | Fase | Status | Prioritas |
| :---: | :--- | :---: | :---: | :---: |
| TICKET-01 | Pondasi Tipe Data, Payloads & Pusat Event | 1 | `Todo` | High |
| TICKET-02 | Katalog Data ScriptableObjects | 1 | `Todo` | High |
| TICKET-03 | Otak Logika Grid 15×15 & AI Musuh | 1 | `Todo` | High |
| TICKET-04 | Visualisasi Arena & Sistem Highlight Tilemap | 1 | `Todo` | High |
| TICKET-05 | Entitas Karakter, Pergerakan Grid Lerp & Animasi | 1 | `Todo` | High |
| TICKET-06 | Antarmuka Kartu UI Toolkit & Drag-and-Drop | 1 | `Todo` | High |
| TICKET-07 | State Machine Giliran Tempur & Integrasi Scene | 1 | `Todo` | High |
| TICKET-08 | Model Data Peta Rute Bercabang Menara Babel | 2 | `Todo` | High |
| TICKET-09 | UI Pemilihan Node Peta & Transisi Scene | 2 | `Todo` | High |
| TICKET-10 | Sistem Drafting Hadiah Pasca-Pertempuran | 2 | `Todo` | High |
| TICKET-11 | Event Naratif Misteri '?' & Campfire Rest | 2 | `Todo` | Medium |
| TICKET-12 | Roster Boss Chapter 1 & Fase Enrage | 3 | `Todo` | High |
| TICKET-13 | Sistem Poin Ekspedisi & Save Data Lokal | 3 | `Todo` | High |
| TICKET-14 | UI Sanctuary & Pohon Talenta Upgrade Permanen | 3 | `Todo` | Medium |
| TICKET-15 | Sistem Modifikator Tingkat Kesulitan Ascension | 3 | `Todo` | Low |
