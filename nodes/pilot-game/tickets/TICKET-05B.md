---
id: TICKET-05B
title: 14 Skill VFX Prefab & Object Pooling System
status: Todo
priority: High
labels: [VFX, Animation, ObjectPooling, Domain2, Fase1]
---

# Deskripsi
Mengimplementasikan **14 prefab visual efek skill (VFX)** untuk semua kartu tempur Nabu (tebasan, proyektil, area efek) dan membangun **sistem Object Pooling** untuk menghindari GC spike saat spawning/destroying VFX dan damage popup. Ini adalah deliverable Milestone 2 dari Domain 2.

Tiket ini adalah komponen `Skill Visuals & Projectiles` dari diagram arsitektur yang belum ada tiketnya.

## Acceptance Criteria

### A. Object Pooling System
- [ ] `VFXPoolManager.cs` (MonoBehaviour, Singleton):
  - Menggunakan `UnityEngine.Pool.ObjectPool<T>` untuk setiap tipe VFX.
  - Method `GameObject GetVFX(string vfxId)` — ambil dari pool berdasarkan ID.
  - Method `void ReturnVFX(string vfxId, GameObject obj)` — kembalikan ke pool.
  - Auto-initialize pool dengan capacity 5 per VFX type saat Awake.
- [ ] `DamagePopup.cs` (MonoBehaviour) dengan Object Pool:
  - Menampilkan angka damage melayang ke atas lalu fade out (durasi: 0.8 detik).
  - Menggunakan TextMeshPro atau UI Toolkit label.
  - Static `DamagePopupPool.Get()` dan `DamagePopupPool.Release(popup)`.
  - Subscribe `CombatEvents.OnUnitDamaged` → spawn popup di posisi unit terdampak.

### B. 14 Skill VFX Prefab
Dibuat di `Assets/Prefabs/VFX/Skills/` — masing-masing berisi: `SpriteRenderer`/`ParticleSystem`, script `AutoReturnToPool.cs` (return ke pool setelah duration selesai):

1. `VFX_Teleport_Blink.prefab` — Efek dissolve + blink sparkle (Card_Teleport)
2. `VFX_Decoy_Spawn.prefab` — Efek muncul bayangan semi-transparan (Card_Decoy)
3. `VFX_Frost_Area.prefab` — Partikel es biru menyebar di area (Card_Frost)
4. `VFX_HeavyRain_Cloud.prefab` — Cloud sprite melayang di atas arena (Card_HeavyRain)
5. `VFX_Fog_Spread.prefab` — Partikel kabut putih memuai (Card_Fog)
6. `VFX_Storm_Lightning.prefab` — Kilat kecil berulang di seluruh arena (Card_Storm)
7. `VFX_ClearWeather.prefab` — Efek sapuan angin bersih (Card_ClearWeather)
8. `VFX_SkeletonSummon.prefab` — Ring spawn tulang kerangka sekeliling pemain (Card_SkeletonArmy)
9. `VFX_ThrowingBlade.prefab` — Proyektil belati terbang lurus (Card_ThrowingBlade)
10. `VFX_SandBurial.prefab` — Pasir muncrat dari tanah mengelilingi target (Card_SandBurial)
11. `VFX_Clone_Fade.prefab` — Bayangan klon bermunculan (Card_Clone)
12. `VFX_Dash_Trail.prefab` — Trail biru saat dash (Card_Dash)
13. `VFX_SuperPunch_Impact.prefab` — Impact shockwave besar (Card_SuperPunch)
14. `VFX_GravityLift_Pull.prefab` — Efek antigravity partikel naik ke atas (Card_GravityLift)

- [ ] `SkillVFXPresenter.cs` (MonoBehaviour):
  - Subscribe `CombatEvents.OnSkillExecuted`.
  - Method `void HandleSkillExecuted(int casterId, int skillId, Vector2Int target)` — spawn VFX yang sesuai dari pool di posisi world target.
  - Mapping `skillId` → VFX prefab via `[SerializeField] SkillVFXMapping[] _mappings`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/VFXPoolManager.cs`
- `Assets/Scripts/Units/DamagePopup.cs`
- `Assets/Scripts/Units/SkillVFXPresenter.cs`
- `Assets/Scripts/Units/AutoReturnToPool.cs`
- `Assets/Prefabs/VFX/Skills/` (14 prefab)

## Dependensi
- **Bergantung pada:** TICKET-01 (`CombatEvents.OnSkillExecuted`, `OnUnitDamaged`), TICKET-02B (14 CardData dengan CardId untuk mapping).
- **Digunakan oleh:** TICKET-05C (UnitAnimatorPresenter memanggil SkillVFXPresenter), TICKET-07 (semua VFX harus ada sebelum scene assembly).

## Catatan Teknis
- VFX placeholder (Unity built-in particle atau Sprite flash) cukup untuk Fase 1. Art VFX final masuk ke sprint terpisah.
- `AutoReturnToPool.cs` menggunakan coroutine yield wait lalu panggil `VFXPoolManager.ReturnVFX`.
- DILARANG `Instantiate`/`Destroy` untuk VFX dan DamagePopup — wajib pakai Object Pool.

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
