---
id: TICKET-06
title: Antarmuka Kartu UI Toolkit & Sistem Drag-and-Drop
status: Todo
priority: High
labels: [UI, UIToolkit, Cards, Input, DragDrop, Domain3]
---

# Deskripsi
Menyusun **antarmuka tangan kartu (*Card Hand HUD*)** menggunakan Unity UI Toolkit (UXML & USS), mengimplementasikan `DeckManager.cs` untuk siklus kartu lengkap (draw 5, discard, reshuffle otomatis), dan menangani interaksi drag-and-drop kartu ke ubin arena dengan output event `CombatEvents.OnCardPlayed`.

Ini adalah implementasi Domain 3 (Card Deck & UI) — satu-satunya interaksi user input yang sah selama PlayerPhase.

## Acceptance Criteria
- [ ] `CardHandHUD.uxml` terstruktur dengan:
  - Root element: `<ui:VisualElement name="hand-hud-root">`.
  - Container tangan: `<ui:VisualElement name="hand-container">` untuk menampung card visual.
  - Setiap kartu sebagai child `<ui:VisualElement name="card-{id}">` dengan child: `card__name`, `card__type-icon`, `card__value`, `card__illustration`.
  - Gunakan BEM naming convention untuk semua class CSS.
- [ ] `Cards.uss` mendefinisikan:
  - Token warna dari Design System (variabel `--color-attack`, `--color-defense`, dll).
  - Style default kartu dengan border-radius, background, dan shadow.
  - Pseudo-class `:hover` pada `.card` memberikan efek lift (transform: translate ke atas).
  - State `.card--selected` untuk kartu yang sedang di-drag (opacity dikurangi).
- [ ] `DeckManager.cs` (MonoBehaviour):
  - `[SerializeField] List<CardData> _fullDeck` — daftar 15 kartu dalam deck.
  - Field internal: `List<CardData> _drawPile`, `List<CardData> _discardPile`, `List<CardData> _hand`.
  - Method `void ShuffleDeck()` — mengacak `_drawPile` menggunakan Fisher-Yates shuffle.
  - Method `void DrawCards(int count)` — menarik `count` kartu ke `_hand`. Jika `_drawPile` kosong, reshuffle `_discardPile` ke `_drawPile` secara otomatis (tanpa penalti).
  - Method `void DiscardCard(CardData card)` — memindahkan kartu dari `_hand` ke `_discardPile`.
  - Subscribe `CombatEvents.OnDrawCardsRequested` → panggil `DrawCards(5)`.
- [ ] `CardHandController.cs` (MonoBehaviour):
  - Menerima referensi `UIDocument` untuk mengakses `rootVisualElement`.
  - Method `RenderHand(List<CardData> hand)` — membuat VisualElement card untuk setiap kartu di tangan.
  - Implementasi pointer drag menggunakan `PointerDownEvent`, `PointerMoveEvent`, `PointerUpEvent`.
  - Saat pointer dilepaskan di atas area arena grid: panggil `OnCardDroppedOnGrid(card, targetCoord)`.
  - Method `OnCardDroppedOnGrid(CardData card, Vector2Int targetCoord)`:
    - Broadcast `CombatEvents.OnCardPlayed?.Invoke(card, targetCoord)`.
    - Panggil `DeckManager.DiscardCard(card)`.
    - Panggil `CombatEvents.OnClearAllHighlights?.Invoke()`.
- [ ] **Verifikasi:** Kartu dapat di-drag dari area HUD, dilepas di atas grid, dan event `OnCardPlayed` ter-log di Console.

## Target Lingkup File (Affected Files)
- `Assets/UI/UXML/CardHandHUD.uxml`
- `Assets/UI/USS/Cards.uss`
- `Assets/Scripts/Cards/DeckManager.cs`
- `Assets/Scripts/UI/CardHandController.cs`

## Dependensi
- **Bergantung pada:** TICKET-01 (`CombatEvents`), TICKET-02 (`CardData`).
- **Digunakan oleh:** TICKET-07 (CardHandHUD di-assembly ke scene, FSM mengontrol lock/unlock UI).

## Catatan Teknis
- **Reshuffle otomatis:** Saat `_drawPile` kosong, reshuffle `_discardPile` → `_drawPile` → panggil `DrawCards(count remaining)`. Ini berjalan tanpa notifikasi ke pemain (seamless).
- Interaksi drag-drop menggunakan **UI Toolkit Pointer Events** bukan `EventSystem` UGUI.
- Untuk mendeteksi "kartu dilepas di atas arena", gunakan `Camera.main.ScreenToWorldPoint` dan konversikan ke koordinat grid integer.
- Di PlayerPhase, kartu interaktif. Di fase lain (`IntentPhase`, `EnemyPhase`, `RoundResetPhase`), semua pointer event harus diblokir (tambahkan state `_isLocked`).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  - *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
