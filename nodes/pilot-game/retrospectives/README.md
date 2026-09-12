# Retrospectives Directory (`retrospectives/`)

Direktori ini adalah pusat **Evaluasi Diri (Self-Healing) AI Agent**. Tujuan utama folder ini adalah untuk melacak, mencatat, dan menganalisis setiap kesalahan, halusinasi, atau keputusan keliru yang dilakukan oleh AI selama siklus pengembangan berlangsung.

Melalui mekanisme ini, AI Agent dipaksa untuk belajar dari kegagalannya, bukannya mengulangi kesalahan yang sama secara terus-menerus.

## Cara Kerja Workflow Retrospective
1. **Pencatatan Masalah:** Saat AI Agent menemui hambatan teknis kronis, gagal memperbaiki *bug* setelah beberapa kali percobaan, salah menginterpretasi instruksi, atau ditegur oleh pengguna, AI **WAJIB** mencatat kejadian tersebut ke dalam file `RETROSPECTIVE.md`.
2. **Pencatatan Resolusi Konflik (Merge):** Apabila terjadi proses *merge branch* (terutama yang menimbulkan konflik), AI Agent atau *developer* wajib mencatat detail konflik dan langkah resolusinya secara presisi ke dalam file `MERGE_HISTORY.md`.
3. **Analisis Mendalam:** Setiap catatan tidak sekadar berisi kalimat "saya salah", melainkan analisis forensik mengenai tanda-tanda (*symptoms*) kesalahan, konteks terjadinya, dan solusi pamungkas yang menyelesaikannya.
4. **Konversi ke Aturan Konkret:** Secara berkala, catatan-catatan evaluasi di dalam folder ini **AKAN DIKONVERSI** menjadi aturan teknis baku yang diabadikan ke dalam pedoman spesifik proyek (`guidelines/project-context.md`). Dengan demikian, memori pembelajaran AI menjadi permanen di tingkat proyek.
5. **Pembersihan Log (Token Optimization):** Demi menjaga efisiensi konteks AI dan menghemat pembacaan *token*, setiap entri log di dalam `RETROSPECTIVE.md` yang telah berhasil dikonversi dan dipindahkan ke dalam `guidelines/project-context.md` **WAJIB DIHAPUS** secara permanen dari file `RETROSPECTIVE.md`. Ini memastikan file retro tersebut hanya berisi masalah baru yang sedang hangat ditangani.

## Kewajiban AI Agent
Setiap AI Agent yang beroperasi di ekosistem ini wajib membaca `RETROSPECTIVE.md` dan `MERGE_HISTORY.md` (dan pedoman spesifik proyek yang dihasilkan darinya) sebelum memulai tugas-tugas krusial, **HANYA JIKA** file tersebut benar-benar berisi entri log permasalahan (bukan sekadar kerangka template kosong). Hal ini guna memastikan AI tidak membuang pembacaan token yang percuma, dan tidak terperosok ke dalam lubang masalah yang pernah diselesaikan sebelumnya.
