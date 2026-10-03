# 📖 Manual Guide: TICKET-11 — Event Naratif Misteri '?' & Rest Campfire Recovery

> **Referensi Tiket:** [TICKET-11.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-11.md)  
> **Domain:** `[🗺️ MAP / MACRO LOOP]`  
> **Fase:** 2 (Peta Eksplorasi & Wave Drafting)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan dua jenis interaksi non-pertempuran di peta ekspedisi:
1. **Node Misteri '?' (`NarrativeEventManager.cs`)**: Menyajikan cuplikan cerita lingkungan (*environmental storytelling* - GDD §7) dengan pilihan bercabang yang memberikan konsekuensi (contoh: mendapatkan kartu terkutuk / *Blind Abilities* vs memulihkan HP).
2. **Node Api Unggun (`CampfireManager.cs`)**: Memberikan pilihan strategis antara memulihkan 30% MaxHP atau meningkatkan (*Upgrade*) 1 kartu di deck menjadi versi plus.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── Map/
│   │   ├── NarrativeEventData.cs
│   │   ├── NarrativeEventManager.cs
│   │   └── CampfireManager.cs
│   └── UI/
│       ├── EventScreenController.cs
│       └── CampfireScreenController.cs
└── ScriptableObjects/
    └── Events/
        ├── Event_AncientShrine.asset
        ├── Event_WhisperingScrolls.asset
        └── Event_LibraryRubble.asset
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Map/NarrativeEventData.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;

namespace PilotGame.Map
{
    public enum EventOutcomeType
    {
        HealHP,
        LoseHP,
        GainGold,
        LoseGold,
        AddCard,
        RemoveCard,
        GainCurseCard
    }

    [Serializable]
    public class EventChoice
    {
        public string ChoiceText;
        [TextArea(2, 3)]
        public string OutcomeDescription;
        public EventOutcomeType OutcomeType;
        public int OutcomeValue;
        public CardData TargetCard;
    }

    [CreateAssetMenu(fileName = "NewEvent", menuName = "PilotGame/Data/Narrative Event")]
    public class NarrativeEventData : ScriptableObject
    {
        public string EventId;
        public string EventTitle;
        [TextArea(4, 8)]
        public string StoryText;
        public Sprite EventIllustration;
        public List<EventChoice> Choices = new();
    }
}
```

---

### B. `Assets/Scripts/Map/CampfireManager.cs`
```csharp
using UnityEngine;
using PilotGame.Cards;

namespace PilotGame.Map
{
    public class CampfireManager : MonoBehaviour
    {
        [Header("Campfire Healing Stats")]
        [SerializeField] private float _healPercentage = 0.30f; // 30% MaxHP

        public int CalculateHealAmount(int maxHP)
        {
            return Mathf.RoundToInt(maxHP * _healPercentage);
        }

        public void ApplyHeal(ref int currentHP, int maxHP)
        {
            int heal = CalculateHealAmount(maxHP);
            currentHP = Mathf.Min(maxHP, currentHP + heal);
            Debug.Log($"[Campfire] Pemain pulih sebesar +{heal} HP (HP sekarang: {currentHP}/{maxHP})");
        }

        public void UpgradeCard(CardData baseCard, CardData upgradedCard)
        {
            Debug.Log($"[Campfire] Kartu {baseCard.Name} berhasil ditingkatkan menjadi {upgradedCard.Name}!");
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Buka `CampfireScene.unity`.
2. Klik tombol "Istirahat": HP bar bertambah 30%.
3. Klik tombol "Upgrade Kartu": pilih 1 kartu untuk digantikan dengan versi yang memiliki statistik damage lebih tinggi.
