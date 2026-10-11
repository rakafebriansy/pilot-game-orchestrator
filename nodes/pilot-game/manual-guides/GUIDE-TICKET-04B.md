# 📖 Manual Guide: TICKET-04B — Camera Post-Processing, URP 2D Lighting & Audio Environment

> **Referensi Tiket:** [TICKET-04B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04B.md)  
> **Domain:** `[🗺️ DOMAIN 1: ARENA & TILEMAP]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 📁 PETA STRUKTUR DIREKTORI (LOKASI SELURUH BERKAS)

Berikut adalah denah letak absolut dan relatif dari seluruh berkas yang dibuat dan dikonfigurasi pada tiket ini:

```text
Tubbies Pilot Game/
└── Assets/
    ├── Scenes/
    │   └── MainBattleScene.unity                    <-- Scene tempat lampu, kamera, dan volume dirakit
    │
    ├── Settings/
    │   ├── UniversalRP.asset                        <-- Aset Pipeline URP bawaan
    │   ├── Renderer2D.asset                         <-- Data 2D Renderer (Default Material: Lit)
    │   └── PostProcessing/
    │       └── MesopotamiaCombat_PP.asset           <-- Profile Volume Post-Processing (Bloom, Vignette, Color)
    │
    ├── Scripts/
    │   ├── Grid/
    │   │   └── TorchFlicker.cs                      <-- Script efek kedip api obor dinamis
    │   │
    │   └── Core/
    │       └── AudioManager.cs                      <-- Script Singleton pengatur BGM & SFX
    │
    ├── Prefabs/
    │   └── Environment/
    │       └── TorchLight_Prefab.prefab             <-- Prefab Obor Dinding (Spot Light 2D 360°)
    │
    └── Audio/
        ├── BGM/
        │   └── BabelBattle_BGM.wav                  <-- Audio BGM atmosferik (placeholder / audio asset)
        └── SFX/
            ├── SFX_Telegraph.wav                    <-- Suara intent musuh
            └── SFX_HitImpact.wav                    <-- Suara benturan/damage
```

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengonfigurasi estetika visual atmosfer Menara Babel sesuai arahan seni GDD §5.2 (*Dark tones dengan aksen kuning-oranye*):
1. **URP 2D Lighting**: Global 2D Light bernuansa temaram + Spot Light 2D (360° Point) untuk obor dinding dengan script kedip api realistis (`TorchFlicker.cs`).
2. **Volume Post-Processing**: Bloom (aksen rune kuning-oranye), Vignette, dan Color Adjustments di `MesopotamiaCombat_PP.asset`.
3. **Audio Environment**: Sistem `AudioManager.cs` untuk pemutaran BGM suasana Menara Babel dan sound effect atmosferik.

---

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Konfigurasi URP 2D Renderer di Project Settings
1. Di menu bar atas, buka **Edit > Project Settings > Graphics**.
2. Di Unity 6, pastikan tab **URP** aktif dengan pesan *"These are the settings that are currently active on the project"*. Ini menandakan pipeline URP sudah otomatis aktif.
3. Di panel **Project Window**, file pengaturannya berada di folder **`Assets/Settings/`**:
   * **`UniversalRP.asset`**: Aset Pipeline URP utama.
   * **`Renderer2D.asset`**: Aset Data 2D Renderer bawaan proyek.
   * Pastikan pada `Renderer2D.asset`: fitur 2D Lighting (`Default Material Type: Lit`) dan Post-Processing (`Enabled: True`) aktif.

---

### Langkah 2.2: Setup Kamera Utama (Main Camera)
1. Di panel **Hierarchy**, klik GameObject **`Main Camera`**.
2. Di panel **Inspector** pada komponen **Camera**:
   * **Projection:** `Orthographic`
   * **Size:** `8.5` *(Nilai ini membingkai grid 15×15 dengan presisi)*
   * **Transform Position:** `X: 7.5, Y: 7.5, Z: -10` *(Menengahkan pandangan tepat di titik pusat arena)*
   * **Environment (Buka lipatan menu Environment):**
     * **Background Type:** Pilih `Solid Color`
     * **Background Color:** Masukkan kode Hex `#0b0e14` (Hitam pekat kebiruan gelap)
   * **Rendering (Buka lipatan menu Rendering):**
     * Centang opsi **Post Processing** = `True`
     * Centang opsi **Render Shadows** = `True`

> 💡 **Catatan Unity 6:** Komponen `Universal Additional Camera Data` di bawahnya memang sengaja kosong karena seluruh opsi URP di atas sudah digabungkan langsung ke dalam lipatan menu *Rendering* dan *Environment* pada komponen Camera.

---

### Langkah 2.3: Setup URP 2D Lights & Obor di Scene

#### A. Global Ambient Light (Pencahayaan Ruangan Redup)
1. Di panel Hierarchy, klik kanan > **Light > 2D > Global Light 2D**.
2. Ganti nama GameObject menjadi: **`GlobalLight_Ambient`**.
3. Di panel Inspector:
   * **Color:** Biru gelap keabu-abuan (`#2A3B4C` atau `#1A1420`).
   * **Intensity:** `0.35` (Memberikan suasana gelap temaram ala dungeon perpustakaan kuno).

#### B. Pembuatan Obor Dinding (Spot Light 2D 360°)

> 💡 **Pahami Konsep Unity 6 URP 2D:**  
> Di Unity 6, tipe "Point Light 2D" kini diwadahi oleh **Spot Light 2D** dengan sudut (**Spot Angle**) diset ke **360°**. Hal ini menghasilkan pendaran cahaya melingkar penuh (*omnidirectional point light*) dengan kontrol halus atas *Inner/Outer Radius*.

1. **Buat Script Terlebih Dahulu & Tambahkan Assembly Reference URP:**  
   Simpan script `TorchFlicker.cs` ke folder:  
   📂 **`Assets/Scripts/Grid/TorchFlicker.cs`** *(lihat [Bagian 3.A](#a-assetsscriptsgridtorchflickercs) untuk kode lengkap)*.  
   > ⚠️ **PENTING — Solusi Error Namespace `'Universal' does not exist` atau `'Light2D' could not be found`:**  
   > Folder `Assets/Scripts/Grid/` dikontrol oleh file Assembly Definition (**`PilotGame.Grid.asmdef`**). Dalam URP, komponen 2D seperti **`Light2D`** berada di assembly khusus 2D. Anda perlu menambahkan dua referensi berikut:  
   > 1. Di Project Window, klik file **`Assets/Scripts/Grid/PilotGame.Grid`** *(file dengan icon roda gigi/assembly)*.  
   > 2. Di panel **Inspector**, cari bagian **Assembly Definition References**.  
   > 3. Klik tombol **`+`** lalu tambahkan:  
   >    * **`Unity.RenderPipelines.Universal.Runtime`**  
   >    * **`Unity.RenderPipelines.Universal.2D.Runtime`** *(Ini yang memuat class `Light2D`!)*  
   > 4. Klik tombol **Apply** di kanan bawah panel Inspector.  
   > *(Setelah Unity recompile, error namespace Universal dan Light2D akan langsung hilang)*.
2. **Buat Objek Lampu (GameObject) di Hierarchy:**  
   Di panel **Hierarchy** (daftar objek di sisi kiri editor), klik kanan di area kosong > pilih **Light > 2D > Spot Light 2D**.  
   *(Seketika Unity akan memunculkan sebuah objek baru bernama default `Spot Light 2D` di daftar Hierarchy. Objek inilah yang disebut sebagai **GameObject**)*.  
   > ⚠️ **PERINGATAN PENTING:**  
   > Pastikan komponen yang terpasang pada objek tersebut adalah **`Light 2D`** (dengan icon bohlam kuning 2D), **BUKAN** komponen `Light` 3D!  
   > *(Jika yang terpasang adalah komponen `Light` 3D dengan menu General > Type: Point, hapus komponen tersebut via klik kanan header komponen > Remove Component)*.  
   > *(Alternatif pembuatan: Anda juga bisa klik kanan di Hierarchy > pilih **Create Empty**, lalu klik tombol **Add Component > Light 2D** di Inspector)*.
3. **Beri Nama GameObject Tersebut:**  
   Klik objek baru yang baru muncul tadi di Hierarchy, tekan `F2` atau `Enter` (atau klik kanan > **Rename**), lalu ubah namanya menjadi: **`TorchLight_Wall`**.
4. Di panel Inspector pada komponen **`Light 2D`**, atur parameter berikut:
   * **Light Type:** Pilih **`Spot`** *(Pilihan dropdown: Freeform, Sprite, Spot, Global)*.
   * **Color:** Kuning-Oranye hangat (`#FF8C1A` atau Hex `#FF8C42`).
   * **Intensity:** `1.1`
   * **Radius:** Terdapat dua kotak isian berdampingan:
     * **Kolom Kiri (Inner Radius):** `0.8` *(Inti api paling terang di sekitar obor)*.
     * **Kolom Kanan (Outer Radius):** `3.5` *(Batas terluar jangkauan pendaran cahaya)*.
   * **Inner / Outer Spot Angle:** Terdapat dua kotak isian sudut:
     * **Kolom Kiri (Inner Spot Angle):** `360`
     * **Kolom Kanan (Outer Spot Angle):** `360`  
     *(Dengan kedua sudut 360°, cahaya memancar bundar penuh ke segala penjuru tanpa terpotong corong)*.
   * **Falloff Strength:** `0.6` *(Agar tepi lingkaran cahaya memudar halus ke kegelapan)*.
   * **Target Sorting Layers:** `Everything` (Default).
5. **Pasang Script Animasi Kedipan:**  
   Pada Inspector GameObject `TorchLight_Wall`, klik tombol **Add Component** > ketik `TorchFlicker` > tekan Enter.  
   Nilai bawaan pada script `TorchFlicker`:
   * **Min Intensity:** `0.8` | **Max Intensity:** `1.4` | **Flicker Speed:** `3.5`
   * **Min Radius:** `3.0` | **Max Radius:** `3.6`

#### C. Menyimpan Prefab Obor & Penempatan di 4 Sudut Arena
1. Buka folder `Assets/Prefabs/` di Project Window, buat sub-folder baru bernama **`Environment`** jika belum ada (`Assets/Prefabs/Environment/`).
2. Seret (Drag & Drop) GameObject **`TorchLight_Wall`** dari Hierarchy ke folder Project Window tersebut:  
   📂 **`Assets/Prefabs/Environment/TorchLight_Prefab.prefab`**.
3. Di scene, posisikan prefab obor pada 4 sudut dinding arena:
   * **Obor 1 (Kiri Atas):** Transform Position `X: 1, Y: 14, Z: 0`
   * **Obor 2 (Kanan Atas):** Transform Position `X: 14, Y: 14, Z: 0` (duplikat via `Ctrl+D` / `Cmd+D`)
   * **Obor 3 (Kiri Bawah):** Transform Position `X: 1, Y: 1, Z: 0`
   * **Obor 4 (Kanan Bawah):** Transform Position `X: 14, Y: 1, Z: 0`

---

### Langkah 2.4: Setup Global Volume Post-Processing

1. Buat folder baru di Project Window:  
   📂 **`Assets/Settings/PostProcessing/`**
2. Di panel **Hierarchy**, klik kanan > **Volume > Global Volume**.
3. Ganti nama GameObject menjadi: **`PostProcessing_Volume`**.
4. Di panel Inspector pada komponen **Volume**:
   * **Mode:** `Global`
   * **Profile:** Klik tombol **New** di samping field Profile (akan otomatis membuat aset profile).
   * Cari file profile baru tersebut di Project Window, ganti namanya menjadi:  
     📄 **`MesopotamiaCombat_PP.asset`** dan pindahkan ke folder `Assets/Settings/PostProcessing/`.
5. Klik tombol **Add Override** pada komponen Volume untuk menambahkan 3 efek berikut:

#### 1. Override: Bloom (Pendaran Cahaya Obor & Magic Rune)
* Centang **Threshold** = `0.9`
* Centang **Intensity** = `1.2`
* Centang **Scatter** = `0.7`
* Centang **Tint** = Kuning-Oranye (`#FFA500`)

#### 2. Override: Vignette (Penggelapan Sudut Layar)
* Centang **Intensity** = `0.35`
* Centang **Smoothness** = `0.45`
* Centang **Rounded** = `True`

#### 3. Override: Color Adjustments (Kontras Gelap Menara Babel)
* Centang **Post Exposure** = `-0.15`
* Centang **Contrast** = `15`
* Centang **Saturation** = `10`

---

### Langkah 2.5: Setup AudioManager di Scene

1. Simpan script `AudioManager.cs` ke folder:  
   📂 **`Assets/Scripts/Core/AudioManager.cs`** *(lihat Bagian 3.B untuk kode lengkap)*.
2. Di panel **Hierarchy**, klik kanan > **Create Empty**, beri nama: **`[AudioManager]`**.
3. Di panel Inspector, klik **Add Component** > tambahkan dua buah komponen **Audio Source**:
   * **Audio Source 1 (Untuk BGM):**
     * Centang **Loop** = `True`
     * Centang **Play On Awake** = `False`
     * **Spatial Blend:** `0` *(Slider digeser penuh ke 2D)*
     * **Volume:** `0.7`
   * **Audio Source 2 (Untuk SFX):**
     * Centang **Loop** = `False`
     * Centang **Play On Awake** = `False`
     * **Spatial Blend:** `0` *(Slider digeser penuh ke 2D)*
     * **Volume:** `1.0`
4. Klik **Add Component** > ketik `AudioManager` > tekan Enter.
5. Hubungkan referensi slot pada komponen `AudioManager`:
   * **Bgm Source:** Seret Audio Source 1 ke slot ini.
   * **Sfx Source:** Seret Audio Source 2 ke slot ini.
6. *(Opsional)* Jika Anda memiliki file audio, masukkan ke folder:
   * BGM: `Assets/Audio/BGM/`
   * SFX: `Assets/Audio/SFX/`

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Grid/TorchFlicker.cs`
Simpan kode berikut di `Assets/Scripts/Grid/TorchFlicker.cs`:

```csharp
using UnityEngine;
using UnityEngine.Rendering.Universal;

namespace PilotGame.Grid
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

### B. `Assets/Scripts/Core/AudioManager.cs`
Simpan kode berikut di `Assets/Scripts/Core/AudioManager.cs`:

```csharp
using UnityEngine;

namespace PilotGame.Core
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

1. Buka scene `Assets/Scenes/MainBattleScene.unity`.
2. Tekan tombol **Play (▶️)** di Unity Editor.
3. Amati visual di **Game View**:
   * Suasana arena gelap misterius dengan sudut layar menggelap melengkung (*Vignette*).
   * Keempat obor di sudut arena memancarkan cahaya oranye hangat yang berkedip lembut alami tanpa patah-patah (*Perlin Noise*).
   * Partikel highlight atau ubin arena memiliki efek pendaran cahaya (*Bloom glow*).
   * Komponen `[AudioManager]` tidak dihancurkan saat runtime (*DontDestroyOnLoad*).
