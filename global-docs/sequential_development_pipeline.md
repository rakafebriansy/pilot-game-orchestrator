# 🛠️ Rencana Panduan Pembuatan Proyek Bertahap (Sequential Development Pipeline)
**Alur Pengerjaan Teknis dari Baris Kode Pertama hingga Playable Slice di Unity C#**

Dokumen ini adalah **panduan sekuensial langkah demi langkah (*step-by-step pipeline*)** untuk membangun sistem pertempuran taktis berbasis grid 15×15 dan kartu. Seluruh alur dirancang berurutan secara modular menggunakan arsitektur *Separation of Concerns* dan *Event-Driven Architecture* tanpa membebani ketergantungan antar modul.

---

## 🧭 DIAGRAM ALUR PENGERJAAN SEKUANSIAL

```text
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 0: PONDASI TIPE DATA, DTO STRUCT & PUSAT EVENT                               │
│ └──> CombatTypes.cs ──> CombatPayloads.cs ──> CombatEvents.cs                     │
└────────────────────────────────────────┬──────────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 1: KATALOG DATA & SCRIPTABLEOBJECTS                                          │
│ └──> CardData.cs ──> EnemyData.cs ──> ConsumableData.cs ──> Aset .asset di Editor │
└────────────────────────────────────────┬──────────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 2: OTAK LOGIKA GRID 15×15 & KALKULASI AI MUSUH (C# MURNI)                    │
│ └──> GridDataModel.cs ──> GridMathValidator.cs ──> EnemyAICalculator.cs           │
└────────────────────────────────────────┬──────────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 3: VISUALISASI ARENA & SISTEM HIGHLIGHT TILEMAP                              │
│ └──> Setup Layer Tilemap 2D ──> GridTilemapView.cs (Konversi Koordinat ke Layar)  │
└────────────────────────────────────────┬──────────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 4: ENTITAS KARAKTER, PERGERAKAN GRID & SISTEM ANIMASI/VFX                    │
│ └──> Unit Prefabs ──> UnitMovementView.cs ──> UnitAnimationPresenter.cs          │
└────────────────────────────────────────┬──────────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 5: ANTARMUKA KARTU (UI TOOLKIT) & SISTEM DRAG-AND-DROP                       │
│ └──> UXML/USS Layout ──> DeckManager.cs ──> CardHandController.cs                 │
└────────────────────────────────────────┬──────────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────────┐
│ FASE 6: STATE MACHINE GILIRAN & INTEGRASI SCENE UTAMA                             │
│ └──> CombatStateMachine.cs ──> MainBattleScene.unity ──> Uji Coba Playable Loop   │
└───────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🚀 TAHAPAN IMPLEMENTASI LENGKAP

---

### 🔹 FASE 0: Pondasi Tipe Data, DTO Struct & Pusat Event
> **Tujuan:** Menyusun struktur data dasar, DTO (*Data Transfer Object*), dan *Event Bus* statis terpusat sebagai protokol komunikasi seluruh sistem.

#### Langkah 0.1: Struktur Direktori Kode Bersih
Buat struktur direktori di dalam folder `Assets/Scripts/`:
```text
Assets/Scripts/
├── Core/
│   ├── Data/
│   ├── Events/
│   └── FSM/
├── Grid/
├── Units/
├── Cards/
├── UI/
└── Environment/
```

#### Langkah 0.2: Definisi Kamus Tipe & Enum (`CombatTypes.cs`)
* **Lokasi File:** `Assets/Scripts/Core/Data/CombatTypes.cs`
* **Kode Implementasi:**
  ```csharp
  public enum CombatPhase
  {
      IntentPhase,    // Fase 1: Kalkulasi niat & telegraph bahaya musuh
      PlayerPhase,    // Fase 2: Interaksi kartu tangan pemain
      EnemyPhase,     // Fase 3: Eksekusi serangan musuh
      RoundResetPhase // Fase 4: Tarik kartu & evaluasi kondisi ronde
  }

  public enum HighlightType
  {
      None,
      DangerEnemyIntent, // Ubin Merah (Area serangan yang diincar musuh)
      ValidCardTarget,   // Ubin Hijau (Area sah untuk aksi kartu pemain)
      MovementRange,     // Ubin Biru (Jangkauan langkah karakter)
      HoverPreview       // Ubin Putih/Kuning (Sorot kursor mouse)
  }

  public enum TileType
  {
      NormalFloor,       // Lantai ubin standar (Dapat dilewati)
      StealthBush,       // Semak Mesopotamia (Menyembunyikan karakter)
      ObstaclePillar,    // Pilar batu (Memblokir pergerakan & proyektil)
      HazardTrap         // Jebakan duri lantai
  }

  public enum CardActionType
  {
      Attack,
      Defense,
      Movement,
      Utility
  }
  ```

#### Langkah 0.3: Definisi Struct DTO Paket Data (`CombatPayloads.cs`)
* **Lokasi File:** `Assets/Scripts/Core/Data/CombatPayloads.cs`
* **Kode Implementasi:**
  ```csharp
  using UnityEngine;

  // Paket data untuk instruksi sorot ubin visual
  public readonly struct TileHighlightRequest
  {
      public readonly Vector2Int[] Coordinates;
      public readonly HighlightType Style;

      public TileHighlightRequest(Vector2Int[] coordinates, HighlightType style)
      {
          Coordinates = coordinates;
          Style = style;
      }
  }

  // Paket data saat unit berpindah petak
  public readonly struct UnitMovePayload
  {
      public readonly int UnitId;
      public readonly Vector2Int FromCoord;
      public readonly Vector2Int ToCoord;

      public UnitMovePayload(int unitId, Vector2Int fromCoord, Vector2Int toCoord)
      {
          UnitId = unitId;
          FromCoord = fromCoord;
          ToCoord = toCoord;
      }
  }

  // Paket data kalkulasi damage & pengurangan HP
  public readonly struct DamagePayload
  {
      public readonly int TargetUnitId;
      public readonly int DamageAmount;
      public readonly int ShieldRemaining;

      public DamagePayload(int targetUnitId, int damageAmount, int shieldRemaining)
      {
          TargetUnitId = targetUnitId;
          DamageAmount = damageAmount;
          ShieldRemaining = shieldRemaining;
      }
  }
  ```

#### Langkah 0.4: Definisi Event Hub Terpusat (`CombatEvents.cs`)
* **Lokasi File:** `Assets/Scripts/Core/Events/CombatEvents.cs`
* **Kode Implementasi:**
  ```csharp
  using System;
  using UnityEngine;

  public static class CombatEvents
  {
      // --- FASE 1: INTENT & HIGHLIGHT ---
      public static Action<int /*enemyId*/, Vector2Int /*target*/> OnEnemyIntentDecided;
      public static Action<TileHighlightRequest> OnHighlightTilesRequested;
      public static Action OnClearAllHighlights;

      // --- FASE 2: AKSI PEMAIN ---
      public static Action<CardData, Vector2Int> OnCardPlayed;

      // --- FASE 3: EKSEKUSI, GERAK & DAMAGE ---
      public static Action<UnitMovePayload> OnUnitMoved;
      public static Action<int /*casterId*/, int /*skillId*/, Vector2Int /*target*/> OnSkillExecuted;
      public static Action<DamagePayload> OnUnitDamaged;

      // --- FASE 4: FSM & RONDE ---
      public static Action<CombatPhase> OnPhaseChanged;
      public static Action OnDrawCardsRequested;
      public static Action<bool /*isVictory*/> OnCombatEnded;
  }
  ```
* **Kriteria Selesai Fase 0:** Seluruh file ter-compile bersih di Unity tanpa warning atau error.

---

### 🔹 FASE 1: Katalog Data & ScriptableObjects
> **Tujuan:** Menyediakan wadah konfigurasi statis untuk kartu jurus dan profil musuh tanpa memasukkan logika runtime.

#### Langkah 1.1: Pembuatan Template ScriptableObject
* **Lokasi File:** `Assets/Scripts/Core/Data/CardData.cs` & `EnemyData.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/Core/Data/CardData.cs
  using UnityEngine;

  [CreateAssetMenu(fileName = "Card_", menuName = "PilotGame/Card Data")]
  public class CardData : ScriptableObject
  {
      [SerializeField] private string _cardId;
      [SerializeField] private string _cardName;
      [SerializeField] private CardActionType _actionType;
      [SerializeField] private int _baseValue;
      [SerializeField] private int _range;
      [SerializeField] private Sprite _cardIllustration;

      public string CardId => _cardId;
      public string CardName => _cardName;
      public CardActionType ActionType => _actionType;
      public int BaseValue => _baseValue;
      public int Range => _range;
      public Sprite CardIllustration => _cardIllustration;
  }
  ```

#### Langkah 1.2: Pembuatan File Aset `.asset` Awal
Buat folder `Assets/ScriptableObjects/Cards/` di Project Window, lalu buat minimal 3 kartu:
1. `Card_PageCutter.asset` : Attack, BaseValue = 6, Range = 1.
2. `Card_TumbleDodge.asset` : Movement, BaseValue = 2, Range = 2.
3. `Card_ClayShield.asset` : Defense, BaseValue = 5, Range = 0.
* **Kriteria Selesai Fase 1:** Data kartu dapat diubah nilainya melalui Unity Inspector dan dibaca saat runtime.

---

### 🔹 FASE 2: Otak Logika Grid 15×15 & AI Musuh (C# Murni)
> **Tujuan:** Membangun struktur matriks petak 15×15, validasi langkah, dan penghitungan target serangan musuh tanpa ketergantungan visual.

#### Langkah 2.1: Model Matriks Grid (`GridDataModel.cs`)
* **Lokasi File:** `Assets/Scripts/Grid/GridDataModel.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/Grid/GridDataModel.cs
  using UnityEngine;

  public class GridDataModel
  {
      public const int Size = 15;

      private readonly TileType[,] _tiles = new TileType[Size, Size];
      private readonly int[,] _occupants = new int[Size, Size]; // 0 = Kosong

      public bool IsInsideGrid(Vector2Int coord)
      {
          return coord.x >= 0 && coord.x < Size && coord.y >= 0 && coord.y < Size;
      }

      public bool IsWalkable(Vector2Int coord)
      {
          if (!IsInsideGrid(coord)) return false;
          return _tiles[coord.x, coord.y] != TileType.ObstaclePillar && _occupants[coord.x, coord.y] == 0;
      }

      public void SetOccupant(Vector2Int coord, int unitId)
      {
          if (IsInsideGrid(coord)) _occupants[coord.x, coord.y] = unitId;
      }

      public void ClearOccupant(Vector2Int coord)
      {
          if (IsInsideGrid(coord)) _occupants[coord.x, coord.y] = 0;
      }

      public int GetOccupant(Vector2Int coord)
      {
          return IsInsideGrid(coord) ? _occupants[coord.x, coord.y] : 0;
      }
  }
  ```

#### Langkah 2.2: Kalkulator Niat AI Musuh (`EnemyAICalculator.cs`)
* **Lokasi File:** `Assets/Scripts/Units/EnemyAICalculator.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/Units/EnemyAICalculator.cs
  using UnityEngine;

  public class EnemyAICalculator
  {
      private readonly GridDataModel _grid;

      public EnemyAICalculator(GridDataModel grid)
      {
          _grid = grid;
      }

      // Menghitung target serangan 2 petak lurus
      public void PlanLinearAttack(int enemyId, Vector2Int enemyCoord, Vector2Int direction)
      {
          Vector2Int targetCoord = enemyCoord + direction * 2;

          if (_grid.IsInsideGrid(targetCoord))
          {
              // 1. Siarkan keputusan niat musuh
              CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, targetCoord);

              // 2. Siarkan permintaan highlight merah ke layer visual
              Vector2Int[] dangerArea = new Vector2Int[] { targetCoord };
              CombatEvents.OnHighlightTilesRequested?.Invoke(
                  new TileHighlightRequest(dangerArea, HighlightType.DangerEnemyIntent)
              );
          }
      }
  }
  ```
* **Kriteria Selesai Fase 2:** Seluruh fungsi logika dan kalkulasi koordinat dapat dijalankan dan diuji melalui C# Unit Test.

---

### 🔹 FASE 3: Visualisasi Arena & Sistem Highlight Tilemap
> **Tujuan:** Menggambar ubin arena 15×15 di layar Unity serta menerima koordinat integer logika untuk diwarnai merah, hijau, atau biru.

#### Langkah 3.1: Setup Unity 2D Tilemap Layers
Di Scene Unity, buat susunan Hierarchy:
```text
Grid (Komponen Grid, Cell Size: 1, 1, 0)
├── BaseFloor_Tilemap        (Sorting Layer: Floor)
├── Obstacles_Tilemap        (Sorting Layer: Environment)
└── HighlightOverlay_Tilemap (Sorting Layer: Overlay)
```

#### Langkah 3.2: Skrip Presenter Tilemap (`GridTilemapView.cs`)
* **Lokasi File:** `Assets/Scripts/Grid/GridTilemapView.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/Grid/GridTilemapView.cs
  using UnityEngine;
  using UnityEngine.Tilemaps;

  public class GridTilemapView : MonoBehaviour
  {
      [SerializeField] private Tilemap _highlightTilemap;
      [SerializeField] private TileBase _dangerTileSprite; // Sprite Merah
      [SerializeField] private TileBase _validTileSprite;  // Sprite Hijau
      [SerializeField] private TileBase _moveTileSprite;   // Sprite Biru

      private void OnEnable()
      {
          CombatEvents.OnHighlightTilesRequested += RenderHighlights;
          CombatEvents.OnClearAllHighlights += ClearHighlights;
      }

      private void OnDisable()
      {
          CombatEvents.OnHighlightTilesRequested -= RenderHighlights;
          CombatEvents.OnClearAllHighlights -= ClearHighlights;
      }

      private void RenderHighlights(TileHighlightRequest request)
      {
          TileBase tileToDraw = request.Style switch
          {
              HighlightType.DangerEnemyIntent => _dangerTileSprite,
              HighlightType.ValidCardTarget   => _validTileSprite,
              HighlightType.MovementRange     => _moveTileSprite,
              _ => null
          };

          if (tileToDraw == null) return;

          // Konversi setiap Vector2Int logika menjadi Vector3Int sel Tilemap
          foreach (Vector2Int coord in request.Coordinates)
          {
              Vector3Int cellPos = new Vector3Int(coord.x, coord.y, 0);
              _highlightTilemap.SetTile(cellPos, tileToDraw);
          }
      }

      private void ClearHighlights()
      {
          _highlightTilemap.ClearAllTiles();
      }
  }
  ```
* **Kriteria Selesai Fase 3:** Memanggil event `CombatEvents.OnHighlightTilesRequested` langsung merender ubin merah di Game View pada koordinat yang ditentukan.

---

### 🔹 FASE 4: Entitas Karakter, Pergerakan Grid & Sistem Animasi/VFX
> **Tujuan:** Membuat representasi karakter yang dapat berpindah petak secara halus (*Lerp interpolation*) dan merespons serangan.

#### Langkah 4.1: Controller Pergerakan Halus Antar Ubin
* **Lokasi File:** `Assets/Scripts/Units/UnitMovementView.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/Units/UnitMovementView.cs
  using System.Collections;
  using UnityEngine;

  public class UnitMovementView : MonoBehaviour
  {
      [SerializeField] private int _unitId;
      [SerializeField] private float _moveSpeed = 8f;

      private void OnEnable()
      {
          CombatEvents.OnUnitMoved += HandleUnitMoved;
      }

      private void OnDisable()
      {
          CombatEvents.OnUnitMoved -= HandleUnitMoved;
      }

      private void HandleUnitMoved(UnitMovePayload payload)
      {
          if (payload.UnitId == _unitId)
          {
              // Offset 0.5f agar posisi unit pas tepat di tengah ubin
              Vector3 targetWorldPos = new Vector3(payload.ToCoord.x + 0.5f, payload.ToCoord.y + 0.5f, 0);
              StopAllCoroutines();
              StartCoroutine(MoveRoutine(targetWorldPos));
          }
      }

      private IEnumerator MoveRoutine(Vector3 targetPos)
      {
          while (Vector3.Distance(transform.position, targetPos) > 0.01f)
          {
              transform.position = Vector3.MoveTowards(transform.position, targetPos, _moveSpeed * Time.deltaTime);
              yield return null;
          }
          transform.position = targetPos;
      }
  }
  ```
* **Kriteria Selesai Fase 4:** Menembakkan event `OnUnitMoved` menggerakkan sprite unit ke koordinat tujuan tanpa tersendat (*smooth movement*).

---

### 🔹 FASE 5: Antarmuka Kartu (UI Toolkit) & Sistem Drag-and-Drop
> **Tujuan:** Menampilkan koleksi kartu di tangan pemain dan mendeteksi drop kartu ke petak grid arena.

#### Langkah 5.1: Controller Interaksi Kartu (`CardHandController.cs`)
* **Lokasi File:** `Assets/Scripts/UI/CardHandController.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/UI/CardHandController.cs
  using UnityEngine;
  using UnityEngine.UIElements;

  public class CardHandController : MonoBehaviour
  {
      private UIDocument _uiDocument;
      private VisualElement _handContainer;

      private void Awake()
      {
          _uiDocument = GetComponent<UIDocument>();
          _handContainer = _uiDocument.rootVisualElement.Q<VisualElement>("hand-container");
      }

      // Dipanggil saat pemain melepaskan kartu di atas ubin arena
      public void OnCardDroppedOnGrid(CardData card, Vector2Int targetGridCoord)
      {
          // Siarkan ke Event Bus: Kartu telah dimainkan
          CombatEvents.OnCardPlayed?.Invoke(card, targetGridCoord);

          // Bersihkan semua highlight ubin
          CombatEvents.OnClearAllHighlights?.Invoke();
      }
  }
  ```
* **Kriteria Selesai Fase 5:** Pemain dapat menarik kartu dari UI tangan dan melepaskannya di ubin arena untuk memicu event.

---

### 🔹 FASE 6: State Machine Giliran & Integrasi Scene Utama
> **Tujuan:** Menghubungkan seluruh sistem dalam Finite State Machine (FSM) 4 fase pertempuran yang utuh.

#### Langkah 6.1: Mesin Pengatur Fase Giliran (`CombatStateMachine.cs`)
* **Lokasi File:** `Assets/Scripts/Core/FSM/CombatStateMachine.cs`
* **Kode Implementasi:**
  ```csharp
  // Assets/Scripts/Core/FSM/CombatStateMachine.cs
  using System.Collections;
  using UnityEngine;

  public class CombatStateMachine : MonoBehaviour
  {
      private CombatPhase _currentPhase;

      private void Start()
      {
          StartCoroutine(IntentPhaseRoutine());
      }

      private void ChangePhase(CombatPhase newPhase)
      {
          _currentPhase = newPhase;
          CombatEvents.OnPhaseChanged?.Invoke(newPhase);
      }

      // 1. FASE INTENT: Musuh memasang telegraph
      private IEnumerator IntentPhaseRoutine()
      {
          ChangePhase(CombatPhase.IntentPhase);
          yield return new WaitForSeconds(0.6f);

          // Transisi ke giliran pemain
          StartPlayerPhase();
      }

      // 2. FASE PEMAIN: Menunggu kartu dimainkan
      private void StartPlayerPhase()
      {
          ChangePhase(CombatPhase.PlayerPhase);
          CombatEvents.OnCardPlayed += HandlePlayerAction;
      }

      private void HandlePlayerAction(CardData card, Vector2Int targetCoord)
      {
          CombatEvents.OnCardPlayed -= HandlePlayerAction;
          StartCoroutine(EnemyPhaseRoutine());
      }

      // 3. FASE MUSUH: Musuh mengeksekusi serangan
      private IEnumerator EnemyPhaseRoutine()
      {
          ChangePhase(CombatPhase.EnemyPhase);
          yield return new WaitForSeconds(0.8f);

          StartCoroutine(RoundResetRoutine());
      }

      // 4. FASE RESET: Tarik kartu baru & evaluasi ronde
      private IEnumerator RoundResetRoutine()
      {
          ChangePhase(CombatPhase.RoundResetPhase);
          CombatEvents.OnClearAllHighlights?.Invoke();
          CombatEvents.OnDrawCardsRequested?.Invoke();
          yield return new WaitForSeconds(0.4f);

          // Mulai ronde baru
          StartCoroutine(IntentPhaseRoutine());
      }
  }
  ```

#### Langkah 6.2: Hierarchy Lengkap di `MainBattleScene.unity`
Pastikan scene memiliki susunan objek berikut:
```text
MainBattleScene
├── 🎮 GameMaster_CombatFSM    (CombatStateMachine.cs)
├── 🗺️ Arena_Environment       (GridTilemapView.cs, 2D Lights, Camera Orthographic)
├── 🤺 Units_Container         (UnitMovementView.cs, Nabu & Musuh)
└── 🃏 Canvas_CardUI_HUD       (UIDocument, CardHandController.cs)
```
* **Kriteria Selesai Fase 6:** Satu siklus penuh pertempuran (Intent ➡️ Player Action ➡️ Enemy Attack ➡️ Round Reset) berjalan mulus secara otomatis.

---

## 📋 MATRIKS VERIFIKASI & CHECKLIST SEKUANSIAL

| No | Tahap Pengembangan | Indikator Keberhasilan (*Definition of Done*) |
| :---: | :--- | :--- |
| **1** | **Pondasi Data & Event** | `CombatTypes.cs`, `CombatPayloads.cs`, dan `CombatEvents.cs` bebas error compile. |
| **2** | **Katalog Data** | Minimal 3 aset `.asset` kartu dan 1 musuh tersimpan di Project Window. |
| **3** | **Logika Grid & AI** | Model 15×15 dan kalkulasi arah serangan lolos pengujian C# Unit Test. |
| **4** | **Visual Tilemap** | Ubin berubah warna merah/hijau/biru saat event highlight dipanggil. |
| **5** | **Unit & Pergerakan** | Sprite karakter bergerak halus (*lerp*) dari ubin A ke B saat event gerak aktif. |
| **6** | **UI Toolkit Kartu** | Tangan kartu muncul di layar dan mengirim event `OnCardPlayed` saat di-drag. |
| **7** | **Integrasi FSM** | Siklus 4-fase giliran berjalan berulang tanpa crash dan dapat dimainkan. |
