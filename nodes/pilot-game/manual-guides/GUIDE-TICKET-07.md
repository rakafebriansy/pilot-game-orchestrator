# 📖 Manual Guide: TICKET-07 — State Machine Giliran Tempur & Integrasi Scene Utama

> **Referensi Tiket:** [TICKET-07.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-07.md)  
> **Domain:** `[👑 PM CORE]`  
> **Fase:** 1 (MVP Vertical Slice)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini adalah puncak integrasi **Fase 1 (Combat MVP)**. Tiket ini membangun arsitektur **Finite State Machine (FSM)** 4-Fase:
1. **`IntentPhaseState`**: AI Musuh mengalkulasi target dan memunculkan telegraf merah + badge intent.
2. **`PlayerPhaseState`**: Pemain wajib memainkan 1 kartu aksi.
3. **`EnemyPhaseState`**: Musuh mengeksekusi serangan secara acak berurutan (*Totally Random* - GDD §4.7).
4. **`RoundResetPhaseState`**: Mengurangi durasi status effect, membersihkan highlight, menarik kartu hingga 5, dan kembali ke Intent Phase.

Serta merakit seluruh prefab dari TICKET-01 s/d TICKET-06C ke dalam **`MainBattleScene.unity`**.

---

## 🖥️ 2. Panduan Lengkap Unity Editor (Step-by-Step GUI Setup)

### Langkah 2.1: Pembuatan Scene Utama `MainBattleScene.unity`
1. Di Project Window, buka folder `Assets/Scenes/`.
2. Klik kanan > **Create > Scene**, beri nama: `MainBattleScene`.
3. Klik dua kali `MainBattleScene` untuk membukanya di editor.

---

### Langkah 2.2: Penyusunan Pohon Hierarchy Scene
Susun GameObjects di panel **Hierarchy** sesuai hierarki berikut:

```text
[Hierarchy - MainBattleScene]
├── Main Camera                  [Camera, Universal Additional Camera Data, CameraShaker]
├── GlobalVolume                 [Volume (Bloom, Vignette, Color Adjustments)]
├── [AudioManager]               [AudioManager, 2x AudioSource]
├── [VFXPoolManager]             [VFXPoolManager]
├── ArenaEnvironment_Prefab      [Grid, 3x Tilemap (Floor, Obstacles, Overlay), GridTilemapView]
├── Nabu_Player_Prefab           [Transform (2.5, 2.5, 0), SpriteRenderer (Units), UnitMovementView, HealthBar]
├── Enemy_Conscript_Prefab       [Transform (6.5, 2.5, 0), SpriteRenderer (Units), UnitMovementView, HealthBar]
├── [UI_CombatHUD]               [UIDocument (CombatHUD.uxml), CombatHUDPresenter]
├── [UI_CardHand]                [UIDocument (CombatHandUI.uxml), DeckManager, CardHandController]
└── [GameController]             [CombatStateMachine, HitStopManager]
```

---

### Langkah 2.3: Konfigurasi Inspector & Wiring Komponen pada `[GameController]`
1. Di Hierarchy, klik kanan > **Create Empty**, beri nama `[GameController]`.
2. Di panel **Inspector**, klik tombol **Add Component** > ketik `CombatStateMachine` > tekan Enter.
3. Hubungkan slot referensi di Inspector `CombatStateMachine`:
   * **Deck Manager:** Seret GameObject `[UI_CardHand]` ke slot ini.
   * **Hand Controller:** Seret GameObject `[UI_CardHand]` ke slot ini.
4. Klik **Add Component** > ketik `HitStopManager` > tekan Enter.

---

### Langkah 2.4: Konfigurasi Main Camera
1. Klik `Main Camera` di Hierarchy:
   * **Position:** `X: 7.5, Y: 7.5, Z: -10`.
   * **Projection:** `Orthographic`.
   * **Size:** `8.5`.
   * **Post Processing:** Centang `True`.
2. Klik **Add Component** > ketik `CameraShaker` > tekan Enter.

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Core/FSM/ICombatState.cs`
```csharp
using System.Collections;

namespace PilotGame.Core.FSM
{
    public interface ICombatState
    {
        IEnumerator Enter();
        void UpdateState();
        IEnumerator Exit();
    }
}
```

---

### B. `Assets/Scripts/Core/FSM/CombatStateMachine.cs`
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.Grid;
using PilotGame.UI;
using PilotGame.Units;

namespace PilotGame.Core.FSM
{
    /// <summary>
    /// Pengendali Utama Finite State Machine (FSM) Siklus Pertempuran.
    /// </summary>
    public class CombatStateMachine : MonoBehaviour
    {
        [Header("Scene Dependencies")]
        [SerializeField] private DeckManager _deckManager;
        [SerializeField] private CardHandController _handController;

        public GridDataModel GridModel { get; private set; }
        public CombatMathEngine MathEngine { get; private set; }
        public EnemyAICalculator AICalculator { get; private set; }

        private ICombatState _currentState;

        public IntentPhaseState IntentState { get; private set; }
        public PlayerPhaseState PlayerState { get; private set; }
        public EnemyPhaseState EnemyState { get; private set; }
        public RoundResetPhaseState ResetState { get; private set; }

        private void Awake()
        {
            GridModel = new GridDataModel();
            MathEngine = new CombatMathEngine();
            AICalculator = new EnemyAICalculator(GridModel);

            // Inisialisasi States
            IntentState = new IntentPhaseState(this);
            PlayerState = new PlayerPhaseState(this, _deckManager, _handController);
            EnemyState = new EnemyPhaseState(this);
            ResetState = new RoundResetPhaseState(this, _deckManager);
        }

        private void Start()
        {
            // Set posisi awal Nabu (2,2) dan Musuh Conscript (6,2)
            GridModel.SetOccupant(new Vector2Int(2, 2), 1);
            GridModel.SetOccupant(new Vector2Int(6, 2), 2);

            ChangeState(IntentState);
        }

        /// <summary>
        /// Mengganti state FSM secara asinkron (Coroutines) dengan memanggil Exit() state lama
        /// dan Enter() pada state baru untuk transisi yang mulus.
        /// </summary>
        public void ChangeState(ICombatState nextState)
        {
            if (_currentState != null)
            {
                StartCoroutine(TransitionRoutine(nextState));
            }
            else
            {
                _currentState = nextState;
                StartCoroutine(_currentState.Enter());
            }
        }

        private IEnumerator TransitionRoutine(ICombatState nextState)
        {
            // 1. Jalankan proses pembersihan / pelepasan listener dari state sebelumnya
            yield return StartCoroutine(_currentState.Exit());

            // 2. Ganti referensi state aktif
            _currentState = nextState;

            // 3. Jalankan inisialisasi / trigger animasi pada state yang baru
            yield return StartCoroutine(_currentState.Enter());
        }

        private void Update()
        {
            _currentState?.UpdateState();
        }
    }
}
```

---

### C. `Assets/Scripts/Core/FSM/IntentPhaseState.cs`
```csharp
using System.Collections;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;

namespace PilotGame.Core.FSM
{
    /// <summary>
    /// Fase 1: Intent Phase (Fase Niat Musuh).
    /// Musuh menghitung target dan memproyeksikan area bahaya merah sebelum giliran pemain dimulai.
    /// </summary>
    public class IntentPhaseState : ICombatState
    {
        private readonly CombatStateMachine _fsm;

        public IntentPhaseState(CombatStateMachine fsm)
        {
            _fsm = fsm;
        }

        public IEnumerator Enter()
        {
            // 1. Publikasikan pergantian fase ke IntentPhase
            CombatEvents.OnPhaseChanged?.Invoke(CombatPhase.IntentPhase);
            yield return new WaitForSeconds(0.6f);

            // 2. Kalkulasi niat serangan AI Musuh:
            // Musuh ID 2 di posisi (6,2) merencanakan serangan linear ke Nabu di posisi (2,2)
            _fsm.AICalculator.PlanLinearAttack(2, new Vector2Int(6, 2), new Vector2Int(2, 2), 4);

            // 3. Beri jeda 0.8 detik agar pemain dapat membaca telegraf bahaya musuh
            yield return new WaitForSeconds(0.8f);

            // 4. Lanjut secara otomatis ke PlayerPhase
            _fsm.ChangeState(_fsm.PlayerState);
        }

        public void UpdateState() { }

        public IEnumerator Exit()
        {
            yield return null;
        }
    }
}
```

---

### D. `Assets/Scripts/Core/FSM/PlayerPhaseState.cs`
```csharp
using System.Collections;
using UnityEngine;
using PilotGame.Cards;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.UI;

namespace PilotGame.Core.FSM
{
    /// <summary>
    /// Fase 2: Player Phase (Giliran Aksi Pemain).
    /// Menunggu pemain memainkan 1 kartu aksi wajib (1 Turn = 1 Card) dengan validasi ketat.
    /// </summary>
    public class PlayerPhaseState : ICombatState
    {
        private readonly CombatStateMachine _fsm;
        private readonly DeckManager _deck;
        private readonly CardHandController _ui;
        private bool _cardPlayedThisTurn;

        public PlayerPhaseState(CombatStateMachine fsm, DeckManager deck, CardHandController ui)
        {
            _fsm = fsm;
            _deck = deck;
            _ui = ui;
        }

        public IEnumerator Enter()
        {
            _cardPlayedThisTurn = false;
            CombatEvents.OnPhaseChanged?.Invoke(CombatPhase.PlayerPhase);

            // Berlangganan event penjatuhan kartu pemain
            CombatEvents.OnCardPlayed += HandleCardPlayed;
            yield return null;
        }

        public void UpdateState() { }

        /// <summary>
        /// Handler saat pemain melepaskan/menjatuhkan kartu ke grid arena.
        /// </summary>
        private void HandleCardPlayed(object cardObj, Vector2Int targetCoord)
        {
            // Cegah input ganda jika kartu sudah dimainkan pada giliran ini
            if (_cardPlayedThisTurn || !(cardObj is CardData card)) return;

            Vector2Int playerPos = new Vector2Int(2, 2);

            // Validasi keabsahan kartu (Jarak, Fase, Rintangan, Stealth) via CardPlayValidator
            if (CardPlayValidator.CanPlayCard(card, playerPos, targetCoord, _fsm.GridModel, CombatPhase.PlayerPhase, out string reason))
            {
                _cardPlayedThisTurn = true; // Kunci input pemain

                // Eksekusi efek kartu sesuai klasifikasi ActionType
                if (card.ActionType == CardActionType.Attack)
                {
                    DamagePayload result = _fsm.MathEngine.CalculateDamage(2, card.BaseDamage, 0);
                    CombatEvents.OnSkillExecuted?.Invoke(1, 1, targetCoord);
                    CombatEvents.OnUnitDamaged?.Invoke(result);
                }
                else if (card.ActionType == CardActionType.Movement)
                {
                    CombatEvents.OnUnitMoved?.Invoke(new UnitMovePayload(1, playerPos, targetCoord));
                }

                // Buang kartu ke discard pile dan perbarui UI tangan
                _deck.DiscardCard(card);
                _ui.RefreshHandVisuals();

                // Lanjut ke fase giliran musuh setelah jeda animasi singkat
                _fsm.StartCoroutine(DelayToEnemyPhase());
            }
            else
            {
                Debug.LogWarning($"[PlayerPhase] Kartu tidak sah: {reason}");
            }
        }

        private IEnumerator DelayToEnemyPhase()
        {
            yield return new WaitForSeconds(1.2f);
            _fsm.ChangeState(_fsm.EnemyState);
        }

        public IEnumerator Exit()
        {
            // Lepas event listener saat keluar dari PlayerPhase agar tidak terpanggil ganda
            CombatEvents.OnCardPlayed -= HandleCardPlayed;
            yield return null;
        }
    }
}
```

---

### E. `Assets/Scripts/Core/FSM/EnemyPhaseState.cs` & `RoundResetPhaseState.cs`
```csharp
using System.Collections;
using UnityEngine;
using PilotGame.Core.Data;
using PilotGame.Core.Events;
using PilotGame.UI;

namespace PilotGame.Core.FSM
{
    /// <summary>
    /// Fase 3: Enemy Phase (Eksekusi Serangan Musuh).
    /// Musuh mengeksekusi niat aksi yang telah direncanakan sebelumnya ke arah pemain.
    /// </summary>
    public class EnemyPhaseState : ICombatState
    {
        private readonly CombatStateMachine _fsm;

        public EnemyPhaseState(CombatStateMachine fsm) { _fsm = fsm; }

        public IEnumerator Enter()
        {
            CombatEvents.OnPhaseChanged?.Invoke(CombatPhase.EnemyPhase);
            yield return new WaitForSeconds(0.6f);

            // Musuh mengeksekusi serangan yang telah ditelegrafkan
            DamagePayload dmg = _fsm.MathEngine.CalculateDamage(1, 5, 0);
            CombatEvents.OnSkillExecuted?.Invoke(2, 1, new Vector2Int(2, 2));
            CombatEvents.OnUnitDamaged?.Invoke(dmg);

            yield return new WaitForSeconds(1.0f);
            _fsm.ChangeState(_fsm.ResetState);
        }

        public void UpdateState() { }
        public IEnumerator Exit() { yield return null; }
    }

    /// <summary>
    /// Fase 4: Round Reset Phase (Evaluasi Efek Status & Persiapan Ronde Baru).
    /// Mengurangi durasi DoT/Buff, membersihkan highlight ubin, dan menarik kartu hingga penuh.
    /// </summary>
    public class RoundResetPhaseState : ICombatState
    {
        private readonly CombatStateMachine _fsm;
        private readonly DeckManager _deck;

        public RoundResetPhaseState(CombatStateMachine fsm, DeckManager deck)
        {
            _fsm = fsm;
            _deck = deck;
        }

        public IEnumerator Enter()
        {
            CombatEvents.OnPhaseChanged?.Invoke(CombatPhase.RoundResetPhase);
            CombatEvents.OnClearAllHighlights?.Invoke(); // Bersihkan telegraf ubin merah/kuning

            // 1. Kurangi durasi seluruh status abnormal (Bleed, Stun, Vulnerable, dsb.)
            _fsm.MathEngine.TickStatusEffects();

            // 2. Isi kembali kartu di tangan pemain hingga 5 kartu
            _deck.DrawToFullHand();

            yield return new WaitForSeconds(0.8f);

            // 3. Putaran selesai -> Kembali ke IntentPhase untuk ronde berikutnya
            _fsm.ChangeState(_fsm.IntentState);
        }

        public void UpdateState() { }
        public IEnumerator Exit() { yield return null; }
    }
}
```

---

## 🧪 4. Langkah Verifikasi di Unity Editor (Playable Loop)
1. Buka Scene `MainBattleScene.unity`.
2. Klik tombol **Play** di toolbar atas Unity Editor.
3. Amati urutan gameplay berjalan otomatis:
   * **Intent Phase (0.0s - 1.4s):** Musuh menyalakan ubin merah dan badge intent.
   * **Player Phase (1.4s+):** Banner *"YOUR TURN"* meluncur turun, tangan terisi 5 kartu.
   * **Aksi Pemain:** Seret kartu `Super Punch` ke ubin musuh.
   * **Umpan Balik Visual:** Efek suara berbunyi, partikel pukulan meledak, musuh berkedip merah dan menerima -12 damage, kamera berguncang dengan jeda hit-stop.
   * **Enemy Phase:** Musuh membalas menyerang Nabu.
   * **Round Reset:** Tangan diisi kembali hingga 5 kartu dan putaran baru dimulai secara mulus.
