# Panduan Pengerjaan Manual 02: TICKET-04 (Galeri Foto & ViewModel)

Selamat! Di TICKET-03, Anda berhasil membuat `MLVisionService` (Sang Koki yang siap memproses gambar dengan AI). 
Sekarang di TICKET-04, kita butuh jembatan penghubung (**ViewModel**) dan antarmuka pengguna (**View**) untuk membiarkan pengguna memilih foto dari galeri HP.

Mari kita lanjutkan!

---

## Tahap 1: Membuat ViewModel (Sang Penghubung)

Kita butuh *State Manager* agar UI (layar) tahu kapan harus me-loading gambar dan apa hasil teksnya.

1. Di Xcode, klik kanan pada folder **`ViewModels`** ➔ **New File...**
2. Pilih **Swift File** ➔ Klik **Next** ➔ Beri nama **`ScannerViewModel.swift`** ➔ **Create**.
3. *Copy* dan *Paste* kode berikut:

```swift
import SwiftUI
import CoreImage

class ScannerViewModel: ObservableObject {
    // @Published agar UI otomatis berubah saat nilainya diubah
    @Published var selectedImage: UIImage? = nil
    @Published var hasilTerjemahan: String = "Pilih foto huruf Jawa untuk diterjemahkan."
    @Published var isProcessing: Bool = false
    
    // Memanggil Service AI yang Anda buat sebelumnya
    private let mlService = MLVisionService()
    
    // Fungsi ini dipanggil dari UI saat pengguna selesai memilih foto
    func prosesGambar(_ image: UIImage) {
        self.selectedImage = image
        self.isProcessing = true
        self.hasilTerjemahan = "Sedang menganalisis..."
        
        // Ubah UIImage menjadi CGImage (format yang diminta oleh Vision Framework)
        guard let cgImage = image.cgImage else {
            self.hasilTerjemahan = "Gagal membaca format gambar."
            self.isProcessing = false
            return
        }
        
        // Suruh Sang Koki (MLVisionService) bekerja!
        mlService.classifyImage(image: cgImage) { [weak self] hasil in
            // Kembali ke Main Thread (Jalur Utama) untuk mengupdate Layar/UI
            DispatchQueue.main.async {
                self?.hasilTerjemahan = hasil
                self?.isProcessing = false
            }
        }
    }
}
```

---

## Tahap 2: Menambahkan PhotosPicker ke UI (Layar)

Kini saatnya memperbarui tampilan depan aplikasi Anda! Di iOS 16+, mengambil foto dari galeri sangat mudah menggunakan kerangka kerja `PhotosUI`.

1. Buka file **`ContentView.swift`** di dalam folder **`Views`**.
2. Timpa (*replace*) seluruh isi kodenya dengan yang baru ini:

```swift
import SwiftUI
import PhotosUI

struct ContentView: View {
    // Menyambungkan ViewModel ke View
    @StateObject private var viewModel = ScannerViewModel()
    
    // Variabel internal untuk menyimpan pilihan PhotosPicker
    @State private var photoItem: PhotosPickerItem? = nil

    var body: some View {
        VStack(spacing: 30) {
            
            Text("Penerjemah Aksara Jawa")
                .font(.title2)
                .fontWeight(.bold)
                .padding(.top)
            
            // 1. Area Tampilan Gambar
            if let image = viewModel.selectedImage {
                Image(uiImage: image)
                    .resizable()
                    .scaledToFit()
                    .frame(height: 300)
                    .cornerRadius(15)
                    .shadow(radius: 5)
            } else {
                // Placeholder jika belum ada foto
                Rectangle()
                    .fill(Color.gray.opacity(0.2))
                    .frame(height: 300)
                    .cornerRadius(15)
                    .overlay(
                        VStack {
                            Image(systemName: "photo")
                                .font(.system(size: 50))
                                .foregroundColor(.gray)
                            Text("Belum ada foto")
                                .foregroundColor(.gray)
                        }
                    )
            }
            
            // 2. Teks Hasil Terjemahan
            if viewModel.isProcessing {
                ProgressView("Menganalisis...")
            } else {
                Text(viewModel.hasilTerjemahan)
                    .font(.title)
                    .bold()
                    .foregroundColor(.blue)
                    .multilineTextAlignment(.center)
            }
            
            Spacer()
            
            // 3. Tombol Galeri (PhotosPicker)
            PhotosPicker(selection: $photoItem, matching: .images, photoLibrary: .shared()) {
                Label("Pilih dari Galeri", systemImage: "photo.on.rectangle")
                    .font(.headline)
                    .foregroundColor(.white)
                    .padding()
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)
                    .cornerRadius(15)
            }
            .onChange(of: photoItem) { newItem in
                // Jika pengguna selesai memilih foto, ubah menjadi UIImage dan kirim ke ViewModel
                Task {
                    if let data = try? await newItem?.loadTransferable(type: Data.self),
                       let uiImage = UIImage(data: data) {
                        viewModel.prosesGambar(uiImage)
                    }
                }
            }
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```

---

## Tahap Akhir: Uji Coba Langsung di Simulator!

1. Tekan **`Cmd + R`** untuk me-*run* aplikasi Anda ke dalam Simulator iPhone.
2. Karena Simulator iPhone mungkin tidak punya foto huruf Aksara Jawa, buka peramban **Safari** *di dalam* Simulator tersebut, lalu cari "Gambar Huruf Hanacaraka" di Google, tekan lama gambarnya, dan pilih **Save to Photos**.
3. Buka lagi aplikasi Anda, klik **"Pilih dari Galeri"**, dan pilih gambar yang baru Anda simpan tadi.

Lihat keajaibannya, AI akan langsung menebak huruf tersebut! Beri tahu saya jika berhasil ya!
