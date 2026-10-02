# Development Planning (Roadmap): Pilot Game

## Apa itu Development Planning
*Development Planning* adalah dokumen peta jalan (*roadmap*) strategis yang menjembatani dokumen makro (seperti PRD dan System Design) dengan tugas-tugas mikro (berupa Tiket). Dokumen ini berfungsi untuk memecah keseluruhan ruang lingkup proyek ke dalam beberapa fase pengerjaan (*Milestones*) yang dapat dikelola secara bertahap.

Dalam pendekatan *Vibe Coding* dengan AI Agent, dokumen ini sangat krusial sebagai "Gudang Antrean Tiket" (*Ticket Backlog*).

> **Referensi Arsitektur Utama:** `pilot-game-team-docs/02_engineering/diagrams/system-architecture-dataflow.puml` — seluruh komponen dalam diagram tersebut harus memiliki tiket yang mengakomodasi implementasinya.

---

## 🏛️ PETA JALAN PENGEMBANGAN PILOT GAME

---

### 🚩 FASE 1: MVP Tactical Combat Slice (Target Utama Iterasi 1)
* **Tujuan Utama:** Membangun *vertical slice* pertempuran taktis yang dapat dimainkan penuh (*playable slice*) pada grid 15×15 dengan sistem 4-fase giliran, protagonis Nabu, musuh dasar, 14 kartu tempur, sistem damage + shield, dan interaksi drag-and-drop UI Toolkit.
* **Referensi:** `docs/system-design.md`, `pilot-game-team-docs/02_engineering/sequential_implementation_guide.md`, `domain_milestone_briefs/`

#### 📦 Domain: PM Core & Communication Bridge
* `[ ]` **[TICKET-01](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-01.md):** Pondasi Tipe Data, Payloads & Pusat Event (`CombatTypes.cs`, `CombatPayloads.cs`, `CombatEvents.cs`).

#### 📦 Domain 4: Data & Logic
* `[ ]` **[TICKET-02](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md):** Katalog Data ScriptableObjects — Template Dasar (`CardData.cs`, `EnemyData.cs`, `ConsumableData.cs`, 5 sample cards).
* `[ ]` **[TICKET-02B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02B.md):** 14 Kartu Tempur Nabu Lengkap + update `CardData` (PhaseRestriction, AreaType, StatusEffect fields).
* `[ ]` **[TICKET-02C](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02C.md):** 9 Musuh SO Lengkap (selected_enemies.md roster) & 5 Item Consumable SO.
* `[ ]` **[TICKET-03](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-03.md):** Otak Logika Grid 15×15 & AI Musuh (`GridDataModel.cs`, `EnemyAICalculator.cs` + Unit Tests).
* `[ ]` **[TICKET-03B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-03B.md):** Mesin Matematika Pertempuran & Validator Kartu (`CombatMathEngine.cs`, `CardPlayValidator.cs`, Status Effect system).

#### 📦 Domain 1: Arena & Tilemap
* `[ ]` **[TICKET-04](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04.md):** Visualisasi Arena & Sistem Highlight Tilemap (`GridTilemapView.cs`, 3 Tilemap layers).
* `[ ]` **[TICKET-04B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04B.md):** Camera Post-Processing, URP 2D Lighting & Audio Environment (`TorchFlicker.cs`, `AudioManager.cs`).
* `[ ]` **[TICKET-04C](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04C.md):** Pulsing Danger Shader & `ArenaEnvironment_Prefab` Final Assembly.

#### 📦 Domain 2: Character & Animation
* `[ ]` **[TICKET-05](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05.md):** Entitas Karakter, Pergerakan Grid Lerp & HealthBar (`UnitMovementView.cs`, Prefabs Nabu & Conscript).
* `[ ]` **[TICKET-05B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05B.md):** 14 Skill VFX Prefab & Object Pooling System (`VFXPoolManager.cs`, `DamagePopup.cs`, `SkillVFXPresenter.cs`).
* `[ ]` **[TICKET-05C](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05C.md):** Animator Controller, Screen Shake & Hit Stop (`NabuAnimatorController`, `CameraShaker.cs`, `HitStopManager.cs`).

#### 📦 Domain 3: Card Deck & UI
* `[ ]` **[TICKET-06](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06.md):** Antarmuka Kartu UI Toolkit & Drag-and-Drop (`CardHandController.cs`, `DeckManager.cs`, UXML/USS).
* `[ ]` **[TICKET-06B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06B.md):** Combat HUD — Energy Counter, Intent Badges & Phase Banner (`CombatHUDPresenter.cs`, `EnemyIntentBadgePresenter.cs`).
* `[ ]` **[TICKET-06C](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06C.md):** Card Range Preview Hover & Card Dissolve Deploy Animation (`CardDeployAnimator.cs`).

#### 📦 PM Core: Integration
* `[ ]` **[TICKET-07](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-07.md):** State Machine Giliran Tempur & Integrasi Scene Utama (`ICombatState.cs`, `CombatStateMachine.cs`, `MainBattleScene.unity`).
  * *⚠️ Prasyarat: TICKET-01 s/d TICKET-06C semua Done.*

---

### 🚩 FASE 2: Peta Eksplorasi & Wave Drafting (Macro Loop)
* **Tujuan Utama:** Menghubungkan arena pertempuran ke peta eksplorasi Menara Babel bercabang, event misteri, api unggun, merchant shop, dan sistem drafting hadiah pasca-wave.
* **Prasyarat:** Seluruh tiket Fase 1 berstatus Done.

* `[ ]` **[TICKET-08](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-08.md):** Model Data Peta Rute Bercabang Menara Babel (`MapNodeData.cs`, `MapGenerator.cs`, `MapLayout.cs`).
* `[ ]` **[TICKET-09](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09.md):** UI Pemilihan Node Peta & Transisi Scene (`MapScreenController.cs`, `MapManager.cs`, `SceneTransitionManager.cs`).
* `[ ]` **[TICKET-09B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09B.md):** Merchant Shop UI — Beli Kartu, Relic & Purge Deck (`GoldManager.cs`, `RelicManager.cs`, `ShopScreenController.cs`).
* `[ ]` **[TICKET-10](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-10.md):** Sistem Drafting Hadiah Pasca-Pertempuran Pick 1 of 3 Cards (`DraftManager.cs`, `DraftScreenController.cs`).
* `[ ]` **[TICKET-11](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-11.md):** Event Naratif Misteri '?' & Rest Campfire Recovery System (`NarrativeEventManager.cs`, `CampfireManager.cs`).

---

### 🚩 FASE 3: Meta-Progression, Boss & GDD 1.0 (Meta Loop)
* **Tujuan Utama:** Sistem persistensi roguelike markas Sanctuary, pohon talenta permanen, Boss Chapter 1, Wave Spawner procedural, 3 Bioma Menara Babel, dan Ascension difficulty.
* **Prasyarat:** Seluruh tiket Fase 2 berstatus Done.

* `[ ]` **[TICKET-12](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12.md):** Roster Boss Chapter 1 — Mekanik Serangan Multi-Tile & Fase Enrage (`BossData.cs`, `BossAIController.cs`).
* `[ ]` **[TICKET-12B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12B.md):** Combat Wave Spawner & Floor Progression Scaling (`CombatWaveSpawner.cs`, `WaveCompositionData.cs`).
* `[ ]` **[TICKET-12C](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12C.md):** 3 Bioma Menara Babel & Dynamic Hazards (`BiomeManager.cs`, `DynamicTileManager.cs`).
* `[ ]` **[TICKET-13](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-13.md):** Sistem Konversi Poin Ekspedisi & Penyimpanan Save Data Lokal (`SaveDataManager.cs`, `ExpeditionPointsManager.cs`).
* `[ ]` **[TICKET-14](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-14.md):** UI Markas Sanctuary & Pohon Talenta Upgrade Permanen (`TalentTreeManager.cs`, `SanctuaryScreenController.cs`).
* `[ ]` **[TICKET-15](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-15.md):** Sistem Modifikator Tingkat Kesulitan Ascension Tiers (`AscensionModifierData.cs`, `AscensionManager.cs`).

---

## 📊 STATUS SUMMARY (28 Tiket Total)

| Tiket | Judul | Domain | Fase | Status | Prioritas |
| :---: | :--- | :---: | :---: | :---: | :---: |
| TICKET-01 | Pondasi Tipe Data, Payloads & Event Bus | PM Core | 1 | `Todo` | High |
| TICKET-02 | Katalog SO Template Dasar | D4 | 1 | `Todo` | High |
| TICKET-02B | 14 Kartu Tempur Nabu Lengkap | D4 | 1 | `Todo` | High |
| TICKET-02C | 9 Musuh SO & 5 Consumable SO | D4 | 1 | `Todo` | High |
| TICKET-03 | Grid 15×15 Logic & EnemyAI | D4 | 1 | `Todo` | High |
| TICKET-03B | CombatMathEngine & CardPlayValidator | D4 | 1 | `Todo` | High |
| TICKET-04 | Arena Tilemap & Highlight View | D1 | 1 | `Todo` | High |
| TICKET-04B | Camera Post-Processing & URP Lighting | D1 | 1 | `Todo` | High |
| TICKET-04C | Pulsing Danger Shader & Arena Prefab Final | D1 | 1 | `Todo` | Medium |
| TICKET-05 | Unit Prefabs & Grid Movement Lerp | D2 | 1 | `Todo` | High |
| TICKET-05B | 14 Skill VFX Prefab & Object Pool | D2 | 1 | `Todo` | High |
| TICKET-05C | Animator Controller & Hit Impact System | D2 | 1 | `Todo` | Medium |
| TICKET-06 | Card Hand UI Toolkit & Drag-Drop | D3 | 1 | `Todo` | High |
| TICKET-06B | Combat HUD (Energy, Intent Badges, Phase) | D3 | 1 | `Todo` | High |
| TICKET-06C | Card Range Preview & Deploy Animation | D3 | 1 | `Todo` | Medium |
| TICKET-07 | Turn FSM & MainBattleScene Assembly | PM Core | 1 | `Todo` | High |
| TICKET-08 | Map Node Data & Generator | Map | 2 | `Todo` | High |
| TICKET-09 | Map Screen UI & Scene Transition | Map | 2 | `Todo` | High |
| TICKET-09B | Merchant Shop UI & Relic System | D3 | 2 | `Todo` | Medium |
| TICKET-10 | Draft Reward Screen (Pick 1 of 3) | D3 | 2 | `Todo` | High |
| TICKET-11 | Narrative Events & Campfire Rest | Map | 2 | `Todo` | Medium |
| TICKET-12 | Boss Chapter 1 & Enrage Phase | D4 | 3 | `Todo` | High |
| TICKET-12B | Combat Wave Spawner & Floor Scaling | D4 | 3 | `Todo` | High |
| TICKET-12C | 3 Bioma Menara Babel & Dynamic Hazards | D1 | 3 | `Todo` | Low |
| TICKET-13 | Expedition Points & Save Data Local | Core | 3 | `Todo` | High |
| TICKET-14 | Sanctuary UI & Talent Tree | Meta | 3 | `Todo` | Medium |
| TICKET-15 | Ascension Difficulty Tiers | Meta | 3 | `Todo` | Low |
