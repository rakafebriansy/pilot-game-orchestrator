# 📖 Manual Guide: TICKET-02B — 14 Kartu Tempur Nabu Lengkap & Metadata Parameter

> **Referensi Tiket:** [TICKET-02B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02B.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mewujudkan **14 Kartu Tempur Nabu** (protagonis bersenjatakan Kitab Kuno / *Grimoire*) sebagai aset `CardData.asset` di Unity. Setiap kartu memiliki karakteristik tempur unik, pembatasan fase (*PhaseRestriction*), area jangkauan (*AreaType*), efek abnormal (*StatusEffect*), dan parameter recoil/knockback.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
└── ScriptableObjects/
    └── Cards/
        ├── Card_Teleport.asset
        ├── Card_Decoy.asset
        ├── Card_Frost.asset
        ├── Card_HeavyRain.asset
        ├── Card_Fog.asset
        ├── Card_Storm.asset
        ├── Card_ClearWeather.asset
        ├── Card_SkeletonArmy.asset
        ├── Card_ThrowingBlade.asset
        ├── Card_SandBurial.asset
        ├── Card_Clone.asset
        ├── Card_Dash.asset
        ├── Card_SuperPunch.asset
        └── Card_GravityLift.asset
```

---

## 📊 3. Spesifikasi Rinci 14 Kartu Tempur Nabu

| No | Nama Kartu | Action Type | Phase Restriction | Area Type | Range / Radius | Damage / Shield | Efek Spesial / Status |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | **Teleport** | `Movement` | `IntentPhase` | `SingleTarget` | Global (15) | 0 / 0 | Berpindah ke petak kosong manapun di arena sebelum fase utama. |
| 2 | **Decoy** | `Utility` | `RoundResetPhase` | `SelfOnly` | 0 | 0 / 5 | Meninggalkan ilusi tiruan di posisi lama, lalu teleport instan. |
| 3 | **Frost** | `Attack` | `PlayerPhase` | `RadiusArea` | Range 3, Radius 1 | 4 / 0 | Memberikan status `Freeze` (1 turn) + ubin licin pada ronde berikutnya. |
| 4 | **Heavy Rain** | `StatusModifier` | `PlayerPhase` | `GlobalAllEnemies` | Global | 0 / 0 | Mengurangi mobilitas seluruh musuh (-1 Movement) selama 3 ronde. |
| 5 | **Fog** | `Utility` | `PlayerPhase` | `RadiusArea` | Range 0, Radius 2 | 0 / 0 | Menyelimuti area sekitar dengan kabut: serangan yang masuk memiliki miss chance 50%. |
| 6 | **Storm** | `Attack` | `PlayerPhase` | `GlobalAllEnemies` | Global | 2 / 0 | Menciptakan badai petir: memberikan 2 damage rutin setiap ronde selama 3 ronde. |
| 7 | **Clear Weather** | `Utility` | `IntentPhase` | `GlobalAllEnemies` | Global | 0 / 0 | *Immediate Action*: Menghapus seluruh efek cuaca aktif secara instan. |
| 8 | **Skeleton Army** | `Attack` | `PlayerPhase` | `RadiusArea` | Range 1, Radius 1 | 8 / 0 | Memanggil lingkaran tulang di sekitar target untuk menyerang serentak. |
| 9 | **Throwing Blade** | `Attack` | `PlayerPhase` | `SingleTarget` | Range 3 | 5 / 0 | Melempar belati tajam: memberikan 5 damage + status `Bleed` (2 turn). |
| 10 | **Sand Burial** | `StatusModifier` | `IntentPhase` | `SingleTarget` | Range 3 | 0 / 0 | Mengurung musuh dengan pasir: status `Immobilize` (abaikan movement). |
| 11 | **Clone** | `Utility` | `RoundResetPhase` | `SelfOnly` | 0 | 0 / 3 | Menciptakan bayangan ganda pengalih perhatian musuh. |
| 12 | **Dash** | `Movement` | `PlayerPhase` | `LinearLine` | Range 3 | 3 / 0 | Melesat 3 petak ke depan; mendorong unit penghalang dan mengurangi 1 move. |
| 13 | **Super Punch** | `Attack` | `PlayerPhase` | `SingleTarget` | Range 1 | 12 / 0 | Serangan pukulan dahsyat (12 damage); menimbulkan recoil mundur 2 petak. |
| 14 | **Gravity Lift** | `Attack` | `IntentPhase` | `RadiusArea` | Range 3, Radius 1 | 4 / 0 | Mengangkat sekeliling ubin 3 petak ke udara; membatalkan aksi unit terangkat. |

---

## 🛠️ 4. Script Batch Pembuatan ScriptableObjects Otomatis
Untuk mempermudah pembuatan ke-14 file `.asset` sekaligus tanpa input manual satu per satu di Unity Editor, buat script editor berikut:

### `Assets/Scripts/Editor/CardGeneratorEditor.cs`
```csharp
#if UNITY_EDITOR
using UnityEditor;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Data;

namespace PilotGame.EditorTools
{
    public static class CardGeneratorEditor
    {
        [MenuItem("PilotGame/Generate/14 Starter Cards")]
        public static void GenerateAllStarterCards()
        {
            string folderPath = "Assets/ScriptableObjects/Cards";
            if (!AssetDatabase.IsValidFolder(folderPath))
            {
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Cards");
            }

            CreateCard("Card_Teleport", "Teleport", "Teleport to any unoccupied tile in the arena.", CardActionType.Movement, TargetAreaType.SingleTarget, CombatPhase.IntentPhase, 0, 0, 15, 0, StatusEffectType.None, 0);
            CreateCard("Card_Decoy", "Decoy", "Leave a decoy illusion behind and reposition.", CardActionType.Utility, TargetAreaType.SelfOnly, CombatPhase.RoundResetPhase, 0, 5, 0, 0, StatusEffectType.None, 0);
            CreateCard("Card_Frost", "Frost", "Freeze a 3x3 area, dealing 4 damage and making tiles slippery.", CardActionType.Attack, TargetAreaType.RadiusArea, CombatPhase.PlayerPhase, 4, 0, 3, 1, StatusEffectType.Freeze, 1);
            CreateCard("Card_HeavyRain", "Heavy Rain", "Reduce all enemy movement by 1 tile for 3 rounds.", CardActionType.StatusModifier, TargetAreaType.GlobalAllEnemies, CombatPhase.PlayerPhase, 0, 0, 15, 0, StatusEffectType.Immobilize, 3);
            CreateCard("Card_Fog", "Fog", "Dense 5x5 fog: enemy attacks inside have a 50% miss chance.", CardActionType.Utility, TargetAreaType.RadiusArea, CombatPhase.PlayerPhase, 0, 0, 0, 2, StatusEffectType.None, 0);
            CreateCard("Card_Storm", "Storm", "Arena storm: deals 2 damage each round to all enemies for 3 rounds.", CardActionType.Attack, TargetAreaType.GlobalAllEnemies, CombatPhase.PlayerPhase, 2, 0, 15, 0, StatusEffectType.Vulnerable, 3);
            CreateCard("Card_ClearWeather", "Clear Weather", "Instantly remove all active weather effects in the arena.", CardActionType.Utility, TargetAreaType.GlobalAllEnemies, CombatPhase.IntentPhase, 0, 0, 15, 0, StatusEffectType.None, 0);
            CreateCard("Card_SkeletonArmy", "Skeleton Army", "Summon a surrounding ring of skeletal warriors to strike for 8 damage.", CardActionType.Attack, TargetAreaType.RadiusArea, CombatPhase.PlayerPhase, 8, 0, 1, 1, StatusEffectType.None, 0);
            CreateCard("Card_ThrowingBlade", "Throwing Blade", "A medium-range sharp dagger throw dealing 5 damage and inflicting Bleed.", CardActionType.Attack, TargetAreaType.SingleTarget, CombatPhase.PlayerPhase, 5, 0, 3, 0, StatusEffectType.Bleed, 2);
            CreateCard("Card_SandBurial", "Sand Burial", "Trap the target in a swirl of heavy sand, applying Immobilize.", CardActionType.StatusModifier, TargetAreaType.SingleTarget, CombatPhase.IntentPhase, 0, 0, 3, 0, StatusEffectType.Immobilize, 1);
            CreateCard("Card_Clone", "Clone", "Create a mirror clone to confuse and distract enemy targeting.", CardActionType.Utility, TargetAreaType.SelfOnly, CombatPhase.RoundResetPhase, 0, 3, 0, 0, StatusEffectType.None, 0);
            CreateCard("Card_Dash", "Dash", "Dash forward 3 tiles, shoving obstacles and dealing 3 damage.", CardActionType.Movement, TargetAreaType.LinearLine, CombatPhase.PlayerPhase, 3, 0, 3, 0, StatusEffectType.None, 0);
            CreateCard("Card_SuperPunch", "Super Punch", "Deliver a heavy punch for 12 damage with a 2-tile recoil knockback.", CardActionType.Attack, TargetAreaType.SingleTarget, CombatPhase.PlayerPhase, 12, 0, 1, 0, StatusEffectType.None, 0);
            CreateCard("Card_GravityLift", "Gravity Lift", "Nullify gravity in a 3x3 area, dealing 4 damage and stunning targets.", CardActionType.Attack, TargetAreaType.RadiusArea, CombatPhase.IntentPhase, 4, 0, 3, 1, StatusEffectType.Stun, 1);

            AssetDatabase.SaveAssets();
            AssetDatabase.Refresh();
            Debug.Log("[CardGeneratorEditor] Sukses membuat 14 Starter Card SO!");
        }

        private static void CreateCard(string fileName, string cardName, string desc, CardActionType actionType, TargetAreaType areaType, CombatPhase phase, int dmg, int shield, int range, int radius, StatusEffectType status, int duration)
        {
            string path = $"Assets/ScriptableObjects/Cards/{fileName}.asset";
            var card = AssetDatabase.LoadAssetAtPath<CardData>(path);
            if (card == null)
            {
                card = ScriptableObject.CreateInstance<CardData>();
                AssetDatabase.CreateAsset(card, path);
            }

            card.Id = fileName.ToLower();
            card.Name = cardName;
            card.Description = desc;
            card.ActionType = actionType;
            card.TargetArea = areaType;
            card.PhaseRestriction = phase;
            card.BaseDamage = dmg;
            card.BaseShield = shield;
            card.Range = range;
            card.AreaRadius = radius;
            card.InflictedStatus = status;
            card.StatusDuration = duration;
            EditorUtility.SetDirty(card);
        }
    }
}
#endif
```

---

## 🧪 5. Langkah Verifikasi
1. Di Unity Editor menu bar, klik **PilotGame > Generate > 14 Starter Cards**.
2. Buka folder `Assets/ScriptableObjects/Cards/` di Project View.
3. Periksa bahwa ke-14 aset `.asset` telah dibuat dengan parameter yang valid.
