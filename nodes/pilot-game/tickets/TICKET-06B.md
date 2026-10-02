---
id: TICKET-06B
title: Combat HUD — Energy Counter, Intent Badges & Phase Banner
status: Todo
priority: High
labels: [UI, HUD, CombatUI, Domain3, Fase1]
---

# Deskripsi
Membangun **Combat HUD (Heads-Up Display)** lengkap untuk arena pertempuran menggunakan UI Toolkit. HUD mencakup tiga elemen kritis yang ada di diagram (`Combat HUD` node di D3): **Energy Counter** (berapa aksi pemain tersisa), **Enemy Intent Badges** (ikon niat musuh di atas kepala mereka), dan **Phase Banner** (indikator fase giliran aktif).

## Acceptance Criteria

### A. Energy Counter
- [ ] Komponen `EnergyCounter` di `CombatHUD.uxml`:
  - Menampilkan "⚡ 1/1" — jumlah aksi kartu tersisa / maksimum.
  - Untuk MVP: 1 aksi per ronde (1 kartu per PlayerPhase).
  - Update visual saat kartu dimainkan (langsung berubah ke 0/1).
  - Reset ke 1/1 saat `RoundResetPhase`.
- [ ] `CombatHUDPresenter.cs` (MonoBehaviour):
  - Subscribe `CombatEvents.OnPhaseChanged` dan `CombatEvents.OnCardPlayed`.
  - Update EnergyCounter display saat kartu dimainkan.
  - Reset counter saat phase berubah ke `RoundResetPhase`.

### B. Enemy Intent Badges
- [ ] `IntentBadge` system — badge visual yang muncul di atas kepala musuh saat `IntentPhase`:
  - Badge berupa UI world-space element (Canvas atau UI Toolkit overlay) di posisi atas sprite musuh.
  - Ikon berbeda per intent type:
    - ⚔️ Merah → `DangerEnemyIntent` (akan menyerang).
    - 🛡️ Biru → musuh bertahan (defensive).
    - 👣 Hijau → musuh akan bergerak (movement only).
  - Badge hilang saat fase berganti ke `PlayerPhase`.
- [ ] `EnemyIntentBadgePresenter.cs`:
  - Subscribe `CombatEvents.OnEnemyIntentDecided` → spawn badge di atas musuh yang sesuai.
  - Subscribe `CombatEvents.OnPhaseChanged` → sembunyikan semua badge saat bukan `IntentPhase`.

### C. Phase Banner
- [ ] Banner teks di bagian atas tengah layar menampilkan fase aktif:
  - "🧠 INTENT PHASE" → merah/gelap.
  - "🃏 YOUR TURN" → biru/terang, animasi pulse.
  - "⚔️ ENEMY PHASE" → merah berkedip.
  - "🔄 ROUND RESET" → abu-abu.
- [ ] Animasi banner: fade in 0.2s → tampil 1.5s → fade out 0.3s.
- [ ] Subscribe `CombatEvents.OnPhaseChanged` untuk trigger animasi banner.

### D. Layout UXML Lengkap
- [ ] `CombatHUD.uxml` mengandung semua elemen:
  - Top-left: Phase Banner.
  - Top-right: Energy Counter.
  - Overlay: Intent Badge container (posisi relatif ke unit).
  - Bottom: Hand Container (sudah ada di TICKET-06, pastikan kompatibel).
- [ ] `CombatHUD.uss` mendefinisikan semua style dengan design tokens.

## Target Lingkup File (Affected Files)
- `Assets/UI/UXML/CombatHUD.uxml`
- `Assets/UI/USS/CombatHUD.uss`
- `Assets/Scripts/UI/CombatHUDPresenter.cs`
- `Assets/Scripts/UI/EnemyIntentBadgePresenter.cs`

## Dependensi
- **Bergantung pada:** TICKET-01 (CombatEvents), TICKET-06 (CardHandHUD kompatibel dalam satu UXML).
- **Digunakan oleh:** TICKET-07 (CardHUD_UI_Prefab di scene assembly mencakup seluruh HUD).

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
