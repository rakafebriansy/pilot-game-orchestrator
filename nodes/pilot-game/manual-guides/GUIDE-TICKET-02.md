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
   * **Pixels Per Unit (PPU):** `64` *(Standar Resmi Proyek: 1 ubin = 64x64 px)*
   * **Filter Mode:** `Point (no filter)` *(PENTING: agar pixel art tajam dan tidak blur!)*
   * **Compression:** `None` (pada tab Default di bagian bawah)
4. Klik tombol **Apply** di kanan bawah Inspector.

---

### Langkah 2.3: Pembuatan File `.asset` ScriptableObject via Unity GUI
Setelah script di Bagian 3 diketik dan di-save:

**A. Membuat Kartu (`Card_ThrowingBlade.asset`):**
1. Masuk ke folder `Assets/ScriptableObjects/Cards/`.
2. Klik kanan > **Create > PilotGame > Data > Card Data**, beri nama `Card_ThrowingBlade.asset`.
3. Di panel **Inspector**, isi:
   * `Id`: `card_throwing_blade`
   * `Name`: `Throwing Blade`
   * `Description`: `Lemparan belati tajam jarak menengah yang memberikan 5 damage dan memicu efek pendarahan (Bleed).`
   * `ActionType`: `Attack`
   * `TargetArea`: `SingleTarget`
   * `PhaseRestriction`: `PlayerPhase`
   * `energyCost`: `1`
   * `BaseDamage`: `5`
   * `BaseShield`: `0`
   * `Range`: `3`
   * `AreaRadius`: `0`
   * `InflictedStatus`: `Bleed`
   * `StatusDuration`: `2`
   * `Art`: Seret sprite `Card_ThrowingBlade_Art` ke slot ini.

**B. Membuat Musuh (`Enemy_TatteredConscript.asset`):**
1. Masuk ke folder `Assets/ScriptableObjects/Enemies/`.
2. Klik kanan > **Create > PilotGame > Data > Enemy Data**, beri nama `Enemy_TatteredConscript.asset`.
3. Di panel **Inspector**, isi:
   * `Id`: `enemy_tattered_conscript`
   * `Name`: `Tattered Conscript`
   * `Description`: `Prajurit garda depan Menara Babel bersenjatakan tombak usang dan pelindung koyak.`
   * `Archetype`: `Melee`
   * `Hierarchy`: `Minion`
   * `MaxHealth`: `18`
   * `AttackPower`: `5`
   * `AttackRange`: `1`
   * `MoveSpeedTiles`: `2`
   * `Sprite`: Seret sprite `Enemy_TatteredConscript_Sprite` ke slot ini.

**C. Membuat Consumable (`Item_ElixirOfLife.asset`):**
1. Masuk ke folder `Assets/ScriptableObjects/Consumables/`.
2. Klik kanan > **Create > PilotGame > Data > Consumable Data**, beri nama `Item_ElixirOfLife.asset`.
3. Di panel **Inspector**, isi:
   * `Id`: `item_elixir_of_life`
   * `Name`: `Elixir of Life`
   * `Description`: `Cairan emas mistis yang memulihkan 10 poin kesehatan secara instan.`
   * `EffectType`: `InstantHeal`
   * `EffectValue`: `10`
   * `Icon`: Seret sprite `Item_ElixirOfLife_Icon` ke slot ini.

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
        SingleTarget, // hanya satu petak target
        LinearLine, // garis lurus dari caster ke target
        RadiusArea, // area lingkaran dengan radius tertentu di sekitar target
        SelfOnly, // hanya diri sendiri
        GlobalAllEnemies // semua musuh di arena
    }

    [CreateAssetMenu(fileName = "NewCard", menuName = "PilotGame/Data/Card Data")]
    public class CardData : ScriptableObject
    {
        [Header("Card Identity")]
        public string Id;
        public string Name;
        [TextArea(2,4)]
        public string Description;
        public Sprite Art;

        [Header("Classification & Cost")]
        public CardActionType ActionType = CardActionType.Attack;
        public TargetAreaType TargetArea = TargetAreaType.SingleTarget;
        public CombatPhase PhaseRestriction = CombatPhase.PlayerPhase;
        public int energyCost = 1;

        [Header("Combat Parameters")]
        public int BaseDamage = 0;
        public int BaseShield = 0;
        public int Range = 1;
        public int AreaRadius = 0;

        [Header("Status Effect")]
        public StatusEffectType InflictedStatus = StatusEffectType.None;
        public int StatusDuration = 0;

        [Header("UX & VFX")]
        public GameObject VFXPrefab;
        public AudioClip SFX;
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
        Melee,
        Ranged,
        Support
    }

    public enum EnemyHierarchy
    {
        Minion,
        Regular,
        Elite,
        Boss
    }

    [CreateAssetMenu(fileName = "NewEnemy", menuName = "PilotGame/Data/Enemy Data")]
    public class EnemyData : ScriptableObject
    {
        [Header("Enemy Identity")]
        public string Id;
        public string Name;
        [TextArea(2,4)]
        public string Description;
        public Sprite Sprite;

        [Header("Classification")]
        public EnemyArchetype Archetype = EnemyArchetype.Melee;
        public EnemyHierarchy Hierarchy = EnemyHierarchy.Minion;

        [Header("Combat Stats")]
        public int MaxHealth = 20;
        public int AttackPower = 5;
        public int AttackRange = 1;
        public int BaseShield = 0;
        public int MoveSpeedTiles = 1;

        [Header("Status Effect")]
        public StatusEffectType InflictedStatus = StatusEffectType.None;
        public int StatusDuration = 0;

        [Header("UX & VFX")]
        public GameObject CharacterPrefab;
        public GameObject VFXPrefab;
        public AudioClip SFX;

        [Header("Classless Default Deck")]
        public List<CardData> CardDeck = new ();
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
        [Header("Item Identity")]
        public string Id;
        public string Name;
        [TextArea(2,3)]
        public string Description;
        public Sprite Icon;

        [Header("Consumable Effect (Free Action)")]
        public ConsumableEffectType EffectType = ConsumableEffectType.InstantHeal;
        public int EffectValue = 0;

        [Header("UX & VFX")]
        public GameObject VFXPrefab;
        public AudioClip SFX;
    }
}
```

---

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Periksa folder `Assets/ScriptableObjects/Cards/` di Project View.
2. Klik file `Card_ThrowingBlade.asset`.
3. Pastikan Inspector menampilkan data kartu dengan benar tanpa ada field yang kosong atau error serialization.
