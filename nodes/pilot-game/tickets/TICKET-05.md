---
id: TICKET-05
title: Entitas Karakter, Pergerakan Grid Lerp & Animasi
status: Todo
priority: High
labels: [View, Units, Animation, Movement, Domain2]
---

# Deskripsi
Membangun **prefab karakter protagonis Nabu** dan musuh dasar, mengimplementasikan controller pergerakan grid halus (`UnitMovementView.cs`) berbasis interpolasi posisi (*MoveTowards/Lerp*), dan mengintegrasikan respons animasi (`UnitAnimatorPresenter.cs`) terhadap event serangan, damage, dan kematian.

Tiket ini adalah implementasi Domain 2 (Character & Animation) dalam arsitektur decoupled — view merespons event, tidak memanggil logika pertempuran langsung.

## Acceptance Criteria
- [ ] `UnitMovementView.cs` (MonoBehaviour):
  - `[SerializeField] int _unitId` — ID unik unit (harus unik per GameObject di scene).
  - `[SerializeField] float _moveSpeed = 8f`.
  - Subscribe `CombatEvents.OnUnitMoved` di `OnEnable()` dan unsubscribe di `OnDisable()`.
  - Method `HandleUnitMoved(UnitMovePayload payload)`:
    - Guard: jika `payload.UnitId != _unitId` → return (ignore event bukan miliknya).
    - Hentikan coroutine aktif jika ada (`StopAllCoroutines()`).
    - Konversi `payload.ToCoord` ke world position: `new Vector3(coord.x + 0.5f, coord.y + 0.5f, 0)`.
    - Mulai coroutine `MoveRoutine(targetWorldPos)`.
  - Coroutine `MoveRoutine(Vector3 targetPos)`:
    - Loop `Vector3.MoveTowards` hingga jarak < 0.01f.
    - Snap posisi tepat di akhir (`transform.position = targetPos`).
- [ ] `UnitAnimatorPresenter.cs` (MonoBehaviour):
  - Subscribe `CombatEvents.OnSkillExecuted` dan `CombatEvents.OnUnitDamaged`.
  - Saat event `OnSkillExecuted` → trigger parameter `"Attack"` di Animator.
  - Saat event `OnUnitDamaged` dengan `targetUnitId == _unitId` → trigger parameter `"Hit"`.
  - Saat HP unit menjadi 0 → trigger parameter `"Die"`.
  - Unsubscribe semua event di `OnDisable()`.
- [ ] **HealthBar** sederhana di atas unit (menggunakan Canvas atau custom Sprite Renderer):
  - Menampilkan persentase HP saat ini / MaxHP.
  - Scale HealthBar fill berubah berdasarkan event `OnUnitDamaged`.
- [ ] **Prefab Nabu_Player_Prefab.prefab** memiliki komponen: `SpriteRenderer`, `Animator`, `UnitMovementView`, `UnitAnimatorPresenter`.
- [ ] **Prefab Enemy_Conscript_Prefab.prefab** memiliki komponen yang sama dengan data `EnemyData` sebagai referensi.
- [ ] Animasi placeholder (`SpriteRenderer` color flash atau simple animation clip) cukup untuk MVP — tidak perlu sprite art final.
- [ ] **Verifikasi visual:** Di Play Mode, broadcast `CombatEvents.OnUnitMoved?.Invoke(new UnitMovePayload(1, new Vector2Int(0,0), new Vector2Int(3,3)))` menggerakkan unit dengan ID 1 secara smooth ke posisi (3,3).

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Units/UnitMovementView.cs`
- `Assets/Scripts/Units/UnitAnimatorPresenter.cs`
- `Assets/Prefabs/Units/Nabu_Player_Prefab.prefab`
- `Assets/Prefabs/Units/Enemy_Conscript_Prefab.prefab`

## Dependensi
- **Bergantung pada:** TICKET-01 (`CombatEvents`, `UnitMovePayload`), TICKET-02 (`EnemyData` untuk data musuh).
- **Digunakan oleh:** TICKET-07 (prefab di-assembly ke `MainBattleScene.unity`).

## Catatan Teknis
- Jangan menggunakan `transform.position = targetPos` secara langsung tanpa coroutine — ini akan membuat unit teleport, bukan bergerak smooth.
- Gunakan `StopAllCoroutines()` sebelum memulai coroutine baru untuk mencegah double movement.
- Posisi world dari koordinat grid: `worldPos = new Vector3(gridCoord.x + 0.5f, gridCoord.y + 0.5f, 0)` (offset 0.5 agar unit berada di tengah tile).
- `_unitId` adalah key untuk filter event — dua unit dengan ID sama di scene akan menyebabkan bug movement.

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
