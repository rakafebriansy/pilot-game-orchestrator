# 📖 Manual Guide: TICKET-04B — Camera Post-Processing, URP 2D Lighting & Audio Environment

> **Referensi Tiket:** [TICKET-04B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04B.md)  
> **Domain:** `[🗺️ DOMAIN 1: ARENA & TILEMAP]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengonfigurasi estetika visual atmosfer Menara Babel sesuai arahan seni GDD §5.2 (*Dark tones dengan aksen kuning-oranye*):
1. **URP 2D Lighting**: Global 2D Light bernuansa temaram + Point Light 2D untuk obor dinding dengan script kedip api realistis (`TorchFlicker.cs`).
2. **Volume Post-Processing**: Bloom (aksen rune kuning-oranye), Vignette, dan Color Adjustments.
3. **Audio Environment**: Sistem `AudioManager.cs` untuk pemutaran BGM suasana Menara Babel dan sound effect atmosferik.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
└── Scripts/
    ├── Environment/
    │   └── TorchFlicker.cs
    └── Audio/
        └── AudioManager.cs
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Environment/TorchFlicker.cs`
```csharp
using UnityEngine;
using UnityEngine.Rendering.Universal;

namespace PilotGame.Environment
{
    /// <summary>
    /// Menghasilkan efek kedipan api obor alami menggunakan algoritma Perlin Noise pada Light2D URP.
    /// </summary>
    [RequireComponent(typeof(Light2D))]
    public class TorchFlicker : MonoBehaviour
    {
        [Header("Parameter Kedipan")]
        [SerializeField] private float _minIntensity = 0.8f;
        [SerializeField] private float _maxIntensity = 1.4f;
        [SerializeField] private float _flickerSpeed = 3.5f;

        [Header("Pergeseran Posisi Mikro (Radius)")]
        [SerializeField] private float _minRadius = 3.0f;
        [SerializeField] private float _maxRadius = 3.6f;

        private Light2D _light2D;
        private float _noiseOffset;

        private void Awake()
        {
            _light2D = GetComponent<Light2D>();
            _noiseOffset = Random.Range(0f, 100f);
        }

        private void Update()
        {
            if (_light2D == null) return;

            float noise = Mathf.PerlinNoise(_noiseOffset, Time.time * _flickerSpeed);
            _light2D.intensity = Mathf.Lerp(_minIntensity, _maxIntensity, noise);
            _light2D.pointLightOuterRadius = Mathf.Lerp(_minRadius, _maxRadius, noise);
        }
    }
}
```

---

### B. `Assets/Scripts/Audio/AudioManager.cs`
```csharp
using UnityEngine;

namespace PilotGame.Audio
{
    /// <summary>
    /// Mengelola pemutaran background music (BGM) dan efek suara (SFX) pertempuran.
    /// </summary>
    public class AudioManager : MonoBehaviour
    {
        public static AudioManager Instance { get; private set; }

        [Header("Audio Sources")]
        [SerializeField] private AudioSource _bgmSource;
        [SerializeField] private AudioSource _sfxSource;

        [Header("Default Battle Audio Clips")]
        [SerializeField] private AudioClip _babelBattleBGM;
        [SerializeField] private AudioClip _cardPlaySFX;
        [SerializeField] private AudioClip _hitImpactSFX;
        [SerializeField] private AudioClip _shieldAbsorbSFX;

        private void Awake()
        {
            if (Instance != null && Instance != this)
            {
                Destroy(gameObject);
                return;
            }
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }

        private void Start()
        {
            if (_babelBattleBGM != null && _bgmSource != null)
            {
                PlayBGM(_babelBattleBGM);
            }
        }

        public void PlayBGM(AudioClip clip, bool loop = true)
        {
            if (_bgmSource == null || clip == null) return;
            _bgmSource.clip = clip;
            _bgmSource.loop = loop;
            _bgmSource.Play();
        }

        public void PlaySFX(AudioClip clip, float volume = 1f)
        {
            if (_sfxSource == null || clip == null) return;
            _sfxSource.PlayOneShot(clip, volume);
        }
    }
}
```

---

## 🛠️ 4. Konfigurasi Volume Post-Processing di URP
1. Di Hierarchy Scene, buat GameObject bernama `GlobalVolume`.
2. Tambahkan komponen **Volume** (Mode: `Global`).
3. Buat Profile baru dan tambahkan Overrides berikut:
   * **Bloom**: Threshold = `0.9`, Intensity = `1.2`, Tint = Kuning-Oranye (`#FFA500`).
   * **Vignette**: Intensity = `0.35`, Smoothness = `0.4`.
   * **Color Adjustments**: Contrast = `15`, Post Exposure = `-0.2` (suasana dark fantasy).

---

## 🧪 5. Langkah Verifikasi
1. Jalankan Play Mode di Unity Editor.
2. Amati obor di dinding arena berkedip lembut secara dinamis.
3. Amati efek Bloom yang menyala pada highlight kuning-oranye tanpa membuat layar over-exposed.
