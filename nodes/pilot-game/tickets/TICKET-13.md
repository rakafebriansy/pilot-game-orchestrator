---
id: TICKET-13
title: Sistem Konversi Poin Ekspedisi & Penyimpanan Save Data Lokal
status: Todo
priority: High
labels: [Persistence, SaveData, MetaProgression, Fase3]
---

# Deskripsi
Mengimplementasikan **sistem penyimpanan data permainan lokal** menggunakan JSON serialization, termasuk penyimpanan state mid-run (deck, HP, posisi di peta) dan state meta-progression antar-run (poin ekspedisi, talenta yang dibuka).

**Poin Ekspedisi** adalah mata uang meta yang diperoleh dari setiap run (berhasil atau gagal), yang dapat dibelanjakan di Sanctuary (TICKET-14) untuk membuka upgrade permanen.

## Acceptance Criteria

### A. Poin Ekspedisi
- [ ] `ExpeditionPointsManager.cs` (MonoBehaviour, DontDestroyOnLoad):
  - Field `int TotalExpeditionPoints` — total poin yang dikumpulkan lintas run.
  - Method `void AddPoints(int amount, ExpeditionPointSource source)` — tambah poin.
  - Enum `ExpeditionPointSource`: `EnemyKilled` (5 pts), `BossKilled` (50 pts), `RunCompleted` (100 pts), `RunFailed` (10 pts), `NarrativeEvent` (variable).
  - Setiap kill musuh, kematian boss, dan hasil akhir run secara otomatis menambah poin.
  - Subscribe event-event yang relevan (`OnUnitDamaged` → cek HP 0 → tambah poin sesuai tipe unit).

### B. SaveData System
- [ ] `RunSaveData.cs` (Serializable POCO Class):
  - `int CurrentHP`, `int MaxHP`.
  - `List<string> DeckCardIds` — ID kartu saat ini di deck.
  - `string CurrentMapNodeId`, `List<string> CompletedNodeIds`.
  - `int ChapterIndex`, `int RunNumber`.
  - `long SaveTimestampEpochMillis` — timestamp save dalam Epoch Milliseconds (sesuai `database.md` guideline).
- [ ] `MetaSaveData.cs` (Serializable POCO Class):
  - `int TotalExpeditionPoints`.
  - `List<string> UnlockedTalentIds` — ID talenta yang sudah dibuka.
  - `int TotalRunsCompleted`, `int TotalRunsFailed`.
  - `long LastSavedTimestampEpochMillis`.
- [ ] `SaveDataManager.cs` (MonoBehaviour, DontDestroyOnLoad):
  - Method `void SaveRunData(RunSaveData data)` — serialize ke JSON → tulis ke `Application.persistentDataPath/save_run.json`.
  - Method `RunSaveData LoadRunData()` — baca file → deserialize. Return null jika tidak ada.
  - Method `void SaveMetaData(MetaSaveData data)` — serialize ke `save_meta.json`.
  - Method `MetaSaveData LoadMetaData()` — baca dan deserialize `save_meta.json`.
  - Method `void DeleteRunData()` — hapus `save_run.json` (dipanggil saat run selesai atau game over).
  - Gunakan `JsonUtility.ToJson` / `JsonUtility.FromJson` untuk serialization.
- [ ] **Auto-save:** Data run di-save otomatis setelah setiap node selesai dikunjungi.
- [ ] **Unit test** `SaveDataTests.cs` (NUnit EditMode): Verifikasi serialize → deserialize menghasilkan data yang identik.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/RunSaveData.cs`
- `Assets/Scripts/Core/Data/MetaSaveData.cs`
- `Assets/Scripts/Core/SaveDataManager.cs`
- `Assets/Scripts/Map/ExpeditionPointsManager.cs`
- `Assets/Tests/EditMode/SaveDataTests.cs`

## Dependensi
- **Bergantung pada:** TICKET-08 (`MapLayout`, node traversal state), TICKET-02 (`CardData` ID untuk deck saving).
- **Digunakan oleh:** TICKET-14 (membaca `TotalExpeditionPoints` untuk belanja di Sanctuary).

## Catatan Teknis
- **WAJIB mengikuti `database.md` guideline:** Semua timestamp disimpan sebagai **Epoch Milliseconds** (`long`) bukan `DateTime` string.
- Gunakan `Application.persistentDataPath` bukan path hardcode untuk kompatibilitas multi-platform.
- Untuk mid-run save, panggil `SaveDataManager.SaveRunData()` setelah setiap node selesai.
- Untuk save meta, panggil `SaveDataManager.SaveMetaData()` setelah setiap run berakhir (menang atau kalah).

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
