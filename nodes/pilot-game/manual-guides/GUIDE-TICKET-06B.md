# 📖 Manual Guide: TICKET-06B — Combat HUD: Energy Counter, Intent Badges & Phase Banner

> **Referensi Tiket:** [TICKET-06B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06B.md)  
> **Domain:** `[🃏 DOMAIN 3: CARD DECK & UI]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan elemen **Heads-Up Display (HUD) Pertempuran**:
1. **Energy / Action Counter**: Indikator aksi wajib 1 kartu per giliran (GDD §4.1).
2. **Enemy Intent Badges**: Ikon telegraf niat serangan (Pedang/Panah/Perisai) melayang di atas kepala musuh yang terpasang otomatis saat `CombatEvents.OnEnemyIntentDecided` terpanggil.
3. **Phase Banner**: Banner transisi teks elegan (*"INTENT PHASE"*, *"PLAYER TURN"*, *"ENEMY TURN"*) yang menyala saat fase berganti.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── UI/
│       ├── CombatHUDPresenter.cs
│       └── EnemyIntentBadgePresenter.cs
└── UI/
    ├── UXML/
    │   └── CombatHUD.uxml
    └── USS/
        └── CombatHUD.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/UI/CombatHUDPresenter.cs`
```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.UIElements;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.UI
{
    /// <summary>
    /// Menampilkan HUD utama pertempuran: Banner transisi fase dan giliran pertempuran.
    /// </summary>
    [RequireComponent(typeof(UIDocument))]
    public class CombatHUDPresenter : MonoBehaviour
    {
        private UIDocument _uiDocument;
        private Label _phaseBannerLabel;
        private VisualElement _phaseBannerContainer;
        private Label _energyLabel;

        private void Awake()
        {
            _uiDocument = GetComponent<UIDocument>();
        }

        private void OnEnable()
        {
            var root = _uiDocument.rootVisualElement;
            _phaseBannerContainer = root.Q<VisualElement>("phase-banner-container");
            _phaseBannerLabel = root.Q<Label>("phase-banner-label");
            _energyLabel = root.Q<Label>("energy-label");

            // Berlangganan event transisi fase untuk memicu pembaruan HUD
            CombatEvents.OnPhaseChanged += HandlePhaseChanged;
        }

        private void OnDisable()
        {
            // Unsubscribe untuk mencegah memory leak dan akses UI null saat scene ditutup
            CombatEvents.OnPhaseChanged -= HandlePhaseChanged;
        }

        private void HandlePhaseChanged(CombatPhase phase)
        {
            // Menentukan teks banner sesuai siklus 4 fase pertempuran taktis
            string bannerText = phase switch
            {
                CombatPhase.IntentPhase => "✦ ENEMY INTENT PHASE ✦",
                CombatPhase.PlayerPhase => "⚔ YOUR TURN (PLAY 1 CARD) ⚔",
                CombatPhase.EnemyPhase => "☠ ENEMY ATTACK PHASE ☠",
                CombatPhase.RoundResetPhase => "↺ ROUND RESET ↺",
                _ => string.Empty
            };

            // Menampilkan banner transisi fase dengan animasi kilau/flash
            if (_phaseBannerLabel != null && _phaseBannerContainer != null)
            {
                _phaseBannerLabel.text = bannerText;
                StartCoroutine(FlashBannerRoutine());
            }

            // Memperbarui indikator aksi: 1 aksi tersedia hanya saat giliran pemain (GDD §4.1)
            if (_energyLabel != null)
            {
                _energyLabel.text = phase == CombatPhase.PlayerPhase ? "Action: 1 / 1" : "Action: 0 / 1";
            }
        }

        /// <summary>
        /// Coroutine untuk memunculkan banner fase selama 1.2 detik lalu menyembunyikannya kembali.
        /// </summary>
        private IEnumerator FlashBannerRoutine()
        {
            // Menghapus kelas 'banner-hidden' agar transisi opacity/translate USS aktif (muncul)
            _phaseBannerContainer.RemoveFromClassList("banner-hidden");
            yield return new WaitForSeconds(1.2f);
            // Menambahkan kembali kelas 'banner-hidden' untuk menyembunyikan banner
            _phaseBannerContainer.AddToClassList("banner-hidden");
        }
    }
}
```

---

### B. `Assets/Scripts/UI/EnemyIntentBadgePresenter.cs`
```csharp
using UnityEngine;
using UnityEngine.UI;
using PilotGame.Core.Events;

namespace PilotGame.UI
{
    /// <summary>
    /// Menampilkan ikon telegraf niat (Pedang/Perisai) di atas kepala musuh saat intent diputuskan.
    /// </summary>
    public class EnemyIntentBadgePresenter : MonoBehaviour
    {
        [SerializeField] private int _enemyId = 2;
        [SerializeField] private GameObject _badgeContainer;
        [SerializeField] private Image _intentIconImage;

        [SerializeField] private Sprite _attackIntentIcon;
        [SerializeField] private Sprite _defendIntentIcon;

        private void OnEnable()
        {
            CombatEvents.OnEnemyIntentDecided += HandleIntentDecided;
            CombatEvents.OnPhaseChanged += HandlePhaseChanged;
        }

        private void OnDisable()
        {
            CombatEvents.OnEnemyIntentDecided -= HandleIntentDecided;
            CombatEvents.OnPhaseChanged -= HandlePhaseChanged;
        }

        /// <summary>
        /// Menangani event saat musuh telah menentukan niat serangannya di Intent Phase.
        /// </summary>
        private void HandleIntentDecided(int enemyId, Vector2Int targetCoord)
        {
            // Hanya perbarui badge jika ID musuh cocok dengan komponen presenter ini
            if (enemyId == _enemyId && _badgeContainer != null)
            {
                _badgeContainer.SetActive(true);
                if (_intentIconImage != null)
                {
                    // Tampilkan ikon niat serang (Pedang) di atas kepala unit
                    _intentIconImage.sprite = _attackIntentIcon;
                }
            }
        }

        /// <summary>
        /// Menangani pembersihan badge saat pergantian putaran pertempuran.
        /// </summary>
        private void HandlePhaseChanged(Core.Data.CombatPhase phase)
        {
            // Sembunyikan badge saat musuh telah selesai mengeksekusi niat pada RoundResetPhase
            if (phase == Core.Data.CombatPhase.RoundResetPhase && _badgeContainer != null)
            {
                _badgeContainer.SetActive(false);
            }
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Di Play Mode, panggil `CombatEvents.OnPhaseChanged?.Invoke(CombatPhase.PlayerPhase)`.
2. Pastikan banner emas *"YOUR TURN"* meluncur turun di tengah atas layar selama 1.2 detik lalu menghilang dengan transisi opacity halus.
