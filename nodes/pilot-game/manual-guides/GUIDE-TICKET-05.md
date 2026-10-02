# 📖 Manual Guide: TICKET-05 — Entitas Karakter, Pergerakan Grid Lerp & HealthBar

> **Referensi Tiket:** [TICKET-05.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05.md)  
> **Domain:** `[🤺 DOMAIN 2: CHARACTER & ANIMATION]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan lapisan presentasi unit karakter di arena:
1. **`UnitMovementView.cs`**: Menggerakkan karakter secara halus antar-koordinat grid menggunakan interpolasi posisi (*MoveTowards/Lerp*) saat menerima event `CombatEvents.OnUnitMoved`.
2. **`UnitAnimatorPresenter.cs`**: Merespons event serangan, hit impact, dan animasi kematian tanpa memanggil logika pertempuran langsung (*Decoupled View*).
3. **`FloatingHealthBarView.cs`**: Menampilkan bar HP dinamis di atas kepala karakter.
4. **Pembuatan Prefab Unit**: `Nabu_Player_Prefab.prefab` dan `Enemy_Conscript_Prefab.prefab`.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Units/
│       ├── UnitMovementView.cs
│       ├── UnitAnimatorPresenter.cs
│       └── FloatingHealthBarView.cs
└── Prefabs/
    └── Units/
        ├── Nabu_Player_Prefab.prefab
        └── Enemy_Conscript_Prefab.prefab
```

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

## 🛠️ 4. Langkah Pembuatan Prefab di Unity Editor
1. Buat GameObject `Nabu_Player` di Scene.
2. Tambahkan komponen: `SpriteRenderer`, `Animator`, `UnitMovementView`, `UnitAnimatorPresenter`.
3. Buat Canvas child (World Space) di atas kepala dan pasang script `FloatingHealthBarView`.
4. Tarik ke `Assets/Prefabs/Units/Nabu_Player_Prefab.prefab`.
5. Duplikasi untuk `Enemy_Conscript_Prefab.prefab` dengan `_unitId = 2`.

---

## 🧪 5. Langkah Verifikasi
1. Di Play Mode, jalankan baris pengujian event:
   ```csharp
   CombatEvents.OnUnitMoved?.Invoke(new UnitMovePayload(1, new Vector2Int(0, 0), new Vector2Int(4, 4)));
   ```
2. Amati karakter Nabu meluncur mulus (bukan teleport patah-patah) dari (0,0) ke (4,4).
