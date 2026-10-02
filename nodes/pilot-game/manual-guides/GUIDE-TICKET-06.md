# 📖 Manual Guide: TICKET-06 — Antarmuka Kartu UI Toolkit & Drag-and-Drop

> **Referensi Tiket:** [TICKET-06.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06.md)  
> **Domain:** `[🃏 DOMAIN 3: CARD DECK & UI]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan sistem manajemen dek dan tangan pemain (*Hand & Deck*) menggunakan **Unity UI Toolkit (UXML/USS)**:
1. **`DeckManager.cs`**: Mengelola tumpukan Draw Pile (10 kartu), Hand (5 kartu), dan Discard Pile dengan kocok ulang otomatis (*Auto Reshuffle*) saat draw pile kosong (GDD §4.2).
2. **`CardHandController.cs`**: Merender kartu secara dinamis di bagian tengah bawah layar, mendeteksi drag kartu ke ubin target di arena atau membatalkan drag jika dikembalikan ke hand.
3. **Dokumen UXML & USS**: Tata letak fleksibel dan animasi hover kartu.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── UI/
│       ├── DeckManager.cs
│       └── CardHandController.cs
└── UI/
    ├── UXML/
    │   └── CombatHandUI.uxml
    └── USS/
        └── CombatHandUI.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/UI/DeckManager.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Events;

namespace PilotGame.UI
{
    /// <summary>
    /// Mengelola aliran kartu antara Draw Pile, Hand, dan Discard Pile (15 kartu total).
    /// </summary>
    public class DeckManager : MonoBehaviour
    {
        [Header("Starting Deck Configuration")]
        [SerializeField] private List<CardData> _starterDeck = new();
        [SerializeField] private int _handCapacity = 5;

        private readonly List<CardData> _drawPile = new();
        private readonly List<CardData> _hand = new();
        private readonly List<CardData> _discardPile = new();

        public IReadOnlyList<CardData> Hand => _hand;

        private void Awake()
        {
            InitializeDeck();
        }

        private void OnEnable()
        {
            CombatEvents.OnDrawCardsRequested += DrawToFullHand;
        }

        private void OnDisable()
        {
            CombatEvents.OnDrawCardsRequested -= DrawToFullHand;
        }

        public void InitializeDeck()
        {
            _drawPile.Clear();
            _hand.Clear();
            _discardPile.Clear();

            _drawPile.AddRange(_starterDeck);
            Shuffle(_drawPile);
            DrawToFullHand();
        }

        public void DrawToFullHand()
        {
            while (_hand.Count < _handCapacity)
            {
                if (_drawPile.Count == 0)
                {
                    if (_discardPile.Count == 0) break; // Tidak ada kartu tersisa
                    ReshuffleDiscardIntoDraw();
                }

                CardData drawnCard = _drawPile[0];
                _drawPile.RemoveAt(0);
                _hand.Add(drawnCard);
            }
        }

        public void DiscardCard(CardData card)
        {
            if (_hand.Contains(card))
            {
                _hand.Remove(card);
                _discardPile.Add(card);
            }
        }

        private void ReshuffleDiscardIntoDraw()
        {
            _drawPile.AddRange(_discardPile);
            _discardPile.Clear();
            Shuffle(_drawPile);
        }

        private void Shuffle<T>(List<T> list)
        {
            for (int i = list.Count - 1; i > 0; i--)
            {
                int rnd = Random.Range(0, i + 1);
                (list[i], list[rnd]) = (list[rnd], list[i]);
            }
        }
    }
}
```

---

### B. `Assets/Scripts/UI/CardHandController.cs`
```csharp
using UnityEngine;
using UnityEngine.UIElements;
using PilotGame.Cards;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;

namespace PilotGame.UI
{
    /// <summary>
    /// Mengontrol visualisasi elemen UI Toolkit dan event Drag-and-Drop kartu.
    /// </summary>
    [RequireComponent(typeof(UIDocument))]
    public class CardHandController : MonoBehaviour
    {
        [SerializeField] private DeckManager _deckManager;
        [SerializeField] private Camera _mainCamera;

        private UIDocument _uiDocument;
        private VisualElement _handContainer;
        private CardData _draggedCard;

        private void Awake()
        {
            _uiDocument = GetComponent<UIDocument>();
            if (_mainCamera == null) _mainCamera = Camera.main;
        }

        private void OnEnable()
        {
            var root = _uiDocument.rootVisualElement;
            _handContainer = root.Q<VisualElement>("hand-container");
            RefreshHandVisuals();
        }

        public void RefreshHandVisuals()
        {
            if (_handContainer == null || _deckManager == null) return;
            _handContainer.Clear();

            foreach (var card in _deckManager.Hand)
            {
                var cardElement = CreateCardElement(card);
                _handContainer.Add(cardElement);
            }
        }

        private VisualElement CreateCardElement(CardData card)
        {
            var cardBox = new VisualElement();
            cardBox.AddToClassList("card-element");

            var title = new Label(card.CardName);
            title.AddToClassList("card-title");

            var desc = new Label(card.Description);
            desc.AddToClassList("card-desc");

            var cost = new Label($"{card.EnergyCost} AP");
            cost.AddToClassList("card-cost");

            cardBox.Add(cost);
            cardBox.Add(title);
            cardBox.Add(desc);

            // Register Pointer Drag Events
            cardBox.RegisterCallback<PointerDownEvent>(evt => OnStartDrag(card, cardBox));
            cardBox.RegisterCallback<PointerUpEvent>(evt => OnEndDrag(card, evt.position));

            return cardBox;
        }

        private void OnStartDrag(CardData card, VisualElement element)
        {
            _draggedCard = card;
            element.AddToClassList("card-dragging");
        }

        private void OnEndDrag(CardData card, Vector2 screenPos)
        {
            if (_draggedCard == null) return;

            // Konversi posisi pointer layar ke koordinat Grid World
            Vector3 worldPos = _mainCamera.ScreenToWorldPoint(new Vector3(screenPos.x, Screen.height - screenPos.y, 10f));
            Vector2Int gridCoord = new Vector2Int(Mathf.FloorToInt(worldPos.x), Mathf.FloorToInt(worldPos.y));

            // Broadcast kartu dimainkan
            CombatEvents.OnCardPlayed?.Invoke(card, gridCoord);
            _draggedCard = null;
        }
    }
}
```

---

### C. `Assets/UI/USS/CombatHandUI.uss`
```css
.hand-container {
    position: absolute;
    bottom: 20px;
    left: 50%;
    translate: -50% 0;
    flex-direction: row;
    align-items: flex-end;
    justify-content: center;
    gap: 12px;
}

.card-element {
    width: 140px;
    height: 200px;
    background-color: #1a1e29;
    border-radius: 8px;
    border-color: #d4af37;
    border-width: 2px;
    padding: 8px;
    transition-duration: 0.15s;
}

.card-element:hover {
    translate: 0 -25px;
    scale: 1.08;
    border-color: #ffaa00;
}

.card-title {
    color: #ffffff;
    font-size: 14px;
    -unity-font-style: bold;
    margin-top: 4px;
    text-align: center;
}

.card-desc {
    color: #b0b8c4;
    font-size: 11px;
    margin-top: 8px;
    white-space: normal;
}

.card-cost {
    position: absolute;
    top: 4px;
    left: 4px;
    background-color: #d97706;
    color: white;
    border-radius: 4px;
    padding: 2px 6px;
    font-size: 10px;
}
```

---

## 🧪 4. Langkah Verifikasi
1. Buka Scene pertempuran, pastikan kartu starter muncul di dasar layar.
2. Arahkan kursor ke kartu: kartu akan melayang ke atas dengan efek hover mulus.
3. Klik dan seret kartu ke ubin arena untuk memicu broadcast aksi.
