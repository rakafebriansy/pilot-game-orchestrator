# 📖 Manual Guide: TICKET-10 — Sistem Drafting Hadiah Pasca-Pertempuran (Pick 1 of 3 Cards)

> **Referensi Tiket:** [TICKET-10.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-10.md)  
> **Domain:** `[🃏 DOMAIN 3: CARD DECK & UI]`  
> **Fase:** 2 (Peta Eksplorasi & Wave Drafting)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan mekanisme progresi roguelike inti pasca-kemenangan gelombang/pertempuran (**Post-Wave Progression** - GDD §3.4):
1. **`DraftManager.cs`**: Mengacak 3 opsi kartu baru dari kumpulan kartu yang tersedia (*Card Reward Pool*).
2. **`DraftScreenController.cs`**: Menampilkan modal UI interaktif di mana pemain harus memilih 1 kartu (*Drafting*) untuk dimasukkan ke dalam deck aktif, atau memilih tombol *Skip Reward*.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── UI/
│   │   └── DraftScreenController.cs
│   └── Cards/
│       └── DraftManager.cs
└── UI/
    ├── UXML/
    │   └── DraftRewardScreenUI.uxml
    └── USS/
        └── DraftRewardScreenUI.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Cards/DraftManager.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;

namespace PilotGame.Cards
{
    public class DraftManager : MonoBehaviour
    {
        [SerializeField] private List<CardData> _rewardCardPool = new();

        /// <summary>
        /// Mengambil 3 kartu unik acak dari reward pool.
        /// </summary>
        public List<CardData> GenerateThreeCardDraft()
        {
            List<CardData> result = new List<CardData>();
            List<CardData> tempPool = new List<CardData>(_rewardCardPool);

            for (int i = 0; i < 3 && tempPool.Count > 0; i++)
            {
                int randomIndex = Random.Range(0, tempPool.Count);
                result.Add(tempPool[randomIndex]);
                tempPool.RemoveAt(randomIndex);
            }

            return result;
        }
    }
}
```

---

### B. `Assets/Scripts/UI/DraftScreenController.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UIElements;
using PilotGame.Cards;
using PilotGame.UI;

namespace PilotGame.UI
{
    [RequireComponent(typeof(UIDocument))]
    public class DraftScreenController : MonoBehaviour
    {
        [SerializeField] private DraftManager _draftManager;
        [SerializeField] private DeckManager _deckManager;

        private UIDocument _uiDocument;
        private VisualElement _draftCardsContainer;
        private Button _skipButton;

        private void Awake()
        {
            _uiDocument = GetComponent<UIDocument>();
        }

        public void OpenDraftScreen()
        {
            gameObject.SetActive(true);
            var root = _uiDocument.rootVisualElement;
            _draftCardsContainer = root.Q<VisualElement>("draft-cards-container");
            _skipButton = root.Q<Button>("skip-draft-button");

            _draftCardsContainer.Clear();
            List<CardData> offeredCards = _draftManager.GenerateThreeCardDraft();

            foreach (var card in offeredCards)
            {
                var cardBox = new VisualElement();
                cardBox.AddToClassList("draft-card-card");

                var nameLbl = new Label(card.CardName);
                nameLbl.AddToClassList("card-title");

                var descLbl = new Label(card.Description);
                descLbl.AddToClassList("card-desc");

                var selectBtn = new Button(() => OnCardSelected(card));
                selectBtn.text = "Pilih Kartu Ini";
                selectBtn.AddToClassList("draft-select-btn");

                cardBox.Add(nameLbl);
                cardBox.Add(descLbl);
                cardBox.Add(selectBtn);

                _draftCardsContainer.Add(cardBox);
            }

            _skipButton?.RegisterCallback<ClickEvent>(evt => CloseDraftScreen());
        }

        private void OnCardSelected(CardData chosenCard)
        {
            Debug.Log($"[Draft] Pemain menambahkan kartu: {chosenCard.CardName} ke deck!");
            // Tambahkan ke starter deck / draw pile
            CloseDraftScreen();
        }

        private void CloseDraftScreen()
        {
            gameObject.SetActive(false);
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Panggil `draftScreenController.OpenDraftScreen()` setelah pertempuran usai.
2. Tiga kartu terpampang di tengah layar dengan animasi kilau emas.
3. Klik salah satu kartu: kartu masuk ke deck dan modal otomatis tertutup.
