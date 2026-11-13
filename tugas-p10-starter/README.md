# Segmentasi Polip pada Citra Kolonoskopi dengan U-Net

<!--
  TEMPLATE LAPORAN — Tugas P10 Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA.
  Petunjuk lengkap: https://mctm.web.id/ai-bm/tugas

  Cara memakai:
  - Ganti semua teks yang diawali "✍️" dengan tulisan kelompokmu, lalu hapus tanda ✍️-nya.
  - Blok komentar seperti ini adalah petunjuk; tidak tampil di GitHub. Boleh dihapus.
  - Gambar diambil dari folder hasil/ yang dibuat otomatis oleh notebook.
  - Jangan hapus judul-judul bagian (##).
-->

**Tugas P10 — Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA**

## Anggota kelompok

| Nama | NIM | Username GitHub | Kontribusi utama |
|---|---|---|---|
| Aiman Zakiy | 123430025 | zakiyaiman226 | Membuat repo, menjalankan skenario |
| Muhammad Rafqi Zulvikar | 123430122 | RafqiRapki | Membuat README.md |

## 1. Pertanyaan

Augmentasi apa yang membantu U-Net menemukan polip pada citra kolonoskopi, dan augmentasi apa yang tidak? Kenapa?

tidak ada augmentasi yang terbukti meningkatkan performa U-Net dibandingkan kondisi tanpa augmentasi. Dari dua metode augmentasi yang diuji, Rotation memberikan hasil yang lebih baik daripada Brightness & Contrast, tetapi keduanya masih berada di bawah baseline.

## 2. Data & pengaturan

<!-- Isi dari Bagian 2 notebook. Pastikan CEPAT = False untuk hasil akhir. -->

- Dataset: Kvasir-SEG (1000 citra kolonoskopi + mask polip), diperkecil ke 128 × 128 piksel.
- Data uji: 200 citra (tetap). Data latih: 200 citra per ulangan.
- Model: U-Net mini (±0,5 juta parameter), 40 epoch, 3 ulangan per skenario.
- Dijalankan di: Google Colab T4 GPU.

## 3. Skenario augmentasi & hipotesis

<!-- Tulis bagian ini SEBELUM menjalankan eksperimen. Commit dulu, baru jalankan.
     Alasan klinis = kenapa variasi ini mungkin (atau tidak mungkin) terjadi pada kolonoskopi sungguhan. -->

| Skenario | Augmentasi (kode) | Alasan klinis | Hipotesis: Dice naik/turun/sama? |
|---|---|---|---|
| Tanpa augmentasi | — | Digunakan sebagai baseline untuk mengetahui kemampuan U-Net dalam melakukan segmentasi tanpa transformasi citra. | Pembanding |
| Rotation | `v2.RandomRotation(degrees=180)` | Orientasi kamera endoskop dapat berubah akibat pergerakan dan manuver alat selama pemeriksaan kolonoskopi. | Dice meningkat |
| Brightness & Contrast | `v2.ColorJitter(brightness=0.3, contrast=0.3)` | Intensitas pencahayaan dan kontras citra dapat bervariasi akibat perubahan pencahayaan endoskop dan kondisi permukaan mukosa. | Dice meningkat |

<!-- Ganti nama file sesuai skenario kalian (lihat isi folder hasil/). -->

## 4. Hasil

<!-- Tempel isi hasil/ringkasan.md di sini. -->
4.1 Konfigurasi Eksperimen

Eksperimen dilakukan menggunakan dataset Kvasir-SEG yang terdiri atas 1.000 citra kolonoskopi beserta mask polip. Sebanyak 200 citra digunakan sebagai data pengujian tetap, sedangkan pelatihan menggunakan 200 citra dari data latih pada setiap pengulangan.

Model yang digunakan ialah U-Net mini dengan konfigurasi pelatihan 40 epoch dan tiga kali pengulangan untuk setiap skenario. Eksperimen dijalankan menggunakan GPU dengan perangkat cuda.

Tiga skenario yang dibandingkan meliputi:
1. Tanpa augmentasi sebagai baseline.
2. Rotation menggunakan RandomRotation(degrees=180).
3. Brightness & Contrast menggunakan ColorJitter(brightness=0.3, contrast=0.3).

4.2 Hasil Perbandingan Dice dan IoU

Evaluasi performa dilakukan menggunakan dua metrik, yaitu Dice Coefficient dan Intersection over Union (IoU). Kedua metrik digunakan untuk mengukur tingkat kesesuaian antara hasil segmentasi model dan mask referensi.

| Skenario Augmentasi | Dice (Mean ± SD) | IoU (Mean ± SD) | Keterangan |
|---|---:|---:|---|
| Tanpa augmentasi | **0.598 ± 0.018** | **0.482 ± 0.018** | Performa terbaik |
| Rotation | 0.590 ± 0.042 | 0.478 ± 0.041 | Sedikit di bawah baseline |
| Brightness & Contrast | 0.562 ± 0.029 | 0.451 ± 0.026 | Performa terendah |

Interpretasi hasil:
- Skenario tanpa augmentasi memperoleh performa tertinggi dengan Dice 0,598 dan IoU 0,482.
- Rotation memperoleh Dice 0,590 dan IoU 0,478, sedikit lebih rendah dibandingkan baseline.
- Brightness & Contrast memperoleh Dice 0,562 dan IoU 0,451, sehingga menjadi skenario dengan performa terendah.
- Rotation memiliki standar deviasi terbesar, menunjukkan variasi hasil antarulangan yang lebih tinggi dibandingkan dua skenario lainnya.
Jika dibandingkan dengan baseline, Rotation mengalami penurunan Dice sekitar 1,34%, sedangkan Brightness & Contrast mengalami penurunan sekitar 6,02%.

## 5. Analisis peta kesalahan

Peta kesalahan digunakan untuk mengamati perbedaan hasil segmentasi model terhadap mask referensi pada beberapa citra pengujian. Visualisasi menggunakan tiga warna utama:
- Hijau — True Positive (TP): area polip yang berhasil diprediksi dengan benar.
- Merah — False Positive (FP): area yang diprediksi sebagai polip, tetapi tidak termasuk dalam mask referensi.
- Biru — False Negative (FN): area polip pada mask referensi yang tidak berhasil diprediksi oleh model.


5.1 Citra Pengujian Pertama

Pada citra pertama, model tanpa augmentasi menghasilkan Dice 0,00. Visualisasi memperlihatkan dominasi warna biru, yang menunjukkan bahwa area polip pada mask referensi tidak berhasil terdeteksi oleh model.

Skenario Rotation menghasilkan Dice 0,87 dengan dominasi area hijau. Hal ini menunjukkan bahwa model berhasil mengenali sebagian besar area polip, meskipun masih terdapat sedikit kesalahan berupa False Positive dan False Negative.

Sementara itu, Brightness & Contrast menghasilkan Dice 0,00. Prediksi model belum berhasil mencakup area polip pada citra tersebut.

Analisis: Rotation menunjukkan hasil yang jauh lebih baik pada contoh ini, tetapi hasil satu citra belum cukup untuk menyatakan bahwa Rotation merupakan augmentasi terbaik secara keseluruhan.

| Citra | Tanpa Augmentasi | Rotation | Brightness & Contrast | Analisis |
|---|---:|---:|---:|---|
| Citra 1 | 0.00 | **0.87** | 0.00 | Rotation berhasil melakukan segmentasi ketika dua skenario lainnya gagal. |
| Citra 2 | 0.00 | 0.00 | 0.00 | Ketiga skenario belum berhasil mendeteksi area polip. |
| Citra 3 | 0.96 | 0.78 | **0.97** | Brightness & Contrast menghasilkan segmentasi terbaik. |
| Citra 4 | **0.97** | 0.96 | 0.93 | Tanpa augmentasi menghasilkan segmentasi terbaik. |


5.2 Citra Pengujian Kedua

Pada citra kedua, ketiga skenario menghasilkan Dice 0,00. Model belum berhasil menghasilkan segmentasi yang beririsan dengan mask referensi.

Pada skenario tanpa augmentasi dan Rotation, terlihat area merah yang menunjukkan prediksi polip pada lokasi yang tidak sesuai dengan mask referensi. Sementara itu, area biru menunjukkan bagian polip yang belum terdeteksi.

Pada Brightness & Contrast, prediksi yang ditampilkan juga belum menunjukkan irisan yang sesuai dengan area polip referensi.

Analisis: Citra ini memperlihatkan bahwa ketiga pendekatan masih mengalami kesulitan dalam mengenali polip pada kondisi visual tertentu.


5.3 Citra Pengujian Ketiga

Pada citra ketiga, tanpa augmentasi memperoleh Dice 0,96, Rotation memperoleh 0,78, dan Brightness & Contrast memperoleh 0,97.

Ketiga skenario berhasil mendeteksi sebagian besar area polip. Namun, pada Rotation terlihat area merah yang lebih besar di luar mask referensi, sehingga menunjukkan adanya False Positive tambahan.

Brightness & Contrast menghasilkan Dice tertinggi pada contoh ini, meskipun selisihnya sangat kecil dibandingkan baseline.

Analisis: Perubahan pencahayaan dan kontras tidak selalu mengurangi kemampuan segmentasi. Pada citra tertentu, skenario tersebut justru menghasilkan prediksi yang sangat mendekati mask referensi.


5.4 Citra Pengujian Keempat

Pada citra keempat, tanpa augmentasi memperoleh Dice 0,97, Rotation memperoleh Dice 0,96, sedangkan Brightness & Contrast memperoleh Dice 0,93.

Ketiga skenario berhasil mengidentifikasi sebagian besar area polip. Perbedaan terlihat pada bagian tepi objek, terutama munculnya sedikit False Positive dan False Negative.

Analisis: Ketiga skenario memiliki kemampuan segmentasi yang relatif baik pada citra ini, dengan baseline menghasilkan Dice tertinggi.


5.5 Ringkasan Analisis Peta Kesalahan

Berdasarkan keempat contoh yang ditampilkan, dapat disimpulkan bahwa:
1. Kemampuan model dalam mendeteksi polip berbeda-beda pada setiap citra pengujian.
2. Rotation menunjukkan keunggulan pada citra pertama, tetapi menghasilkan kesalahan segmentasi lebih besar pada citra ketiga.
3. Brightness & Contrast memperoleh performa terbaik pada citra ketiga, tetapi gagal melakukan segmentasi pada dua contoh pertama.
4. Baseline menunjukkan hasil yang sangat baik pada citra ketiga dan keempat, tetapi tidak berhasil mendeteksi polip pada dua contoh pertama.
Temuan ini memperlihatkan bahwa efektivitas augmentasi dapat berbeda bergantung pada karakteristik visual citra polip. Peta kesalahan juga menunjukkan bahwa evaluasi tidak cukup hanya mengamati keberhasilan mendeteksi objek, tetapi perlu memperhatikan kesalahan prediksi pada batas dan area di sekitar polip.

<!-- Hijau = benar, merah = false positive, biru = false negative.
     Pertanyaan pemandu: Polip seperti apa yang sering terlewat (kecil? datar? warna mirip dinding usus?)
     Apa yang sering dikira polip padahal bukan? Apakah augmentasi memperbaiki kasus sulit? -->

## 6. Pembahasan & kesimpulan

6.1 Pembahasan

Hasil eksperimen menunjukkan bahwa penerapan augmentasi data tidak selalu meningkatkan performa model U-Net dalam melakukan segmentasi polip pada citra kolonoskopi. Berdasarkan nilai rata-rata Dice dan IoU, skenario tanpa augmentasi menghasilkan performa tertinggi dibandingkan Rotation dan Brightness & Contrast.

Skenario Rotation secara teoritis dapat membantu model mengenali polip dalam berbagai orientasi citra akibat perubahan posisi kamera endoskop selama pemeriksaan. Namun, hasil eksperimen menunjukkan Dice sebesar 0,590 dan IoU sebesar 0,478, sedikit lebih rendah dibandingkan baseline. Selain itu, standar deviasi Dice sebesar 0,042 menunjukkan variasi hasil antarulangan yang lebih besar. Dengan demikian, penerapan rotasi hingga 180° belum memberikan peningkatan performa yang konsisten pada konfigurasi penelitian ini.

Pada skenario Brightness & Contrast, nilai Dice sebesar 0,562 dan IoU sebesar 0,451 menunjukkan performa terendah di antara ketiga skenario. Perubahan intensitas kecerahan dan kontras berpotensi mengubah karakteristik visual polip, termasuk tekstur dan perbedaan intensitas antara polip dengan jaringan di sekitarnya. Kondisi tersebut dapat memengaruhi fitur visual yang dipelajari oleh model. Meskipun demikian, penyebab penurunan performa tersebut belum dapat dipastikan hanya berdasarkan hasil evaluasi yang tersedia.

Analisis peta kesalahan memperlihatkan bahwa performa setiap skenario tidak selalu konsisten pada seluruh citra pengujian. Rotation mampu menghasilkan Dice 0,87 pada salah satu contoh ketika baseline dan Brightness & Contrast memperoleh Dice 0,00. Sebaliknya, pada contoh lain, Brightness & Contrast menghasilkan Dice 0,97, lebih tinggi daripada baseline dan Rotation. Hal tersebut menunjukkan bahwa augmentasi tertentu dapat membantu segmentasi pada karakteristik citra tertentu, tetapi belum tentu memberikan manfaat secara keseluruhan.

Terdapat beberapa faktor yang mungkin berkontribusi terhadap hasil eksperimen. Pertama, jumlah data latih sebanyak 200 citra masih terbatas untuk merepresentasikan keragaman karakteristik polip. Kedua, parameter augmentasi yang digunakan belum tentu sesuai dengan seluruh variasi kondisi citra kolonoskopi. Ketiga, perbedaan hasil antarulangan menunjukkan bahwa performa model masih dipengaruhi oleh variasi proses pelatihan.

Secara keseluruhan, hasil penelitian ini menunjukkan bahwa pemilihan augmentasi perlu mempertimbangkan karakteristik dataset dan dampaknya terhadap proses pembelajaran model. Penambahan variasi citra tidak secara otomatis menghasilkan peningkatan kemampuan segmentasi.

| Skenario | Hipotesis Awal | Hasil Eksperimen | Status Hipotesis | Penjelasan |
|---|---|---|---|---|
| Tanpa augmentasi | Menjadi baseline pembanding | Dice 0.598; IoU 0.482 | Baseline | Menghasilkan performa rata-rata tertinggi. |
| Rotation | Dice meningkat | Dice 0.590; IoU 0.478 | Tidak didukung | Rotasi belum memberikan peningkatan performa dibandingkan baseline. |
| Brightness & Contrast | Dice meningkat | Dice 0.562; IoU 0.451 | Tidak didukung | Perubahan kecerahan dan kontras belum meningkatkan performa segmentasi. |


6.2 Kesimpulan

Berdasarkan hasil eksperimen dan analisis yang telah dilakukan, diperoleh beberapa kesimpulan:
1. Skenario tanpa augmentasi menghasilkan performa segmentasi terbaik dengan Dice sebesar 0,598 ± 0,018 dan IoU sebesar 0,482 ± 0,018.
2. Augmentasi Rotation belum memberikan peningkatan performa dibandingkan baseline, dengan Dice sebesar 0,590 ± 0,042 dan IoU sebesar 0,478 ± 0,041.
3. Augmentasi Brightness & Contrast menghasilkan performa terendah dengan Dice sebesar 0,562 ± 0,029 dan IoU sebesar 0,451 ± 0,026.
4. Peta kesalahan menunjukkan bahwa keberhasilan segmentasi bergantung pada karakteristik masing-masing citra. Tidak ada satu skenario yang selalu menghasilkan segmentasi terbaik pada seluruh contoh.
5. Pada konfigurasi eksperimen ini, augmentasi Rotation dan Brightness & Contrast belum terbukti meningkatkan kemampuan U-Net dalam melakukan segmentasi polip dibandingkan kondisi tanpa augmentasi.
Dengan demikian, hipotesis bahwa penerapan Rotation dan Brightness & Contrast dapat meningkatkan performa segmentasi belum didukung oleh hasil eksperimen. Pengujian lebih lanjut dengan jumlah data latih yang lebih besar, variasi parameter augmentasi, serta pengulangan eksperimen yang lebih banyak diperlukan untuk memperoleh kesimpulan yang lebih kuat.

| Aspek Evaluasi | Temuan |
|---|---|
| Skenario terbaik | Tanpa augmentasi |
| Dice tertinggi | 0.598 ± 0.018 |
| IoU tertinggi | 0.482 ± 0.018 |
| Augmentasi Rotation | Belum meningkatkan performa dibandingkan baseline. |
| Augmentasi Brightness & Contrast | Menghasilkan performa terendah. |
| Hasil analisis visual | Performa setiap skenario berbeda pada masing-masing citra pengujian. |
| Kesimpulan utama | Augmentasi tidak selalu meningkatkan performa segmentasi U-Net. |

<!-- Bandingkan hasil dengan hipotesis di Bagian 3. Hipotesis yang ternyata salah tetap bernilai,
     asalkan dijelaskan kenapa. Perbedaan kecil (lebih kecil dari simpangan baku) belum tentu berarti. -->

## 7. Pembagian kerja

<!-- Siapa mengerjakan apa. Penilaian proses kerja dilihat dari riwayat commit tiap anggota. -->

Aiman Zakiy: Membuat repo, menjalankan skenario

Muhammad Rafqi Zulvikar: Membuat README.md

## 8. Penggunaan AI

<!-- Wajib diisi, walau tidak memakai AI (tulis "Tidak memakai AI").
     Contoh: "Claude Code (CLI) untuk menulis skenario augmentasi dan memperbaiki error di Bagian 4.
     Draf Bagian 6 ditulis sendiri, lalu dirapikan bahasanya dengan bantuan AI." -->

GPT AI untuk membatu menulis skenario augmentasi dan memperbaiki error, pada bagian README kami menulis semua sendiri.

## Cara menjalankan ulang

Eksperimen segmentasi polip dijalankan melalui dua metode, yaitu menggunakan lingkungan lokal dengan Jupyter Lab atau menggunakan Google Colab.

### 1. Menjalankan melalui lingkungan lokal

Pastikan Python dan `uv` sudah terpasang pada komputer. Selanjutnya, lakukan langkah berikut:

1. Clone repository GitHub:
   ```bash
   git clone URL_REPOSITORY
   ```

2. Masuk ke direktori proyek:
   ```bash
   cd tugas-p10-starter
   ```

3. Instal seluruh dependensi yang dibutuhkan:
   ```bash
   uv sync
   ```

4. Jalankan Jupyter Lab dengan membuka notebook:
   ```bash
   uv run jupyter lab segmentasi_polip.ipynb
   ```

5. Jalankan setiap cell notebook secara berurutan hingga seluruh proses eksperimen selesai.

### 2. Menjalankan melalui Google Colab

Eksperimen juga menggunakan Google Colab dengan langkah berikut:

1. Buka Google Colab.
2. Unggah file `segmentasi_polip.ipynb` atau buka notebook melalui repository GitHub.
3. Pilih menu **Runtime → Change runtime type**.
4. Pilih **T4 GPU** pada bagian Hardware accelerator.
5. Pastikan konfigurasi eksperimen menggunakan:
   - Jumlah citra latih: 200
   - Jumlah epoch: 40
   - Jumlah pengulangan: 3
   - Mode cepat (`CEPAT`): `False`
6. Jalankan seluruh cell notebook menggunakan menu **Runtime → Run all**.
7. Tunggu hingga seluruh skenario augmentasi selesai dijalankan.

## Referensi

- Jha, D., dkk. (2020). Kvasir-SEG: A Segmented Polyp Dataset. *MultiMedia Modeling (MMM 2020)*, 451–462.
- Ronneberger, O., Fischer, P., & Brox, T. (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation. *MICCAI*. arXiv:1505.04597.
