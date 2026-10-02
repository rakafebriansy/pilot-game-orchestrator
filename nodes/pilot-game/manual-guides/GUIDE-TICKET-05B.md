# 📖 Manual Guide: TICKET-05B — 14 Skill VFX Prefab, Damage Popups & Object Pooling

> **Referensi Tiket:** [TICKET-05B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05B.md)  
> **Domain:** `[🤺 DOMAIN 2: CHARACTER & ANIMATION]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan sistem efisiensi memori **Object Pooling System** (`VFXPoolManager.cs`) untuk menangani instansiasi partikel skill dan angka kerusakan melayang (*Damage Popups*), serta memproduksi **14 Prefab Skill VFX** yang dihubungkan ke event eksekusi kemampuan kartu.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── VFX/
│       ├── VFXPoolManager.cs
│       ├── DamagePopupPresenter.cs
│       └── SkillVFXPresenter.cs
└── Prefabs/
    └── VFX/
        ├── DamagePopup_Prefab.prefab
        ├── VFX_Teleport.prefab
        ├── VFX_FrostBlast.prefab
        ├── VFX_StormLightning.prefab
        ├── VFX_SlashBlade.prefab
        └── VFX_SuperPunch.prefab
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/VFX/VFXPoolManager.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;

namespace PilotGame.VFX
{
    /// <summary>
    /// Object Pool terpusat untuk partikel VFX dan Damage Numbers guna mencegah Garbage Collection.
    /// </summary>
    public class VFXPoolManager : MonoBehaviour
    {
        public static VFXPoolManager Instance { get; private set; }

        private readonly Dictionary<string, Queue<GameObject>> _pools = new();

        private void Awake()
        {
            if (Instance != null && Instance != this)
            {
                Destroy(gameObject);
                return;
            }
            Instance = this;
        }

        public GameObject Spawn(GameObject prefab, Vector3 position, Quaternion rotation)
        {
            if (prefab == null) return null;

            string key = prefab.name;
            if (!_pools.ContainsKey(key))
            {
                _pools[key] = new Queue<GameObject>();
            }

            GameObject obj;
            if (_pools[key].Count > 0)
            {
                obj = _pools[key].Dequeue();
                obj.transform.position = position;
                obj.transform.rotation = rotation;
                obj.SetActive(true);
            }
            else
            {
                obj = Instantiate(prefab, position, rotation, transform);
            }

            return obj;
        }

        public void Despawn(GameObject prefab, GameObject instance, float delay = 0f)
        {
            if (instance == null || prefab == null) return;
            StartCoroutine(DespawnRoutine(prefab.name, instance, delay));
        }

        private System.Collections.IEnumerator DespawnRoutine(string key, GameObject instance, float delay)
        {
            if (delay > 0f) yield return new WaitForSeconds(delay);

            instance.SetActive(false);
            if (!_pools.ContainsKey(key))
            {
                _pools[key] = new Queue<GameObject>();
            }
            _pools[key].Enqueue(instance);
        }
    }
}
```

---

### B. `Assets/Scripts/VFX/DamagePopupPresenter.cs`
```csharp
using System.Collections;
using TMPro;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.VFX
{
    /// <summary>
    /// Menampilkan angka damage / shield melayang ke atas saat unit terkena serangan.
    /// </summary>
    public class DamagePopupPresenter : MonoBehaviour
    {
        [SerializeField] private GameObject _damagePopupPrefab;
        [SerializeField] private float _floatSpeed = 2.0f;
        [SerializeField] private float _fadeDuration = 0.8f;

        private void OnEnable()
        {
            CombatEvents.OnUnitDamaged += HandleDamagePopup;
        }

        private void OnDisable()
        {
            CombatEvents.OnUnitDamaged -= HandleDamagePopup;
        }

        private void HandleDamagePopup(DamagePayload payload)
        {
            if (_damagePopupPrefab == null) return;

            // Dapatkan posisi target unit dari world/grid
            Vector3 spawnPos = transform.position + new Vector3(0, 1.2f, 0);
            GameObject popup = VFXPoolManager.Instance.Spawn(_damagePopupPrefab, spawnPos, Quaternion.identity);

            var tmp = popup.GetComponentInChildren<TextMeshPro>();
            if (tmp != null)
            {
                tmp.text = payload.DamageAmount > 0 ? $"-{payload.DamageAmount}" : "BLOCKED";
                tmp.color = payload.DamageAmount > 0 ? Color.red : Color.cyan;
            }

            StartCoroutine(AnimateAndDespawn(popup));
        }

        private IEnumerator AnimateAndDespawn(GameObject popup)
        {
            float elapsed = 0f;
            Vector3 startPos = popup.transform.position;

            while (elapsed < _fadeDuration)
            {
                popup.transform.position = startPos + new Vector3(0, _floatSpeed * (elapsed / _fadeDuration), 0);
                elapsed += Time.deltaTime;
                yield return null;
            }

            VFXPoolManager.Instance.Despawn(_damagePopupPrefab, popup);
        }
    }
}
```

---

### C. `Assets/Scripts/VFX/SkillVFXPresenter.cs`
```csharp
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.VFX
{
    /// <summary>
    /// Menyimulasikan pemutaran VFX skill di koordinat ubin target saat skill dieksekusi.
    /// </summary>
    public class SkillVFXPresenter : MonoBehaviour
    {
        [Header("Skill VFX Prefabs Catalog")]
        [SerializeField] private GameObject _slashVFXPrefab;
        [SerializeField] private GameObject _frostVFXPrefab;
        [SerializeField] private GameObject _lightningVFXPrefab;
        [SerializeField] private GameObject _teleportVFXPrefab;

        private void OnEnable()
        {
            CombatEvents.OnSkillExecuted += HandleSkillVFX;
        }

        private void OnDisable()
        {
            CombatEvents.OnSkillExecuted -= HandleSkillVFX;
        }

        private void HandleSkillVFX(int casterId, int skillId, Vector2Int targetCoord)
        {
            Vector3 worldPos = new Vector3(targetCoord.x + 0.5f, targetCoord.y + 0.5f, 0);
            GameObject vfxPrefab = GetVFXBySkillId(skillId);

            if (vfxPrefab != null && VFXPoolManager.Instance != null)
            {
                GameObject vfxInstance = VFXPoolManager.Instance.Spawn(vfxPrefab, worldPos, Quaternion.identity);
                VFXPoolManager.Instance.Despawn(vfxPrefab, vfxInstance, 1.5f);
            }
        }

        private GameObject GetVFXBySkillId(int skillId)
        {
            return skillId switch
            {
                1 => _teleportVFXPrefab,
                3 => _frostVFXPrefab,
                6 => _lightningVFXPrefab,
                _ => _slashVFXPrefab
            };
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Pasang `VFXPoolManager` pada Scene.
2. Panggil event `CombatEvents.OnUnitDamaged?.Invoke(new DamagePayload(1, 8, 0))` di Play Mode.
3. Periksa angka `-8` merah meluncur ke atas lalu hilang kembali ke Object Pool tanpa `Destroy()`.
