# 📖 Manual Guide: TICKET-14B — Sistem Inventaris Stash vs Wearable & Boss Equipment Loot

> **Referensi Tiket:** [TICKET-14B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-14B.md)  
> **Domain:** `[🃏 DOMAIN 3: CARD DECK & UI]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan pemisahan sistem inventaris luar vs dalam run sesuai GDD §4.3 & §4.7:
1. **Stash (Gudang Eksternal Sanctuary)**: Kapasitas besar untuk menyimpan relik/equipment yang didapat dari berbagai ekspedisi.
2. **Wearable Slots (Loadout Ekspedisi)**: 3 Slot terbatas yang dibawa karakter masuk ke dalam pertempuran Menara Babel.
3. **Modal Distribusi Boss Loot**: Layar pemilihan hadiah equipment pasca-kemenangan bos.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Inventory/
│       ├── EquipmentData.cs
│       ├── InventoryManager.cs
│       ├── InventoryScreenController.cs
│       └── BossLootModalController.cs
└── UI/
    ├── UXML/
    │   └── InventoryScreenUI.uxml
    └── USS/
        └── InventoryScreenUI.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Inventory/EquipmentData.cs` & `InventoryManager.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Persistence;

namespace PilotGame.Inventory
{
    public enum EquipmentSlotType
    {
        Weapon,
        Armor,
        RelicTrinket
    }

    [CreateAssetMenu(fileName = "NewEquipment", menuName = "PilotGame/Data/Equipment Data")]
    public class EquipmentData : ScriptableObject
    {
        public string Id;
        public string Name;
        [TextArea(2, 3)]
        public string Description;
        public Sprite Icon;
        public EquipmentSlotType SlotType;
        public List<CardData> GrantedCards = new();
        public int BonusMaxHP = 0;
        public int BonusShield = 0;
    }

    /// <summary>
    /// Mengelola inventaris dua lapis: Gudang Sanctuary (Stash) dan 3 Slot Beban Tempur (Wearable Slots).
    /// </summary>
    public class InventoryManager : MonoBehaviour
    {
        public const int MaxWearableSlots = 3; // Batas beban ekspedisi 3 item (GDD §4.3)

        public List<EquipmentData> StashItems = new(); // Gudang penyimpanan kapasitas besar
        public EquipmentData[] WearableSlots = new EquipmentData[MaxWearableSlots]; // Slot aktif dibawa ke run

        /// <summary>
        /// Memasang equipment dari Stash ke slot Wearable:
        /// - Jika slot target sudah terisi, pindahkan item lama kembali ke Stash secara otomatis (Auto-swap).
        /// - Pasang item baru ke slot target dan hapus dari Stash.
        /// </summary>
        public bool EquipItem(EquipmentData item, int targetSlotIndex)
        {
            if (item == null || targetSlotIndex < 0 || targetSlotIndex >= MaxWearableSlots) return false;

            // 1. Jika slot target sudah ada item, kembalikan item lama ke Stash
            if (WearableSlots[targetSlotIndex] != null)
            {
                StashItems.Add(WearableSlots[targetSlotIndex]);
            }

            // 2. Pasang item baru ke slot aktif dan keluarkan dari Stash
            StashItems.Remove(item);
            WearableSlots[targetSlotIndex] = item;
            Debug.Log($"[Inventory] Berhasil memasang {item.Name} di slot {targetSlotIndex + 1}");
            return true;
        }

        /// <summary>
        /// Melepaskan equipment dari slot Wearable dan mengembalikannya ke Stash.
        /// </summary>
        public bool UnequipItem(int slotIndex)
        {
            if (slotIndex < 0 || slotIndex >= MaxWearableSlots || WearableSlots[slotIndex] == null) return false;

            // Pindahkan kembali ke Stash dan kosongkan slot
            StashItems.Add(WearableSlots[slotIndex]);
            WearableSlots[slotIndex] = null;
            return true;
        }
    }
}
```

---

### B. `Assets/Scripts/Inventory/BossLootModalController.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UIElements;
using PilotGame.Inventory;

namespace PilotGame.UI
{
    /// <summary>
    /// Mengontrol modal pemilihan hadiah equipment langka pasca-mengalahkan Boss Lantai 15 (GDD §4.7).
    /// </summary>
    [RequireComponent(typeof(UIDocument))]
    public class BossLootModalController : MonoBehaviour
    {
        [SerializeField] private InventoryManager _inventory;
        [SerializeField] private List<EquipmentData> _bossLootPool = new();

        private UIDocument _uiDocument;
        private VisualElement _lootOptionsContainer;

        /// <summary>
        /// Membuka modal reward dan menampilkan hingga 3 pilihan equipment langka.
        /// </summary>
        public void OpenBossLootModal()
        {
            gameObject.SetActive(true);
            var root = _uiDocument.rootVisualElement;
            _lootOptionsContainer = root.Q<VisualElement>("loot-options-container");

            _lootOptionsContainer.Clear();

            // Berikan maksimal 3 opsi equipment langka dari loot pool
            for (int i = 0; i < 3 && i < _bossLootPool.Count; i++)
            {
                var equip = _bossLootPool[i];
                var box = new VisualElement();
                box.AddToClassList("loot-card-box");

                var nameLbl = new Label(equip.Name);
                var descLbl = new Label(equip.Description);
                var claimBtn = new Button(() => ClaimLoot(equip));
                claimBtn.text = "Ambil & Simpan ke Stash";

                box.Add(nameLbl);
                box.Add(descLbl);
                box.Add(claimBtn);
                _lootOptionsContainer.Add(box);
            }
        }

        /// <summary>
        /// Mengklaim item pilihan dan menyimpannya langsung ke Stash Sanctuary permanen.
        /// </summary>
        private void ClaimLoot(EquipmentData equip)
        {
            _inventory.StashItems.Add(equip);
            Debug.Log($"[BossLoot] Item {equip.Name} tersimpan di Stash Sanctuary!");
            gameObject.SetActive(false);
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Buka Tab Inventaris di Sanctuary.
2. Pasang item dari Stash ke 3 Slot Wearable: stat bonus dan kartu bawaan otomatis terhubung ke loadout ekspedisi.
