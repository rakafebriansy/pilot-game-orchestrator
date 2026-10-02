---
id: TICKET-09B
title: Merchant Shop UI — Beli Kartu, Relic & Purge Deck
status: Todo
priority: Medium
labels: [UI, Shop, Merchant, Domain3, Fase2]
---

# Deskripsi
Membangun **layar Merchant Shop** — node toko di peta eksplorasi di mana pemain dapat membelanjakan **Gold** (mata uang in-run) untuk membeli kartu langka, item relic, atau menghapus kartu buruk dari deck (*Deck Purge*). Ini adalah deliverable Milestone 3 Domain 3 (diagram D3).

## Acceptance Criteria
- [ ] `GoldManager.cs` (MonoBehaviour):
  - Property `int CurrentGold` — gold in-run (reset setiap run baru).
  - Method `bool SpendGold(int amount)` — kurangi gold, return false jika tidak cukup.
  - Method `void AddGold(int amount)` — tambah gold (dari loot musuh, event, dll).
  - Subscribe `CombatEvents.OnUnitDamaged` → jika kill enemy: tambah gold sesuai tipe musuh (Minion: 5g, Regular: 15g, Elite: 35g).
- [ ] `ShopData.cs` (`ScriptableObject`) — katalog item toko:
  - `List<ShopCardItem> CardStock` — 5 kartu random dari pool saat masuk toko.
  - `List<ShopRelicItem> RelicStock` — 3 relic random.
  - `int DeckPurgeCost` (default: 75 gold) — biaya hapus 1 kartu dari deck.
- [ ] `ShopScreenController.cs` (MonoBehaviour):
  - Method `void GenerateShopInventory()` — generate item random saat masuk toko.
  - Panel "Kartu Dijual": 5 card visual dengan harga masing-masing (30-60 gold).
  - Panel "Relic": 3 relic item visual dengan harga (80-150 gold).
  - Panel "Purge Deck": daftar deck pemain → klik kartu → konfirmasi bayar → hapus dari deck.
  - Tombol "Pergi" untuk keluar toko tanpa membeli apapun.
- [ ] `RelicData.cs` (`ScriptableObject`):
  - `string RelicId`, `string RelicName`, `string Effect`, `int Price`.
  - `RelicEffectType Type` — Enum: `PassiveHP`, `DamageBonus`, `GoldMultiplier`, `ExtraCardDraw`, dll.
- [ ] Minimal 5 relic `.asset` awal di `Assets/ScriptableObjects/Relics/`.
- [ ] `RelicManager.cs` — menyimpan relic aktif dan mengaplikasikan efeknya di run.
- [ ] **Verifikasi:** Masuk toko → beli 1 kartu → gold berkurang → kartu masuk deck → keluar toko → deck bertambah.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Map/GoldManager.cs`
- `Assets/Scripts/Core/Data/ShopData.cs`
- `Assets/Scripts/Core/Data/RelicData.cs`
- `Assets/Scripts/Map/RelicManager.cs`
- `Assets/Scripts/UI/ShopScreenController.cs`
- `Assets/UI/UXML/ShopScreenUI.uxml`
- `Assets/UI/USS/ShopScreen.uss`
- `Assets/ScriptableObjects/Relics/` (5 file `.asset`)

## Dependensi
- **Bergantung pada:** TICKET-09 (MapManager untuk navigasi), TICKET-06 (DeckManager untuk add/remove kartu).
- **Digunakan oleh:** TICKET-10 (DraftScreen dapat referensi ShopData pool), TICKET-13 (GoldManager state perlu di-save di RunSaveData).

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
