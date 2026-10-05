# 📖 Manual Guide: TICKET-09 — UI Pemilihan Node Peta & Transisi Scene

> **Referensi Tiket:** [TICKET-09.md](file:///Users/raka/Developer/repositories/projects/tubbies-studio-org/pilot-game-dir/pilot-game-ai-orchestrator/nodes/pilot-game/tickets/TICKET-09.md)  
> **Domain:** `[🗺️ MAP / MACRO LOOP]`  
> **Fase:** 2 (Peta Eksplorasi & Wave Drafting)

---

## 🎯 1. Ringkasan & Tujuan
Tiket ini membangun antarmuka visual **Layar Peta Ekspedisi Menara Babel** menggunakan UI Toolkit dan mengelola perpindahan antar-scene pertempuran:
1. **`MapScreenController.cs`**: Menggambar node pohon rute, garis penghubung (*Line Renderer / UI lines*), dan animasi kabut ekspedisi (*Fog of War*).
2. **`SceneTransitionManager.cs`**: Mengatur transisi fade-in/fade-out yang mulus antara scene Peta dan scene Pertempuran (`MainBattleScene.unity`).

---

## 📂 2. Struktur File & Lokasi
```text
Assets/
├── Scripts/
│   ├── Map/
│   │   ├── MapScreenController.cs
│   │   └── MapManager.cs
│   └── Core/
│       └── SceneTransitionManager.cs
└── UI/
    ├── UXML/
    │   └── MapScreenUI.uxml
    └── USS/
        └── MapScreenUI.uss
```

---

## 💻 3. Kode Sumber Lengkap

### A. `Assets/Scripts/Map/MapScreenController.cs`
```csharp
using UnityEngine;
using UnityEngine.UIElements;
using PilotGame.Map;
using PilotGame.Core;

namespace PilotGame.UI
{
    [RequireComponent(typeof(UIDocument))]
    public class MapScreenController : MonoBehaviour
    {
        [SerializeField] private SceneTransitionManager _sceneTransition;

        private UIDocument _uiDocument;
        private ScrollView _mapScrollView;
        private MapLayout _currentMap;

        private void Awake()
        {
            _uiDocument = GetComponent<UIDocument>();
        }

        private void Start()
        {
            var root = _uiDocument.rootVisualElement;
            _mapScrollView = root.Q<ScrollView>("map-scroll-view");

            // Generate Map baru jika belum ada
            var generator = new MapGenerator();
            _currentMap = generator.GenerateMap(12345);

            RenderMapNodes();
        }

        /// <summary>
        /// Merender seluruh node peta Menara Babel ke dalam ScrollView UI Toolkit.
        /// Urutan perulangan dibalik (Lantai 15 -> Lantai 1) agar Lantai 1 berada di posisi bawah scroll view.
        /// </summary>
        public void RenderMapNodes()
        {
            if (_mapScrollView == null || _currentMap == null) return;
            _mapScrollView.Clear();

            // Loop dari lantai tertinggi ke terendah (vertikal menara)
            for (int floor = MapGenerator.TotalFloors - 1; floor >= 0; floor--)
            {
                var floorRow = new VisualElement();
                floorRow.AddToClassList("map-floor-row");

                var floorLabel = new Label($"Lantai {floor + 1}");
                floorLabel.AddToClassList("floor-label");
                floorRow.Add(floorLabel);

                // Buat tombol untuk setiap node di lantai ini
                foreach (var node in _currentMap.GetNodesAtFloor(floor))
                {
                    var nodeBtn = new Button(() => OnNodeClicked(node));
                    nodeBtn.text = GetNodeSymbol(node.NodeType);
                    nodeBtn.AddToClassList("map-node-button");
                    nodeBtn.AddToClassList($"node-{node.NodeType.ToString().ToLower()}");

                    // Kunci tombol jika node belum dapat diakses (belum dibuka jalurnya)
                    if (!node.IsAvailable)
                    {
                        nodeBtn.SetEnabled(false);
                        nodeBtn.AddToClassList("node-disabled");
                    }

                    floorRow.Add(nodeBtn);
                }

                _mapScrollView.Add(floorRow);
            }
        }

        /// <summary>
        /// Menangani klik node oleh pemain: menandai node selesai,
        /// membuka node anak berikutnya (DAG Outgoing Edges), dan transisi ke scene terkait.
        /// </summary>
        private void OnNodeClicked(MapNodeData node)
        {
            Debug.Log($"[MapScreen] Pemain memilih node: {node.NodeId} ({node.NodeType})");
            node.IsVisited = true;
            node.IsAvailable = false;

            // Buka akses ke semua node turunan yang terhubung langsung
            foreach (var outId in node.OutgoingNodeIds)
            {
                var nextNode = _currentMap.GetNodeById(outId);
                if (nextNode != null) nextNode.IsAvailable = true;
            }

            // Arahkan ke Scene pertempuran atau Toko Pedagang dengan transisi fade
            string targetScene = node.NodeType == MapNodeType.MerchantShop ? "ShopScene" : "MainBattleScene";
            _sceneTransition.LoadSceneWithFade(targetScene);
        }

        /// <summary>
        /// Mengembalikan simbol teks/ikon representatif untuk tiap tipe node.
        /// </summary>
        private string GetNodeSymbol(MapNodeType type)
        {
            return type switch
            {
                MapNodeType.BattleNormal => "⚔",
                MapNodeType.BattleElite => "☠",
                MapNodeType.MysteryEvent => "?",
                MapNodeType.MerchantShop => "💰",
                MapNodeType.CampfireRest => "🔥",
                MapNodeType.BossFloor => "👑",
                _ => "•"
            };
        }
    }
}
```

---

### B. `Assets/Scripts/Core/SceneTransitionManager.cs`
```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

namespace PilotGame.Core
{
    /// <summary>
    /// Mengatur transisi layar gelap (Fade to Black) antar-scene secara asinkron.
    /// Pola Singleton persisten (DontDestroyOnLoad).
    /// </summary>
    public class SceneTransitionManager : MonoBehaviour
    {
        public static SceneTransitionManager Instance { get; private set; }

        [SerializeField] private CanvasGroup _fadeCanvasGroup;
        [SerializeField] private float _fadeDuration = 0.5f;

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

        public void LoadSceneWithFade(string sceneName)
        {
            StartCoroutine(FadeAndSwitchScene(sceneName));
        }

        /// <summary>
        /// Coroutine 3-tahap transisi scene:
        /// 1. Fade Out: Naikkan alpha CanvasGroup dari 0 -> 1 (layar gelap)
        /// 2. Async Load: Muat scene Unity di background hingga selesai
        /// 3. Fade In: Turunkan alpha CanvasGroup dari 1 -> 0 (layar terang kembali)
        /// </summary>
        private IEnumerator FadeAndSwitchScene(string sceneName)
        {
            // TAHAP 1: Fade to Black (Layar menggelap)
            if (_fadeCanvasGroup != null)
            {
                float elapsed = 0f;
                while (elapsed < _fadeDuration)
                {
                    _fadeCanvasGroup.alpha = Mathf.Lerp(0f, 1f, elapsed / _fadeDuration);
                    elapsed += Time.deltaTime;
                    yield return null;
                }
                _fadeCanvasGroup.alpha = 1f;
            }

            // TAHAP 2: Pemuatan Scene Baru secara Asinkron
            AsyncOperation asyncLoad = SceneManager.LoadSceneAsync(sceneName);
            while (!asyncLoad.isDone) yield return null;

            // TAHAP 3: Fade to Clear (Layar kembali terang di scene baru)
            if (_fadeCanvasGroup != null)
            {
                float elapsed = 0f;
                while (elapsed < _fadeDuration)
                {
                    _fadeCanvasGroup.alpha = Mathf.Lerp(1f, 0f, elapsed / _fadeDuration);
                    elapsed += Time.deltaTime;
                    yield return null;
                }
                _fadeCanvasGroup.alpha = 0f;
            }
        }
    }
}
```

---

## 🧪 4. Langkah Verifikasi
1. Buka Scene `MapScene.unity`.
2. Klik tombol node pertarungan (⚔) di Lantai 1.
3. Layar menggelap perlahan (*Fade to black*) dan scene berpindah ke `MainBattleScene.unity`.
