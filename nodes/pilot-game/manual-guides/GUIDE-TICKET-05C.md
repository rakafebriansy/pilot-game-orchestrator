# 📖 Manual Guide: TICKET-05C — Animator Controller, Screen Shake & Hit Stop System

> **Referensi Tiket:** [TICKET-05C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-05C.md)  
> **Domain:** `[🤺 DOMAIN 2: CHARACTER & ANIMATION]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini meningkatkan kualitas *game feel* dan dampak benturan fisik (*Hit Impact*):
1. **Traumatic Screen Shake (`CameraShaker.cs`)**: Guncangan kamera non-linier menggunakan formula *Trauma ($Trauma^2$)* untuk guncangan yang terasa bertenaga namun tidak memicu pusing.
2. **Hit Stop Manager (`HitStopManager.cs`)**: Menjeda waktu mikro (30-80 milidetik `Time.timeScale = 0f`) sesaat saat pukulan telak mengenai musuh untuk memberikan sensasi benturan tajam (*impact freeze*).
3. **Animator Controller Nabu & Conscript**: State Machine animasi (Idle, Attack, Hit, Walk, Die).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Polish/
│       ├── CameraShaker.cs
│       └── HitStopManager.cs
└── Animations/
    ├── Controllers/
    │   ├── Nabu_AnimatorController.controller
    │   └── Conscript_AnimatorController.controller
    └── Clips/
        ├── Nabu_Idle.anim
        ├── Nabu_Attack.anim
        ├── Nabu_Hit.anim
        └── Nabu_Die.anim
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Polish/CameraShaker.cs`
```csharp
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Polish
{
    /// <summary>
    /// Menghasilkan guncangan kamera kuadratik berbasis trauma (Decay Trauma Shake).
    /// </summary>
    public class CameraShaker : MonoBehaviour
    {
        public static CameraShaker Instance { get; private set; }

        [Header("Trauma Parameters")]
        [SerializeField] private float _traumaDecay = 1.5f;
        [SerializeField] private float _maxAngle = 5f;
        [SerializeField] private float _maxOffset = 0.4f;

        private float _trauma = 0f;
        private Vector3 _originalPos;

        private void Awake()
        {
            Instance = this;
            _originalPos = transform.localPosition;
        }

        private void OnEnable()
        {
            CombatEvents.OnUnitDamaged += HandleDamageShake;
        }

        private void OnDisable()
        {
            CombatEvents.OnUnitDamaged -= HandleDamageShake;
        }

        private void HandleDamageShake(DamagePayload payload)
        {
            // Tambahkan trauma sebanding dengan besaran damage yang diterima (Clamped 0 s/d 1)
            float addedTrauma = Mathf.Clamp01(payload.DamageAmount / 20f + 0.2f);
            AddTrauma(addedTrauma);
        }

        public void AddTrauma(float amount)
        {
            _trauma = Mathf.Clamp01(_trauma + amount);
        }

        private void Update()
        {
            if (_trauma > 0f)
            {
                // 1. Formula Trauma Kuadratik (shake = trauma^2):
                // Menghasilkan intensitas guncangan non-linear yang terasa organik dan tidak membuat pusing
                float shake = _trauma * _trauma;

                // 2. Sampling Perlin Noise pada 3 seed berbeda (0, 1, 2) dengan mapping range [-1, +1] via (* 2f - 1f)
                float offsetX = _maxOffset * shake * (Mathf.PerlinNoise(0, Time.time * 25f) * 2f - 1f);
                float offsetY = _maxOffset * shake * (Mathf.PerlinNoise(1, Time.time * 25f) * 2f - 1f);
                float angle = _maxAngle * shake * (Mathf.PerlinNoise(2, Time.time * 25f) * 2f - 1f);

                // 3. Terapkan displacement translasi dan rotasi z-axis ke kamera
                transform.localPosition = _originalPos + new Vector3(offsetX, offsetY, 0);
                transform.localRotation = Quaternion.Euler(0, 0, angle);

                // 4. Redam nilai trauma secara bertahap seiring waktu (Linear Decay)
                _trauma = Mathf.Max(0f, _trauma - _traumaDecay * Time.deltaTime);
            }
            else
            {
                // Reset posisi dan rotasi kamera ke posisi asal ketika trauma = 0
                transform.localPosition = _originalPos;
                transform.localRotation = Quaternion.identity;
            }
        }
    }
}
```

---

### B. `Assets/Scripts/Polish/HitStopManager.cs`
```csharp
using System.Collections;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Polish
{
    /// <summary>
    /// Memberikan efek freeze frame mikro (Hit Stop) saat benturan kuat terjadi.
    /// Menghentikan simulasi waktu global sementara untuk memberikan sensasi hantaman fisik yang mantap.
    /// </summary>
    public class HitStopManager : MonoBehaviour
    {
        public static HitStopManager Instance { get; private set; }

        private bool _isFreezing = false;

        private void Awake()
        {
            Instance = this;
        }

        private void OnEnable()
        {
            CombatEvents.OnUnitDamaged += HandleDamageHitStop;
        }

        private void OnDisable()
        {
            CombatEvents.OnUnitDamaged -= HandleDamageHitStop;
        }

        private void HandleDamageHitStop(DamagePayload payload)
        {
            // Memicu hit stop hanya jika damage signifikan (>= 6)
            if (payload.DamageAmount >= 6)
            {
                TriggerHitStop(0.06f); // Freeze 60 milidetik untuk pukulan berat
            }
        }

        public void TriggerHitStop(float durationSeconds)
        {
            if (!_isFreezing)
            {
                StartCoroutine(HitStopRoutine(durationSeconds));
            }
        }

        /// <summary>
        /// Coroutine hit stop: Menyetel Time.timeScale = 0f dan menunggu durasi realtime.
        /// </summary>
        private IEnumerator HitStopRoutine(float duration)
        {
            _isFreezing = true;

            // Jeda seluruh waktu game (animasi, physics, update delta time)
            Time.timeScale = 0f;

            // Tunggu berdasarkan waktu nyata (Realtime) karena Time.timeScale sedang 0
            yield return new WaitForSecondsRealtime(duration);

            // Kembalikan aliran waktu normal
            Time.timeScale = 1f;
            _isFreezing = false;
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Pasang `CameraShaker` pada Main Camera.
2. Panggil event `CombatEvents.OnUnitDamaged?.Invoke(new DamagePayload(1, 15, 0))` di Play Mode.
3. Rasakan guncangan kamera yang tegas disertai jeda mikro 60ms yang memberikan sensasi kepuasan pukulan (*crunchy hit feel*).
