# 📖 Manual Guide: TICKET-11B — Sistem Checkpoint Ekspedisi & Pengorbanan Skill

> **Referensi Tiket:** [TICKET-11B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-11B.md)  
> **Domain:** `[🗺️ MAP / MACRO LOOP]`  
> **Fase:** 2 (Peta Eksplorasi & Wave Drafting)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan mekanisme khusus **Sistem Checkpoint Roguelike** sesuai spesifikasi GDD §4.4:
- Pemain dibekali maksimal **3 slot checkpoint** yang dapat dipasang di sembarang node peta selama satu ekspedisi.
- Penempatan checkpoint membutuhkan harga (*cost*): pemain harus **mengorbankan (*sacrifice*) 1 kartu/skill** dari deck aktifnya.
- Jika pemain mati dalam pertarungan setelah checkpoint dipasang, pemain dapat bangkit di checkpoint terakhir tanpa harus mengulang run dari lantai 1.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── Map/
│   │   ├── CheckpointData.cs
│   │   └── CheckpointManager.cs
│   └── UI/
│       └── CheckpointModalController.cs
└── Tests/
    └── EditMode/
        └── CheckpointLogicTests.cs
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Map/CheckpointData.cs` & `CheckpointManager.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Events;

namespace PilotGame.Map
{
    [Serializable]
    public class CheckpointData
    {
        public string NodeId;
        public int FloorNumber;
        public string SacrificedCardId;
        public int SavedPlayerHP;

        public CheckpointData(string nodeId, int floor, string cardId, int hp)
        {
            NodeId = nodeId;
            FloorNumber = floor;
            SacrificedCardId = cardId;
            SavedPlayerHP = hp;
        }
    }

    /// <summary>
    /// Mengelola penempatan Checkpoint di peta ekspedisi (Maksimal 3 slot per run - GDD §4.4).
    /// Membutuhkan pengorbanan (sacrifice) 1 kartu aktif setiap penempatan.
    /// </summary>
    public class CheckpointManager : MonoBehaviour
    {
        public const int MaxCheckpoints = 3;
        public int AvailableCheckpoints { get; private set; } = MaxCheckpoints;

        private readonly List<CheckpointData> _activeCheckpoints = new();

        /// <summary>
        /// Validasi apakah pemain memenuhi syarat menaruh checkpoint:
        /// 1. Masih memiliki sisa kuota checkpoint (> 0).
        /// 2. Memiliki minimal 2 kartu di deck (agar pemain tidak kehabisan kartu sama sekali).
        /// </summary>
        public bool CanPlaceCheckpoint(List<CardData> currentDeck)
        {
            return AvailableCheckpoints > 0 && currentDeck != null && currentDeck.Count > 1;
        }

        /// <summary>
        /// Menempatkan checkpoint baru di node peta yang ditentukan:
        /// - Mengurangi kuota slot checkpoint.
        /// - Menghapus kartu yang dikorbankan secara permanen dari deck run saat ini.
        /// - Menyimpan snapshot HP pemain dan koordinat node.
        /// </summary>
        public bool PlaceCheckpoint(string nodeId, int floor, CardData sacrificedCard, int currentHP, List<CardData> deck)
        {
            if (!CanPlaceCheckpoint(deck) || sacrificedCard == null) return false;

            // 1. Kurangi kuota checkpoint yang tersedia
            AvailableCheckpoints--;

            // 2. Hapus kartu yang dikorbankan dari deck aktif pemain
            deck.Remove(sacrificedCard);

            // 3. Simpan data checkpoint
            var cp = new CheckpointData(nodeId, floor, sacrificedCard.Id, currentHP);
            _activeCheckpoints.Add(cp);

            // 4. Siarkan event notifikasi
            CombatEvents.OnCheckpointPlaced?.Invoke(nodeId);
            Debug.Log($"[Checkpoint] Checkpoint aktif di Node {nodeId}. Mengorbankan kartu: {sacrificedCard.Name}. Sisa slot: {AvailableCheckpoints}");

            return true;
        }

        /// <summary>
        /// Mengembalikan data checkpoint paling terakhir (LIFO - Last In First Out) menggunakan operator [^1].
        /// </summary>
        public CheckpointData GetLatestCheckpoint()
        {
            return _activeCheckpoints.Count > 0 ? _activeCheckpoints[^1] : null;
        }

        /// <summary>
        /// Mereset status checkpoint saat memulai ekspedisi baru.
        /// </summary>
        public void ResetForNewRun()
        {
            AvailableCheckpoints = MaxCheckpoints;
            _activeCheckpoints.Clear();
        }
    }
}
```

---

### B. `Assets/Tests/EditMode/CheckpointLogicTests.cs`
```csharp
using System.Collections.Generic;
using NUnit.Framework;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Map;

namespace PilotGame.Tests.EditMode
{
    [TestFixture]
    public class CheckpointLogicTests
    {
        private CheckpointManager _manager;
        private List<CardData> _sampleDeck;

        [SetUp]
        public void Setup()
        {
            var go = new GameObject();
            _manager = go.AddComponent<CheckpointManager>();

            _sampleDeck = new List<CardData>
            {
                ScriptableObject.CreateInstance<CardData>(),
                ScriptableObject.CreateInstance<CardData>()
            };
            _sampleDeck[0].Id = "card_1";
            _sampleDeck[1].Id = "card_2";
        }

        [Test]
        public void PlacingCheckpoint_DecrementsSlotAndRemovesSacrificedCard()
        {
            CardData victim = _sampleDeck[0];
            bool success = _manager.PlaceCheckpoint("node_5", 5, victim, 20, _sampleDeck);

            Assert.IsTrue(success);
            Assert.AreEqual(2, _manager.AvailableCheckpoints);
            Assert.IsFalse(_sampleDeck.Contains(victim));
            Assert.AreEqual(1, _sampleDeck.Count);
        }

        [Test]
        public void MaxThreeCheckpoints_RejectsFourthPlacement()
        {
            for (int i = 0; i < 3; i++)
            {
                _sampleDeck.Add(ScriptableObject.CreateInstance<CardData>());
                _manager.PlaceCheckpoint($"node_{i}", i, _sampleDeck[0], 20, _sampleDeck);
            }

            Assert.AreEqual(0, _manager.AvailableCheckpoints);

            _sampleDeck.Add(ScriptableObject.CreateInstance<CardData>());
            bool fourthAttempt = _manager.PlaceCheckpoint("node_4", 4, _sampleDeck[0], 20, _sampleDeck);

            Assert.IsFalse(fourthAttempt);
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Jalankan `CheckpointLogicTests` di Unity Test Runner (EditMode).
2. Pastikan logika pengurangan slot 3 -> 2 -> 1 -> 0 dan penghapusan kartu kurban berjalan 100% lulus (Hijau) ✅.
