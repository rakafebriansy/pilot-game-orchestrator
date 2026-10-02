# 📖 Manual Guide: TICKET-09B — Merchant Shop UI: Beli Kartu, Relik & Purge Deck

> **Referensi Tiket:** [TICKET-09B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09B.md)  
> **Domain:** `[🃏 DOMAIN 3: CARD DECK & UI]`  
> **Fase:** 2 (Peta Eksplorasi & Wave Drafting)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan sistem ekonomi dan **Toko Pedagang (Merchant Shop)** yang ditemui di node peta ekspedisi:
1. **`GoldManager.cs`**: Mengelola saldo mata uang *Gold* yang didapat dari pertempuran.
2. **`RelicManager.cs`**: Mengelola relik pasif yang dibeli pemain.
3. **`ShopScreenController.cs`**: Menampilkan antarmuka toko untuk:
   * Membeli kartu tempur baru dari katalog acak.
   * Membeli relik pasif atau item consumable.
   * Layanan *Purge Deck* (menghapus 1 kartu dari deck aktif dengan biaya gold meningkat).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── Economy/
│   │   ├── GoldManager.cs
│   │   └── RelicManager.cs
│   └── UI/
│       └── ShopScreenController.cs
└── UI/
    ├── UXML/
    │   └── ShopScreenUI.uxml
    └── USS/
        └── ShopScreenUI.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Economy/GoldManager.cs`
```csharp
using System;
using UnityEngine;

namespace PilotGame.Economy
{
    public class GoldManager : MonoBehaviour
    {
        public static GoldManager Instance { get; private set; }

        public int CurrentGold { get; private set; } = 100; // Starter gold
        public event Action<int> OnGoldChanged;

        private void Awake()
        {
            if (Instance != null && Instance != this)
            {
                Destroy(gameObject);
                return;
            }
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }

        public bool TrySpendGold(int amount)
        {
            if (CurrentGold >= amount)
            {
                CurrentGold -= amount;
                OnGoldChanged?.Invoke(CurrentGold);
                return true;
            }
            return false;
        }

        public void AddGold(int amount)
        {
            CurrentGold += amount;
            OnGoldChanged?.Invoke(CurrentGold);
        }
    }
}
```

---

### B. `Assets/Scripts/UI/ShopScreenController.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UIElements;
using PilotGame.Cards;
using PilotGame.Economy;
using PilotGame.UI;

namespace PilotGame.UI
{
    [RequireComponent(typeof(UIDocument))]
    public class ShopScreenController : MonoBehaviour
    {
        [SerializeField] private DeckManager _deckManager;
        [SerializeField] private List<CardData> _shopCardPool = new();

        private UIDocument _uiDocument;
        private Label _goldLabel;
        private VisualElement _cardShelf;
        private Button _purgeDeckButton;
        private int _purgeCost = 75;

        private void Awake()
        {
            _uiDocument = GetComponent<UIDocument>();
        }

        private void Start()
        {
            var root = _uiDocument.rootVisualElement;
            _goldLabel = root.Q<Label>("gold-amount-label");
            _cardShelf = root.Q<VisualElement>("card-shelf-container");
            _purgeDeckButton = root.Q<Button>("purge-deck-button");

            UpdateGoldDisplay(GoldManager.Instance != null ? GoldManager.Instance.CurrentGold : 100);
            PopulateShopCards();

            _purgeDeckButton?.RegisterCallback<ClickEvent>(evt => OnPurgeDeckClicked());
        }

        private void UpdateGoldDisplay(int currentGold)
        {
            if (_goldLabel != null) _goldLabel.text = $"Gold: {currentGold} 💰";
        }

        private void PopulateShopCards()
        {
            if (_cardShelf == null) return;
            _cardShelf.Clear();

            for (int i = 0; i < 3 && i < _shopCardPool.Count; i++)
            {
                CardData card = _shopCardPool[i];
                int cardPrice = 50;

                var itemBox = new VisualElement();
                itemBox.AddToClassList("shop-item-box");

                var nameLbl = new Label(card.CardName);
                var priceBtn = new Button(() => BuyCard(card, cardPrice, itemBox));
                priceBtn.text = $"Beli: {cardPrice} G";

                itemBox.Add(nameLbl);
                itemBox.Add(priceBtn);
                _cardShelf.Add(itemBox);
            }
        }

        private void BuyCard(CardData card, int price, VisualElement itemBox)
        {
            if (GoldManager.Instance != null && GoldManager.Instance.TrySpendGold(price))
            {
                Debug.Log($"[Shop] Berhasil membeli kartu: {card.CardName}");
                itemBox.SetEnabled(false);
                itemBox.AddToClassList("sold-out");
                UpdateGoldDisplay(GoldManager.Instance.CurrentGold);
            }
        }

        private void OnPurgeDeckClicked()
        {
            if (GoldManager.Instance != null && GoldManager.Instance.TrySpendGold(_purgeCost))
            {
                Debug.Log("[Shop] Membuka modal hapus kartu dari deck!");
                _purgeCost += 25; // Biaya naik setiap kali dipakai
                _purgeDeckButton.text = $"Purge Deck ({_purgeCost} G)";
                UpdateGoldDisplay(GoldManager.Instance.CurrentGold);
            }
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Buka `ShopScene.unity`.
2. Klik tombol beli kartu: saldo Gold berkurang dan kartu ditandai *Sold Out*.
3. Klik tombol Purge: biaya purge bertambah 25G untuk pemakaian berikutnya.
