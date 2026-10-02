---
id: TICKET-09
title: UI Pemilihan Node Peta & Transisi Scene
status: Todo
priority: High
labels: [UI, MapScreen, SceneTransition, Domain3, Fase2]
---

# Deskripsi
Membangun **layar peta eksplorasi Menara Babel** menggunakan Unity UI Toolkit — menampilkan node-node peta yang dihasilkan oleh `MapGenerator.cs` (TICKET-08), memungkinkan pemain mengklik node tujuan, dan menangani transisi scene antara layar peta dan arena pertempuran.

Ini adalah "Macro Loop Screen" — penghubung antara pertempuran satu dan pertempuran berikutnya.

## Acceptance Criteria
- [ ] `MapScreenController.cs` (MonoBehaviour):
  - Menerima referensi `MapLayout` dari `MapManager` (singleton atau ScriptableObject).
  - Method `RenderMap(MapLayout layout)` — menggambar node sebagai VisualElement di UI Toolkit.
  - Node aktif (bisa dipilih) memiliki state visual berbeda dari node terkunci.
  - Node yang sudah dilewati memiliki state visual "completed" (warna berbeda/icon centang).
  - Klik pada node aktif → highlight node tersebut sebagai "selected" → tombol "Confirm" muncul.
  - Klik "Confirm" → panggil `MapManager.SelectNode(nodeId)` → mulai transisi scene.
- [ ] `MapScreenUI.uxml` — layout UXML untuk layar peta dengan:
  - Background Menara Babel (gambar atau solid color bertema Mesopotamia).
  - Area scroll vertikal untuk peta jika layernya banyak.
  - Info panel di samping: nama node, tipe encounter, keterangan singkat.
- [ ] `MapManager.cs` (MonoBehaviour / Singleton):
  - Menyimpan state `MapLayout currentRun` dan `string currentNodeId`.
  - Method `void SelectNode(string nodeId)` — update `currentNodeId`, simpan state.
  - Method `void StartNodeEncounter()` — load scene yang sesuai berdasarkan `NodeType`:
    - `Combat` → load `MainBattleScene`.
    - `CampfireRest` → load `CampfireScene` (atau tampilkan panel overlay).
    - `MysteryEvent` → load `EventScene`.
    - `Shop` → load `ShopScene`.
- [ ] `SceneTransitionManager.cs`:
  - Method `void LoadScene(string sceneName, float fadeOutDuration = 0.5f)` — fade out → load scene → fade in menggunakan `UnityEngine.SceneManagement`.
  - Menggunakan `Coroutine` + `AsyncOperation` (tidak blocking).
- [ ] **Verifikasi:** Klik node Combat → fade out → MainBattleScene terbuka → selesai combat → kembali ke MapScene dengan node baru terbuka.

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Map/MapScreenController.cs`
- `Assets/Scripts/Map/MapManager.cs`
- `Assets/Scripts/Core/SceneTransitionManager.cs`
- `Assets/UI/UXML/MapScreenUI.uxml`
- `Assets/UI/USS/MapScreen.uss`
- `Assets/Scenes/MapScene.unity`

## Dependensi
- **Bergantung pada:** TICKET-08 (`MapLayout`, `MapNodeData`, `MapNodeType`), TICKET-07 (`MainBattleScene` harus sudah ada).
- **Digunakan oleh:** TICKET-10 (setelah combat → layar draft), TICKET-11 (event misteri dari node type).

## Catatan Teknis
- Gunakan `SceneManager.LoadSceneAsync` bukan `LoadScene` (blocking) untuk transisi yang mulus.
- State run (node mana yang sudah dilewati, HP Nabu saat ini) harus persisten saat berganti scene → simpan di `MapManager` yang tidak di-destroy (`DontDestroyOnLoad`).
- Untuk Fase 2 MVP, art map bisa menggunakan placeholder (node sebagai lingkaran berwarna, garis sebagai koneksi).

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  *(Akan diisi saat tiket dieksekusi)*
- **Keputusan Desain & Arsitektur:**
  *(Akan diisi saat tiket dieksekusi)*
- **Ringkasan File Terpengaruh:**
  - *(Akan diisi saat tiket dieksekusi)*
- **Catatan & Temuan Tak Terduga:**
  *(Akan diisi saat tiket dieksekusi)*
