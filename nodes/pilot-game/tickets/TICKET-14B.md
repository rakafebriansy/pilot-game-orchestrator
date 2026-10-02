---
id: TICKET-14B
title: Sistem Inventaris Stash vs Wearable & Boss Equipment Loot
status: Todo
priority: Medium
labels: [Inventory, Stash, Equipment, Loot, Domain3, Fase3]
---

# Deskripsi
Mengimplementasikan **Sistem Inventaris Luar/Dalam Run (Stash vs Wearables)** dan alur loot perlengkapan/artefak dari Boss sesuai GDD §4.3:
- **Stash (Gudang Eksternal):** Ruang penyimpanan besar di markas *Sanctuary* yang persisten antar-run.
- **Wearable Slots (Loadout Ekspedisi):** Slot inventaris terbatas (misal: 3 slot Relic/Wearable dan 3 slot Consumable) yang dibawa pemain saat memulai ekspedisi baru.
- **Boss Loot Distribution:** Setelah mengalahkan Boss, pemain mendapatkan loot item/relic khusus yang dapat langsung dipasang (jika slot kosong/replace) atau dikirim ke Stash saat kembali ke Sanctuary.

## Acceptance Criteria

### A. Data & Logic Layer (`InventoryManager.cs` & `EquipmentData.cs`)
- [ ] `EquipmentData.cs` (`ScriptableObject`):
  - `string ItemId`, `string ItemName`, `string Description`, `Sprite Icon`.
  - `EquipmentSlotType SlotType` (`Weapon`, `Armor`, `Trinket`, `Relic`).
  - `List<CardData> GrantedCards` — kartu bonus yang ditambahkan ke deck jika item dipakai.
  - `StatModifier PassiveStatBonus` (misal: +MaxHP, +BonusShield).
- [ ] `InventoryManager.cs` (Pure C# / MonoBehaviour):
  - Field `List<EquipmentData> StashItems` (kapasitas besar, disimpan di SaveData).
  - Field `EquipmentData[] WearableSlots` (kapasitas 3 slot aktif).
  - Method `bool EquipItem(EquipmentData item, int targetSlot)` — pasang item ke slot wearable; jika slot terisi, swap/replace dengan item sebelumnya ke Stash.
  - Method `bool UnequipItem(int slotIndex)` — lepas item dan pindahkan kembali ke Stash.
  - Method `void AddToStash(EquipmentData item)` — simpan item baru hasil loot boss ke Stash.

### B. Antarmuka UI Inventaris (`InventoryScreenController.cs` + UXML/USS)
- [ ] Tab / Layar Inventaris di Markas Sanctuary ([TICKET-14](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-14.md)):
  - Grid View untuk **Stash** (katalog item tersimpan dengan pagination/scroll).
  - 3 Panel **Wearable Slots** di sisi kanan (menampilkan item aktif yang akan dibawa ke run).
  - Panel rincian item saat di-hover/klik (deskripsi, passive bonus, bonus kartu yang didapat).
- [ ] Interaksi Drag-and-Drop atau Klik untuk memindahkan item antara Stash dan Wearable Slots.

### C. Boss Loot Modal (`BossLootModalController.cs` + UXML)
- [ ] Muncul setelah Boss dikalahkan ([TICKET-12](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12.md)):
  - Menampilkan 2-3 pilihan item *Boss Loot*.
  - Pemain memilih 1 item untuk dibawa atau dikirim ke Stash.

### D. Unit Tests
- [ ] EditMode Test `InventoryLogicTests.cs`:
  - Test `EquipItem` menambahkan bonus stat/kartu ke loadout.
  - Test `EquipItem` saat slot penuh melakukan swap/replace ke Stash secara aman tanpa kehilangan item.
  - Test `SaveDataManager` menyimpan isi Stash dan Wearable slots ke file JSON lokal secara presisi.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Inventory/EquipmentData.cs`
- `Assets/Scripts/Inventory/InventoryManager.cs`
- `Assets/Scripts/UI/InventoryScreenController.cs`
- `Assets/Scripts/UI/BossLootModalController.cs`
- `Assets/UI/UXML/InventoryScreenUI.uxml`
- `Assets/UI/USS/InventoryScreenUI.uss`
- `Assets/Tests/EditMode/InventoryLogicTests.cs`

## Dependensi
- **Bergantung pada:** [TICKET-02](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md) (`CardData`), [TICKET-13](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-13.md) (`SaveDataManager`), [TICKET-14](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-14.md) (`SanctuaryScreenController`).
- **Digunakan oleh:** [TICKET-07](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-07.md) (inisialisasi deck & stat pertempuran dengan bonus item wearable).

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
