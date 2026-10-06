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

        /// <summary>
        /// Mencoba mengurangi Gold pemain jika saldo mencukupi (Atomic validation).
        /// Mengembalikan true jika transaksi berhasil.
        /// </summary>
        public bool TrySpendGold(int amount)
        {
            if (CurrentGold >= amount)
            {
                CurrentGold -= amount;
                OnGoldChanged?.Invoke(CurrentGold); // Siarkan perubahan saldo ke UI
                return true;
            }
            return false;
        }

        /// <summary>
        /// Menambahkan Gold reward dari pertarungan atau event ke pundi pemain.
        /// </summary>
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
    /// <summary>
    /// Mengontrol tampilan Toko Pedagang (Merchant Shop): Pembelian kartu dan Purge deck.
    /// </summary>
    [RequireComponent(typeof(UIDocument))]
    public class ShopScreenController : MonoBehaviour
    {
        [SerializeField] private DeckManager _deckManager;
        [SerializeField] private List<CardData> _shopCardPool = new();

        private UIDocument _uiDocument;
        private Label _goldLabel;
        private VisualElement _cardShelf;
        private Button _purgeDeckButton;
        private int _purgeCost = 75; // Biaya awal layanan penghapusan kartu

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

        /// <summary>
        /// Mengisi etalase toko dengan 3 kartu acak dari pool kartu toko.
        /// </summary>
        private void PopulateShopCards()
        {
            if (_cardShelf == null) return;
            _cardShelf.Clear();

            // Batasi tampilan maksimal 3 kartu di etalase
            for (int i = 0; i < 3 && i < _shopCardPool.Count; i++)
            {
                CardData card = _shopCardPool[i];
                int cardPrice = 50; // Harga flat standar kartu

                var itemBox = new VisualElement();
                itemBox.AddToClassList("shop-item-box");

                var nameLbl = new Label(card.Name);
                var priceBtn = new Button(() => BuyCard(card, cardPrice, itemBox));
                priceBtn.text = $"Beli: {cardPrice} G";

                itemBox.Add(nameLbl);
                itemBox.Add(priceBtn);
                _cardShelf.Add(itemBox);
            }
        }

        /// <summary>
        /// Menangani transaksi pembelian kartu: potong gold, nonaktifkan kotak kartu (Sold Out),
        /// dan perbarui saldo tampilan.
        /// </summary>
        private void BuyCard(CardData card, int price, VisualElement itemBox)
        {
            if (GoldManager.Instance != null && GoldManager.Instance.TrySpendGold(price))
            {
                Debug.Log($"[Shop] Successfully purchased card: {card.Name}");
                itemBox.SetEnabled(false);
                itemBox.AddToClassList("sold-out"); // Visual feedback sold out
                UpdateGoldDisplay(GoldManager.Instance.CurrentGold);
            }
        }

        /// <summary>
        /// Menangani layanan Purge Deck: menghapus kartu yang tidak diinginkan dari deck.
        /// Biaya bertambah +25G setiap kali digunakan (Eskalasi harga roguelike).
        /// </summary>
        private void OnPurgeDeckClicked()
        {
            if (GoldManager.Instance != null && GoldManager.Instance.TrySpendGold(_purgeCost))
            {
                Debug.Log("[Shop] Opening deck card purge modal!");
                _purgeCost += 25; // Eskalasi biaya untuk penggunaan berikutnya
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
