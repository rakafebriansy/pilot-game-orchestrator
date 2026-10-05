# 📖 Manual Guide: TICKET-14 — UI Markas Sanctuary & Pohon Talenta Upgrade Permanen

> **Referensi Tiket:** [TICKET-14.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-14.md)  
> **Domain:** `[🏛️ META / SANCTUARY]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan **Markas Permanen (*Sanctuary / Camp*)** di luar *run* tempat pemain dapat:
1. Menikmati lingkungan aman sebelum memulai ekspedisi baru.
2. Membelanjakan *Knowledge Shards* yang terkumpul untuk membuka **Pohon Talenta Permanen (*Talent Tree*)** seperti peningkatan Base Max HP, Starter Shield, atau tambahan slot inventaris.
3. Pertumbuhan kekuatan dibatasi secara matematis agar karakter tidak *overpowered* di dalam run (GDD §4.4).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Sanctuary/
│       ├── TalentNodeData.cs
│       ├── TalentTreeManager.cs
│       └── SanctuaryScreenController.cs
└── UI/
    ├── UXML/
    │   └── SanctuaryUI.uxml
    └── USS/
        └── SanctuaryUI.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Sanctuary/TalentNodeData.cs`
```csharp
using System;
using UnityEngine;

namespace PilotGame.Sanctuary
{
    public enum TalentBonusType
    {
        IncreaseBaseHP,        // +5 Max HP permanen
        IncreaseStarterShield, // +2 Starter Shield setiap awal pertempuran
        ExtraStartingGold,     // +25 Starter Gold di run
        DiscountShopPrices     // -10% harga toko
    }

    [CreateAssetMenu(fileName = "NewTalentNode", menuName = "PilotGame/Data/Talent Node")]
    public class TalentNodeData : ScriptableObject
    {
        public string TalentId;
        public string TalentName;
        [TextArea(2, 3)]
        public string Description;
        public int ShardCost = 50;
        public TalentBonusType BonusType;
        public int BonusValue = 5;
        public TalentNodeData PrerequisiteTalent; // Talenta prasyarat
    }
}
```

---

### B. `Assets/Scripts/Sanctuary/TalentTreeManager.cs`
```csharp
using UnityEngine;
using PilotGame.Persistence;

namespace PilotGame.Sanctuary
{
    /// <summary>
    /// Mengelola pembukaan pohon talenta permanen (Talent Tree) di Markas Sanctuary (GDD §4.4).
    /// </summary>
    public class TalentTreeManager : MonoBehaviour
    {
        /// <summary>
        /// Mencoba membuka node talenta baru:
        /// 1. Validasi keberadaan data dan save manager.
        /// 2. Validasi status node (apakah sudah pernah dibuka sebelumnya).
        /// 3. Validasi graf dependensi prasyarat (apakah talenta pendahulu sudah dibuka).
        /// 4. Validasi saldo Knowledge Shards yang dimiliki.
        /// 5. Eksekusi transaksi, potong shard, catat status Unlocked, dan simpan permanen ke disk.
        /// </summary>
        public bool TryUnlockTalent(TalentNodeData talent)
        {
            if (talent == null || SaveDataManager.Instance == null) return false;

            var save = SaveDataManager.Instance.CurrentSave;

            // 1. Periksa apakah talenta ini sudah terbuka sebelumnya
            if (save.UnlockedTalentNodeIds.Contains(talent.TalentId)) return false;

            // 2. Periksa graf dependensi: apakah talenta prasyarat (parent) sudah terbuka?
            if (talent.PrerequisiteTalent != null && !save.UnlockedTalentNodeIds.Contains(talent.PrerequisiteTalent.TalentId))
            {
                Debug.LogWarning("[Talent] Prasyarat talenta belum terbuka!");
                return false;
            }

            // 3. Periksa kecukupan saldo mata uang meta (Knowledge Shards)
            if (save.TotalKnowledgeShards < talent.ShardCost)
            {
                Debug.LogWarning("[Talent] Knowledge Shard tidak mencukupi!");
                return false;
            }

            // 4. Eksekusi transaksi: kurangi shard dan masukkan ke daftar terbuka
            save.TotalKnowledgeShards -= talent.ShardCost;
            save.UnlockedTalentNodeIds.Add(talent.TalentId);
            SaveDataManager.Instance.SaveGame();

            Debug.Log($"[Talent] Berhasil membuka talenta: {talent.TalentName}!");
            return true;
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Buka `SanctuaryScene.unity`.
2. Klik salah satu node talenta di pohon keterampilan (*Talent Tree*).
3. Saldo Shard berkurang dan icon talenta menyala terang menandakan status *Unlocked*.
