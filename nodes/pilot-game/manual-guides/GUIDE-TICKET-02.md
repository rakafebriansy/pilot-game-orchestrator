# 📖 Manual Guide: TICKET-02 — Katalog Data ScriptableObjects (Template Dasar)

> **Referensi Tiket:** [TICKET-02.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mendefinisikan template dasar **ScriptableObject (SO)** di Unity untuk tiga pilar entitas game:
1. **`CardData.cs`** — cetak biru kartu aksi pertempuran.
2. **`EnemyData.cs`** — konfigurasi statistik, archetype, dan hierarki musuh.
3. **`ConsumableData.cs`** — item konsumsi sekali pakai (*Free Action*).

Data-driven design menggunakan ScriptableObject memungkinkan Game Designer mengubah nilai statistik langsung dari Unity Inspector tanpa menyentuh baris kode logika.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Cards/
│       ├── CardData.cs
│       ├── EnemyData.cs
│       └── ConsumableData.cs
└── ScriptableObjects/
    ├── Cards/
    ├── Enemies/
    └── Consumables/
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Cards/CardData.cs`
```csharp
using UnityEngine;
using PilotGame.Core.Data;

namespace PilotGame.Cards
{
    public enum TargetAreaType
    {
        SingleTarget,      // Tepat pada 1 petak target
        LinearLine,        // Garis lurus ke depan
        RadiusArea,        // Lingkaran radius di sekitar target
        SelfOnly,          // Diri sendiri
        GlobalAllEnemies   // Seluruh musuh di arena
    }

    [CreateAssetMenu(fileName = "NewCard", menuName = "PilotGame/Data/Card Data")]
    public class CardData : ScriptableObject
    {
        [Header("Identitas Kartu")]
        public string CardId;
        public string CardName;
        [TextArea(2, 4)]
        public string Description;
        public Sprite CardArt;

        [Header("Klasifikasi & Biaya")]
        public CardActionType ActionType = CardActionType.Attack;
        public TargetAreaType AreaType = TargetAreaType.SingleTarget;
        public CombatPhase PhaseRestriction = CombatPhase.PlayerPhase;
        public int EnergyCost = 1; // Default 1 aksi per turn

        [Header("Parameter Tempur")]
        public int BaseDamage = 0;
        public int BaseShield = 0;
        public int Range = 1; // Jangkauan jarak Manhattan dari karakter
        public int AreaRadius = 0; // Digunakan jika AreaType == RadiusArea

        [Header("Status Effect")]
        public StatusEffectType InflictedStatus = StatusEffectType.Shielded;
        public int StatusDuration = 0;

        [Header("UX & Visual FX")]
        public GameObject CastVFXPrefab;
        public AudioClip CastSFX;
    }
}
```

---

### B. `Assets/Scripts/Cards/EnemyData.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Core.Data;

namespace PilotGame.Cards
{
    public enum EnemyArchetype
    {
        Melee,      // Petarung jarak dekat
        Ranged,     // Penyerang jarak jauh
        Support     // Pendukung / Buffer / Healer
    }

    public enum EnemyHierarchy
    {
        Minion,     // Kroco biasa, sinergi minimal
        Regular,    // Prajurit standar, beberapa sinergi
        Elite       // Pasukan elit, sinergi penuh & tanggap situasi
    }

    [CreateAssetMenu(fileName = "NewEnemy", menuName = "PilotGame/Data/Enemy Data")]
    public class EnemyData : ScriptableObject
    {
        [Header("Identitas")]
        public string EnemyId;
        public string EnemyName;
        public Sprite EnemySprite;

        [Header("Klasifikasi")]
        public EnemyArchetype Archetype = EnemyArchetype.Melee;
        public EnemyHierarchy Hierarchy = EnemyHierarchy.Minion;

        [Header("Statistik Tempur")]
        public int MaxHealth = 20;
        public int BaseShield = 0;
        public int AttackPower = 5;
        public int AttackRange = 1;
        public int MoveSpeedTiles = 1;

        [Header("Classless Deck Bawaan")]
        public List<CardData> EnemyDeck = new List<CardData>();

        [Header("Prefab Visual")]
        public GameObject CharacterPrefab;
    }
}
```

---

### C. `Assets/Scripts/Cards/ConsumableData.cs`
```csharp
using UnityEngine;
using PilotGame.Core.Data;

namespace PilotGame.Cards
{
    public enum ConsumableEffectType
    {
        InstantHeal,
        InstantShield,
        DrawExtraCard,
        CleanseDebuff,
        TeleportSafe
    }

    [CreateAssetMenu(fileName = "NewConsumable", menuName = "PilotGame/Data/Consumable Data")]
    public class ConsumableData : ScriptableObject
    {
        [Header("Identitas Item")]
        public string ItemId;
        public string ItemName;
        [TextArea(2, 3)]
        public string Description;
        public Sprite ItemIcon;

        [Header("Efek Konsumsi (Free Action)")]
        public ConsumableEffectType EffectType = ConsumableEffectType.InstantHeal;
        public int EffectValue = 10;
        public AudioClip UseSFX;
    }
}
```

---

## 🛠️ 4. Langkah Pembuatan Aset di Unity Editor
1. Klik kanan di folder `Assets/ScriptableObjects/Cards/` → pilih **Create > PilotGame > Data > Card Data**.
2. Beri nama file: `Card_PageCutter.asset`.
3. Isi Inspector dengan data:
   * `CardId`: `"card_page_cutter"`
   * `CardName`: `"Page Cutter"`
   * `ActionType`: `Attack`
   * `BaseDamage`: `6`
   * `Range`: `1`
4. Buat minimal 4 kartu sampel lainnya (`Card_TomeBash`, `Card_QuickStep`, `Card_BookmarkBarrier`, `Card_InkSplatter`).

---

## 🧪 5. Langkah Verifikasi
1. Pastikan tidak ada error kompilasi di Unity Editor.
2. Klik setiap file `.asset` yang dibuat dan pastikan semua field tersimpan secara presisi di Unity Inspector.
