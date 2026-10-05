# 📖 Manual Guide: TICKET-15 — Sistem Tingkat Kesulitan Ascension Tiers

> **Referensi Tiket:** [TICKET-15.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-15.md)  
> **Domain:** `[🏛️ META / SANCTUARY]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan sistem kesulitan berjenjang (*Ascension Tiers*) ala *Slay the Spire* / *Hades Heat* (GDD §4.4 & §5.8):
- Setiap kali pemain berhasil menamatkan run (mengalahkan Boss Lantai 15), **Ascension Tier berikutnya terbuka** (Tier 1 s/d Tier 10).
- Setiap level Ascension menambahkan modifikator tantangan kumulatif:
  * *Ascension 1:* Musuh Elit memiliki +20% HP tambahan.
  * *Ascension 2:* Biaya toko pedagang naik 15%.
  * *Ascension 3:* Musuh memiliki +1 attack power dasar.
  * *Ascension 5:* Boss memasuki fase *Enrage* pada 60% HP (alih-alih 50%).
  * *Ascension 10:* Pemain memulai run dengan -5 Max HP.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Meta/
│       ├── AscensionModifierData.cs
│       └── AscensionManager.cs
└── UI/
    └── UXML/
        └── AscensionSelectorUI.uxml
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Meta/AscensionModifierData.cs`
```csharp
using System;
using UnityEngine;

namespace PilotGame.Meta
{
    [Serializable]
    public class AscensionTier
    {
        public int TierLevel;
        public string Title;
        [TextArea(2, 3)]
        public string ModifierDescription;

        public float EnemyHPMultiplier = 1.0f;
        public int EnemyAttackBonus = 0;
        public float ShopPriceMultiplier = 1.0f;
        public int PlayerMaxHPModifier = 0;
        public float BossEnrageThreshold = 0.50f;
    }

    [CreateAssetMenu(fileName = "AscensionCatalog", menuName = "PilotGame/Data/Ascension Catalog")]
    public class AscensionModifierData : ScriptableObject
    {
        public AscensionTier[] Tiers = new AscensionTier[10];
    }
}
```

---

### B. `Assets/Scripts/Meta/AscensionManager.cs`
```csharp
using UnityEngine;
using PilotGame.Persistence;

namespace PilotGame.Meta
{
    /// <summary>
    /// Mengelola tingkat kesulitan berjenjang (Ascension Tiers 1-10) ala Slay the Spire / Hades (GDD §5.8).
    /// </summary>
    public class AscensionManager : MonoBehaviour
    {
        public static AscensionManager Instance { get; private set; }

        [SerializeField] private AscensionModifierData _catalog;
        public int SelectedAscensionLevel { get; private set; } = 0; // 0 = Normal/Standard run

        private void Awake()
        {
            if (Instance != null && Instance != this)
            {
                Destroy(gameObject);
                return;
            }
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }

        /// <summary>
        /// Memilih tingkat kesulitan Ascension untuk ekspedisi berikutnya.
        /// Memastikan pemain hanya dapat memilih tier yang sudah terbuka di Save Data.
        /// </summary>
        public bool SetAscensionLevel(int level)
        {
            int maxUnlocked = SaveDataManager.Instance != null
                ? SaveDataManager.Instance.CurrentSave.UnlockedAscensionTier
                : 0;

            // Validasi: level harus berada di antara 0 s/d batas maksimum tier yang sudah terbuka
            if (level >= 0 && level <= maxUnlocked)
            {
                SelectedAscensionLevel = level;
                Debug.Log($"[Ascension] Tingkat kesulitan terpilih: Ascension {level}");
                return true;
            }
            return false;
        }

        /// <summary>
        /// Mengembalikan objek modifikator aktif untuk memengaruhi kalkulasi pertempuran,
        /// bonus attack musuh, harga toko pedagang, dan HP awal pemain.
        /// </summary>
        public AscensionTier GetCurrentModifiers()
        {
            if (_catalog == null || SelectedAscensionLevel == 0 || SelectedAscensionLevel > _catalog.Tiers.Length)
            {
                return new AscensionTier { TierLevel = 0, Title = "Normal" };
            }
            // Array 0-indexed: Ascension 1 berada di indeks 0
            return _catalog.Tiers[SelectedAscensionLevel - 1];
        }

        /// <summary>
        /// Dipanggil saat pemain berhasil mengalahkan Boss Lantai 15:
        /// Jika pemain menamatkan tier tertinggi yang dimilikinya saat ini, buka Ascension Tier berikutnya (hingga tier 10).
        /// </summary>
        public void OnRunWon()
        {
            if (SaveDataManager.Instance != null)
            {
                var save = SaveDataManager.Instance.CurrentSave;
                if (SelectedAscensionLevel == save.UnlockedAscensionTier && save.UnlockedAscensionTier < 10)
                {
                    save.UnlockedAscensionTier++;
                    SaveDataManager.Instance.SaveGame();
                    Debug.Log($"[Ascension] 🎉 Selamat! Ascension {save.UnlockedAscensionTier} Terbuka!");
                }
            }
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Pilih **Ascension 2** di layar Markas Sanctuary.
2. Masuk ke pertempuran: verifikasi bahwa pengali statistik musuh dan harga toko terkonfigurasi sesuai modifikator aktif.
3. Tamatkan run: Ascension 3 terbuka secara otomatis di Save Data.
