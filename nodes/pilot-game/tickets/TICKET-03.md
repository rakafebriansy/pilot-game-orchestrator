---
id: TICKET-03
title: Otak Logika Grid 15×15 & AI Musuh (Headless Pure C#)
status: Todo
priority: High
labels: [Logic, Grid, AI, UnitTests, PureCS]
---

# Deskripsi
Mengembangkan **logika matematika murni (Pure C# Non-MonoBehaviour)** untuk representasi matriks arena 15×15, validasi batas grid, pemeriksaan walkability & rintangan pilar, dan algoritma kalkulasi niat serangan musuh (*Intent Calculator*).

Semua kelas di tiket ini harus 100% *headless* — dapat diuji sepenuhnya melalui Unity Test Framework (NUnit EditMode) **tanpa membuka scene**.

Tiket ini bergantung pada TICKET-01 (membutuhkan `TileType`, `CombatEvents`, `TileHighlightRequest`) dan TICKET-02 (membutuhkan `EnemyData`).

## Acceptance Criteria
- [ ] `GridDataModel.cs` (Pure C#, Non-MonoBehaviour):
  - Konstanta `Width = 15` dan `Height = 15`.
  - Field internal: `TileType[,] _tiles` dan `int[,] _occupants` (0 = kosong).
  - Method `bool IsInsideGrid(Vector2Int coord)` — mengembalikan false jika di luar batas 15×15.
  - Method `bool IsWalkable(Vector2Int coord)` — false jika di luar grid, `ObstaclePillar`, atau ditempati unit lain.
  - Method `void SetOccupant(Vector2Int coord, int unitId)` — menetapkan unit (0 untuk kosongkan).
  - Method `int GetOccupant(Vector2Int coord)` — mengembalikan unitId atau 0 jika kosong.
  - Method `void SetTileType(Vector2Int coord, TileType type)` — mengubah tipe ubin.
  - Method `TileType GetTileType(Vector2Int coord)` — mendapatkan tipe ubin.
- [ ] `EnemyAICalculator.cs` (Pure C#, Non-MonoBehaviour):
  - Constructor menerima injeksi `GridDataModel`.
  - Method `void PlanLinearAttack(int enemyId, Vector2Int enemyCoord, Vector2Int attackDirection)`:
    - Menghitung koordinat target 2 petak ke depan.
    - Jika target berada di dalam grid: broadcast `CombatEvents.OnEnemyIntentDecided?.Invoke(enemyId, targetCoord)`.
    - Broadcast `CombatEvents.OnHighlightTilesRequested?.Invoke(new TileHighlightRequest(dangerArea, HighlightType.DangerEnemyIntent))`.
  - Method `void PlanAreaAttack(int enemyId, Vector2Int enemyCoord, int radius)`:
    - Menghitung semua ubin dalam radius Manhattan dari enemyCoord.
    - Broadcast highlight merah untuk semua ubin tersebut.
- [ ] `GridLogicTests.cs` (NUnit EditMode Test Suite):
  - Test `IsInsideGrid` — coord (0,0), (14,14) return true; coord (-1,0), (15,0) return false.
  - Test `IsWalkable` — ubin kosong return true; ubin dengan `ObstaclePillar` return false; ubin dengan occupant return false.
  - Test `PlanLinearAttack` — event `OnEnemyIntentDecided` terpanggil dengan koordinat target yang benar (enemyCoord + direction * 2).
  - Test batas arena: serangan yang melampaui batas grid tidak men-trigger event.

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
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  - *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
