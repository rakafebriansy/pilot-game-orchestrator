# System Design

## 1. Arsitektur Umum
Aplikasi ini menggunakan pola arsitektur **MVVM (Model-View-ViewModel)** standar SwiftUI. Hal ini dipilih untuk memisahkan antara UI (*View*) dengan logika pengambilan data ML (*ViewModel*), serta memastikan keterujian (*testability*) yang mudah melalui XCTest.

## 2. Diagram Arsitektur
Diagram arsitektur sistem menunjukkan interaksi antara komponen UI, ViewModel, Service layer (Kamera & Galeri), dan Vision Framework.
Lihat Diagram: [System Architecture](file:///Users/rakafebriansyahputra/Developer/repositories/projects/personal-challenge-project/personal-challenge-ai-orchestrator/nodes/personal-challenge/docs/diagrams/system-architecture.puml)

## 3. Komponen Utama
*   **Views (SwiftUI):** 
    *   `MainView`: Titik masuk aplikasi, menampilkan tab atau tombol seleksi mode.
    *   `CameraScannerView`: Menampilkan *live camera feed* dengan overlay *bounding box*.
    *   `TranslationResultSheet`: Menampilkan teks hasil terjemahan.
*   **ViewModels:**
    *   `ScannerViewModel`: Mengelola state aplikasi (apakah sedang memindai, loading model, atau menampilkan hasil). Menjembatani request dari View ke Service.
*   **Services:**
    *   `CameraService`: Berinteraksi dengan `AVFoundation` untuk mengambil frame dari kamera.
    *   `MLVisionService`: Inti dari pemrosesan logika. Menerima gambar atau pixel buffer, menjalankannya melalui `VNCoreMLModel`, dan mem-parsing hasil prediksi/klasifikasi untuk dikembalikan sebagai *String* Latin.

## 4. Alur Data (Data Flow)
1. Pengguna membuka mode kamera. `CameraService` menginisialisasi `AVCaptureSession`.
2. Setiap frame yang tertangkap dikonversi ke `CVPixelBuffer`.
3. `ScannerViewModel` meneruskan buffer tersebut ke `MLVisionService`.
4. `MLVisionService` memproses buffer dengan `VNImageRequestHandler`.
5. Hasil observasi (`VNRecognizedTextObservation` atau `VNClassificationObservation` tergantung jenis model dari CreateML) diolah menjadi *String*.
6. *String* dikirim kembali ke `ScannerViewModel` dan men-trigger pembaruan pada *UI State*.

## 5. Pertimbangan Keamanan & Performa
*   Semua pemrosesan bersifat *on-device*, sehingga privasi data pengguna sepenuhnya terjaga.
*   Kamera *live-feed* harus menggunakan *background queue* (`DispatchQueue.global(qos: .userInteractive)`) saat memproses frame agar tidak memblokir *Main Thread* (UI Thread).
