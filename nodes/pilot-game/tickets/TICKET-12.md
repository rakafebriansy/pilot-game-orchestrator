---
id: TICKET-12
title: Roster Boss Chapter 1 — Mekanik Serangan Multi-Tile & Fase Enrage
status: Todo
priority: High
labels: [Boss, Combat, AI, Enemies, Fase3]
---

# Deskripsi
Mengimplementasikan **Boss pertama Chapter 1** dari Menara Babel dengan mekanik pertempuran yang secara signifikan lebih kompleks dari musuh biasa. Boss memiliki **serangan multi-tile yang dapat menyapu area**, pola AI yang scripted, dan **Fase Enrage** yang aktif ketika HP di bawah 50%.

Berdasarkan data desain dari `pilot-game-team-docs/01_game_design/enemies.md` (kategori Boss).

## Acceptance Criteria

### A. Data Boss
- [ ] `BossData.cs` (`ScriptableObject`) extends `EnemyData`:
  - `int EnrageThresholdPercent` — persentase HP yang memicu Fase Enrage (default: 50).
  - `List<BossAttackPattern> AttackPatterns` — daftar pola serangan (normal + enrage).
  - Inner class `BossAttackPattern`: `string PatternName`, `List<Vector2Int> AffectedTileOffsets`, `int Damage`, `bool IsEnrageOnly`.
  - `[CreateAssetMenu]` menggunakan menuName: `"PilotGame/Boss Data"`.
- [ ] File asset `Boss_ArchivistSentinel.asset` dibuat berdasarkan lore Menara Babel:
  - HP: 200 | Gerak: 1 tile | Fase Enrage: < 100 HP.
  - Attack Pattern 1 (Normal): Sapu 3 horizontal tiles di depannya (15 damage).
  - Attack Pattern 2 (Normal): Mundur 1 tile + sorot seluruh row horizontal di depannya (telegraph 1 ronde, execute ronde berikutnya).
  - Attack Pattern 3 (Enrage): Sapu 5 tiles berbentuk cross/plus di sekitarnya (25 damage) + telegraph berkedip.

### B. BossAIController.cs (MonoBehaviour)
- [ ] Subscribe `CombatEvents.OnPhaseChanged` untuk memantau IntentPhase.
- [ ] Method `void DecideNextPattern()`:
  - Jika HP > EnrageThreshold: pilih dari normal patterns secara berurutan (*scripted rotation*).
  - Jika HP ≤ EnrageThreshold dan belum Enrage: broadcast `OnBossEnrageActivated` (event baru) → mulai Enrage pattern.
  - Jika Enrage aktif: pilih dari enrage patterns, dengan kemungkinan random.
- [ ] Method `IEnumerator ExecuteAttackPattern(BossAttackPattern pattern)`:
  - Broadcast `OnHighlightTilesRequested` dengan tile offset pattern (merah berkedip selama 1 detik).
  - Yield wait 1 detik (telegraph window untuk pemain merespons).
  - Broadcast `OnUnitDamaged` untuk setiap unit yang berada di tile yang ditandai.
- [ ] Komponen `BossHealthBarPresenter.cs` — menampilkan HP bar boss yang besar di bagian atas layar dengan indikator threshold Enrage.

### C. Boss Battle Scene Integration
- [ ] Boss memiliki prefab terpisah `Boss_ArchivistSentinel_Prefab.prefab` dengan komponen `BossAIController`.
- [ ] Boss dapat di-spawn di `MainBattleScene` sebagai special encounter yang di-trigger dari `MapManager`.
- [ ] **Verifikasi:** Saat HP boss < 50%, animation/visual boss berubah (warna sprite merah atau efek shader) dan pola serangan berubah ke Enrage pattern.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/BossData.cs`
- `Assets/Scripts/Units/BossAIController.cs`
- `Assets/Scripts/UI/BossHealthBarPresenter.cs`
- `Assets/ScriptableObjects/Enemies/Boss_ArchivistSentinel.asset`
- `Assets/Prefabs/Units/Boss_ArchivistSentinel_Prefab.prefab`

## Dependensi
- **Bergantung pada:** TICKET-01 (events), TICKET-03 (`EnemyAICalculator` sebagai referensi pola), TICKET-07 (FSM harus mendukung boss encounter).
- **Digunakan oleh:** TICKET-09 (boss encounter di-trigger dari map node type `BossEncounter`).

## Catatan Teknis
- `BossAttackPattern.AffectedTileOffsets` adalah list Vector2Int relatif terhadap posisi boss. Contoh: sapu horizontal `[(0,0),(1,0),(2,0)]`.
- Event `OnBossEnrageActivated` perlu ditambahkan ke `CombatEvents.cs` (update TICKET-01 jika perlu, atau tambahkan langsung).
- Telegraph berkedip diimplementasikan dengan coroutine yang toggle tile highlight on/off setiap 0.3 detik selama 1 detik.

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
