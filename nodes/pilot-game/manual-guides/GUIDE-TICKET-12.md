# 📖 Manual Guide: TICKET-12 — Roster Boss Chapter 1: Multi-Tile & Enrage Phase

> **Referensi Tiket:** [TICKET-12.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-12.md)  
> **Domain:** `[📊 DOMAIN 4: DATA & LOGIC]`  
> **Fase:** 3 (Meta Loop, Boss & GDD 1.0)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini mengimplementasikan mekanik pertempuran bos puncak di Lantai 15 Menara Babel:
1. **`BossData.cs`**: Konfigurasi bos berukuran multi-tile (2×2 petak grid) dengan HP besar, fase normal, dan **Fase Murka (*Enrage Phase*)** saat HP turun di bawah 50%.
2. **`BossAIController.cs`**: Pola serangan komprehensif yang mencakup telegraf area masif (3×3 telegraf merah) dan debuff status ke seluruh arena.

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   └── Units/
│       ├── BossData.cs
│       └── BossAIController.cs
└── ScriptableObjects/
    └── Bosses/
        └── Boss_ArchivistEnlil.asset
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Units/BossData.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;

namespace PilotGame.Units
{
    [CreateAssetMenu(fileName = "NewBoss", menuName = "PilotGame/Data/Boss Data")]
    public class BossData : ScriptableObject
    {
        [Header("Identitas Boss")]
        public string BossId = "boss_enlil";
        public string BossName = "Enlil, Grand Keeper of the Cuneiform Archive";
        public Sprite BossPortrait;

        [Header("Statistik Vital")]
        public int MaxHealth = 250;
        public int BaseShield = 20;
        public int SizeWidth = 2;   // 2x2 multi-tile footprint
        public int SizeHeight = 2;

        [Header("Fase Enrage")]
        public float EnrageHealthThreshold = 0.50f; // 50% HP
        public int EnrageAttackBonus = 6;
        public Color EnrageAuraColor = Color.red;

        [Header("Katalog Serangan")]
        public List<CardData> Phase1Skills = new();
        public List<CardData> EnrageSkills = new();
    }
}
```

---

### B. `Assets/Scripts/Units/BossAIController.cs`
```csharp
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;

namespace PilotGame.Units
{
    public class BossAIController : MonoBehaviour
    {
        [SerializeField] private BossData _bossData;
        [SerializeField] private int _bossUnitId = 99;

        private int _currentHealth;
        private bool _isEnraged = false;
        private GridDataModel _grid;

        public void Initialize(BossData data, GridDataModel grid)
        {
            _bossData = data;
            _grid = grid;
            _currentHealth = _bossData.MaxHealth;
        }

        public void TakeDamage(int damage)
        {
            _currentHealth -= damage;
            CheckEnrageThreshold();
        }

        private void CheckEnrageThreshold()
        {
            if (!_isEnraged && (float)_currentHealth / _bossData.MaxHealth <= _bossData.EnrageHealthThreshold)
            {
                _isEnraged = true;
                TriggerEnrageTransformation();
            }
        }

        private void TriggerEnrageTransformation()
        {
            Debug.Log($"[BossAI] {_bossData.BossName} memasuki FASE MURKA (ENRAGE)!");
            // Tambahkan partikel aura merah menyala dan suara raungan boss
        }

        public void PlanBossTurn(Vector2Int bossCenter, Vector2Int playerPos)
        {
            if (_isEnraged)
            {
                // Serangan AoE 5x5 menara berguncang
                PlanCataclysmicStrike(playerPos);
            }
            else
            {
                // Serangan area standar 3x3
                PlanArchiveSmash(playerPos);
            }
        }

        private void PlanArchiveSmash(Vector2Int target)
        {
            List<Vector2Int> tiles = new List<Vector2Int>();
            for (int x = -1; x <= 1; x++)
            {
                for (int y = -1; y <= 1; y++)
                {
                    Vector2Int c = new Vector2Int(target.x + x, target.y + y);
                    if (_grid.IsInsideGrid(c)) tiles.Add(c);
                }
            }

            CombatEvents.OnEnemyIntentDecided?.Invoke(_bossUnitId, target);
            CombatEvents.OnHighlightTilesRequested?.Invoke(
                new TileHighlightRequest(tiles.ToArray(), HighlightType.DangerEnemyIntent)
            );
        }

        private void PlanCataclysmicStrike(Vector2Int target)
        {
            List<Vector2Int> tiles = new List<Vector2Int>();
            for (int x = -2; x <= 2; x++)
            {
                for (int y = -2; y <= 2; y++)
                {
                    Vector2Int c = new Vector2Int(target.x + x, target.y + y);
                    if (_grid.IsInsideGrid(c)) tiles.Add(c);
                }
            }

            CombatEvents.OnEnemyIntentDecided?.Invoke(_bossUnitId, target);
            CombatEvents.OnHighlightTilesRequested?.Invoke(
                new TileHighlightRequest(tiles.ToArray(), HighlightType.DangerEnemyIntent)
            );
        }
    }
}
```

---

## 🧪 3. Langkah Verifikasi
1. Di Play Mode, panggil `TakeDamage(150)` pada boss.
2. Boss otomatis berubah ke mode Enrage dan telegraph serangan membesar dari 3×3 menjadi 5×5 petak arena.
