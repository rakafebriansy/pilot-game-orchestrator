# 📖 Manual Guide: TICKET-05 — Entitas Karakter, Pergerakan Grid Lerp & HealthBar

> **Referensi Tiket:** [TICKET-05.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05.md)  
> **Domain:** `[🤺 DOMAIN 2: CHARACTER & ANIMATION]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan entitas visual karakter protagonis Nabu dan musuh dasar di Unity:
1. **`UnitMovementView.cs`**: Menggerakkan transform karakter antar-ubin grid secara mulus (*MoveTowards/Lerp*) saat menerima event `CombatEvents.OnUnitMoved`.
2. **`UnitAnimatorPresenter.cs`**: Merespons event animasi (Attack, Hit, Die) tanpa dependensi langsung ke logika matematika (*Decoupled View*).
3. **`FloatingHealthBarView.cs`**: Menampilkan bar HP dan Shield dinamis di atas kepala unit menggunakan *World Space Canvas*.
4. **Pembuatan Prefab Unit**: `Nabu_Player_Prefab.prefab` dan `Enemy_Conscript_Prefab.prefab`.

---

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Pembuatan GameObject Karakter Nabu
1. Di panel **Hierarchy**, klik kanan > **Create Empty**, beri nama `Nabu_Player`.
2. Atur **Transform Position:** `X: 2.5, Y: 2.5, Z: 0` *(Titik tengah ubin [2,2])*.
3. Di panel **Inspector**, tambahkan komponen berikut via tombol **Add Component**:
   * **Sprite Renderer**:
     * **Sprite:** Pasang sprite Nabu *(Resolusi 64x64 px, PPU: 64, Filter: Point)*.
     * **Sorting Layer:** Pilih `Units` *(PENTING: agar karakter berdiri di atas ubin lantai!)*.
     * **Order in Layer:** `0`.
   * **Animator**:
     * **Controller:** Pasang `Nabu_AnimatorController.controller`.
   * **UnitMovementView**:
     * **Unit Id:** `1` *(ID 1 selalu dialokasikan untuk Nabu/Player)*.
     * **Move Speed:** `8`.
   * **UnitAnimatorPresenter**:
     * **Unit Id:** `1`.

---

### Langkah 2.2: Setup World Space Canvas HealthBar di Atas Kepala
1. Klik kanan pada GameObject `Nabu_Player` di Hierarchy > pilih **UI > Canvas**.
2. Ganti nama Canvas child tersebut menjadi `HealthBar_Canvas`.
3. Di panel Inspector komponen **Canvas**:
   * **Render Mode:** Pilih `World Space`.
   * **Event Camera:** Seret `Main Camera` ke slot ini.
4. Di komponen **Rect Transform**:
   * **Pos X:** `0`, **Pos Y:** `1.1`, **Pos Z:** `0` *(Tepat melayang di atas sprite)*.
   * **Width:** `100`, **Height:** `14`.
   * **Scale:** `X: 0.01, Y: 0.01, Z: 0.01` *(Wajib diskalakan 0.01 agar sesuai ukuran piksel grid)*.
5. Di bawah `HealthBar_Canvas`, buat 3 elemen UI Image via klik kanan > **UI > Image**:
   * **Image 1 (`Background`):**
     * Color: Hitam semi-transparan (`#00000088`), Width: 100, Height: 12.
   * **Image 2 (`HealthFill`):**
     * Color: Merah / Hijau (`#22C55E`), Image Type: `Filled`, Fill Method: `Horizontal`, Fill Origin: `Left`.
   * **Image 3 (`ShieldFill`):**
     * Color: Biru Muda Cyan (`#06B6D4`), Image Type: `Filled`, Fill Method: `Horizontal`.
6. Klik GameObject `HealthBar_Canvas`, lalu klik **Add Component** > ketik `FloatingHealthBarView` > tekan Enter.
7. Hubungkan slot Inspector:
   * **Unit Id:** `1`.
   * **Max Health:** `20`.
   * **Health Fill Image:** Seret `HealthFill` ke slot ini.
   * **Shield Fill Image:** Seret `ShieldFill` ke slot ini.

```text
[Hierarchy]
└── Nabu_Player                  [SpriteRenderer (Units), Animator, UnitMovementView, UnitAnimatorPresenter]
    └── HealthBar_Canvas         [Canvas (World Space), FloatingHealthBarView]
        ├── Background           [Image]
        ├── HealthFill           [Image (Filled: Horizontal)]
        └── ShieldFill           [Image (Filled: Horizontal)]
```

---

### Langkah 2.3: Pembuatan Prefab Unit
1. Buat folder `Assets/Prefabs/Units/` di Project Window.
2. Seret GameObject `Nabu_Player` dari Hierarchy ke folder `Assets/Prefabs/Units/` untuk membuat `Nabu_Player_Prefab.prefab`.
3. Di Hierarchy, duplikasi `Nabu_Player` (`Ctrl+D`), ubah namanya menjadi `Enemy_Conscript`.
4. Di Inspector `Enemy_Conscript`:
   * Ubah sprite menjadi sprite prajurit musuh.
   * Di `UnitMovementView`, set **Unit Id** = `2`.
   * Di `UnitAnimatorPresenter`, set **Unit Id** = `2`.
   * Di `FloatingHealthBarView`, set **Unit Id** = `2` dan **Max Health** = `18`.
5. Seret `Enemy_Conscript` ke folder `Assets/Prefabs/Units/` untuk membuat `Enemy_Conscript_Prefab.prefab`.

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Units/UnitMovementView.cs`
```csharp
using System.Collections;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Units
{
    /// <summary>
    /// Mengelola animasi pergerakan visual halus unit di atas grid koordinat.
    /// </summary>
    public class UnitMovementView : MonoBehaviour
    {
        [Header("Unit Identity")]
        [SerializeField] private int _unitId = 1;
        [SerializeField] private float _moveSpeed = 8.0f;

        private Coroutine _moveCoroutine;

        public int UnitId => _unitId;

        private void OnEnable()
        {
            CombatEvents.OnUnitMoved += HandleUnitMoved;
        }

        private void OnDisable()
        {
            CombatEvents.OnUnitMoved -= HandleUnitMoved;
        }

        private void HandleUnitMoved(UnitMovePayload payload)
        {
            if (payload.UnitId != _unitId) return;

            if (_moveCoroutine != null)
            {
                StopCoroutine(_moveCoroutine);
            }

            Vector3 targetWorldPos = new Vector3(payload.ToCoord.x + 0.5f, payload.ToCoord.y + 0.5f, 0f);
            _moveCoroutine = StartCoroutine(MoveRoutine(targetWorldPos));
        }

        private IEnumerator MoveRoutine(Vector3 targetPos)
        {
            while (Vector3.Distance(transform.position, targetPos) > 0.01f)
            {
                transform.position = Vector3.MoveTowards(transform.position, targetPos, _moveSpeed * Time.deltaTime);
                yield return null;
            }
            transform.position = targetPos;
            _moveCoroutine = null;
        }
    }
}
```

---

### B. `Assets/Scripts/Units/UnitAnimatorPresenter.cs`
```csharp
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Units
{
    /// <summary>
    /// Mengatur trigger parameter Animator sesuai sinyal event pertempuran.
    /// </summary>
    [RequireComponent(typeof(Animator))]
    public class UnitAnimatorPresenter : MonoBehaviour
    {
        [SerializeField] private int _unitId = 1;
        private Animator _animator;

        private static readonly int AttackHash = Animator.StringToHash("Attack");
        private static readonly int HitHash = Animator.StringToHash("Hit");
        private static readonly int DieHash = Animator.StringToHash("Die");

        private void Awake()
        {
            _animator = GetComponent<Animator>();
        }

        private void OnEnable()
        {
            CombatEvents.OnSkillExecuted += HandleSkillExecuted;
            CombatEvents.OnUnitDamaged += HandleUnitDamaged;
        }

        private void OnDisable()
        {
            CombatEvents.OnSkillExecuted -= HandleSkillExecuted;
            CombatEvents.OnUnitDamaged -= HandleUnitDamaged;
        }

        private void HandleSkillExecuted(int casterId, int skillId, Vector2Int target)
        {
            if (casterId == _unitId && _animator != null)
            {
                _animator.SetTrigger(AttackHash);
            }
        }

        private void HandleUnitDamaged(DamagePayload payload)
        {
            if (payload.TargetUnitId == _unitId && _animator != null)
            {
                _animator.SetTrigger(HitHash);
            }
        }
    }
}
```

---

### C. `Assets/Scripts/Units/FloatingHealthBarView.cs`
```csharp
using UnityEngine;
using UnityEngine.UI;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Units
{
    /// <summary>
    /// Bar HP di atas kepala unit yang merespons perubahan damage.
    /// </summary>
    public class FloatingHealthBarView : MonoBehaviour
    {
        [SerializeField] private int _unitId = 1;
        [SerializeField] private int _maxHealth = 20;
        [SerializeField] private Image _healthFillImage;
        [SerializeField] private Image _shieldFillImage;

        private int _currentHealth;

        private void Awake()
        {
            _currentHealth = _maxHealth;
            UpdateDisplay(0);
        }

        private void OnEnable()
        {
            CombatEvents.OnUnitDamaged += HandleDamage;
        }

        private void OnDisable()
        {
            CombatEvents.OnUnitDamaged -= HandleDamage;
        }

        private void HandleDamage(DamagePayload payload)
        {
            if (payload.TargetUnitId != _unitId) return;

            _currentHealth = Mathf.Max(0, _currentHealth - payload.DamageAmount);
            UpdateDisplay(payload.ShieldRemaining);

            if (_currentHealth <= 0)
            {
                gameObject.SetActive(false);
            }
        }

        private void UpdateDisplay(int shield)
        {
            if (_healthFillImage != null)
            {
                _healthFillImage.fillAmount = (float)_currentHealth / _maxHealth;
            }
            if (_shieldFillImage != null)
            {
                _shieldFillImage.gameObject.SetActive(shield > 0);
            }
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Pasang prefab `Nabu_Player_Prefab` di posisi `(2.5, 2.5, 0)`.
2. Klik tombol **Play** di Unity Editor.
3. Buka Console atau jalankan test broadcast:
   ```csharp
   // Uji Gerak:
   CombatEvents.OnUnitMoved?.Invoke(new UnitMovePayload(1, new Vector2Int(2, 2), new Vector2Int(5, 2)));
   // Uji Damage:
   CombatEvents.OnUnitDamaged?.Invoke(new DamagePayload(1, 6, 0));
   ```
4. Di **Game View**, amati Nabu berjalan halus ke petak (5,2) dan bar HP berkurang dari 100% menjadi 70%.
