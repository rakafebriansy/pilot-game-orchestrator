# 📖 Manual Guide: TICKET-02B — Kartu Sinergi 1: Bleed & Assassination Archetype

> **Referensi Tiket:** [TICKET-02B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-02B.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan **Set Kartu Sinergi 1: Bleed & Assassination Archetype** mengacu pada `cards.md` (§11) sebagai aset `CardData.asset` di Unity. 

Sinergi ini merupakan siklus kombo pembunuh (*Assassination Burst Loop*):
1. **Throwing Blade (`CARD-009`):** Pembuka kombo jarak jauh (Range 3) yang memberikan 5 Damage dan menginfeksi target dengan status **`Bleed`** (2 Direct Damage per ronde selama 3 ronde, menembus shield).
2. **Shadow Step (`CARD-034`):** Manuver reposisi instan (*Teleport*) ke petak tepat di belakang musuh sasaran (Range 4), dan memberikan bonus status `Bleed` pada serangan penyerangan berikutnya.
3. **Serrated Dagger (`CARD-031`):** Eksekutor melee (*Bleed Finisher*) berjarak 1 petak yang memberikan 6 Physical Damage. Jika musuh **sudah memiliki status `Bleed`**, serangannya otomatis berlipat ganda menjadi **$2\times\text{ Damage}$ (12 Damage)** dan me-refresh durasi `Bleed` target kembali ke durasi penuh (3 ronde).

---

## 📂 2. Struktur File & Lokasi Aset
```text
Assets/
├── Art/
│   └── Sprites/
│       ├── Card_ThrowingBlade_Art.jpg
│       ├── Card_ShadowStep_Art.jpg
│       └── Card_SerratedDagger_Art.jpg
└── ScriptableObjects/
    └── Cards/
        ├── Card_ThrowingBlade.asset
        ├── Card_ShadowStep.asset
        └── Card_SerratedDagger.asset
```

---

## 📊 3. Spesifikasi Rinci & Konfigurasi Kartu (Inspector Setup)

### 🗡️ Kartu 1: Throwing Blade (`CARD-009`)
* **ID:** `CARD-009` (atau `card_throwing_blade`)
* **Nama:** `Throwing Blade`
* **Deskripsi (English):** `Hurl a concealed dagger at a single target up to 3 tiles away. Deals 5 Physical Damage and inflicts Bleed (2 Direct Damage per turn for 3 turns, bypassing Shield).`
* **Pengaturan Inspector Unity (`Card_ThrowingBlade.asset`):**
  * `Id`: `CARD-009`
  * `Name`: `Throwing Blade`
  * `Description`: `Hurl a concealed dagger at a target up to 3 tiles away. Deals 5 Damage and inflicts Bleed (2 Direct Dmg/turn for 3 turns).`
  * `ActionType`: `Attack`
  * `TargetArea`: `SingleTarget`
  * `PhaseRestriction`: `PlayerPhase`
  * `BaseDamage`: `5`
  * `BaseShield`: `0`
  * `Range` (*Cast Range*): `3`
  * `AreaRadius` (*AoE Radius*): `0`
  * `InflictedStatus`: `Bleed`
  * `StatusDuration`: `3`
  * `Art`: Seret sprite `Card_ThrowingBlade_Art` ke slot ini.

---

### 🌑 Kartu 2: Shadow Step (`CARD-034`)
* **ID:** `CARD-034` (atau `card_shadow_step`)
* **Nama:** `Shadow Step`
* **Deskripsi (English):** `Instantly teleport to the tile directly behind a target enemy within 4 tiles. The next attack from behind inflicts Bleed (2 Direct Damage per turn for 2 turns).`
* **Pengaturan Inspector Unity (`Card_ShadowStep.asset`):**
  * `Id`: `CARD-034`
  * `Name`: `Shadow Step`
  * `Description`: `Teleport directly behind a target enemy within 4 tiles. Next attack from behind inflicts Bleed (2 Direct Dmg/turn for 2 turns).`
  * `ActionType`: `Movement`
  * `TargetArea`: `SingleTarget`
  * `PhaseRestriction`: `PlayerPhase`
  * `BaseDamage`: `0`
  * `BaseShield`: `0`
  * `Range` (*Cast Range*): `4`
  * `AreaRadius` (*AoE Radius*): `0`
  * `InflictedStatus`: `None`
  * `StatusDuration`: `0`
  * `Art`: Seret sprite `Card_ShadowStep_Art` ke slot ini.

---

### 🩸 Kartu 3: Serrated Dagger (`CARD-031`)
* **ID:** `CARD-031` (atau `card_serrated_dagger`)
* **Nama:** `Serrated Dagger`
* **Deskripsi (English):** `Stab an adjacent enemy dealing 6 Physical Damage. If the target is already Bleeding, deals double damage (12 Damage) and refreshes the target's Bleed duration to full (3 turns).`
* **Pengaturan Inspector Unity (`Card_SerratedDagger.asset`):**
  * `Id`: `CARD-031`
  * `Name`: `Serrated Dagger`
  * `Description`: `Stab an adjacent enemy for 6 Damage. Deals 2x Damage (12 Damage) and refreshes Bleed if target is Bleeding.`
  * `ActionType`: `Attack`
  * `TargetArea`: `SingleTarget`
  * `PhaseRestriction`: `PlayerPhase`
  * `BaseDamage`: `6`
  * `BaseShield`: `0`
  * `Range` (*Cast Range*): `1`
  * `AreaRadius` (*AoE Radius*): `0`
  * `InflictedStatus`: `None`
  * `StatusDuration`: `0`
  * `Art`: Seret sprite `Card_SerratedDagger_Art` ke slot ini.

---

## 📊 Matriks Ringkasan Parameter Sinergi 1

| ID | Nama Kartu | Action Type | Phase | Cast Range | AoE Radius | Base Dmg | Base Shld | Inflicted Status | Durasi | Aset Sprite | Ringkasan Mekanik & Sinergi (English) |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- | :--- |
| `CARD-009` | **Throwing Blade** | `Attack` | `PlayerPhase` | 3 Tiles | 0 (Single) | 5 | 0 | `Bleed` | 3 | `Card_ThrowingBlade_Art` | Ranged Bleed Opener: 5 Dmg + Bleed (2 Direct Dmg/turn, direct to HP). |
| `CARD-034` | **Shadow Step** | `Movement` | `PlayerPhase` | 4 Tiles | 0 (Single) | 0 | 0 | `None` | 0 | `Card_ShadowStep_Art` | Flanker Teleport: Teleport behind target (Range 4) + next hit applies Bleed. |
| `CARD-031` | **Serrated Dagger** | `Attack` | `PlayerPhase` | 1 Tile (Melee) | 0 (Single) | 6 | 0 | `None` | 0 | `Card_SerratedDagger_Art` | Bleed Finisher: Melee 6 Dmg $\rightarrow$ **$2\times$ (12 Dmg)** if target Bleeding + refresh DoT. |

---

## 🛠️ 4. Script Batch Pembuatan ScriptableObjects Otomatis

Script editor di `Assets/Scripts/Editor/CardGeneratorEditor.cs` menghubungkan otomatis ID, Nama, Deskripsi (English), dan Sprite Art ke setiap file `.asset`:

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
        [MenuItem("PilotGame/Generate/Bleed Synergy Cards (Sinergi 1)")]
        public static void GenerateBleedSynergyCards()
        {
            string folderPath = "Assets/ScriptableObjects/Cards";
            if (!AssetDatabase.IsValidFolder(folderPath))
            {
                AssetDatabase.CreateFolder("Assets/ScriptableObjects", "Cards");
            }

            // 1. Throwing Blade (CARD-009)
            CreateCard(
                fileName: "Card_ThrowingBlade",
                cardId: "CARD-009",
                cardName: "Throwing Blade",
                desc: "Hurl a concealed dagger at a target up to 3 tiles away. Deals 5 Damage and inflicts Bleed (2 Direct Dmg/turn for 3 turns).",
                actionType: CardActionType.Attack,
                targetArea: TargetAreaType.SingleTarget,
                phase: CombatPhase.PlayerPhase,
                baseDamage: 5,
                baseShield: 0,
                castRange: 3,
                aoeRadius: 0,
                status: StatusEffectType.Bleed,
                duration: 3,
                artSpritePath: "Assets/Art/Sprites/Card_ThrowingBlade_Art.jpg"
            );

            // 2. Shadow Step (CARD-034)
            CreateCard(
                fileName: "Card_ShadowStep",
                cardId: "CARD-034",
                cardName: "Shadow Step",
                desc: "Teleport directly behind a target enemy within 4 tiles. Next attack from behind inflicts Bleed (2 Direct Dmg/turn for 2 turns).",
                actionType: CardActionType.Movement,
                targetArea: TargetAreaType.SingleTarget,
                phase: CombatPhase.PlayerPhase,
                baseDamage: 0,
                baseShield: 0,
                castRange: 4,
                aoeRadius: 0,
                status: StatusEffectType.None,
                duration: 0,
                artSpritePath: "Assets/Art/Sprites/Card_ShadowStep_Art.jpg"
            );

            // 3. Serrated Dagger (CARD-031)
            CreateCard(
                fileName: "Card_SerratedDagger",
                cardId: "CARD-031",
                cardName: "Serrated Dagger",
                desc: "Stab an adjacent enemy for 6 Damage. Deals 2x Damage (12 Damage) and refreshes Bleed if target is Bleeding.",
                actionType: CardActionType.Attack,
                targetArea: TargetAreaType.SingleTarget,
                phase: CombatPhase.PlayerPhase,
                baseDamage: 6,
                baseShield: 0,
                castRange: 1,
                aoeRadius: 0,
                status: StatusEffectType.None,
                duration: 0,
                artSpritePath: "Assets/Art/Sprites/Card_SerratedDagger_Art.jpg"
            );

            AssetDatabase.SaveAssets();
            AssetDatabase.Refresh();
            Debug.Log("[CardGeneratorEditor] Sukses membuat Kartu Sinergi 1 (Bleed & Assassination) dengan data & sprite art lengkap!");
        }

        private static void CreateCard(
            string fileName,
            string cardId,
            string cardName,
            string desc,
            CardActionType actionType,
            TargetAreaType targetArea,
            CombatPhase phase,
            int baseDamage,
            int baseShield,
            int castRange,
            int aoeRadius,
            StatusEffectType status,
            int duration,
            string artSpritePath)
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

            if (!string.IsNullOrEmpty(artSpritePath))
            {
                card.Art = AssetDatabase.LoadAssetAtPath<Sprite>(artSpritePath);
            }

            EditorUtility.SetDirty(card);
        }
    }
}
#endif
```

---

## 🧪 5. Langkah Verifikasi
1. Di Unity Editor menu bar, klik **PilotGame > Generate > Bleed Synergy Cards (Sinergi 1)**.
2. Buka folder `Assets/ScriptableObjects/Cards/` di Project View.
3. Klik masing-masing file `.asset` (`Card_ThrowingBlade.asset`, `Card_ShadowStep.asset`, `Card_SerratedDagger.asset`) dan periksa di Inspector:
   * **Id, Name, dan Description (English)** terisi lengkap sesuai spesifikasi di atas.
   * **Slot Art** terhubung dengan file Sprite masing-masing di `Assets/Art/Sprites/`.
4. Pasang ke-3 kartu ini ke dalam list starter deck `DeckManager.cs` (masing-masing 5 lembar kartu untuk membentuk deck 15 kartu) untuk menguji kombo *Bleed & Assassination* di scene pertempuran.
