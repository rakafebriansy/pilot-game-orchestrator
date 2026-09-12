# Development Planning (Roadmap): Pilot Game

## Apa itu Development Planning
*Development Planning* adalah dokumen peta jalan (*roadmap*) strategis yang menjembatani dokumen makro (seperti PRD dan System Design) dengan tugas-tugas mikro (berupa Tiket). Dokumen ini berfungsi untuk memecah keseluruhan ruang lingkup proyek ke dalam beberapa fase pengerjaan (*Milestones*) yang dapat dikelola secara bertahap.

Dalam pendekatan *Vibe Coding* dengan AI Agent, dokumen ini sangat krusial sebagai "Gudang Antrean Tiket" (*Ticket Backlog*).

---

## 🏛️ PETA JALAN PENGEMBANGAN PILOT GAME

### 🚩 FASE 1: MVP Tactical Combat Slice (Target Utama Iterasi 1)
* **Tujuan Utama:** Membangun *vertical slice* pertempuran taktis yang dapat dimainkan penuh (*playable slice*) pada grid 15×15 dengan sistem 4-fase giliran, protagonis Nabu, 3 musuh dasar, 14 kartu tempur, dan interaksi drag-and-drop UI Toolkit.

* **Daftar Backlog Tiket (Fase 1):**
  * `[ ]` **[TICKET-01](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-01.md):** Pondasi Tipe Data, Payloads & Pusat Event (`CombatTypes.cs`, `CombatPayloads.cs`, `CombatEvents.cs`).
  * `[ ]` **[TICKET-02](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md):** Katalog Data ScriptableObjects (`CardData.cs`, `EnemyData.cs`, Template `.asset`).
  * `[ ]` **[TICKET-03](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-03.md):** Otak Logika Grid 15×15 & AI Musuh (`GridDataModel.cs`, `EnemyAICalculator.cs` + Unit Tests).
  * `[ ]` **[TICKET-04](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04.md):** Visualisasi Arena & Sistem Highlight Tilemap (`GridTilemapView.cs`, Tilemap Layers, URP 2D Lights).
  * `[ ]` **[TICKET-05](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05.md):** Entitas Karakter, Pergerakan Grid Lerp & Animasi (`UnitMovementView.cs`, `UnitAnimatorPresenter.cs`).
  * `[ ]` **[TICKET-06](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06.md):** Antarmuka Kartu UI Toolkit & Drag-and-Drop (`CardHandController.cs`, `DeckManager.cs`, UXML/USS).
  * `[ ]` **[TICKET-07](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-07.md):** State Machine Giliran Tempur & Integrasi Scene Utama (`CombatStateMachine.cs`, `MainBattleScene.unity`).

---

### 🚩 FASE 2: Peta Eksplorasi & Wave Drafting (Macro Loop)
* **Tujuan Utama:** Menghubungkan arena pertempuran ke peta eksplorasi Menara Babel bercabang (*Node Traversal*), event misteri '?', istirahat api unggun, dan pemilihan hadiah *drafting* 1 dari 3 kartu pasca-wave.
* **Daftar Backlog Tiket (Fase 2):**
  * `[ ]` **TICKET-08:** Model Data Peta Rute Bercabang Menara Babel (`MapNodeData.cs`, `MapGenerator.cs`).
  * `[ ]` **TICKET-09:** Sistem UI Pemilihan Node Peta & Transisi Scene.
  * `[ ]` **TICKET-10:** Sistem Drafting Hadiah Pasca-Pertempuran (Pick 1 of 3 Cards UI).
  * `[ ]` **TICKET-11:** Event Naratif Misteri '?' & Rest Campfire Recovery System.

---

### 🚩 FASE 3: Meta-Progression & Chapter Boss (Meta Loop)
* **Tujuan Utama:** Sistem persistensi roguelike markas (*Sanctuary*), pohon talenta upgrade permanen, pertarungan Boss Lantai, dan tingkat kesulitan *Ascension*.
* **Daftar Backlog Tiket (Fase 3):**
  * `[ ]` **TICKET-12:** Roster Boss Chapter 1 (Mekanik Serangan Multi-Tile Khusus & Fase Enrage).
  * `[ ]` **TICKET-13:** Sistem Konversi Poin Ekspedisi & Penyimpanan Save Data Lokal.
  * `[ ]` **TICKET-14:** UI Markas Sanctuary & Pohon Talenta Upgrade Permanen.
  * `[ ]` **TICKET-15:** Sistem Modifikator Tingkat Kesulitan (*Ascension Tiers*).
