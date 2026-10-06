# 📖 Manual Guide: TICKET-02C — Dataset SO MVP Terpilih (3 Kartu Sinergi 1, 1 Musuh, 1 Consumable)

> **Referensi Tiket:** [TICKET-02C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02C.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Panduan ini mengonfigurasi dan memvalidasi **Dataset ScriptableObjects MVP Terpilih** yang digunakan secara aktif pada proyek Unity (`Tubbies Pilot Game`) untuk pengujian siklus pertempuran taktis:
1. **3 Kartu Sinergi 1 (Bleed & Assassination):** `Throwing Blade`, `Shadow Step`, `Serrated Dagger`.
2. **1 Musuh Dasar (Melee Minion):** `Tattered Conscript` (HP 18, Shield 2, Atk 5, Move 2).
3. **1 Item Consumable (Instant Heal):** `Elixir of Life` (Heal 10 HP, *Free Action*).

---

## 📂 2. Struktur File & Lokasi Aset (Sesuai Unity Saat Ini)
```text
Assets/
├── Art/
│   └── Sprites/
│       ├── Card_ThrowingBlade_Art.jpg
│       ├── Card_ShadowStep_Art.jpg
│       ├── Card_SerratedDagger_Art.jpg
│       ├── Enemy_TatteredConscript_Sprite.jpg
│       └── Consumable_ElixirOfLife_Icon.jpg
├── ScriptableObjects/
│   ├── Cards/
│   │   ├── Card_ThrowingBlade.asset
│   │   ├── Card_ShadowStep.asset
│   │   └── Card_SerratedDagger.asset
│   ├── Enemies/
│   │   └── Enemy_TatteredConscript.asset
│   └── Consumables/
│       └── Consumable_ElixirOfLife.asset
└── Scripts/
    └── Cards/
        ├── CardData.cs
        ├── EnemyData.cs
        └── ConsumableData.cs
```

---

## 📊 3. Spesifikasi Rinci & Konfigurasi Inspector Unity

### 🗡️ A. 3 Kartu Tempur (Sinergi 1: Bleed & Assassination)

1. **`Card_ThrowingBlade.asset` (CARD-009):**
   * `Id`: `CARD-009`
   * `Name`: `Throwing Blade`
   * `Description`: `Hurl a concealed dagger at a target up to 3 tiles away. Deals 5 Damage and inflicts Bleed (2 Direct Dmg/turn for 3 turns).`
   * `ActionType`: `Attack` | `TargetArea`: `SingleTarget` | `PhaseRestriction`: `PlayerPhase`
   * `BaseDamage`: `5` | `BaseShield`: `0` | `Range`: `3` | `AreaRadius`: `0`
   * `InflictedStatus`: `Bleed` | `StatusDuration`: `3`
   * `Art`: `Card_ThrowingBlade_Art`

2. **`Card_ShadowStep.asset` (CARD-034):**
   * `Id`: `CARD-034`
   * `Name`: `Shadow Step`
   * `Description`: `Teleport directly behind a target enemy within 4 tiles. Next attack from behind inflicts Bleed (2 Direct Dmg/turn for 2 turns).`
   * `ActionType`: `Movement` | `TargetArea`: `SingleTarget` | `PhaseRestriction`: `PlayerPhase`
   * `BaseDamage`: `0` | `BaseShield`: `0` | `Range`: `4` | `AreaRadius`: `0`
   * `InflictedStatus`: `None` | `StatusDuration`: `0`
   * `Art`: `Card_ShadowStep_Art`

3. **`Card_SerratedDagger.asset` (CARD-031):**
   * `Id`: `CARD-031`
   * `Name`: `Serrated Dagger`
   * `Description`: `Stab an adjacent enemy for 6 Damage. Deals 2x Damage (12 Damage) and refreshes Bleed if target is Bleeding.`
   * `ActionType`: `Attack` | `TargetArea`: `SingleTarget` | `PhaseRestriction`: `PlayerPhase`
   * `BaseDamage`: `6` | `BaseShield`: `0` | `Range`: `1` | `AreaRadius`: `0`
   * `InflictedStatus`: `None` | `StatusDuration`: `0`
   * `Art`: `Card_SerratedDagger_Art`

---

### 👾 B. 1 Musuh Dasar Terpilih (Melee Minion)

* **File Asset:** `Assets/ScriptableObjects/Enemies/Enemy_TatteredConscript.asset`
* **Pengaturan Inspector:**
  * `Id`: `enemy_tattered_conscript`
  * `Name`: `Tattered Conscript`
  * `Description`: `Vanguard soldier of the Tower of Babel armed with a worn spear and tattered armor.`
  * `Archetype`: `Melee`
  * `Hierarchy`: `Minion`
  * `MaxHealth`: `18`
  * `BaseShield`: `2`
  * `AttackPower`: `5`
  * `AttackRange`: `1`
  * `MoveSpeedTiles`: `2`
  * `InflictedStatus`: `None`
  * `StatusDuration`: `0`
  * `Sprite`: Seret sprite `Enemy_TatteredConscript_Sprite` ke slot ini.

---

### 🍵 C. 1 Item Consumable Terpilih (Instant Heal)

* **File Asset:** `Assets/ScriptableObjects/Consumables/Consumable_ElixirOfLife.asset`
* **Pengaturan Inspector:**
  * `Id`: `consumable_elixir_of_life`
  * `Name`: `Elixir of Life`
  * `Description`: `A mystical golden elixir that instantly restores 10 health points.`
  * `EffectType`: `InstantHeal`
  * `EffectValue`: `10`
  * `Icon`: Seret sprite `Consumable_ElixirOfLife_Icon` ke slot ini.

---

## 📊 4. Matriks Ringkasan Parameter Dataset MVP

| Tipe Entitas | File Asset | ID | Nama | Tipe / Klasifikasi | Stat Utama | Nilai Efek / Sinergi | Aset Visual |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Card** | `Card_ThrowingBlade.asset` | `CARD-009` | Throwing Blade | `Attack` (PlayerPhase) | Range 3 | 5 Dmg + Bleed 3 turns | `Card_ThrowingBlade_Art.jpg` |
| **Card** | `Card_ShadowStep.asset` | `CARD-034` | Shadow Step | `Movement` (PlayerPhase) | Range 4 | Teleport behind + Bleed prep | `Card_ShadowStep_Art.jpg` |
| **Card** | `Card_SerratedDagger.asset` | `CARD-031` | Serrated Dagger | `Attack` (PlayerPhase) | Range 1 (Melee) | 6 Dmg ($2\times=12$ if Bleed) | `Card_SerratedDagger_Art.jpg` |
| **Enemy** | `Enemy_TatteredConscript.asset` | `enemy_tattered_conscript` | Tattered Conscript | `Melee Minion` | HP: 18, Move: 2 | Atk: 5, Shield: 2 | `Enemy_TatteredConscript_Sprite.jpg` |
| **Consumable** | `Consumable_ElixirOfLife.asset` | `consumable_elixir_of_life` | Elixir of Life | `InstantHeal` (Free Action) | Single Use | Restore 10 HP | `Consumable_ElixirOfLife_Icon.jpg` |

---

## 🛠️ 5. Script Generator Editor untuk Dataset MVP

### `Assets/Scripts/Editor/MVPDatasetGeneratorEditor.cs`
```csharp
#if UNITY_EDITOR
using UnityEditor;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Data;

namespace PilotGame.EditorTools
{
    public static class MVPDatasetGeneratorEditor
    {
        [MenuItem("PilotGame/Generate/MVP Selected Dataset (3 Cards, 1 Enemy, 1 Consumable)")]
        public static void GenerateMVPDataset()
        {
            EnsureFolders();
            GenerateCards();
            GenerateEnemy();
            GenerateConsumable();

            AssetDatabase.SaveAssets();
            AssetDatabase.Refresh();
            Debug.Log("[MVPDatasetGeneratorEditor] Successfully created & updated 3 MVP Cards, 1 Enemy, and 1 Consumable!");
        }

        private static void EnsureFolders()
        {
            if (!AssetDatabase.IsValidFolder("Assets/ScriptableObjects/Cards"))
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Cards");
            if (!AssetDatabase.IsValidFolder("Assets/ScriptableObjects/Enemies"))
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Enemies");
            if (!AssetDatabase.IsValidFolder("Assets/ScriptableObjects/Consumables"))
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Consumables");
        }

        private static void GenerateCards()
        {
            CreateCard("Card_ThrowingBlade", "CARD-009", "Throwing Blade",
                "Hurl a concealed dagger at a target up to 3 tiles away. Deals 5 Damage and inflicts Bleed (2 Direct Dmg/turn for 3 turns).",
                CardActionType.Attack, TargetAreaType.SingleTarget, CombatPhase.PlayerPhase, 5, 0, 3, 0, StatusEffectType.Bleed, 3,
                "Assets/Art/Sprites/Card_ThrowingBlade_Art.jpg");

            CreateCard("Card_ShadowStep", "CARD-034", "Shadow Step",
                "Teleport directly behind a target enemy within 4 tiles. Next attack from behind inflicts Bleed (2 Direct Dmg/turn for 2 turns).",
                CardActionType.Movement, TargetAreaType.SingleTarget, CombatPhase.PlayerPhase, 0, 0, 4, 0, StatusEffectType.None, 0,
                "Assets/Art/Sprites/Card_ShadowStep_Art.jpg");

            CreateCard("Card_SerratedDagger", "CARD-031", "Serrated Dagger",
                "Stab an adjacent enemy for 6 Damage. Deals 2x Damage (12 Damage) and refreshes Bleed if target is Bleeding.",
                CardActionType.Attack, TargetAreaType.SingleTarget, CombatPhase.PlayerPhase, 6, 0, 1, 0, StatusEffectType.None, 0,
                "Assets/Art/Sprites/Card_SerratedDagger_Art.jpg");
        }

        private static void CreateCard(string fileName, string cardId, string cardName, string desc,
            CardActionType actionType, TargetAreaType targetArea, CombatPhase phase,
            int baseDamage, int baseShield, int castRange, int aoeRadius, StatusEffectType status, int duration, string artPath)
        {
            string path = $"Assets/ScriptableObjects/Cards/{fileName}.asset";
            var card = AssetDatabase.LoadAssetAtPath<CardData>(path);
            if (card == null)
            {
                card = ScriptableObject.CreateInstance<CardData>();
                AssetDatabase.CreateAsset(card, path);
            }
            card.Id = cardId;
            card.Name = cardName;
            card.Description = desc;
            card.ActionType = actionType;
            card.TargetArea = targetArea;
            card.PhaseRestriction = phase;
            card.BaseDamage = baseDamage;
            card.BaseShield = baseShield;
            card.Range = castRange;
            card.AreaRadius = aoeRadius;
            card.InflictedStatus = status;
            card.StatusDuration = duration;

            if (!string.IsNullOrEmpty(artPath))
            {
                card.Art = AssetDatabase.LoadAssetAtPath<Sprite>(artPath);
            }
            EditorUtility.SetDirty(card);
        }

        private static void GenerateEnemy()
        {
            string path = "Assets/ScriptableObjects/Enemies/Enemy_TatteredConscript.asset";
            var enemy = AssetDatabase.LoadAssetAtPath<EnemyData>(path);
            if (enemy == null)
            {
                enemy = ScriptableObject.CreateInstance<EnemyData>();
                AssetDatabase.CreateAsset(enemy, path);
            }
            enemy.Id = "enemy_tattered_conscript";
            enemy.Name = "Tattered Conscript";
            enemy.Description = "Vanguard soldier of the Tower of Babel armed with a worn spear and tattered armor.";
            enemy.Archetype = EnemyArchetype.Melee;
            enemy.Hierarchy = EnemyHierarchy.Minion;
            enemy.MaxHealth = 18;
            enemy.BaseShield = 2;
            enemy.AttackPower = 5;
            enemy.AttackRange = 1;
            enemy.MoveSpeedTiles = 2;
            enemy.InflictedStatus = StatusEffectType.None;
            enemy.StatusDuration = 0;
            enemy.Sprite = AssetDatabase.LoadAssetAtPath<Sprite>("Assets/Art/Sprites/Enemy_TatteredConscript_Sprite.jpg");
            EditorUtility.SetDirty(enemy);
        }

        private static void GenerateConsumable()
        {
            string path = "Assets/ScriptableObjects/Consumables/Consumable_ElixirOfLife.asset";
            var item = AssetDatabase.LoadAssetAtPath<ConsumableData>(path);
            if (item == null)
            {
                item = ScriptableObject.CreateInstance<ConsumableData>();
                AssetDatabase.CreateAsset(item, path);
            }
            item.Id = "consumable_elixir_of_life";
            item.Name = "Elixir of Life";
            item.Description = "A mystical golden elixir that instantly restores 10 health points.";
            item.EffectType = ConsumableEffectType.InstantHeal;
            item.EffectValue = 10;
            item.Icon = AssetDatabase.LoadAssetAtPath<Sprite>("Assets/Art/Sprites/Consumable_ElixirOfLife_Icon.jpg");
            EditorUtility.SetDirty(item);
        }
    }
}
#endif
```

---

## 🧪 6. Langkah Verifikasi
1. Di Unity Editor menu bar, klik **PilotGame > Generate > MVP Selected Dataset (3 Cards, 1 Enemy, 1 Consumable)**.
2. Buka folder `Assets/ScriptableObjects/`:
   * Di `Cards/`: Cek `Card_ThrowingBlade.asset`, `Card_ShadowStep.asset`, `Card_SerratedDagger.asset`.
   * Di `Enemies/`: Cek `Enemy_TatteredConscript.asset`.
   * Di `Consumables/`: Cek `Consumable_ElixirOfLife.asset`.
3. Pastikan seluruh file `.asset` memiliki ID, Nama, Deskripsi (English), parameter numerik, serta Sprite Art terhubung secara valid di Inspector.
