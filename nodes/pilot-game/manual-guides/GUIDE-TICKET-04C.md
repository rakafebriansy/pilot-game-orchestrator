# 📖 Manual Guide: TICKET-04C — Pulsing Danger Shader & ArenaEnvironment_Prefab

> **Referensi Tiket:** [TICKET-04C.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-04C.md)  
> **Domain:** `[🗺️ DOMAIN 1: ARENA & TILEMAP]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan shader visual khusus **Pulsing Danger Shader** (HLSL / Shader Graph) yang membuat ubin merah telegraph serangan musuh berdenyut (*pulsing*) secara ritmis, serta merakit seluruh komponen lingkungan arena (Tilemap 3-layer, URP Light2D, Obor, dan Shader) menjadi satu prefab lingkungan utuh: `ArenaEnvironment_Prefab.prefab`.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Shaders/
│   └── DangerTilePulse.shader
└── Prefabs/
    └── Arena/
        └── ArenaEnvironment_Prefab.prefab
```

---

## 💻 3. Kode Sumber Lengkap

### `Assets/Shaders/DangerTilePulse.shader`
```hlsl
Shader "PilotGame/2D/DangerTilePulse"
{
    Properties
    {
        _MainTex ("Sprite Texture", 2D) = "white" {}
        _BaseColor ("Base Danger Color", Color) = (1, 0.15, 0.15, 0.6)
        _PulseColor ("Peak Danger Color", Color) = (1, 0.8, 0.1, 0.9)
        _PulseSpeed ("Pulse Speed", Float) = 4.0
        _GlowIntensity ("Glow Intensity", Float) = 1.5
    }

    SubShader
    {
        Tags
        {
            "Queue" = "Transparent"
            "RenderType" = "Transparent"
            "RenderPipeline" = "UniversalPipeline"
        }

        Blend SrcAlpha OneMinusSrcAlpha
        Cull Off
        ZWrite Off

        Pass
        {
            HLSLPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"

            struct Attributes
            {
                float4 positionOS : POSITION;
                float2 uv : TEXCOORD0;
                float4 color : COLOR;
            };

            struct Varyings
            {
                float4 positionCS : SV_POSITION;
                float2 uv : TEXCOORD0;
                float4 color : COLOR;
            };

            Texture2D _MainTex;
            SamplerState sampler_MainTex;

            CBUFFER_START(UnityPerMaterial)
                float4 _BaseColor;
                float4 _PulseColor;
                float _PulseSpeed;
                float _GlowIntensity;
            CBUFFER_END

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
                output.uv = input.uv;
                output.color = input.color;
                return output;
            }

            float4 frag(Varyings input) : SV_Target
            {
                // Mengambil sampel tekstur dasar sprite
                float4 texColor = _MainTex.Sample(sampler_MainTex, input.uv);
                
                // KALKULASI GELOMBANG SINUS (PULSING):
                // 1. _Time.y menghasilkan waktu dalam detik sejak scene dimulai.
                // 2. sin(_Time.y * _PulseSpeed) menghasilkan osilasi antara [-1.0 s/d +1.0].
                // 3. (+ 1.0) * 0.5 menormalisasi rentang nilai ke [0.0 s/d 1.0] (0 = Base, 1 = Peak).
                float pulseFactor = (sin(_Time.y * _PulseSpeed) + 1.0) * 0.5;

                // Interpolasi linear (lerp) antara warna dasar merah dan warna puncak kuning/oranye
                // Dikalikan _GlowIntensity untuk efek visual URP Bloom/Glow yang kontras
                float4 dangerColor = lerp(_BaseColor, _PulseColor, pulseFactor) * _GlowIntensity;

                // Menggabungkan tekstur sprite, warna bahaya berdenyut, dan vertex color
                return texColor * dangerColor * input.color;
            }
            ENDHLSL
        }
    }
}
```

---

## 🛠️ 4. Langkah Pembuatan Prefab `ArenaEnvironment_Prefab`
1. Di Unity Editor, klik kanan `DangerTilePulse.shader` → **Create > Material** → beri nama `Mat_DangerTilePulse.mat`.
2. Buat Material Tilemap untuk ubin bahaya merah dengan material tersebut.
3. Di Hierarchy Scene, gabungkan:
   * `ArenaGridRoot` (3-Layer Tilemap dari [TICKET-04](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-04.md)).
   * `GlobalVolume` (Post-processing dari [TICKET-04B](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/manual-guides/GUIDE-TICKET-04B.md)).
   * `Light2D_Global` (Ambient Dark Blue).
   * Obor Dinding dengan script `TorchFlicker.cs`.
4. Tarik root GameObject ke folder `Assets/Prefabs/Arena/` dan namakan `ArenaEnvironment_Prefab.prefab`.

---

## 🧪 5. Langkah Verifikasi
1. Pasang prefab ke scene kosong.
2. Panggil event broadcast highlight merah di Play Mode:
   ```csharp
   CombatEvents.OnHighlightTilesRequested?.Invoke(
       new TileHighlightRequest(new Vector2Int[] { new Vector2Int(7,7) }, HighlightType.DangerEnemyIntent)
   );
   ```
3. Periksa ubin di (7,7) berdenyut merah menyala secara ritmis dan kontras.
