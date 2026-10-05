---
id: TICKET-05B
title: Skill VFX Prefabs (Sinergi 1: Bleed & Assassination) & Object Pooling System
status: Todo
priority: High
labels: [VFX, Animation, ObjectPooling, BleedSynergy, Domain2, Fase1]
---

# Deskripsi
Mengimplementasikan **prefab visual efek skill (VFX)** khusus untuk kartu **Sinergi 1: Bleed & Assassination Archetype** (`Throwing Blade`, `Shadow Step`, `Serrated Dagger`, dan efek visual status `Bleed Tick`) serta membangun **sistem Object Pooling** (`VFXPoolManager.cs`, `DamagePopup.cs`) untuk mencegah GC spike saat spawning dan me-release partikel dan angka damage.

## Acceptance Criteria

### A. Object Pooling System
- [ ] `VFXPoolManager.cs` (MonoBehaviour, Singleton):
  - Menggunakan `UnityEngine.Pool.ObjectPool<T>` untuk setiap tipe VFX.
  - Method `GameObject GetVFX(string vfxId)` — ambil dari pool berdasarkan ID.
  - Method `void ReturnVFX(string vfxId, GameObject obj)` — kembalikan ke pool.
  - Auto-initialize pool dengan capacity 5 per VFX type saat Awake.
- [ ] `DamagePopup.cs` (MonoBehaviour) dengan Object Pool:
  - Menampilkan angka damage melayang ke atas lalu fade out (durasi: 0.8 detik). Warna teks merah untuk Direct Damage / Bleed, oranye/kuning untuk serangan reguler, dan merah menyala untuk Critical/Synergy $2\times$ damage.
  - Menggunakan TextMeshPro atau UI Toolkit label.
  - Static `DamagePopupPool.Get()` dan `DamagePopupPool.Release(popup)`.
  - Subscribe `CombatEvents.OnUnitDamaged` → spawn popup di posisi unit terdampak.

### B. Skill VFX Prefabs Sinergi 1 (Bleed & Assassination)
Dibuat di `Assets/Prefabs/VFX/Skills/` — masing-masing berisi: `SpriteRenderer`/`ParticleSystem`, script `AutoReturnToPool.cs` (return ke pool setelah duration selesai):

1. `VFX_ThrowingBlade.prefab` — Proyektil belati meluncur cepat ke arah target + percikan darah merah tajam saat impact (`Card_ThrowingBlade`).
2. `VFX_ShadowStep_Blink.prefab` — Efek kabut bayangan ungu-hitam (*Shadow Warp*) di posisi caster dan muncul di petak belakang target (`Card_ShadowStep`).
3. `VFX_SerratedDagger_Slash.prefab` — Efek tebasan ganda melengkung merah menyala dengan ledakan darah ganda (*Blood Burst*) saat mengenai musuh berstatus Bleed (`Card_SerratedDagger`).
4. `VFX_Bleed_Tick.prefab` — Partikel tetesan darah berulang di atas unit yang mengalami pengurangan HP akibat DoT `Bleed` di `RoundResetPhase`.

- [ ] `SkillVFXPresenter.cs` (MonoBehaviour):
  - Subscribe `CombatEvents.OnSkillExecuted`.
  - Method `void HandleSkillExecuted(int casterId, string cardId, Vector2Int target)` — spawn VFX yang sesuai dari pool di posisi world target/caster.
  - Mapping `cardId` → VFX prefab via `[SerializeField] SkillVFXMapping[] _mappings`.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/VFXPoolManager.cs`
- `Assets/Scripts/Units/DamagePopup.cs`
- `Assets/Scripts/Units/SkillVFXPresenter.cs`
- `Assets/Scripts/Units/AutoReturnToPool.cs`
- `Assets/Prefabs/VFX/Skills/VFX_ThrowingBlade.prefab`
- `Assets/Prefabs/VFX/Skills/VFX_ShadowStep_Blink.prefab`
- `Assets/Prefabs/VFX/Skills/VFX_SerratedDagger_Slash.prefab`
- `Assets/Prefabs/VFX/Skills/VFX_Bleed_Tick.prefab`

## Dependensi
- **Bergantung pada:** TICKET-01 (`CombatEvents.OnSkillExecuted`, `OnUnitDamaged`), TICKET-02B (ScriptableObject kartu Sinergi 1).
- **Digunakan oleh:** TICKET-05C (UnitAnimatorPresenter memanggil SkillVFXPresenter), TICKET-07 (scene assembly final).

## Catatan Teknis
- VFX placeholder (Unity built-in particle atau Sprite flash) cukup untuk Fase 1.
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
