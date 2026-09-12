# System Design: Pilot Game

## Apa itu System Design
System Design adalah dokumen teknis komprehensif yang menjelaskan arsitektur perangkat lunak, tumpukan teknologi (*tech stack*), pola desain (*design patterns*), struktur data, serta cara berbagai komponen sistem saling berintegrasi. Jika PRD berfokus pada "apa" yang akan dibangun untuk pengguna, System Design berfokus secara mendalam pada "bagaimana" sistem tersebut akan dibangun dari sisi rekayasa perangkat lunak (*software engineering*) agar efisien, aman, dan dapat diskalakan (*scalable*).

Dalam pendekatan *Vibe Coding*, System Design memegang peran krusial sebagai "cetak biru arsitektur" bagi AI Agent. Dokumen ini menjadi batasan teknis (*technical constraints*) agar AI tidak membuat keputusan struktural secara sembarangan saat menulis kode.

---

## 🏛️ ARSITEKTUR REKAYASA SISTEM PILOT GAME

### 1. Pola Arsitektur Perangkat Lunak (*Software Architecture*)

Sistem dibangun menggunakan **Trias Pemisahan Kode (Model-View-Presenter / MVC Decoupled)** dengan komunikasi berbasis **Event Bus Terpusat (`CombatEvents.cs`)**:

```text
┌───────────────────────────┐      ┌───────────────────────────┐
│ 📚 DATA LAYER (SO)        │      │ 🧠 LOGIC LAYER (Pure C#)  │
│ - CardData.cs             │─────►│ - GridDataModel.cs        │
│ - EnemyData.cs            │      │ - CombatMathEngine.cs     │
└───────────────────────────┘      │ - EnemyAICalculator.cs    │
                                   └─────────────┬─────────────┘
                                                 │
                                                 ▼ (Publish Event)
                                   ┌───────────────────────────┐
                                   │ ⚡ COMBATEVENTS (Event Bus)│
                                   └─────────────┬─────────────┘
                                                 │
                                                 ▼ (Subscribe Event)
                                   ┌───────────────────────────┐
                                   │ 🎨 VIEW LAYER (Unity MB)  │
                                   │ - GridTilemapView.cs      │
                                   │ - UnitMovementView.cs     │
                                   │ - CardHandController.cs   │
                                   └───────────────────────────┘
```

1. **Data Layer (`ScriptableObject`):** Menyimpan data statis kartu, atribut musuh, dan item. Tidak boleh memanipulasi GameObject atau menjalankan logika update.
2. **Logic Layer (Pure C# Class, Non-MonoBehaviour):** Mengelola matematika matriks petak 15×15, validasi jangkauan serang, penghitungan damage, dan algoritma AI musuh. Bersifat *headless* dan 100% *unit-testable*.
3. **View/Presentation Layer (`MonoBehaviour`):** Bertanggung jawab murni untuk rendering visual, mengubah koordinat integer logika menjadi posisi layar (`CellToWorld`), memutar animasi, dan partikel VFX.
4. **Tautan Diagram Arsitektur:** [system-architecture.puml](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/docs/diagrams/system-architecture.puml)

---

### 2. Struktur Direktori Source Code (`Assets/Scripts/`)

```text
Assets/
├── ScriptableObjects/           # Aset .asset (Cards, Enemies, Items)
│   ├── Cards/
│   ├── Enemies/
│   └── Consumables/
│
├── Scripts/
│   ├── Core/
│   │   ├── Data/                # CardData.cs, CombatTypes.cs, CombatPayloads.cs
│   │   ├── Events/              # CombatEvents.cs (Event Bus)
│   │   └── FSM/                 # CombatStateMachine.cs, ICombatState.cs
│   │
│   ├── Grid/                    # GridDataModel.cs, GridTilemapView.cs
│   ├── Units/                   # UnitMovementView.cs, UnitAnimatorPresenter.cs, EnemyAICalculator.cs
│   ├── Cards/                   # DeckManager.cs, CardPlayValidator.cs
│   └── UI/                      # CardHandController.cs, CombatHUDPresenter.cs
│
├── UI/                          # UXML Layouts & USS Style Sheets (UI Toolkit)
├── Prefabs/                     # Unit Prefabs, Arena Grid Prefab, HUD Prefab
└── Scenes/                      # MainBattleScene.unity
```

---

### 3. Tumpukan Teknologi (*Tech Stack*) & Dependensi

* **Game Engine:** Unity 2022 LTS / Unity 6
* **Rendering Pipeline:** Universal Render Pipeline 2D (URP 2D) dengan `Light2D`
* **UI Framework:** Unity UI Toolkit (`com.unity.modules.uielements`)
* **Input System:** Unity New Input System (`com.unity.inputsystem`)
* **Tilemap System:** 2D Tilemap Editor & Tilemap Extras (`com.unity.2d.tilemap`)
* **Testing Framework:** Unity Test Framework (NUnit) EditMode & PlayMode

---

### 4. Skema Data & Payload Struktur DTO (*Data Modeling*)

* **Tautan Diagram ERD:** [data-class-erd.puml](file:///Users/raka/Developer/repositories/projects/tubbies-studio/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/docs/diagrams/data-class-erd.puml)

#### A. Model Status Grid (`GridDataModel.cs`)
```csharp
public class GridDataModel
{
    public const int Size = 15;
    private readonly TileType[,] _tiles = new TileType[Size, Size];
    private readonly int[,] _occupants = new int[Size, Size];

    public bool IsInsideGrid(Vector2Int coord);
    public bool IsWalkable(Vector2Int coord);
    public void SetOccupant(Vector2Int coord, int unitId);
    public int GetOccupant(Vector2Int coord);
}
```

#### B. Struct DTO Komunikasi (*Payloads*)
```csharp
// Paket instruksi sorot ubin
public readonly struct TileHighlightRequest
{
    public readonly Vector2Int[] Coordinates;
    public readonly HighlightType Style;
}

// Paket instruksi gerak unit
public readonly struct UnitMovePayload
{
    public readonly int UnitId;
    public readonly Vector2Int FromCoord;
    public readonly Vector2Int ToCoord;
}

// Paket instruksi kalkulasi damage
public readonly struct DamagePayload
{
    public readonly int TargetUnitId;
    public readonly int DamageAmount;
    public readonly int ShieldRemaining;
}
```

---

### 5. Finite State Machine (FSM) Giliran Tempur

```text
[IntentPhaseState] ────► [PlayerPhaseState] ────► [EnemyPhaseState] ────► [RoundResetPhaseState]
        ▲                                                                            │
        └────────────────────────── (Ronde Baru) ────────────────────────────────────┘
```

1. **`IntentPhaseState`:** Memerintahkan `EnemyAICalculator` menghitung target serangan ➡️ Broadcast `OnEnemyIntentDecided` & `OnHighlightTilesRequested` (Ubin Merah).
2. **`PlayerPhaseState`:** Mengaktifkan interaksi tangan kartu UI ➡️ Menunggu broadcast `OnCardPlayed(card, targetCoord)` ➡️ Memvalidasi langkah ➡️ Transisi ke Enemy Phase.
3. **`EnemyPhaseState`:** Mengunci UI ➡️ Musuh mengeksekusi serangan secara acak ➡️ Broadcast `OnUnitDamaged` / `OnUnitMoved` ➡️ Evaluasi apakah ada unit tereliminasi.
4. **`RoundResetPhaseState`:** Membersihkan highlight arena (`OnClearAllHighlights`) ➡️ Menarik kartu baru (`OnDrawCardsRequested`) ➡️ Cek kondisi Menang/Kalah.

---

### 6. Optimasi Memori & Anti-GC Spike

1. **Zero Allocation di Hot Path:** Tidak ada inisialisasi `new List<T>()` atau string concatenation di dalam method `Update()`.
2. **Object Pooling:** Menggunakan `UnityEngine.Pool.ObjectPool<T>` untuk damage text popup dan efek tebasan/proyektil visual.
3. **Value Types untuk Komunikasi:** Seluruh payload komunikasi antar modul dikemas dalam bentuk `readonly struct` atau `Vector2Int` agar teralokasi di *Stack memory*.
