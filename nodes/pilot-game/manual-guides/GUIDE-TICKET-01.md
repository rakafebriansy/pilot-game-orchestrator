# 📖 Manual Guide: TICKET-01 — Pondasi Tipe Data, Payloads & Pusat Event (Event Bus)

> **Referensi Tiket:** [TICKET-01.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-01.md)  
> **Domain:** `[👑 PM CORE]` & `[⚡ COMMUNICATION BRIDGE]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini membangun fondasi arsitektur **Shared Data Contracts** dan **Static Event Bus** (`CombatEvents.cs`). Komponen ini memungkinkan seluruh domain (Grid Logic, Tilemap View, Character Animation, UI Toolkit Hand) saling bertukar data secara *decoupled* tanpa dependensi langsung antar kelas.

Semua file di tiket ini adalah **Pure C#** tanpa ketergantungan pada `MonoBehaviour`.

---

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

Sebelum mengetik kode program, ikuti langkah-langkah pengaturan proyek di antarmuka Unity Editor berikut:

### Langkah 2.1: Verifikasi Package Manager
Pastikan package inti sudah terpasang di Unity:
1. Di menu bar atas Unity Editor, buka **Window > Package Manager**.
2. Ubah dropdown di kiri atas menjadi **Packages: Unity Registry**.
3. Cari dan pastikan package berikut terpasang (bercentang hijau):
   * **Universal RP** (com.unity.render-pipelines.universal)
   * **2D Tilemap Editor** (com.unity.2d.tilemap)
   * **UI Toolkit** (com.unity.ui)
   * **Test Framework** (com.unity.test-framework)
4. Jika belum terpasang, klik tombol **Install** di pojok kanan bawah.

---

### Langkah 2.2: Pembuatan Struktur Folder di Project Window
1. Buka tab **Project** (biasanya di bagian bawah editor).
2. Klik kanan pada folder `Assets` > pilih **Create > Folder**, beri nama `Scripts`.
3. Di dalam `Assets/Scripts/`, buat folder `Core`.
4. Di dalam `Assets/Scripts/Core/`, buat dua subfolder:
   * `Data`
   * `Events`
5. Struktur folder akhir di Project Window akan terlihat seperti ini:
   ```text
   Assets/
   └── Scripts/
       └── Core/
           ├── Data/
           └── Events/
   ```

---

### Langkah 2.3: Pembuatan File C# Script via Unity GUI
1. Masuk ke folder `Assets/Scripts/Core/Data/`.
2. Klik kanan pada area kosong > pilih **Create > C# Script** > beri nama `CombatTypes` (tekan Enter).
3. Klik kanan lagi > pilih **Create > C# Script** > beri nama `CombatPayloads` (tekan Enter).
4. Pindah ke folder `Assets/Scripts/Core/Events/`.
5. Klik kanan > pilih **Create > C# Script** > beri nama `CombatEvents` (tekan Enter).
6. Buka file-file tersebut di IDE pilihan Anda (Visual Studio / VS Code / Rider) untuk memasukkan kode di Bagian 3.

---

### Langkah 2.4: Pembuatan Assembly Definition (Asmdef)
Untuk memastikan arsitektur tetap bersih dan modular, serta mempercepat waktu kompilasi di Unity:
1. Di folder `Assets/Scripts/Core/`, klik kanan > pilih **Create > Assembly Definition**.
2. Beri nama: `PilotGame.Core`.
3. Klik file `PilotGame.Core.asmdef` tersebut, lalu lihat panel **Inspector** di sebelah kanan:
   * Centang **Auto Referenced** = `True`.
   * Di bagian **Platforms**, biarkan default (Any Platform).
   * Klik tombol **Apply** di bagian bawah Inspector.

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Core/Data/CombatTypes.cs`
```csharp
using System;

namespace PilotGame.Core.Data
{
    /// <summary>
    /// 4 Fase pertempuran taktis dalam satu putaran (Turn Loop).
    /// </summary>
    public enum CombatPhase
    {
        IntentPhase,      // Musuh menghitung dan menampilkan niat aksi
        PlayerPhase,      // Pemain mengeksekusi 1 kartu aksi wajib
        EnemyPhase,       // Musuh mengeksekusi niat secara acak berurutan
        RoundResetPhase   // Evaluasi efek status, reset status, redraw kartu
    }

    /// <summary>
    /// Jenis visualisasi highlight ubin pada Tilemap Overlay.
    /// </summary>
    public enum HighlightType
    {
        None,
        DangerEnemyIntent, // Ubin merah: area serangan musuh
        ValidCardTarget,   // Ubin hijau: jangkauan kartu yang sah
        MovementRange,     // Ubin biru: jangkauan pergerakan
        HoverPreview       // Ubin kuning/oranye: pratinjau kursor hover
    }

    /// <summary>
    /// Tipe properti dan karakteristik medan ubin di arena 15x15.
    /// </summary>
    public enum TileType
    {
        NormalFloor,       // Lantai ubin biasa (walkable)
        StealthBush,       // Semak taktis: musuh tidak bisa menarget dari jarak > 2 petak
        ObstaclePillar,    // Pilar/Rintangan permanen (non-walkable & block attack linear)
        HazardTrap,        // Jebakan duri/api: memberikan damage saat diinjak
        BurnedBush         // Semak yang hangus akibat skill elemen api
    }

    /// <summary>
    /// Kategori aksi fungsional dari kartu tempur.
    /// </summary>
    public enum CardActionType
    {
        Attack,            // Memberikan damage langsung atau AoE
        Defense,           // Memberikan perisai (Shield) atau mitigasi
        Movement,          // Memindahkan posisi karakter (Dash, Teleport, Reposition)
        StatusModifier,    // Memberikan buff statistik atau debuff ke target
        Utility            // Menarik kartu, manipulasi turn, dsb.
    }

    /// <summary>
    /// Tipe status abnormal dan pengubah status karakter.
    /// </summary>
    public enum StatusEffectType
    {
        None,              // Tidak ada efek status aktif
        Bleed,             // Menerima damage saat berpindah ubin
        Freeze,            // Membekukan giliran / skip aksi
        Immobilize,        // Tidak dapat menggunakan kartu pergerakan
        Stun,              // Terpental dan kehilangan giliran
        Vulnerable,        // Menerima +50% damage tambahan
        Shielded,          // Memiliki perlindungan perisai yang menyerap damage
        Poison,            // Menerima damage racun berkala
        Burn               // Memberikan damage api saat ubin terbakar
    }

    /// <summary>
    /// Data status abnormal yang sedang aktif pada sebuah unit.
    /// </summary>
    public struct ActiveStatusEffect
    {
        public StatusEffectType Type;
        public int RemainingDuration;
        public int UnitId;

        public ActiveStatusEffect(StatusEffectType type, int duration, int unitId)
        {
            Type = type;
            RemainingDuration = duration;
            UnitId = unitId;
        }
    }
}
```

---

### B. `Assets/Scripts/Core/Data/CombatPayloads.cs`
```csharp
using System;
using UnityEngine;

namespace PilotGame.Core.Data
{
    /// <summary>
    /// Payload data permintaan perubahan highlight ubin arena.
    /// </summary>
    public readonly struct TileHighlightRequest
    {
        public readonly Vector2Int[] Coordinates;
        public readonly HighlightType Style;

        public TileHighlightRequest(Vector2Int[] coordinates, HighlightType style)
        {
            Coordinates = coordinates ?? Array.Empty<Vector2Int>();
            Style = style;
        }
    }

    /// <summary>
    /// Payload data pergerakan unit antar koordinat grid.
    /// </summary>
    public readonly struct UnitMovePayload
    {
        public readonly int UnitId;
        public readonly Vector2Int FromCoord;
        public readonly Vector2Int ToCoord;

        public UnitMovePayload(int unitId, Vector2Int fromCoord, Vector2Int toCoord)
        {
            UnitId = unitId;
            FromCoord = fromCoord;
            ToCoord = toCoord;
        }
    }

    /// <summary>
    /// Payload hasil kalkulasi pertempuran (damage dan sisa shield).
    /// </summary>
    public readonly struct DamagePayload
    {
        public readonly int TargetUnitId;
        public readonly int DamageAmount;
        public readonly int ShieldRemaining;

        public DamagePayload(int targetUnitId, int damageAmount, int shieldRemaining)
        {
            TargetUnitId = targetUnitId;
            DamageAmount = damageAmount;
            ShieldRemaining = shieldRemaining;
        }
    }
}
```

---

### C. `Assets/Scripts/Core/Events/CombatEvents.cs`
```csharp
using System;
using UnityEngine;
using PilotGame.Core.Data;

namespace PilotGame.Core.Events
{
    /// <summary>
    /// Static Event Bus pusat komunikasi pertempuran taktis.
    /// Semua domain saling mengirim sinyal melalui event di kelas ini.
    /// </summary>
    public static class CombatEvents
    {
        // --- Event Niat Musuh (Intent Phase) ---
        public static Action<int, Vector2Int> OnEnemyIntentDecided;

        // --- Event Visualisasi Highlight Tilemap (Domain 1) ---
        public static Action<TileHighlightRequest> OnHighlightTilesRequested;
        public static Action OnClearAllHighlights;

        // --- Event Interaksi Kartu (Domain 3 & Logic) ---
        public static Action<object, Vector2Int> OnCardPlayed; // object = CardData (di-cast saat runtime)
        public static Action OnDrawCardsRequested;

        // --- Event Pergerakan & Aksi Unit (Domain 2) ---
        public static Action<UnitMovePayload> OnUnitMoved;
        public static Action<int, int, Vector2Int> OnSkillExecuted; // casterId, skillId, targetCoord
        public static Action<DamagePayload> OnUnitDamaged;

        // --- Event Status Effect ---
        public static Action<int, StatusEffectType, int> OnStatusEffectApplied; // unitId, type, duration
        public static Action<int, StatusEffectType> OnStatusEffectExpired;       // unitId, type

        // --- Event Alur Siklus Fase Giliran (FSM) ---
        public static Action<CombatPhase> OnPhaseChanged;
        public static Action<bool> OnCombatEnded; // true = Menang, false = Kalah

        // --- Event Navigasi & Meta ---
        public static Action<string> OnCheckpointPlaced;

        /// <summary>
        /// Membersihkan seluruh delegate subscribers saat berpindah scene atau restart run.
        /// Mencegah Memory Leak dan NullReferenceException.
        /// </summary>
        public static void ResetAllEvents()
        {
            OnEnemyIntentDecided = null;
            OnHighlightTilesRequested = null;
            OnClearAllHighlights = null;
            OnCardPlayed = null;
            OnDrawCardsRequested = null;
            OnUnitMoved = null;
            OnSkillExecuted = null;
            OnUnitDamaged = null;
            OnStatusEffectApplied = null;
            OnStatusEffectExpired = null;
            OnPhaseChanged = null;
            OnCombatEnded = null;
            OnCheckpointPlaced = null;
        }
    }
}
```

---

## 🔍 4. Penjelasan Baris-demi-Baris & Prinsip Arsitektur
1. **`readonly struct`**: Mengapa struct readonly digunakan untuk Payload?
   * *Zero Allocation / Garbage Collection:* Struct dialokasikan di *Stack*, sehingga transmisi event tidak memicu GC Spike di Unity.
   * *Immutability:* Data di dalam event tidak dapat dimodifikasi oleh subscriber yang mendengarkan event tersebut di tengah jalan.
2. **`ResetAllEvents()`**: Wajib dipanggil saat transisi scene pertempuran ke scene eksplorasi atau saat GameOver, agar referensi MonoBehaviour lama yang telah di-destroy tidak terpanggil (*Dangling Delegate*).
3. **Pemisahan Namespace:** Semua namespace menggunakan pola `PilotGame.Core.*` untuk standardisasi arsitektur enterprise.

---

## 🧪 5. Langkah Verifikasi di Unity Editor
1. Kembali ke Unity Editor.
2. Amati ikon lingkaran kecil berputar di pojok kanan bawah Unity (proses kompilasi C#).
3. Buka tab **Console** (`Ctrl+Shift+C` / `Cmd+Shift+C` atau menu **Window > General > Console**).
4. **Kriteria Sukses:** Console harus bersih dari error merah (*0 Compilation Errors*).
5. Jika muncul pesan warning, klik tombol **Clear** di pojok kiri atas Console.
