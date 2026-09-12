---
id: TICKET-06
title: Antarmuka Kartu UI Toolkit & Drag-and-Drop
status: Todo
priority: High
labels: [UI, UIToolkit, Cards, Input]
---

# Deskripsi
Menyusun antarmuka tangan kartu (*Card Hand HUD*) menggunakan Unity UI Toolkit (UXML & USS), mengimplementasikan `DeckManager.cs` untuk siklus kartu (draw 5, discard, reshuffle), dan menangani interaksi drag-and-drop kartu ke ubin arena.

## Acceptance Criteria
- [ ] `CardHand.uxml` dan `Cards.uss` tersusun rapi dengan BEM naming convention dan token warna Design System.
- [ ] Kartu memiliki efek hover lift dan mendukung pointer drag.
- [ ] Saat kartu dilepaskan di atas petak grid arena, method `OnCardDroppedOnGrid` menembakkan event `CombatEvents.OnCardPlayed(card, targetCoord)`.

## Target Lingkup File (Affected Files)
- `Assets/UI/UXML/CardHandHUD.uxml`
- `Assets/UI/USS/Cards.uss`
- `Assets/Scripts/Cards/DeckManager.cs`
- `Assets/Scripts/UI/CardHandController.cs`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
