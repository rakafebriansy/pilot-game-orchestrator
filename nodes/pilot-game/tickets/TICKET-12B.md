---
id: TICKET-12B
title: Combat Wave Spawner & Floor Progression Scaling
status: Todo
priority: High
labels: [Logic, WaveSpawner, Difficulty, Scaling, Domain4, Fase3]
---

# Deskripsi
Mengimplementasikan **sistem spawn gelombang musuh** secara otomatis berdasarkan posisi pemain di peta Menara Babel. Sistem ini mengatur komposisi musuh per encounter (berapa Minion, Regular, Elite) sesuai kurva kesulitan lantai (*Floor Progression Scaling*), menggantikan hardcoded enemy placement dari TICKET-07.

## Acceptance Criteria
- [ ] `WaveCompositionData.cs` (`ScriptableObject`):
  - `int MinimumFloor`, `int MaximumFloor` — range lantai yang menggunakan komposisi ini.
  - `List<EnemySpawnEntry> SpawnEntries` — daftar musuh yang bisa muncul.
  - `int MinEnemyCount`, `int MaxEnemyCount` — jumlah musuh per encounter.
  - Inner class `EnemySpawnEntry`: `EnemyData EnemyPrefab`, `int Weight` (probabilitas spawn).
- [ ] `CombatWaveSpawner.cs` (MonoBehaviour):
  - Method `void SpawnWave(int currentFloor)`:
    - Pilih `WaveCompositionData` yang sesuai untuk `currentFloor`.
    - Tentukan jumlah musuh random dalam range Min-Max.
    - Pilih tipe musuh berdasarkan weighted probability.
    - Instantiate prefab musuh di spawn points acak (tidak tumpang tindih dengan Nabu).
  - Method `void ClearWave()` — destroy semua musuh aktif saat wave selesai.
  - Method `bool IsWaveComplete()` — return true jika semua musuh HP ≤ 0.
- [ ] Minimal **4 WaveCompositionData `.asset`**:
  - `Wave_Floor1to3.asset` — 2-3 Minion saja.
  - `Wave_Floor4to6.asset` — 1-2 Minion + 1 Regular.
  - `Wave_Floor7to9.asset` — 1 Minion + 2 Regular.
  - `Wave_Floor10Plus.asset` — 1 Regular + 1 Elite.
- [ ] Balancing Stats (sesuai Domain 4 Milestone 3):
  - HP Nabu awal: 30.
  - Damage kartu Attack range: 4-15.
  - Shield musuh dasar: 0 (Minion), 4 (Regular), 8 (Elite).
  - Efek status Bleed: 2 damage/round, Vulnerable: +50% damage received.
- [ ] `EnemySpawnPoint[]` di scene: array minimal 9 posisi spawn valid (tidak di tile obstacle atau spawn area Nabu).
- [ ] Integration: `CombatStateMachine.IntentPhaseState.Enter()` memanggil `CombatWaveSpawner.SpawnWave(currentFloor)` jika wave belum spawn.
- [ ] Unit test `WaveSpawnerTests.cs`: verifikasi `IsWaveComplete()` return true setelah semua unit HP = 0.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/WaveCompositionData.cs`
- `Assets/Scripts/Grid/CombatWaveSpawner.cs`
- `Assets/ScriptableObjects/Waves/` (4 file `.asset`)
- `Assets/Tests/EditMode/WaveSpawnerTests.cs`

## Dependensi
- **Bergantung pada:** TICKET-02C (9 EnemyData SO), TICKET-07 (FSM harus mendukung wave spawner hook).
- **Digunakan oleh:** TICKET-12C (bioma menentukan spawn point visual), TICKET-13 (kill count dari wave untuk expedition points).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
