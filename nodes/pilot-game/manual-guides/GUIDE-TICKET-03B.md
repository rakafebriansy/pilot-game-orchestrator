# 📖 Manual Guide: TICKET-03B — Mesin Matematika Pertempuran & Validator Kartu

> **Referensi Tiket:** [TICKET-03B.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-03B.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan dua mesin logika murni yang menjadi otak eksekusi kartu:
1. **`CombatMathEngine.cs`**: Menghitung kalkulasi damage dengan mitigasi shield (`rawDamage - currentShield`), mengelola penumpukan dan durasi status abnormal (*Status Effects*), serta kalkulasi jarak Manhattan.
2. **`CardPlayValidator.cs`**: Memvalidasi keabsahan kartu sebelum dimainkan (memeriksa fase giliran, jangkauan ubin, validitas target, semak siluman *StealthBush*, dan jalur tabrakan).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── Core/
│   │   └── Data/
│   │       └── CombatMathEngine.cs
│   └── Cards/
│       └── CardPlayValidator.cs
└── Tests/
    └── EditMode/
        └── CombatMathTests.cs
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Core/Data/CombatMathEngine.cs`
```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Core.Data
{
    /// <summary>
    /// <summary>
    /// Mesin matematika kalkulasi damage, mitigasi shield, dan status effects (Pure C# Non-MonoBehaviour).
    /// </summary>
    public class CombatMathEngine
    {
        // Menyimpan daftar efek status aktif untuk setiap unit berdasarkan unitId
        private readonly Dictionary<int, List<ActiveStatusEffect>> _unitStatusEffects = new();

        /// <summary>
        /// Menghitung kalkulasi damage akhir dengan mitigasi Shield:
        /// 1. Shield menyerap damage mentah terlebih dahulu (1 Shield = 1 Damage).
        /// 2. Sisa damage yang tidak terserap Shield akan menembus langsung ke HP.
        /// 3. Mengembalikan struct DamagePayload berisi finalDamage dan remainingShield.
        /// </summary>
        public DamagePayload CalculateDamage(int targetUnitId, int rawDamage, int targetShield)
        {
            // Hitung seberapa banyak damage yang diserap shield (maksimal sebesar shield yang ada)
            int absorbedByShield = Math.Min(rawDamage, targetShield);

            // Sisa damage yang menembus ke HP (jika rawDamage > targetShield)
            int finalDamage = Math.Max(0, rawDamage - targetShield);

            // Sisa shield target setelah menyerap damage
            int remainingShield = Math.Max(0, targetShield - rawDamage);

            return new DamagePayload(targetUnitId, finalDamage, remainingShield);
        }

        /// <summary>
        /// Menghitung jarak grid ortogonal (Manhattan Distance: |x1 - x2| + |y1 - y2|).
        /// Digunakan untuk pengukuran range kartu, pergerakan, dan targeting grid taktis.
        /// </summary>
        public int CalculateManhattanDistance(Vector2Int a, Vector2Int b)
        {
            return Math.Abs(a.x - b.x) + Math.Abs(a.y - b.y);
        }

        /// <summary>
        /// Mengecek apakah koordinat target berada dalam jangkauan range ortogonal dari posisi asal.
        /// </summary>
        public bool IsInRange(Vector2Int origin, Vector2Int target, int range)
        {
            return CalculateManhattanDistance(origin, target) <= range;
        }

        /// <summary>
        /// Menerapkan efek status abnormal ke unit target dan memicu event OnStatusEffectApplied.
        /// </summary>
        public void ApplyStatusEffect(int unitId, StatusEffectType type, int duration)
        {
            if (!_unitStatusEffects.ContainsKey(unitId))
            {
                _unitStatusEffects[unitId] = new List<ActiveStatusEffect>();
            }

            _unitStatusEffects[unitId].Add(new ActiveStatusEffect(type, duration, unitId));
            CombatEvents.OnStatusEffectApplied?.Invoke(unitId, type, duration);
        }

        /// <summary>
        /// Mengurangi sisa durasi seluruh efek status aktif saat pergantian ronde (Round Reset Phase).
        /// Menggunakan iterasi terbalik (backward loop) agar aman saat menghapus elemen yang durasinya habis.
        /// </summary>
        public void TickStatusEffects()
        {
            // Menggunakan Tuple Deconstruction (unitId, list) pada Dictionary
            foreach (var (unitId, list) in _unitStatusEffects)
            {
                // Loop dari indeks terakhir ke 0 untuk mencegah CollectionModifiedException saat RemoveAt
                for (int i = list.Count - 1; i >= 0; i--)
                {
                    var effect = list[i];
                    effect.RemainingDuration--;

                    if (effect.RemainingDuration <= 0)
                    {
                        // Durasi habis -> Picu event kedaluwarsa dan hapus dari list aktif
                        CombatEvents.OnStatusEffectExpired?.Invoke(unitId, effect.Type);
                        list.RemoveAt(i);
                    }
                    else
                    {
                        // Update nilai struct yang telah dimodifikasi kembali ke list
                        list[i] = effect;
                    }
                }
            }
        }

        public void ResetEngine()
        {
            _unitStatusEffects.Clear();
        }
    }
}
```

---

### B. `Assets/Scripts/Cards/CardPlayValidator.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Data;
using PilotGame.Grid;

namespace PilotGame.Cards
{
    /// <summary>
    /// Validator keabsahan eksekusi kartu (Pure C# - Stateless).
    /// Memastikan kartu memenuhi semua aturan sebelum dimainkan (Fase, Jarak, Stealth, Target Valid).
    /// </summary>
    public static class CardPlayValidator
    {
        /// <summary>
        /// Mengevaluasi apakah suatu kartu sah untuk dimainkan pada koordinat target tertentu.
        /// Mengembalikan false dan alasan kegagalan jika ada aturan yang dilanggar.
        /// </summary>
        public static bool CanPlayCard(
            CardData card,
            Vector2Int casterPos,
            Vector2Int targetCoord,
            GridDataModel grid,
            CombatPhase currentPhase,
            out string failureReason)
        {
            failureReason = string.Empty;

            // 0. Validasi Eksistensi Data Kartu
            if (card == null)
            {
                failureReason = "Card data is invalid or null.";
                return false;
            }

            // 1. Validasi Batasan Fase (Contoh: Kartu aksi hanya boleh dimainkan saat PlayerPhase)
            if (card.PhaseRestriction != currentPhase)
            {
                failureReason = $"Card can only be played during phase: {card.PhaseRestriction}.";
                return false;
            }

            // 2. Validasi Batas Arena Grid 15x15
            if (!grid.IsInsideGrid(targetCoord))
            {
                failureReason = "Target is outside grid arena boundaries.";
                return false;
            }

            // 3. Validasi Jarak Jangkauan (Manhattan Distance |x1-x2| + |y1-y2|)
            int distance = Mathf.Abs(casterPos.x - targetCoord.x) + Mathf.Abs(casterPos.y - targetCoord.y);
            if (distance > card.Range)
            {
                failureReason = $"Target is out of card range ({card.Range} tiles). Current distance: {distance}.";
                return false;
            }

            // 4. Validasi Aturan Semak Siluman (Stealth Bush - GDD §4.1 via helper terpusat):
            // Unit di dalam semak taktis tidak dapat ditarget jika tersamarkan dari sudut pandang caster (jarak >= 2 petak)
            if (grid.IsTargetStealthed(casterPos, targetCoord))
            {
                failureReason = "Target is stealthed in tactical bush (must be adjacent at 1 tile distance).";
                return false;
            }

            // 5. Validasi Sasaran Kartu Serangan Target Tunggal (SingleTarget)
            if (card.ActionType == CardActionType.Attack && card.TargetArea == TargetAreaType.SingleTarget)
            {
                int occupant = grid.GetOccupant(targetCoord);
                if (occupant == 0)
                {
                    failureReason = "Single target attack requires an occupant at the target tile.";
                    return false;
                }
            }

            // 6. Validasi Kartu Pergerakan (Movement): Ubin tujuan harus kosong dan tidak berupa rintangan
            if (card.ActionType == CardActionType.Movement)
            {
                if (!grid.IsWalkable(targetCoord))
                {
                    failureReason = "Destination tile is blocked by an obstacle or occupied by another unit.";
                    return false;
                }
            }

            // Seluruh validasi lulus -> Kartu sah untuk dimainkan
            return true;
        }

        /// <summary>
        /// Menghasilkan daftar seluruh koordinat ubin yang sah sebagai target kartu saat ini.
        /// Digunakan oleh sistem visualisasi Tilemap Highlight saat pemain mengarahkan kartu (Hover).
        /// </summary>
        public static List<Vector2Int> GetValidTargetTiles(CardData card, Vector2Int casterPos, GridDataModel grid)
        {
            List<Vector2Int> validTiles = new List<Vector2Int>();
            if (card == null) return validTiles;

            // Pindai seluruh petak arena 15x15
            for (int x = 0; x < GridDataModel.Width; x++)
            {
                for (int y = 0; y < GridDataModel.Height; y++)
                {
                    Vector2Int coord = new Vector2Int(x, y);
                    int dist = Mathf.Abs(casterPos.x - coord.x) + Mathf.Abs(casterPos.y - coord.y);

                    // Saring hanya petak dalam radius jangkauan kartu
                    if (dist <= card.Range)
                    {
                        // Lewati petak jika tersamarkan di semak siluman dari sudut pandang caster
                        if (grid.IsTargetStealthed(casterPos, coord)) continue;

                        validTiles.Add(coord);
                    }
                }
            }

            return validTiles;
        }
    }
}
```

---

### C. `Assets/Tests/EditMode/CombatMathTests.cs`
```csharp
using NUnit.Framework;
using UnityEngine;
using PilotGame.Core.Data;

namespace PilotGame.Tests.EditMode
{
    [TestFixture]
    public class CombatMathTests
    {
        private CombatMathEngine _engine;

        [SetUp]
        public void Setup()
        {
            _engine = new CombatMathEngine();
        }

        [Test]
        public void CalculateDamage_ShieldAbsorbsDamageCorrectly()
        {
            // Raw 10 damage vs 4 shield -> 6 damage tembus, 0 shield sisa
            var payload = _engine.CalculateDamage(1, 10, 4);
            Assert.AreEqual(6, payload.DamageAmount);
            Assert.AreEqual(0, payload.ShieldRemaining);

            // Raw 5 damage vs 10 shield -> 0 damage tembus, 5 shield sisa
            var payload2 = _engine.CalculateDamage(1, 5, 10);
            Assert.AreEqual(0, payload2.DamageAmount);
            Assert.AreEqual(5, payload2.ShieldRemaining);
        }

        [Test]
        public void ManhattanDistance_CalculatesAccurately()
        {
            Vector2Int a = new Vector2Int(1, 1);
            Vector2Int b = new Vector2Int(4, 5);
            // |1-4| + |1-5| = 3 + 4 = 7
            Assert.AreEqual(7, _engine.CalculateManhattanDistance(a, b));
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Jalankan Unity Test Runner di tab **EditMode**.
2. Pastikan `CombatMathTests` selesai dengan status passed (Hijau) ✅.
