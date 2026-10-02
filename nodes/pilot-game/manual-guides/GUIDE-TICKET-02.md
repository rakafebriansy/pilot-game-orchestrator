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

---

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Pembuatan Folder & Script di Project Window
1. Buka tab **Project**, masuk ke folder `Assets/Scripts/`.
2. Klik kanan > **Create > Folder**, beri nama `Cards`.
3. Di dalam `Assets/Scripts/Cards/`, buat 3 C# script via klik kanan > **Create > C# Script**:
   * `CardData`
   * `EnemyData`
   * `ConsumableData`
4. Buat juga folder untuk menyimpan aset data hasil instansiasi:
   * `Assets/ScriptableObjects/Cards/`
   * `Assets/ScriptableObjects/Enemies/`
   * `Assets/ScriptableObjects/Consumables/`

---

### Langkah 2.2: Pengaturan Impor Sprite Pixel Art di Inspector
Sebelum memasukkan gambar/ikon ke ScriptableObject, pastikan tekstur pixel art dikonfigurasi dengan benar:
1. Masukkan gambar sprite (.png) ke folder `Assets/Art/Sprites/`.
2. Klik file sprite tersebut di Project Window.
3. Di panel **Inspector**, ubah pengaturan berikut:
   * **Texture Type:** `Sprite (2D and UI)`
   * **Sprite Mode:** `Single`
   * **Pixels Per Unit (PPU):** `16` (atau disesuaikan dengan grid art)
   * **Filter Mode:** `Point (no filter)` *(PENTING: agar pixel art tajam dan tidak blur!)*
   * **Compression:** `None` (pada tab Default di bagian bawah)
4. Klik tombol **Apply** di kanan bawah Inspector.

---

### Langkah 2.3: Pembuatan File `.asset` ScriptableObject via Unity GUI
Setelah script di Bagian 3 diketik dan di-save:
1. Masuk ke folder `Assets/ScriptableObjects/Cards/`.
2. Klik kanan pada area kosong Project View > pilih **Create > PilotGame > Data > Card Data**.
3. Beri nama file: `Card_PageCutter.asset`.
4. Klik file `Card_PageCutter.asset` tersebut, lalu isi nilai pada panel **Inspector**:
   * `CardId`: `card_page_cutter`
   * `CardName`: `Page Cutter`
   * `Description`: `Tebasan lembaran kitab kuno yang memberikan 6 damage ke musuh di depannya.`
   * `ActionType`: `Attack`
   * `AreaType`: `SingleTarget`
   * `EnergyCost`: `1`
   * `BaseDamage`: `6`
   * `Range`: `1`
   * `CardArt`: Seret sprite ikon pedang/kitab ke slot ini.

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
        public int EnergyCost = 1;

        [Header("Parameter Tempur")]
        public int BaseDamage = 0;
        public int BaseShield = 0;
        public int Range = 1;
        public int AreaRadius = 0;

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

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Periksa folder `Assets/ScriptableObjects/Cards/` di Project View.
2. Klik file `Card_PageCutter.asset`.
3. Pastikan Inspector menampilkan data kartu dengan benar tanpa ada field yang kosong atau error serialization.
