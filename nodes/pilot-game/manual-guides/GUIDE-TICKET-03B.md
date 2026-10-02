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
    /// Mesin matematika kalkulasi damage, mitigasi shield, dan status effects (Pure C#).
    /// </summary>
    public class CombatMathEngine
    {
        private readonly Dictionary<int, List<ActiveStatusEffect>> _unitStatusEffects = new();

        /// <summary>
        /// Menghitung damage akhir setelah diserap oleh Shield target.
        /// </summary>
        public DamagePayload CalculateDamage(int targetUnitId, int rawDamage, int targetShield)
        {
            int absorbedByShield = Math.Min(rawDamage, targetShield);
            int finalDamage = Math.Max(0, rawDamage - targetShield);
            int remainingShield = Math.Max(0, targetShield - rawDamage);

            return new DamagePayload(targetUnitId, finalDamage, remainingShield);
        }

        public int CalculateManhattanDistance(Vector2Int a, Vector2Int b)
        {
            return Math.Abs(a.x - b.x) + Math.Abs(a.y - b.y);
        }

        public bool IsInRange(Vector2Int origin, Vector2Int target, int range)
        {
            return CalculateManhattanDistance(origin, target) <= range;
        }

        public void ApplyStatusEffect(int unitId, StatusEffectType type, int duration)
        {
            if (!_unitStatusEffects.ContainsKey(unitId))
            {
                _unitStatusEffects[unitId] = new List<ActiveStatusEffect>();
            }

            _unitStatusEffects[unitId].Add(new ActiveStatusEffect(type, duration, unitId));
            CombatEvents.OnStatusEffectApplied?.Invoke(unitId, type, duration);
        }

        public void TickStatusEffects()
        {
            foreach (var kvp in _unitStatusEffects)
            {
                int unitId = kvp.Key;
                var list = kvp.Value;

                for (int i = list.Count - 1; i >= 0; i--)
                {
                    var effect = list[i];
                    effect.RemainingDuration--;

                    if (effect.RemainingDuration <= 0)
                    {
                        CombatEvents.OnStatusEffectExpired?.Invoke(unitId, effect.Type);
                        list.RemoveAt(i);
                    }
                    else
                    {
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
    /// </summary>
    public static class CardPlayValidator
    {
        public static bool CanPlayCard(
            CardData card,
            Vector2Int casterPos,
            Vector2Int targetCoord,
            GridDataModel grid,
            CombatPhase currentPhase,
            out string failureReason)
        {
            failureReason = string.Empty;

            if (card == null)
            {
                failureReason = "Kartu tidak valid.";
                return false;
            }

            // 1. Validasi Fase
            if (card.PhaseRestriction != currentPhase)
            {
                failureReason = $"Kartu hanya dapat dimainkan pada fase: {card.PhaseRestriction}.";
                return false;
            }

            // 2. Validasi Batas Grid
            if (!grid.IsInsideGrid(targetCoord))
            {
                failureReason = "Target berada di luar arena.";
                return false;
            }

            // 3. Validasi Jangkauan Jarak Manhattan
            int distance = Mathf.Abs(casterPos.x - targetCoord.x) + Mathf.Abs(casterPos.y - targetCoord.y);
            if (distance > card.Range)
            {
                failureReason = $"Target berada di luar jangkauan kartu ({card.Range} petak).";
                return false;
            }

            // 4. Validasi Aturan Stealth Bush (GDD §4.1)
            if (grid.IsStealthed(targetCoord) && distance > 2)
            {
                failureReason = "Target bersembunyi di dalam semak (harus berada dalam jarak <= 2 petak).";
                return false;
            }

            // 5. Validasi Tipe Area
            if (card.ActionType == CardActionType.Attack && card.AreaType == TargetAreaType.SingleTarget)
            {
                int occupant = grid.GetOccupant(targetCoord);
                if (occupant == 0)
                {
                    failureReason = "Serangan target tunggal membutuhkan musuh di ubin target.";
                    return false;
                }
            }

            if (card.ActionType == CardActionType.Movement)
            {
                if (!grid.IsWalkable(targetCoord))
                {
                    failureReason = "Ubin tujuan tidak dapat ditempati.";
                    return false;
                }
            }

            return true;
        }

        public static List<Vector2Int> GetValidTargetTiles(CardData card, Vector2Int casterPos, GridDataModel grid)
        {
            List<Vector2Int> validTiles = new List<Vector2Int>();
            if (card == null) return validTiles;

            for (int x = 0; x < GridDataModel.Width; x++)
            {
                for (int y = 0; y < GridDataModel.Height; y++)
                {
                    Vector2Int coord = new Vector2Int(x, y);
                    int dist = Mathf.Abs(casterPos.x - coord.x) + Mathf.Abs(casterPos.y - coord.y);

                    if (dist <= card.Range)
                    {
                        if (grid.IsStealthed(coord) && dist > 2) continue;
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
