---
id: TICKET-06C
title: Card Range Preview Hover & Card Dissolve Deploy Animation
status: Todo
priority: Medium
labels: [UI, CardUX, Animation, Domain3, Fase1]
---

# Deskripsi
Mengimplementasikan dua UX enhancement kritis dari Domain 3 Milestone 1 & 2 yang **belum ada tiketnya**:

1. **Card Range Preview on Hover** — saat pemain hover kartu di tangan, arena secara otomatis menyorot tile valid target (hijau/biru sesuai tipe kartu) menggunakan `CardPlayValidator.GetValidTargetTiles()`.
2. **Card Dissolve Deploy Animation** — saat kartu berhasil di-drop ke arena, kartu visual melakukan animasi dissolve/disintegrate sebelum menghilang dari tangan.

## Acceptance Criteria

### A. Card Range Preview on Hover
- [ ] Di `CardHandController.cs`, tambahkan pointer event `PointerEnterEvent`:
  - Method `PreviewCardRange(CardData card)`:
    - Panggil `CardPlayValidator.GetValidTargetTiles(card, playerPos, grid)`.
    - Broadcast `CombatEvents.OnHighlightTilesRequested` dengan tiles valid (HighlightType.ValidCardTarget = hijau).
- [ ] Event `PointerLeaveEvent` pada kartu:
  - Broadcast `CombatEvents.OnClearAllHighlights?.Invoke()` untuk hapus preview.
- [ ] Preview hanya aktif di `PlayerPhase` — cek state sebelum highlight.

### B. Card Dissolve Deploy Animation
- [ ] `CardDeployAnimator.cs` (MonoBehaviour atau USS Transition):
  - Method `IEnumerator PlayDeployAnimation(VisualElement cardElement)`:
    - Phase 1 (0.0-0.1s): Scale naik dari 1.0x ke 1.2x + border glow biru menyala.
    - Phase 2 (0.1-0.3s): Scale turun + opacity dari 1.0 ke 0.0 + translate naik 20px.
    - Phase 3: Hapus elemen dari DOM (atau sembunyikan).
  - Dipanggil oleh `CardHandController.OnCardDroppedOnGrid` sebelum memanggil event bus.
- [ ] Alternatif CSS Transition via USS:
  - `.card--deploying` class: `transition: opacity 0.2s ease-out, scale 0.2s ease-in, translate 0.2s ease-out`.
  - Method adds class → wait → remove element.

### C. Card Lift on Hover Effect (Enhancement dari TICKET-06)
- [ ] Verify dan polish: hover card melakukan lift 24px dengan smooth transition 0.15s.
- [ ] Border glow `box-shadow: 0 0 12px 2px #58A6FF` saat hover.
- [ ] Scale: `scale: 1.05` saat hover.
- [ ] Transition: `transition: transform 0.15s ease, box-shadow 0.15s ease`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/UI/CardHandController.cs` (update dari TICKET-06)
- `Assets/Scripts/UI/CardDeployAnimator.cs`
- `Assets/UI/USS/Cards.uss` (update)

## Dependensi
- **Bergantung pada:** TICKET-03B (`CardPlayValidator.GetValidTargetTiles`), TICKET-06 (CardHandController base), TICKET-04 (`GridTilemapView` harus siap menerima highlight event dari hover).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
