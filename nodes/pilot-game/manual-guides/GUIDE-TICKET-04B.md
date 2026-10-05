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

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Konfigurasi URP 2D Renderer di Project Settings
1. Di menu bar atas, buka **Edit > Project Settings > Graphics**.
2. Pastikan field **Scriptable Render Pipeline Settings** terisi dengan `URP-2D-Asset`.
3. Klik file aset `URP-2D-Asset` tersebut di Project Window untuk melihat konfigurasi 2D Renderer Data:
   * Di panel Inspector, pastikan **Default Material Type** disetel ke `Lit`.
   * Di bagian **Post-processing**, pastikan checkbox **Enabled** tercentang.

---

### Langkah 2.2: Setup Kamera Utama (Main Camera)
1. Di panel **Hierarchy**, klik GameObject `Main Camera`.
2. Di panel **Inspector** pada komponen **Camera**:
   * **Projection:** `Orthographic`
   * **Size:** `8.5` *(Nilai ini pas membingkai grid 15×15 dengan ruang untuk UI bawah)*
   * **Transform Position:** `X: 7.5, Y: 7.5, Z: -10` *(Menengahkan pandangan tepat di titik pusat arena)*
   * **Background Type:** `Solid Color`, warna: `#0b0e14` (Hitam pekat kebiruan gelap)
3. Pada komponen **Universal Additional Camera Data**:
   * Centang opsi **Post Processing** = `True`.
   * Centang opsi **Render Shadows** = `True`.

---

### Langkah 2.3: Setup URP 2D Lights di Scene
1. **Global Ambient Light (Pencahayaan Redup):**
   * Di Hierarchy, klik kanan > **Light > 2D > Global Light 2D**.
   * Ganti nama menjadi `GlobalLight_Ambient`.
   * Di Inspector:
     * **Color:** Biru gelap keabu-abuan (`#2A3B4C`).
     * **Intensity:** `0.35` (Memberikan suasana gelap temaram ala dungeon perpustakaan kuno).
2. **Point Light 2D Obor (Torch Light):**
   * Di Hierarchy, klik kanan > **Light > 2D > Point Light 2D**.
   * Ganti nama menjadi `Torch_Wall_Left`.
   * Di Inspector:
     * **Transform Position:** `X: 1, Y: 7.5, Z: 0`.
     * **Light Type:** `Point`.
     * **Outer Radius:** `3.5`.
     * **Inner Radius:** `0.8`.
     * **Color:** Kuning-Oranye hangat (`#FF8C1A`).
     * **Intensity:** `1.1`.
   * Klik tombol **Add Component** > ketik `TorchFlicker` > tekan Enter.
3. Duplikasi `Torch_Wall_Left` (`Ctrl+D` / `Cmd+D`), ganti nama menjadi `Torch_Wall_Right` dan posisikan di `X: 14, Y: 7.5, Z: 0`.

---

### Langkah 2.4: Setup Global Volume Post-Processing
1. Di panel Hierarchy, klik kanan > **Volume > Global Volume**.
2. Ganti nama menjadi `PostProcessing_Volume`.
3. Di panel Inspector pada komponen **Volume**:
   * **Mode:** `Global`
   * **Profile:** Klik tombol **New** di samping field Profile (akan otomatis membuat aset profile di folder scene).
4. Klik tombol **Add Override** untuk menambahkan 3 efek berikut:
   * **Override 1: Bloom**
     * Centang **Threshold** = `0.9`
     * Centang **Intensity** = `1.2`
     * Centang **Scatter** = `0.7`
     * Centang **Tint** = Kuning-Oranye (`#FFA500`)
   * **Override 2: Vignette**
     * Centang **Intensity** = `0.35`
     * Centang **Smoothness** = `0.45`
     * Centang **Rounded** = `True`
   * **Override 3: Color Adjustments**
     * Centang **Post Exposure** = `-0.15`
     * Centang **Contrast** = `15`
     * Centang **Saturation** = `10`

---

### Langkah 2.5: Setup AudioManager di Scene
1. Di Hierarchy, klik kanan > **Create Empty**, beri nama `[AudioManager]`.
2. Di Inspector, klik **Add Component** > tambahkan dua komponen **Audio Source**:
   * **Audio Source 1 (BGM):**
     * Centang **Loop** = `True`
     * Centang **Play On Awake** = `False`
     * **Spatial Blend:** `0` (2D Sound)
     * **Volume:** `0.7`
   * **Audio Source 2 (SFX):**
     * Centang **Loop** = `False`
     * Centang **Play On Awake** = `False`
     * **Spatial Blend:** `0` (2D Sound)
     * **Volume:** `1.0`
3. Klik **Add Component** > ketik `AudioManager` > tekan Enter.
4. Hubungkan Audio Source 1 ke slot `_bgmSource` dan Audio Source 2 ke slot `_sfxSource`.

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
        [Header("Flicker Parameters")]
        [SerializeField] private float _minIntensity = 0.8f;
        [SerializeField] private float _maxIntensity = 1.4f;
        [SerializeField] private float _flickerSpeed = 3.5f;

        [Header("Micro Jitter Radius")]
        [SerializeField] private float _minRadius = 3.0f;
        [SerializeField] private float _maxRadius = 3.6f;

        private Light2D _light2D;
        private float _noiseOffset;

        private void Awake()
        {
            _light2D = GetComponent<Light2D>();
            // Offset acak agar setiap obor di scene memiliki variasi kedipan unik (tidak seragam)
            _noiseOffset = Random.Range(0f, 100f);
        }

        private void Update()
        {
            if (_light2D == null) return;

            // 1. Sampel Perlin Noise 1D/2D yang menghasilkan nilai transisi kontinu halus (0.0 s/d 1.0)
            float noise = Mathf.PerlinNoise(_noiseOffset, Time.time * _flickerSpeed);

            // 2. Interpolasi linier (Lerp) intensitas cahaya berdasarkan nilai noise
            _light2D.intensity = Mathf.Lerp(_minIntensity, _maxIntensity, noise);

            // 3. Interpolasi radius jangkauan cahaya agar ukuran halo api terasa berdenyut alami
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
    /// Mengelola pemutaran background music (BGM) dan efek suara (SFX) pertempuran secara terpusat (Singleton).
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
            // Pola Singleton: Memastikan hanya ada 1 instance aktif di seluruh scene dan bertahan antar scene
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

        /// <summary>
        /// Memainkan BGM dengan proteksi agar lagu yang sama tidak diputar ulang dari awal.
        /// </summary>
        public void PlayBGM(AudioClip clip, bool loop = true)
        {
            if (_bgmSource == null || clip == null) return;
            _bgmSource.clip = clip;
            _bgmSource.loop = loop;
            _bgmSource.Play();
        }

        /// <summary>
        /// Memainkan efek suara one-shot (dapat ditumpuk/overlap tanpa memotong suara sebelumnya).
        /// </summary>
        public void PlaySFX(AudioClip clip, float volume = 1f)
        {
            if (_sfxSource == null || clip == null) return;
            _sfxSource.PlayOneShot(clip, volume);
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi di Unity Editor
1. Tekan tombol **Play** di Unity Editor.
2. Amati visual di **Game View**:
   * Suasana arena gelap misterius dengan sudut layar gelap melengkung (*Vignette*).
   * Obor di sisi kiri dan kanan memancarkan cahaya oranye hangat yang berdenyut lembut alami.
   * Audio BGM suasana Menara Babel berputar secara mulus (*looping*).
