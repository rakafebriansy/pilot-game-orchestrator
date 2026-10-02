---
id: TICKET-04B
title: Camera Post-Processing, URP 2D Lighting & Audio Environment
status: Todo
priority: High
labels: [View, Camera, URP, Lighting, Audio, Domain1, Fase1]
---

# Deskripsi
Tiket ini mengimplementasikan komponen visual kritis dari Domain 1 yang **tidak tercakup di TICKET-04**: konfigurasi kamera Post-Processing (Bloom, Color Grading tema Mesopotamia), pencahayaan dinamis URP `Light2D` (obor berkedip, ambient malam), dan audio environment awal (BGM ambient + SFX telegraph musuh).

Tanpa tiket ini, visual game terlihat flat dan tidak atmosferik — melanggar standar premium design yang diharapkan.

## Acceptance Criteria

### A. Camera & Post-Processing
- [ ] `Main Camera` dikonfigurasi:
  - Projection: Orthographic.
  - Orthographic Size: disesuaikan agar grid 15×15 terlihat penuh (sekitar 8-9 unit).
  - Universal Additional Camera Data: Render Type = Base, Anti-aliasing: SMAA Low.
- [ ] **Volume Post Processing** ditambahkan ke scene dengan profile `MesopotamiaCombat_PP.asset`:
  - `Bloom`: Intensity 0.6, Threshold 0.9 (efek glow lembut pada obor dan rune).
  - `Color Grading`: Mode LDR, Saturation -15, Contrast +15, Color Filter tinted warm amber (#D4A96A).
  - `Vignette`: Intensity 0.3 (sudut layar sedikit gelap untuk suasana).

### B. URP 2D Lighting
- [ ] `Global Light 2D` (ambient malam): Color #1A1420, Intensity 0.3 — suasana gelap menara.
- [ ] `Point Light 2D` obor: Prefab `TorchLight_Prefab.prefab` dengan:
  - Color: #FF8C42 (oranye obor).
  - Radius Inner: 1.5, Radius Outer: 3.0.
  - Falloff: Smooth.
  - Script `TorchFlicker.cs` — coroutine yang menganimasikan intensitas antara 0.8 dan 1.2 setiap 0.1-0.3 detik (random).
- [ ] Minimal 4 torch light ditempatkan di sudut-sudut arena dalam `ArenaGrid_Prefab`.
- [ ] Shadow Caster 2D ditambahkan ke `Obstacles_Tilemap` agar pilar menghasilkan bayangan.

### C. Audio Environment
- [ ] `AudioManager.cs` (Singleton, DontDestroyOnLoad):
  - Method `void PlayBGM(AudioClip clip, float volume = 0.6f)` — play dengan `AudioSource` loop.
  - Method `void PlaySFX(AudioClip clip, float volume = 1.0f)` — play one-shot.
  - Method `void StopBGM()`.
- [ ] `CombatAudioPresenter.cs` (MonoBehaviour):
  - Subscribe `CombatEvents.OnPhaseChanged` → play appropriate audio cue per phase.
  - Subscribe `CombatEvents.OnEnemyIntentDecided` → play SFX telegraph (sinister hum/click).
  - Subscribe `CombatEvents.OnUnitDamaged` → play SFX hit impact.
- [ ] Placeholder AudioClip (.wav silent) untuk verifikasi structure — art asset audio akan diisi terpisah.
- [ ] `AudioSettings.asset` ScriptableObject: Master volume, BGM volume, SFX volume sliders.

## Target Lingkup File (Affected Files)
- `Assets/Settings/PostProcessing/MesopotamiaCombat_PP.asset`
- `Assets/Scripts/Grid/TorchFlicker.cs`
- `Assets/Prefabs/Environment/TorchLight_Prefab.prefab`
- `Assets/Scripts/Core/AudioManager.cs`
- `Assets/Scripts/UI/CombatAudioPresenter.cs`
- `Assets/Scripts/Core/Data/AudioSettings.cs`

## Dependensi
- **Bergantung pada:** TICKET-04 (ArenaGrid_Prefab sudah ada), TICKET-01 (CombatEvents untuk audio triggers).
- **Digunakan oleh:** TICKET-04C (Pulsing Danger Shader memerlukan URP material setup), TICKET-07 (scene assembly).

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
