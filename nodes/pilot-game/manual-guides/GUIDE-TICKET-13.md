# 📖 Manual Guide: TICKET-13 — Konversi Poin Ekspedisi & Penyimpanan Save Data Lokal

> **Referensi Tiket:** [TICKET-13.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-13.md)  
> **Domain:** `[👑 CORE / PERSISTENCE]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan persistensi roguelike lintas ekspedisi (**Meta-Progression Persistence** - GDD §4.4):
1. **`ExpeditionPointsManager.cs`**: Menghitung skor ekspedisi saat mati atau menang (berdasarkan jumlah musuh kalah, lantai tercapai, dan sisa gold) lalu mengonversikannya menjadi **Knowledge Shards (Poin Meta)**.
2. **`SaveDataManager.cs`**: Menyimpan dan memuat data progres permanen (Poin Meta, Talenta yang terbuka, riwayat run, Ascension tier tertinggi) ke file JSON lokal di `Application.persistentDataPath`.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Persistence/
│       ├── GameSaveData.cs
│       ├── SaveDataManager.cs
│       └── ExpeditionPointsManager.cs
└── Tests/
    └── EditMode/
        └── SaveDataTests.cs
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Persistence/GameSaveData.cs`
```csharp
using System;
using System.Collections.Generic;

namespace PilotGame.Persistence
{
    [Serializable]
    public class GameSaveData
    {
        public int TotalKnowledgeShards = 0;
        public int HighestFloorReached = 0;
        public int UnlockedAscensionTier = 0;
        public List<string> UnlockedTalentNodeIds = new();
        public List<string> StashItemIds = new();
        public int TotalRunsCompleted = 0;
    }
}
```

---

### B. `Assets/Scripts/Persistence/SaveDataManager.cs`
```csharp
using System.IO;
using UnityEngine;

namespace PilotGame.Persistence
{
    /// <summary>
    /// Mengelola penyimpanan dan pemuatan berkas progres permainan lokal (JSON Serialization).
    /// Menggunakan Application.persistentDataPath untuk kompatibilitas lintas platform (PC, Mac, Mobile).
    /// </summary>
    public class SaveDataManager : MonoBehaviour
    {
        public static SaveDataManager Instance { get; private set; }

        private string SaveFilePath => Path.Combine(Application.persistentDataPath, "pilot_game_save.json");
        public GameSaveData CurrentSave { get; private set; } = new();

        private void Awake()
        {
            if (Instance != null && Instance != this)
            {
                Destroy(gameObject);
                return;
            }
            Instance = this;
            DontDestroyOnLoad(gameObject);
            LoadGame();
        }

        /// <summary>
        /// Menyimpan data progres aktif ke file JSON lokal secara terformat (pretty-print).
        /// </summary>
        public void SaveGame()
        {
            string json = JsonUtility.ToJson(CurrentSave, true);
            File.WriteAllText(SaveFilePath, json);
            Debug.Log($"[SaveDataManager] Progres tersimpan ke: {SaveFilePath}");
        }

        /// <summary>
        /// Memuat data simpanan dari file JSON. Jika file belum ada (pengguna baru),
        /// buat instance simpanan default baru dan simpan ke disk.
        /// </summary>
        public void LoadGame()
        {
            if (File.Exists(SaveFilePath))
            {
                string json = File.ReadAllText(SaveFilePath);
                CurrentSave = JsonUtility.FromJson<GameSaveData>(json);
                Debug.Log("[SaveDataManager] Save data berhasil dimuat!");
            }
            else
            {
                // Inisialisasi save data kosong untuk run pertama kali
                CurrentSave = new GameSaveData();
                SaveGame();
            }
        }
    }
}
```

---

### C. `Assets/Scripts/Persistence/ExpeditionPointsManager.cs`
```csharp
using UnityEngine;

namespace PilotGame.Persistence
{
    /// <summary>
    /// Menghitung akumulasi skor ekspedisi saat pemain menang atau gugur,
    /// dan mengonversinya menjadi mata uang meta-progres permanen (Knowledge Shards).
    /// </summary>
    public class ExpeditionPointsManager : MonoBehaviour
    {
        /// <summary>
        /// Formula Konversi Skor Ekspedisi:
        /// - Poin Lantai: +10 Shards per lantai yang berhasil diselesaikan.
        /// - Poin Eliminasi: +5 Shards per musuh yang dikalahkan.
        /// - Poin Sisa Gold: +1 Shards per 10 sisa koin emas yang dibawa.
        /// </summary>
        public int CalculateShards(int floorReached, int enemiesKilled, int goldRemaining)
        {
            // 1. Hitung subtotal poin masing-masing komponen
            int floorPoints = floorReached * 10;
            int killPoints = enemiesKilled * 5;
            int goldPoints = goldRemaining / 10; // Pembagian integer: sisa di bawah 10 diabaikan

            // 2. Akumulasi total Shards
            int totalShards = floorPoints + killPoints + goldPoints;
            Debug.Log($"[Expedition] Hasil konversi: {floorReached} Lantai + {enemiesKilled} Kill + {goldRemaining} Gold = {totalShards} Shards");

            // 3. Simpan langsung ke Save Data persisten
            if (SaveDataManager.Instance != null)
            {
                SaveDataManager.Instance.CurrentSave.TotalKnowledgeShards += totalShards;
                SaveDataManager.Instance.SaveGame();
            }

            return totalShards;
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Jalankan unit test `SaveDataTests.cs` (EditMode).
2. Verifikasi bahwa file `pilot_game_save.json` terbuat dan dapat dimuat kembali tanpa data korup.
