# 📖 Manual Guide: TICKET-02C — 9 Roster Musuh SO Lengkap & 5 Item Consumable SO

> **Referensi Tiket:** [TICKET-02C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02C.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan katalog data lengkap untuk **9 Musuh Terpilih (*Selected Enemies Roster*)** yang mencakup 3 Archetype (*Melee, Ranged, Support*) dan 3 Hierarki (*Minion, Regular, Elite*), serta **5 Item Consumable (*Free Action*)** yang dapat digunakan oleh pemain selama pertempuran.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
└── ScriptableObjects/
    ├── Enemies/
    │   ├── Enemy_TatteredConscript.asset     # Melee Minion
    │   ├── Enemy_CuneiformSlicer.asset       # Melee Regular
    │   ├── Enemy_ExecutionerArchive.asset    # Melee Elite
    │   ├── Enemy_ArchiveSlingBoy.asset       # Ranged Minion
    │   ├── Enemy_ScrollPyromancer.asset      # Ranged Regular
    │   ├── Enemy_GrandMarksmanAshur.asset    # Ranged Elite
    │   ├── Enemy_TombBellRinger.asset        # Support Minion
    │   ├── Enemy_BabelWardTemplar.asset      # Support Regular
    │   └── Enemy_HighOracleEnki.asset        # Support Elite
    └── Consumables/
        ├── Item_ChainedHarpoon.asset         # Pull Target 2 Tiles
        ├── Item_EuphratesSmokeGrenade.asset  # Fog Screen
        ├── Item_ScrollInstantBlink.asset     # Blink 3 Tiles
        ├── Item_BronzeCaltrops.asset         # Caltrop Trap
        └── Item_ElixirOfLife.asset           # Heal 10 HP + 3 Shield
```

---

## 📊 3. Spesifikasi Roster 9 Musuh Terpilih

| Archetype | Minion (Kroco) | Regular (Menengah) | Elite (Komandan / Bos Mini) |
| :--- | :--- | :--- | :--- |
| **Melee** | **Tattered Conscript**<br>• HP: 18, Move: 2, Range: 1<br>• Kartu: *Spear Thrust (5 Dmg)*, *Brace Step (3 Shd)* | **Cuneiform Slicer**<br>• HP: 32, Move: 2, Range: 1<br>• Kartu: *Twin Cleave (8 Dmg)*, *Flanking Dash* | **Executioner of the Archive**<br>• HP: 85, Move: 2, Range: 1-2<br>• Kartu: *Guillotine Swing (15 Dmg)*, *Intimidating Roar* |
| **Ranged** | **Archive Sling-Boy**<br>• HP: 14, Move: 3, Range: 3<br>• Kartu: *Pebble Fling (4 Dmg)*, *Fleeing Step* | **Scroll-Pyromancer**<br>• HP: 28, Move: 2, Range: 4<br>• Kartu: *Cuneiform Fireball (7 AoE Dmg)*, *Ignite Bush* | **Grand Marksman of Ashur**<br>• HP: 70, Move: 2, Range: 5<br>• Kartu: *Sniper Volley (12 Dmg)*, *Eagle Eye Focus* |
| **Support** | **Tomb Bell-Ringer**<br>• HP: 16, Move: 2, Range: 2<br>• Kartu: *Dissonant Toll (2 Dmg)*, *Minor Mend (5 Heal)* | **Babel Ward-Templar**<br>• HP: 45, Move: 2, Range: 2<br>• Kartu: *Aegis Aura (+8 Ally Shield)*, *Shield Bash* | **High Oracle of Enki**<br>• HP: 75, Move: 2, Range: 4<br>• Kartu: *Prophecy of Doom (Curse)*, *Divine Sanctuary* |

---

## 🍵 4. Spesifikasi 5 Item Consumable

1. **Chained Harpoon Hook:** Jangkauan 4 petak garis lurus. Menarik (*Pull*) musuh 2 petak mendekat ke pemain.
2. **Euphrates Smoke Grenade:** Menciptakan tabir kabut asap 3×3 selama 2 ronde (50% evasion chance).
3. **Scroll of Instant Blink:** Teleportasi instan ke ubin kosong dalam radius 3 petak tanpa memicu tabrakan (*No Collision*).
4. **Bronze Caltrops:** Menebar ranjau duri di 1-3 petak sekitar: Memberikan 5 Damage + status `Immobilize` saat diinjak.
5. **Elixir of Life:** Memulihkan 10 HP pemain seketika + 3 Shield instan.

---

## 🛠️ 5. Script Generator Editor untuk Roster & Item

### `Assets/Scripts/Editor/RosterGeneratorEditor.cs`
```csharp
#if UNITY_EDITOR
using UnityEditor;
using UnityEngine;
using PilotGame.Cards;

namespace PilotGame.EditorTools
{
    public static class RosterGeneratorEditor
    {
        [MenuItem("PilotGame/Generate/9 Enemies and 5 Consumables")]
        public static void GenerateAllRosterAndItems()
        {
            EnsureFolders();
            GenerateEnemies();
            GenerateConsumables();
            AssetDatabase.SaveAssets();
            AssetDatabase.Refresh();
            Debug.Log("[RosterGeneratorEditor] Sukses membuat 9 Enemy SO & 5 Consumable SO!");
        }

        private static void EnsureFolders()
        {
            if (!AssetDatabase.IsValidFolder("Assets/ScriptableObjects/Enemies"))
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Enemies");
            if (!AssetDatabase.IsValidFolder("Assets/ScriptableObjects/Consumables"))
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Consumables");
        }

        private static void GenerateEnemies()
        {
            CreateEnemy("Enemy_TatteredConscript", "Tattered Conscript", "Vanguard soldier of the Tower of Babel armed with a worn spear and tattered armor.", EnemyArchetype.Melee, EnemyHierarchy.Minion, 18, 0, 5, 1, 2);
            CreateEnemy("Enemy_CuneiformSlicer", "Cuneiform Slicer", "Swift melee blade-dancer wielding twin sharpened cuneiform daggers.", EnemyArchetype.Melee, EnemyHierarchy.Regular, 32, 0, 8, 1, 2);
            CreateEnemy("Enemy_ExecutionerArchive", "Executioner of the Archive", "Towering archive executioner carrying a heavy executioner cleaver.", EnemyArchetype.Melee, EnemyHierarchy.Elite, 85, 5, 15, 2, 2);

            CreateEnemy("Enemy_ArchiveSlingBoy", "Archive Sling-Boy", "Agile ranged youth harassing intruders with high-velocity clay sling stones.", EnemyArchetype.Ranged, EnemyHierarchy.Minion, 14, 0, 4, 3, 3);
            CreateEnemy("Enemy_ScrollPyromancer", "Scroll-Pyromancer", "Pyromancer scholar who ignites ancient parchment scrolls into scorching fireballs.", EnemyArchetype.Ranged, EnemyHierarchy.Regular, 28, 0, 7, 4, 2);
            CreateEnemy("Enemy_GrandMarksmanAshur", "Grand Marksman of Ashur", "Veteran elite sniper with piercing sight across extreme distances.", EnemyArchetype.Ranged, EnemyHierarchy.Elite, 70, 0, 12, 5, 2);

            CreateEnemy("Enemy_TombBellRinger", "Tomb Bell-Ringer", "Crypt guardian who resonates dissonant bells to disrupt enemies and mend allies.", EnemyArchetype.Support, EnemyHierarchy.Minion, 16, 0, 2, 2, 2);
            CreateEnemy("Enemy_BabelWardTemplar", "Babel Ward-Templar", "Stalwart temple guardian generating protective radiant aegis barriers for allies.", EnemyArchetype.Support, EnemyHierarchy.Regular, 45, 8, 4, 2, 2);
            CreateEnemy("Enemy_HighOracleEnki", "High Oracle of Enki", "High priest channeling esoteric prophecies of ruin and divine sanctuaries.", EnemyArchetype.Support, EnemyHierarchy.Elite, 75, 5, 6, 4, 2);
        }

        private static void CreateEnemy(string fileName, string name, string desc, EnemyArchetype arch, EnemyHierarchy hier, int hp, int shield, int atk, int range, int move)
        {
            string path = $"Assets/ScriptableObjects/Enemies/{fileName}.asset";
            var enemy = AssetDatabase.LoadAssetAtPath<EnemyData>(path);
            if (enemy == null)
            {
                enemy = ScriptableObject.CreateInstance<EnemyData>();
                AssetDatabase.CreateAsset(enemy, path);
            }
            enemy.Id = fileName.ToLower();
            enemy.Name = name;
            enemy.Description = desc;
            enemy.Archetype = arch;
            enemy.Hierarchy = hier;
            enemy.MaxHealth = hp;
            enemy.BaseShield = shield;
            enemy.AttackPower = atk;
            enemy.AttackRange = range;
            enemy.MoveSpeedTiles = move;
            EditorUtility.SetDirty(enemy);
        }

        private static void GenerateConsumables()
        {
            CreateItem("Item_ChainedHarpoon", "Chained Harpoon Hook", "Fires a barbed hook to pull an enemy 2 tiles closer.", ConsumableEffectType.TeleportSafe, 2);
            CreateItem("Item_EuphratesSmokeGrenade", "Euphrates Smoke Grenade", "Deploys a protective smoke screen concealing the immediate area.", ConsumableEffectType.InstantShield, 5);
            CreateItem("Item_ScrollInstantBlink", "Scroll of Instant Blink", "Instantly teleports the user to an empty tile within 3 tiles range.", ConsumableEffectType.TeleportSafe, 3);
            CreateItem("Item_BronzeCaltrops", "Bronze Caltrops", "Scatters sharp caltrops that inflict damage and hinder enemy movement.", ConsumableEffectType.CleanseDebuff, 5);
            CreateItem("Item_ElixirOfLife", "Elixir of Life", "A mystical golden elixir that restores 10 HP and grants 3 Shield.", ConsumableEffectType.InstantHeal, 10);
        }

        private static void CreateItem(string fileName, string name, string desc, ConsumableEffectType effect, int val)
        {
            string path = $"Assets/ScriptableObjects/Consumables/{fileName}.asset";
            var item = AssetDatabase.LoadAssetAtPath<ConsumableData>(path);
            if (item == null)
            {
                item = ScriptableObject.CreateInstance<ConsumableData>();
                AssetDatabase.CreateAsset(item, path);
            }
            item.Id = fileName.ToLower();
            item.Name = name;
            item.Description = desc;
            item.EffectType = effect;
            item.EffectValue = val;
            EditorUtility.SetDirty(item);
        }
    }
}
#endif
```

---

## 🧪 6. Langkah Verifikasi
1. Klik **PilotGame > Generate > 9 Enemies and 5 Consumables** di Unity Editor.
2. Pastikan file tersimpan rapi di folder masing-masing dan parameternya terisi penuh.
