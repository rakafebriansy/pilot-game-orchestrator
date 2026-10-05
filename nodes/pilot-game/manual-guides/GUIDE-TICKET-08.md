# 📖 Manual Guide: TICKET-08 — Model Data Peta Rute Bercabang Menara Babel

> **Referensi Tiket:** [TICKET-08.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-08.md)  
> **Domain:** `[🗺️ MAP / MACRO LOOP]`  
> **Fase:** 2 (Peta Eksplorasi & Wave Drafting)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan lapisan data untuk **Peta Eksplorasi Menara Babel Bercabang (*Directed Acyclic Graph - DAG*)** ala *Slay the Spire*:
1. **`MapNodeData.cs`**: Representasi tipe node (Battle, Elite, Mystery, Shop, Campfire, Boss).
2. **`MapGenerator.cs`**: Algoritma pembuatan jalur bercabang otomatis dari Lantai 1 s/d Lantai 15 (Boss Floor) dengan jaminan tidak ada jalur buntu (*Dead End*).
3. **`MapLayout.cs`**: Struktur data penyimpanan peta aktif untuk disimpan ke save data.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Map/
│       ├── MapNodeType.cs
│       ├── MapNodeData.cs
│       ├── MapLayout.cs
│       └── MapGenerator.cs
└── Tests/
    └── EditMode/
        └── MapGenerationTests.cs
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Map/MapNodeType.cs` & `MapNodeData.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;

namespace PilotGame.Map
{
    public enum MapNodeType
    {
        BattleNormal,   // Pertempuran biasa
        BattleElite,    // Pertempuran musuh elit
        MysteryEvent,   // Event naratif misteri '?'
        MerchantShop,   // Toko pedagang
        CampfireRest,   // Tempat istirahat / upgrade kartu
        BossFloor       // Pertempuran puncak Bos Lantai
    }

    [Serializable]
    public class MapNodeData
    {
        public string NodeId;
        public int FloorIndex;       // Tingkat lantai (0 = Ground, 14 = Puncak)
        public int ColumnIndex;      // Posisi horizontal kolom (0..4)
        public MapNodeType NodeType;
        public List<string> OutgoingNodeIds = new(); // Jalur menuju lantai berikutnya
        public bool IsVisited = false;
        public bool IsAvailable = false;

        public MapNodeData(string id, int floor, int col, MapNodeType type)
        {
            NodeId = id;
            FloorIndex = floor;
            ColumnIndex = col;
            NodeType = type;
        }
    }
}
```

---

### B. `Assets/Scripts/Map/MapGenerator.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;

namespace PilotGame.Map
{
    /// <summary>
    /// Generator prosedural graf peta rute bercabang (Directed Acyclic Graph / DAG - Pure C#).
    /// Mengelola struktur lantai, titik istirahat garansi, koneksi cabang, dan probabilitas tipe node.
    /// </summary>
    public class MapGenerator
    {
        public const int TotalFloors = 15;
        public const int Columns = 4;

        /// <summary>
        /// Menghasilkan tata letak peta lengkap berbasis Seed deterministik.
        /// </summary>
        public MapLayout GenerateMap(int seed)
        {
            Random.InitState(seed);
            MapLayout layout = new MapLayout();

            // 1. Tahap Pembentukan Node per Lantai (0 s/d 14)
            for (int floor = 0; floor < TotalFloors; floor++)
            {
                if (floor == TotalFloors - 1)
                {
                    // Lantai Terakhir (Lantai 14): Selalu berupa 1 Boss Node di tengah kolom
                    var bossNode = new MapNodeData($"node_{floor}_boss", floor, Columns / 2, MapNodeType.BossFloor);
                    layout.AddNode(bossNode);
                    continue;
                }

                for (int col = 0; col < Columns; col++)
                {
                    // 75% probabilitas node aktif di kolom untuk menghasilkan variasi celah rute
                    if (Random.value < 0.75f || col == 0)
                    {
                        MapNodeType type = DetermineNodeType(floor);
                        var node = new MapNodeData($"node_{floor}_{col}", floor, col, type);
                        layout.AddNode(node);
                    }
                }
            }

            // 2. Tahap Penghubungan Jalur (Outgoing Edges) antar lantai yang berurutan
            for (int floor = 0; floor < TotalFloors - 1; floor++)
            {
                var currentFloorNodes = layout.GetNodesAtFloor(floor);
                var nextFloorNodes = layout.GetNodesAtFloor(floor + 1);

                foreach (var currentNode in currentFloorNodes)
                {
                    foreach (var nextNode in nextFloorNodes)
                    {
                        // Hanya sambungkan jika kolom berada tepat di atas atau diagonal 1 langkah (|colA - colB| <= 1)
                        if (Mathf.Abs(currentNode.ColumnIndex - nextNode.ColumnIndex) <= 1)
                        {
                            currentNode.OutgoingNodeIds.Add(nextNode.NodeId);
                        }
                    }

                    // Garansi Anti Jalur Buntu (Dead-End Fallback):
                    // Jika node tidak memiliki koneksi keluar, paksa sambungkan ke node terdekat di lantai atasnya
                    if (currentNode.OutgoingNodeIds.Count == 0 && nextFloorNodes.Count > 0)
                    {
                        currentNode.OutgoingNodeIds.Add(nextFloorNodes[0].NodeId);
                    }
                }
            }

            // 3. Inisialisasi Ketersediaan: Aktifkan seluruh node di lantai 0 sebagai opsi rute awal
            foreach (var startNode in layout.GetNodesAtFloor(0))
            {
                startNode.IsAvailable = true;
            }

            return layout;
        }

        /// <summary>
        /// Menentukan tipe konten node berdasarkan lantai dan kurva distribusi probabilitas.
        /// </summary>
        private MapNodeType DetermineNodeType(int floor)
        {
            if (floor == 0) return MapNodeType.BattleNormal; // Lantai 0 selalu pertarungan normal
            if (floor == 7) return MapNodeType.CampfireRest; // Lantai 7 selalu titik istirahat (Midpoint Rest)

            float roll = Random.value;
            if (roll < 0.50f) return MapNodeType.BattleNormal; // 50% Pertarungan Biasa
            if (roll < 0.70f) return MapNodeType.MysteryEvent; // 20% Event Misteri
            if (roll < 0.85f) return MapNodeType.BattleElite;   // 15% Pertarungan Musuh Elite
            if (roll < 0.95f) return MapNodeType.MerchantShop;  // 10% Toko Pedagang
            return MapNodeType.CampfireRest;                   // 5% Api Unggun
        }
    }

    [System.Serializable]
    public class MapLayout
    {
        public List<MapNodeData> AllNodes = new();

        public void AddNode(MapNodeData node) => AllNodes.Add(node);

        public List<MapNodeData> GetNodesAtFloor(int floor)
        {
            return AllNodes.FindAll(n => n.FloorIndex == floor);
        }

        public MapNodeData GetNodeById(string id)
        {
            return AllNodes.Find(n => n.NodeId == id);
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Buat unit test di `Assets/Tests/EditMode/MapGenerationTests.cs`.
2. Uji bahwa map layout lantai 0 s/d 14 terhubung secara kontinu dari bawah ke bos atas tanpa ada node yang terisolasi.
