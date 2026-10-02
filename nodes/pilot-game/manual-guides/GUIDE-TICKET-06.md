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

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Pembuatan Aset Panel Settings UI Toolkit
1. Di Project Window, buat folder `Assets/UI/Settings/`.
2. Klik kanan di folder tersebut > pilih **Create > UI Toolkit > Panel Settings Asset**.
3. Beri nama: `CombatPanelSettings.asset`.
4. Di panel **Inspector** pada `CombatPanelSettings`:
   * **Scale Mode:** `Scale With Screen Size`.
   * **Reference Resolution:** `X: 1920, Y: 1080`.
   * **Screen Match Mode:** `Match Width Or Height`.
   * **Match:** `0.5`.
   * **Sorting Order:** `100` *(Memastikan UI selalu berada paling depan di atas seluruh elemen pertempuran)*.

---

### Langkah 2.2: Pembuatan Dokumen UXML & USS di UI Builder
1. Di Project Window, buat folder:
   * `Assets/UI/UXML/`
   * `Assets/UI/USS/`
2. Klik kanan di folder `Assets/UI/USS/` > **Create > UI Toolkit > Style Sheet**, beri nama `CombatHandUI.uss`.
3. Klik kanan di folder `Assets/UI/UXML/` > **Create > UI Toolkit > UI Document**, beri nama `CombatHandUI.uxml`.
4. Klik dua kali file `CombatHandUI.uxml` untuk membukanya di jendela **UI Builder**:
   * Di panel kiri atas (StyleSheets), klik tombol **`+`** > pilih **Add Existing USS** > pilih `CombatHandUI.uss`.
   * Di panel Library (kiri bawah), tarik elemen **VisualElement** ke Hierarchy UI Builder.
   * Di panel Inspector (kanan), beri nama Name = `hand-container` dan tambahkan Class = `hand-container`.
   * Simpan file via menu **File > Save** di UI Builder (`Ctrl+S` / `Cmd+S`), lalu tutup UI Builder.

---

### Langkah 2.3: Setup GameObject UI Document di Hierarchy Scene
1. Di panel **Hierarchy**, klik kanan > **UI Toolkit > UI Document**.
2. Ganti nama GameObject menjadi `[UI_CardHand]`.
3. Di panel **Inspector** pada komponen **UI Document**:
   * **Panel Settings:** Seret `CombatPanelSettings.asset` ke slot ini.
   * **Source Asset:** Seret `CombatHandUI.uxml` ke slot ini.
4. Klik tombol **Add Component** > ketik `DeckManager` > tekan Enter.
   * Di Inspector `DeckManager`, tambahkan 5 kartu dari `Assets/ScriptableObjects/Cards/` ke list **Starter Deck**.
5. Klik tombol **Add Component** > ketik `CardHandController` > tekan Enter.
   * **Deck Manager:** Seret komponen `DeckManager` ke slot ini.
   * **Main Camera:** Seret `Main Camera` ke slot ini.

```text
[Hierarchy]
└── [UI_CardHand]                 [UIDocument, DeckManager, CardHandController]
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
                    if (_discardPile.Count == 0) break;
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

### C. `Assets/UI/UXML/CombatHandUI.uxml`
```xml
<ui:UXML xmlns:ui="UnityEngine.UIElements" xmlns:uie="UnityEditor.UIElements" editor-extension-mode="False">
    <Style src="project://database/Assets/UI/USS/CombatHandUI.uss" />
    <ui:VisualElement name="hand-root" class="hand-root">
        <ui:VisualElement name="hand-container" class="hand-container" />
    </ui:VisualElement>
</ui:UXML>
```

---

### D. `Assets/UI/USS/CombatHandUI.uss`
```css
.hand-root {
    width: 100%;
    height: 100%;
    position: absolute;
    justify-content: flex-end;
    align-items: center;
    pointer-events: none;
}

.hand-container {
    margin-bottom: 24px;
    flex-direction: row;
    align-items: flex-end;
    justify-content: center;
    gap: 16px;
    pointer-events: auto;
}

.card-element {
    width: 150px;
    height: 220px;
    background-color: #1a1e29;
    border-radius: 10px;
    border-color: #d4af37;
    border-width: 2px;
    padding: 10px;
    transition-duration: 0.15s;
    transition-timing-function: ease-out;
}

.card-element:hover {
    translate: 0 -30px;
    scale: 1.1;
    border-color: #ffaa00;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.6);
}

.card-title {
    color: #ffffff;
    font-size: 15px;
    -unity-font-style: bold;
    margin-top: 6px;
    text-align: center;
}

.card-desc {
    color: #b0b8c4;
    font-size: 11px;
    margin-top: 10px;
    white-space: normal;
}

.card-cost {
    position: absolute;
    top: 6px;
    left: 6px;
    background-color: #d97706;
    color: white;
    border-radius: 4px;
    padding: 2px 8px;
    font-size: 11px;
    -unity-font-style: bold;
}
```

---

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Tekan tombol **Play** di Unity Editor.
2. 5 kartu starter akan berbaris rapi di bagian bawah tengah layar.
3. Sorot mouse ke setiap kartu untuk mengamati animasi melayang naik (*hover elevate*).
4. Klik dan tahan kartu, lalu lepaskan di arena untuk memverifikasi penerimaan input pointer.
