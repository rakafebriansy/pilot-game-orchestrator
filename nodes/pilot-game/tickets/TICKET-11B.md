---
id: TICKET-11B
title: Sistem Checkpoint Ekspedisi & Pengorbanan Skill (Max 3 Checkpoints)
status: Todo
priority: Medium
labels: [Map, Checkpoint, MetaProgression, UI, Domain4, Fase2]
---

# Deskripsi
Mengimplementasikan **Sistem Checkpoint Ekspedisi** sesuai spesifikasi GDD §4.4:
- Pemain dibekali maksimal **3 slot checkpoint** per run yang bisa diletakkan secara bebas di node peta mana pun (misal: sebelum node Elite atau Boss).
- Penempatan checkpoint bersifat permanen dalam run tersebut (tidak bisa ditarik kembali).
- Penempatan memiliki konsekuensi/harga: pemain harus **mengorbankan 1 skill/kartu** dari deck aktifnya untuk mengaktifkan checkpoint.
- Jika pemain gugur (HP = 0) saat checkpoint aktif, pemain dapat memilih untuk bangkit di checkpoint terakhir dengan deck yang sudah dikurangi skill yang dikorbankan tersebut alih-alih langsung Game Over total.

## Acceptance Criteria

### A. Logika Checkpoint (`CheckpointManager.cs` - Pure C# / MonoBehaviour)
- [ ] Field `int MaxCheckpoints = 3`.
- [ ] Field `int AvailableCheckpoints` (default: 3 di awal run).
- [ ] Field `List<CheckpointData> ActiveCheckpoints`:
  - Struct/Class `CheckpointData`: `string NodeId`, `int FloorNumber`, `CardData SacrificedCard`, `int PlayerHPAtCheckpoint`.
- [ ] Method `bool CanPlaceCheckpoint(string nodeId, List<CardData> currentDeck)`:
  - Return true jika `AvailableCheckpoints > 0` dan deck memiliki > 1 kartu.
- [ ] Method `bool PlaceCheckpoint(string nodeId, CardData sacrificedCard, int currentHP)`:
  - Kurangi `AvailableCheckpoints` sebanyak 1.
  - Hapus `sacrificedCard` dari deck pemain.
  - Simpan snapshot status checkpoint ke `ActiveCheckpoints`.
  - Broadcast `CombatEvents.OnCheckpointPlaced?.Invoke(nodeId)`.
- [ ] Method `CheckpointData GetLatestCheckpoint()`:
  - Mengembalikan checkpoint paling akhir yang diaktifkan, atau `null` jika tidak ada.
- [ ] Method `void RespawnAtCheckpoint()`:
  - Pulihkan posisi map ke node checkpoint, set HP ke `PlayerHPAtCheckpoint` (atau 50% MaxHP), dan load kembali map state.

### B. Antarmuka UI Checkpoint (`CheckpointModalController.cs` + UXML/USS)
- [ ] Tombol "Set Checkpoint (Tersisa: X/3)" di layar Peta Rute ([TICKET-09](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09.md)).
- [ ] Saat tombol diklik:
  - Muncul modal dialog konfirmasi penempatan checkpoint.
  - Menampilkan daftar kartu di deck saat ini untuk dipilih sebagai korban (*Sacrifice Skill*).
  - Peringatan: *"Penempatan checkpoint tidak dapat dibatalkan. Kartu yang dikorbankan akan hilang dari run ini."*
  - Tombol Konfirmasi "Kurbankan & Pasang Checkpoint" dan tombol Batal.
- [ ] Visual pin / marker checkpoint menyala pada node map terkait.

### C. Unit Tests & Verifikasi
- [ ] EditMode Test `CheckpointLogicTests.cs`:
  - Test pasang checkpoint mengurangi slot dari 3 menjadi 2.
  - Test kartu yang dikorbankan berhasil terhapus dari deck.
  - Test batas maksimum 3 checkpoint — penempatan ke-4 ditolak (`return false`).
  - Test respawn mengembalikan checkpoint snapshot state dengan benar.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Map/CheckpointManager.cs`
- `Assets/Scripts/Map/CheckpointData.cs`
- `Assets/Scripts/UI/CheckpointModalController.cs`
- `Assets/UI/UXML/CheckpointModalUI.uxml`
- `Assets/UI/USS/CheckpointModalUI.uss`
- `Assets/Tests/EditMode/CheckpointLogicTests.cs`

## Dependensi
- **Bergantung pada:** [TICKET-08](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-08.md) (`MapNodeData`), [TICKET-09](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09.md) (`MapScreenController`), [TICKET-02](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md) (`CardData`).
- **Digunakan oleh:** [TICKET-13](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-13.md) (`SaveDataManager` untuk persistensi checkpoint aktif dalam run).

## Catatan Teknis
- Checkpoint hanya berlaku di dalam *current run*, di-reset saat ekspedisi berakhir (selesai/menyerah).
- Biaya pengorbanan kartu adalah keputusan strategis berisiko tinggi (*High Risk / Safe Net*).

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
