---
id: TICKET-08
title: Model Data & Generator Peta Rute Bercabang Menara Babel
status: Todo
priority: High
labels: [Data, MapSystem, Progression, MacroLoop, Fase2]
---

# Deskripsi
Membangun **model data peta eksplorasi Menara Babel** yang bercabang (*branching node map*) sebagai macro loop roguelike. Peta terdiri dari simpul-simpul (*node*) yang masing-masing merepresentasikan satu encounter (pertempuran, event misteri, api unggun, atau toko). Pemain memilih jalur dari node aktif ke node berikutnya di layer atasnya.

Arsitektur ini menghubungkan arena pertempuran (Fase 1) ke sistem meta-game Fase 2. Tiket ini adalah data & logic layer — tanpa UI.

## Acceptance Criteria
- [ ] `MapNodeData.cs` (`ScriptableObject`) memuat:
  - `string NodeId` — ID unik node (contoh: `"node_01_01"`).
  - `MapNodeType NodeType` — Enum: `Combat`, `MysteryEvent`, `CampfireRest`, `Shop`, `BossEncounter`.
  - `List<string> ConnectedNextNodeIds` — ID node tujuan yang dapat dipilih pemain (cabang kanan/kiri).
  - `int ChapterIndex` — Chapter ke berapa node ini berada.
  - `int LayerIndex` — Layer vertikal dalam chapter (0 = pertama, max = boss).
  - `[CreateAssetMenu]` attribute menggunakan menuName: `"PilotGame/Map Node Data"`.
- [ ] `MapGenerator.cs` (Pure C#, Non-MonoBehaviour):
  - Method `MapLayout GenerateChapter(int chapterIndex, int layerCount, int nodesPerLayer)`:
    - Membuat grid node dengan probabilitas: Combat 60%, MysteryEvent 15%, CampfireRest 15%, Shop 10%.
    - Layer terakhir selalu `BossEncounter`.
    - Setiap node terhubung ke 1-2 node di layer berikutnya.
    - Tidak ada node yang terisolir (semua dapat dijangkau dari node start).
  - Output berupa `MapLayout` (class/struct dengan `List<MapNodeData>` dan adjacency data).
- [ ] `MapLayout.cs` — Data class yang merepresentasikan seluruh peta satu chapter.
- [ ] `MapNodeType` enum didefinisikan di `CombatTypes.cs` (bersama enum lainnya) atau di file terpisah `MapTypes.cs`.
- [ ] Unit test `MapGeneratorTests.cs` (NUnit EditMode):
  - Test bahwa output selalu memiliki minimal `layerCount × 2` node.
  - Test bahwa layer terakhir selalu berisi `BossEncounter`.
  - Test bahwa tidak ada node orphan (setiap node dapat dijangkau dari root).

## Target Lingkup File (Affected Files)
- `Assets/Scripts/Core/Data/MapNodeData.cs`
- `Assets/Scripts/Map/MapGenerator.cs`
- `Assets/Scripts/Map/MapLayout.cs`
- `Assets/Scripts/Core/Data/MapTypes.cs` (jika enum dipisah)
- `Assets/Tests/EditMode/MapGeneratorTests.cs`

## Dependensi
- **Bergantung pada:** TICKET-01 (struct/enum pattern).
- **Digunakan oleh:** TICKET-09 (UI pemilihan node), TICKET-10 (draft reward), TICKET-11 (event misteri).

## Catatan Teknis
- `MapNodeData` adalah ScriptableObject yang bisa di-serialized per run, atau dibuat secara procedural runtime. Untuk Fase 2, cukup gunakan pendekatan runtime generation.
- Probabilitas node type menggunakan `Random.Range` dengan weighted probability array.
- Struktur data peta: `List<List<MapNodeData>>` — outer list = layers, inner list = nodes per layer.

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
