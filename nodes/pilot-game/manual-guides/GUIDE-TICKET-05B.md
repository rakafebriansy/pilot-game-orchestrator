# 📖 Manual Guide: TICKET-05B — Skill VFX Prefabs (Sinergi 1: Bleed & Assassination) & Object Pooling

> **Referensi Tiket:** [TICKET-05B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05B.md)  
> **Domain:** `[🤺 DOMAIN 2: CHARACTER & ANIMATION]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan sistem efisiensi memori **Object Pooling System** (`VFXPoolManager.cs`) untuk menangani instansiasi partikel skill dan angka kerusakan melayang (*Damage Popups*), serta memproduksi **4 Prefab Skill VFX khusus Sinergi 1 (Bleed & Assassination Archetype)**:
1. `VFX_ThrowingBlade.prefab`: Proyektil belati terbang lurus + percikan darah merah.
2. `VFX_ShadowStep_Blink.prefab`: Efek kabut bayangan / teleportasi shadow ke belakang target.
3. `VFX_SerratedDagger_Slash.prefab`: Tebasan ganda merah menyala dengan ledakan darah ganda jika target terkena status Bleed.
4. `VFX_Bleed_Tick.prefab`: Tetesan darah DoT saat resolusi ronde di `RoundResetPhase`.

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
        ├── VFX_ThrowingBlade.prefab
        ├── VFX_ShadowStep_Blink.prefab
        ├── VFX_SerratedDagger_Slash.prefab
        └── VFX_Bleed_Tick.prefab
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

            // Buat queue pool baru jika prefab ini belum pernah di-spawn sebelumnya
            if (!_pools.ContainsKey(key))
            {
                _pools[key] = new Queue<GameObject>();
            }

            GameObject obj;

            // 1. Ambil dari Pool (Reuse) jika ada objek non-aktif yang tersedia
            if (_pools[key].Count > 0)
            {
                obj = _pools[key].Dequeue();
                obj.transform.position = position;
                obj.transform.rotation = rotation;
                obj.SetActive(true);
            }
            else
            {
                // 2. Jika pool kosong, instantiate objek baru sebagai child dari VFXPoolManager
                obj = Instantiate(prefab, position, rotation, transform);
            }

            return obj;
        }

        /// <summary>
        /// Mengembalikan instance objek ke dalam pool (dengan opsi delay).
        /// </summary>
        public void Despawn(GameObject prefab, GameObject instance, float delay = 0f)
        {
            if (instance == null || prefab == null) return;
            StartCoroutine(DespawnRoutine(prefab.name, instance, delay));
        }

        private System.Collections.IEnumerator DespawnRoutine(string key, GameObject instance, float delay)
        {
            if (delay > 0f) yield return new WaitForSeconds(delay);

            // Matikan visual GameObject dan masukkan kembali ke antrian (Queue)
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
    /// Menampilkan angka damage / status DoT melayang ke atas saat unit terkena serangan.
    /// Menggunakan sistem Object Pooling untuk meminimalkan alokasi Garbage Collection (GC Alloc).
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
            if (_damagePopupPrefab == null || VFXPoolManager.Instance == null) return;

            // Spawn popup sedikit di atas kepala unit (+1.2f pada sumbu Y)
            Vector3 spawnPos = transform.position + new Vector3(0, 1.2f, 0);
            GameObject popup = VFXPoolManager.Instance.Spawn(_damagePopupPrefab, spawnPos, Quaternion.identity);

            var tmp = popup.GetComponentInChildren<TextMeshPro>();
            if (tmp != null)
            {
                // Tampilkan angka damage merah jika tembus, atau teks cyan BLOCKED jika sepenuhnya diserap shield
                tmp.text = payload.DamageAmount > 0 ? $"-{payload.DamageAmount}" : "BLOCKED";
                tmp.color = payload.DamageAmount >= 10 ? new Color(1f, 0.2f, 0.2f) : (payload.DamageAmount > 0 ? Color.red : Color.cyan);
            }

            StartCoroutine(AnimateAndDespawn(popup));
        }

        /// <summary>
        /// Menggerakkan teks melayang vertikal ke atas sebelum mengembalikannya ke pool.
        /// </summary>
        private IEnumerator AnimateAndDespawn(GameObject popup)
        {
            float elapsed = 0f;
            Vector3 startPos = popup.transform.position;

            while (elapsed < _fadeDuration)
            {
                // Hitung posisi lerp melayang ke atas berdasarkan rasio waktu
                popup.transform.position = startPos + new Vector3(0, _floatSpeed * (elapsed / _fadeDuration), 0);
                elapsed += Time.deltaTime;
                yield return null;
            }

            // Kembalikan ke pool setelah durasi animasi selesai
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
    /// Memutar VFX skill Sinergi 1 (Throwing Blade, Shadow Step, Serrated Dagger, Bleed Tick) di koordinat target.
    /// </summary>
    public class SkillVFXPresenter : MonoBehaviour
    {
        [Header("Sinergi 1 VFX Prefabs")]
        [SerializeField] private GameObject _throwingBladeVFXPrefab;
        [SerializeField] private GameObject _shadowStepVFXPrefab;
        [SerializeField] private GameObject _serratedDaggerVFXPrefab;
        [SerializeField] private GameObject _bleedTickVFXPrefab;

        private void OnEnable()
        {
            CombatEvents.OnSkillExecuted += HandleSkillVFX;
            CombatEvents.OnStatusEffectApplied += HandleStatusVFX;
        }

        private void OnDisable()
        {
            CombatEvents.OnSkillExecuted -= HandleSkillVFX;
            CombatEvents.OnStatusEffectApplied -= HandleStatusVFX;
        }

        private void HandleSkillVFX(int casterId, string cardId, Vector2Int targetCoord)
        {
            Vector3 worldPos = new Vector3(targetCoord.x + 0.5f, targetCoord.y + 0.5f, 0);
            GameObject vfxPrefab = GetVFXByCardId(cardId);

            if (vfxPrefab != null && VFXPoolManager.Instance != null)
            {
                GameObject vfxInstance = VFXPoolManager.Instance.Spawn(vfxPrefab, worldPos, Quaternion.identity);
                VFXPoolManager.Instance.Despawn(vfxPrefab, vfxInstance, 1.2f);
            }
        }

        private void HandleStatusVFX(int unitId, StatusEffectType status, int duration)
        {
            if (status == StatusEffectType.Bleed && _bleedTickVFXPrefab != null && VFXPoolManager.Instance != null)
            {
                Vector3 worldPos = transform.position + new Vector3(0, 0.8f, 0);
                GameObject vfx = VFXPoolManager.Instance.Spawn(_bleedTickVFXPrefab, worldPos, Quaternion.identity);
                VFXPoolManager.Instance.Despawn(_bleedTickVFXPrefab, vfx, 1.0f);
            }
        }

        private GameObject GetVFXByCardId(string cardId)
        {
            return cardId switch
            {
                "CARD-009" or "card_throwingblade" => _throwingBladeVFXPrefab,
                "CARD-034" or "card_shadowstep" => _shadowStepVFXPrefab,
                "CARD-031" or "card_serrateddagger" => _serratedDaggerVFXPrefab,
                _ => _throwingBladeVFXPrefab
            };
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Pasang `VFXPoolManager` dan `SkillVFXPresenter` pada Scene pertempuran.
2. Panggil event `CombatEvents.OnSkillExecuted?.Invoke(1, "CARD-009", new Vector2Int(3, 2))` di Play Mode.
3. Periksa partikel proyektil `VFX_ThrowingBlade` muncul di koordinat (3, 2) lalu kembali ke pool.
4. Panggil event `CombatEvents.OnSkillExecuted?.Invoke(1, "CARD-031", new Vector2Int(3, 2))` untuk memeriksa tebasan ganda `VFX_SerratedDagger`.
