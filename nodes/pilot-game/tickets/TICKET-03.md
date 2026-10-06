---
id: TICKET-03
title: Otak Logika Grid 15×15 & AI Musuh (Headless Pure C#)
status: Done
priority: High
labels: [Logic, Grid, AI, UnitTests, PureCS]
---

# Deskripsi
Mengembangkan **logika matematika murni (Pure C# Non-MonoBehaviour)** untuk representasi matriks arena 15×15, validasi batas grid, pemeriksaan walkability & rintangan pilar, dan algoritma kalkulasi niat serangan musuh (*Intent Calculator*).

Semua kelas di tiket ini harus 100% *headless* — dapat diuji sepenuhnya melalui Unity Test Framework (NUnit EditMode) **tanpa membuka scene**.

Tiket ini bergantung pada TICKET-01 (membutuhkan `TileType`, `CombatEvents`, `TileHighlightRequest`) dan TICKET-02 (membutuhkan `EnemyData`).

## Acceptance Criteria
- [x] `GridDataModel.cs` (Pure C#, Non-MonoBehaviour):
  - Konstanta `Width = 15` dan `Height = 15`.
  - Field internal: `TileType[,] _tiles` dan `int[,] _occupants` (0 = kosong).
  - Method `bool IsInsideGrid(Vector2Int coord)` — mengembalikan false jika di luar batas 15×15.
  - Method `bool IsWalkable(Vector2Int coord)` — false jika di luar grid, `ObstaclePillar`, atau ditempati unit lain.
  - Method `bool IsStealthed(Vector2Int coord)` — mengembalikan true jika `GetTileType(coord) == TileType.StealthBush` (GDD §4.1).
  - Method `bool IsTargetStealthed(Vector2Int observerCoord, Vector2Int targetCoord)` — mengembalikan true jika target berada di `StealthBush` dan jarak Manhattan >= 2 petak (GDD §4.1).
  - Method `Vector2Int CalculateLinearMoveDestination(Vector2Int start, Vector2Int direction, int distance)` — menghitung titik henti pergerakan; jika jalur melewati tile yang ditempati unit/obstacle, unit akan tertabrak dan berhenti tepat 1 petak di depan rintangan (GDD §4.6 Collision).
  - Method `void SetOccupant(Vector2Int coord, int unitId)` — menetapkan unit (0 untuk kosongkan).
  - Method `int GetOccupant(Vector2Int coord)` — mengembalikan unitId atau 0 jika kosong.
  - Method `void SetTileType(Vector2Int coord, TileType type)` — mengubah tipe ubin.
  - Method `TileType GetTileType(Vector2Int coord)` — mendapatkan tipe ubin.
- [x] `EnemyAICalculator.cs` (Pure C#, Non-MonoBehaviour):
  - Constructor menerima injeksi `GridDataModel`.
  - Rule Bush/Stealth (GDD §4.1): AI musuh tidak dapat menargetkan pemain jika pemain berada di `StealthBush` dan jarak Manhattan >= 2 tile. Musuh harus mendekat tepat 1 petak (bersebelahan) sebelum bisa menargetkan.
  - Method `void PlanLinearAttack(int enemyId, Vector2Int enemyCoord, Vector2Int playerCoord, int attackRange)`:
    - Menghitung arah vektor normalisasi (-1, 0, 1) dan raymarch dengan early break saat menabrak batas grid.
    - Jika target valid: broadcast `CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, dangerArea[0])`.
    - Broadcast `CombatEvents.OnHighlightTilesRequested?.Invoke(new TileHighlightRequest(dangerArea.ToArray(), HighlightType.DangerEnemyIntent))`.
  - Method `void PlanAreaAttack(int enemyId, Vector2Int targetCenter, int radius, AreaShapeType shape)`:
    - Menghitung area AoE multi-bentuk (Square, Diamond, Cross, Circle, DiagonalX, Ring) via `AreaShapeEvaluator.IsOffsetInShape`.
    - Melempar `NotImplementedException` jika tipe bentuk baru belum diimplementasikan.
    - Broadcast highlight merah untuk seluruh ubin area bahaya yang valid di dalam arena.
- [x] `GridLogicTests.cs` (NUnit EditMode Test Suite):
  - Test `IsInsideGrid` — coord (0,0), (14,14) return true; coord (-1,0), (15,0) return false.
  - Test `IsWalkable` — ubin kosong return true; ubin dengan `ObstaclePillar` return false; ubin dengan occupant return false.
  - Test `CalculateLinearMoveDestination` (Collision) — pergerakan 3 petak yang terhalang di petak ke-2 berhenti di petak ke-1 (GDD §4.6).
  - Test `StealthBush` targeting — target di dalam semak pada jarak >= 2 petak ditolak/diabaikan oleh AI, tetapi pada jarak 1 petak diterima (GDD §4.1).
  - Test `PlanAreaAttack` Square & Diamond radius 1.
  - Test `PlanAreaAttack_UnimplementedShape_ThrowsNotImplementedException`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Grid/GridDataModel.cs`
- `Assets/Scripts/Units/EnemyAICalculator.cs`
- `Assets/Tests/EditMode/GridLogicTests.cs`

## Dependensi
- **Bergantung pada:** TICKET-01 (`TileType`, `HighlightType`, `CombatEvents`, `TileHighlightRequest`).
- **Digunakan oleh:** TICKET-04 (view merespons event highlight), TICKET-07 (FSM memanggil `EnemyAICalculator`).

## Catatan Teknis
- Gunakan **Dependency Injection via Constructor** untuk `GridDataModel` — jangan menggunakan Singleton atau `FindObjectOfType`.
- `EnemyAICalculator` DILARANG mewarisi `MonoBehaviour`.
- Koordinat grid berbasis 0 (kiri-bawah = `[0,0]`, kanan-atas = `[14,14]`).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Menulis `GridDataModel.cs` di namespace `PilotGame.Grid` mencakup matriks 15x15, walkability 3-tahap, deteksi stealth bush, dan raymarching collision movement.
  2. Menulis `EnemyAICalculator.cs` dan `AreaShapeEvaluator` di namespace `PilotGame.Units` mendukung evaluasi raymarch linear attack dan area attack AoE multi-bentuk (Square, Diamond, Cross, Circle, DiagonalX, Ring) dengan validasi `NotImplementedException`.
  3. Menulis `GridLogicTests.cs` suite NUnit EditMode lengkap menguji seluruh aturan grid, stealth bush, collision, dan AoE pattern matching.
- **Keputusan Desain & Arsitektur:**
  - Menerapkan C# Switch Expression pattern matching pada `AreaShapeEvaluator` agar ringkas, performan tinggi, dan aman dari unhandled enum type.
  - Aturan Stealth Bush diselaraskan ke `distance >= 2` (pemain di semak hanya dapat dilihat pada jarak 1 petak / bersebelahan).
- **Ringkasan File Terpengaruh:**
  - `Assets/Scripts/Grid/GridDataModel.cs`
  - `Assets/Scripts/Units/EnemyAICalculator.cs`
  - `Assets/Tests/EditMode/GridLogicTests.cs`
- **Catatan & Temuan Tak Terduga:**
  - Raymarch linear attack dioptimasi menggunakan `break` seketika saat keluar batas grid.
