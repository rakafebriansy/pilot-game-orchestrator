# 📖 Manual Guide: TICKET-06C — Card Range Preview Hover & Card Dissolve Animation

> **Referensi Tiket:** [TICKET-06C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-06C.md)  
> **Domain:** `[🃏 DOMAIN 3: CARD DECK & UI]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini menambahkan dua penyempurnaan pengalaman pengguna (*UX Polish*):
1. **Range Preview Highlighter saat Hover Kartu**: Ketika kursor mouse berada di atas kartu di tangan, sistem secara otomatis menghitung jangkauan valid via `CardPlayValidator.GetValidTargetTiles()` dan menyalakan highlight biru/hijau di arena.
2. **Animasi Dissolve / Burn Deploy Kartu**: Saat kartu dilepas dan berhasil dimainkan, kartu terbakar (*dissolve/burn*) menjadi abu cahaya sebelum masuk ke *Discard Pile*.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── UI/
│       ├── CardRangePreviewer.cs
│       └── CardDeployAnimator.cs
└── Shaders/
    └── CardDissolveBurn.shader
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/UI/CardRangePreviewer.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;

namespace PilotGame.UI
{
    /// <summary>
    /// Menghubungkan hover kartu di UI dengan visualisasi jangkauan ubin di arena.
    /// </summary>
    public class CardRangePreviewer : MonoBehaviour
    {
        private GridDataModel _gridModel;
        private Vector2Int _playerGridPos = new Vector2Int(2, 2);

        public void Initialize(GridDataModel gridModel)
        {
            _gridModel = gridModel;
        }

        public void SetPlayerPosition(Vector2Int pos)
        {
            _playerGridPos = pos;
        }

        public void OnCardHoverEnter(CardData card)
        {
            if (card == null || _gridModel == null) return;

            List<Vector2Int> targetTiles = CardPlayValidator.GetValidTargetTiles(card, _playerGridPos, _gridModel);

            HighlightType style = card.ActionType == CardActionType.Movement
                ? HighlightType.MovementRange
                : HighlightType.ValidCardTarget;

            CombatEvents.OnHighlightTilesRequested?.Invoke(
                new TileHighlightRequest(targetTiles.ToArray(), style)
            );
        }

        public void OnCardHoverExit()
        {
            CombatEvents.OnClearAllHighlights?.Invoke();
        }
    }
}
```

---

### B. `Assets/Scripts/UI/CardDeployAnimator.cs`
```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.UIElements;

namespace PilotGame.UI
{
    /// <summary>
    /// Menganimasikan dissolve terbakar kartu UI saat kartu sah dimainkan.
    /// </summary>
    public class CardDeployAnimator : MonoBehaviour
    {
        public void PlayDeployAnimation(VisualElement cardElement, System.Action onComplete)
        {
            if (cardElement == null)
            {
                onComplete?.Invoke();
                return;
            }

            StartCoroutine(DissolveRoutine(cardElement, onComplete));
        }

        private IEnumerator DissolveRoutine(VisualElement element, System.Action onComplete)
        {
            float duration = 0.35f;
            float elapsed = 0f;

            while (elapsed < duration)
            {
                float t = elapsed / duration;
                element.style.opacity = 1f - t;
                element.style.scale = new Scale(Vector3.one * (1f + (t * 0.2f)));
                element.style.translate = new Translate(0, -t * 60f);

                elapsed += Time.deltaTime;
                yield return null;
            }

            onComplete?.Invoke();
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Di Play Mode, arahkan kursor ke kartu `Throwing Blade` (Range 3).
2. Perhatikan ubin hijau menyala di sekitar pemain dalam radius 3 petak.
3. Lepaskan kursor dari kartu, ubin highlight otomatis bersih kembali (*ClearAllHighlights*).
4. Mainkan kartu, kartu akan memudar ke atas dengan animasi dissolve elegan sebelum menghilang dari tangan.
