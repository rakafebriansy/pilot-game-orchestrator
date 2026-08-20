# Panduan Pengerjaan Manual: TICKET-01 & TICKET-03 (Setup MVVM & CoreML)

Karena Anda ingin belajar dan mengerjakannya secara manual, ikuti langkah-langkah di bawah ini secara berurutan di dalam Xcode Anda.

---

## Tahap 1: Membersihkan "Boilerplate" SwiftData Xcode

Saat pertama kali dibuat, Xcode menyertakan kode database *SwiftData* bawaan yang tidak kita perlukan untuk aplikasi sederhana ini. Mari kita hapus:

1. Di panel kiri Xcode (Project Navigator), cari file **`Item.swift`**.
2. Klik kanan pada file tersebut ➔ pilih **Delete** ➔ **Move to Trash**.
3. Buka file **`personal_challengeApp.swift`**.
4. Hapus semua kode di dalamnya, dan ganti (*copy-paste*) menjadi sangat bersih seperti ini:

```swift
import SwiftUI

@main
struct personal_challengeApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

---

## Tahap 2: Membuat Struktur Folder MVVM

Agar kode rapi, mari buat struktur foldernya:
1. Klik kanan pada folder kuning `personal-challenge` di kiri atas Xcode.
2. Pilih **New Group**.
3. Beri nama grup baru tersebut: **`Views`**.
4. Ulangi langkah 1-3 untuk membuat grup **`ViewModels`** dan **`Services`**.
5. Pindahkan file `ContentView.swift` (dengan cara di-*drag*) ke dalam folder **`Views`**.

*(Jika sudah, struktur folder Anda di sebelah kiri harusnya terlihat rapi: Views, ViewModels, Services).*

---

## Tahap 3: Membuat `MLVisionService` (Otak AI-nya)

Ini adalah file terpenting yang akan memuat model `AksaraJawaModel.mlmodel` yang baru Anda masukkan dan membacanya menggunakan kerangka kerja *Vision*.

1. Klik kanan pada folder **`Services`** ➔ **New File...**
2. Pilih **Swift File** ➔ Klik **Next**.
3. Beri nama **`MLVisionService.swift`** ➔ Klik **Create**.
4. *Copy* dan *Paste* seluruh kode berikut ke dalam file tersebut:

```swift
import Foundation
import CoreML
import Vision
import CoreImage

class MLVisionService {
    // Properti untuk menyimpan model Vision
    private var visionModel: VNCoreMLModel?
    
    init() {
        setupModel()
    }
    
    private func setupModel() {
        do {
            // 1. Memuat model bawaan dari file .mlmodel Anda.
            // (Pastikan namanya sesuai dengan file .mlmodel Anda, misal: AksaraJawaModel)
            let configuration = MLModelConfiguration()
            let coreMLModel = try AksaraJawaModel(configuration: configuration).model
            
            // 2. Memasukkannya ke dalam Vision Model
            visionModel = try VNCoreMLModel(for: coreMLModel)
            print("Berhasil memuat ML Model!")
        } catch {
            print("Gagal memuat ML Model: \(error.localizedDescription)")
        }
    }
    
    // Fungsi untuk memproses gambar
    func classifyImage(image: CGImage, completion: @escaping (String) -> Void) {
        guard let model = visionModel else {
            completion("Error: Model belum siap.")
            return
        }
        
        // Membuat request klasifikasi
        let request = VNCoreMLRequest(model: model) { (request, error) in
            if let error = error {
                completion("Gagal memproses: \(error.localizedDescription)")
                return
            }
            
            // Mengambil hasil prediksi teratas (Top 1)
            if let results = request.results as? [VNClassificationObservation], let firstResult = results.first {
                // Mengembalikan nama kelas huruf (misal: "ha") beserta tingkat keyakinannya
                let confidence = Int(firstResult.confidence * 100)
                let hasil = "\(firstResult.identifier.uppercased()) (\(confidence)%)"
                completion(hasil)
            } else {
                completion("Huruf tidak dikenali.")
            }
        }
        
        // Mengeksekusi request
        let handler = VNImageRequestHandler(cgImage: image, options: [:])
        DispatchQueue.global(qos: .userInitiated).async {
            do {
                try handler.perform([request])
            } catch {
                completion("Gagal mengeksekusi request: \(error.localizedDescription)")
            }
        }
    }
}
```

---

## Tahap 4: Menghubungkan UI dengan Model (Masih Placeholder)

Untuk memastikan bahwa aplikasinya bisa di-*build* tanpa error, kita bersihkan dulu `ContentView.swift`.

1. Buka file **`ContentView.swift`** di dalam folder **`Views`**.
2. Hapus isinya dan ganti menjadi:

```swift
import SwiftUI

struct ContentView: View {
    @State private var hasilTerjemahan: String = "Belum ada hasil"
    
    // Inisialisasi Service ML
    let mlService = MLVisionService()

    var body: some View {
        VStack(spacing: 20) {
            Image(systemName: "camera.viewfinder")
                .resizable()
                .scaledToFit()
                .frame(width: 100, height: 100)
                .foregroundColor(.blue)
            
            Text("Penerjemah Aksara Jawa")
                .font(.title)
                .bold()
            
            Text(hasilTerjemahan)
                .font(.headline)
                .padding()
                .background(Color.gray.opacity(0.2))
                .cornerRadius(10)
            
            Spacer()
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```

---

### Verifikasi Mandiri (Testing)
Jika Anda sudah mengetik / *copy-paste* semua kode di atas, cobalah tekan tombol **`Cmd + B`** (Build) di Xcode Anda.

Jika tulisan **"Build Succeeded"** muncul di tengah atas layar Xcode, artinya Anda telah sukses membersihkan proyek dan menyambungkan *CoreML* ke aplikasi Anda secara manual! 

Beri tahu saya jika Anda mengalami *error* (tulisan merah) atau jika sudah berhasil *Build Succeeded*, agar kita bisa catat pencapaian ini di *Changelog*!
