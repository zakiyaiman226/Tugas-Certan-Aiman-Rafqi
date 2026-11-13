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
Skenario | Dice (rata-rata ± SD) | IoU (rata-rata ± SD)
Tanpa augmentasi | 0,598 ± 0,018 | 0,482 ± 0,018
Rotation | 0,590 ± 0,042 | 0,478 ± 0,041
Brightness & Contrast | 0,562 ± 0,029 | 0,451 ± 0,026
Interpretasi hasil:
- Skenario tanpa augmentasi memperoleh performa tertinggi dengan Dice 0,598 dan IoU 0,482.
- Rotation memperoleh Dice 0,590 dan IoU 0,478, sedikit lebih rendah dibandingkan baseline.
- Brightness & Contrast memperoleh Dice 0,562 dan IoU 0,451, sehingga menjadi skenario dengan performa terendah.
- Rotation memiliki standar deviasi terbesar, menunjukkan variasi hasil antarulangan yang lebih tinggi dibandingkan dua skenario lainnya.
Jika dibandingkan dengan baseline, Rotation mengalami penurunan Dice sekitar 1,34%, sedangkan Brightness & Contrast mengalami penurunan sekitar 6,02%.

## 5. Analisis peta kesalahan

![Peta kesalahan](hasil/peta_kesalahan.png)

<!-- Hijau = benar, merah = false positive, biru = false negative.
     Pertanyaan pemandu: Polip seperti apa yang sering terlewat (kecil? datar? warna mirip dinding usus?)
     Apa yang sering dikira polip padahal bukan? Apakah augmentasi memperbaiki kasus sulit? -->

✍️

## 6. Pembahasan & kesimpulan

<!-- Bandingkan hasil dengan hipotesis di Bagian 3. Hipotesis yang ternyata salah tetap bernilai,
     asalkan dijelaskan kenapa. Perbedaan kecil (lebih kecil dari simpangan baku) belum tentu berarti. -->

✍️

## 7. Pembagian kerja

<!-- Siapa mengerjakan apa. Penilaian proses kerja dilihat dari riwayat commit tiap anggota. -->

✍️

## 8. Penggunaan AI

<!-- Wajib diisi, walau tidak memakai AI (tulis "Tidak memakai AI").
     Contoh: "Claude Code (CLI) untuk menulis skenario augmentasi dan memperbaiki error di Bagian 4.
     Draf Bagian 6 ditulis sendiri, lalu dirapikan bahasanya dengan bantuan AI." -->

✍️

## Cara menjalankan ulang

```bash
uv sync
uv run jupyter lab segmentasi_polip.ipynb
```

Atau buka notebook di Google Colab (Runtime → Change runtime type → T4 GPU).

## Referensi

- Jha, D., dkk. (2020). Kvasir-SEG: A Segmented Polyp Dataset. *MultiMedia Modeling (MMM 2020)*, 451–462.
- Ronneberger, O., Fischer, P., & Brox, T. (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation. *MICCAI*. arXiv:1505.04597.
- ✍️ tambahkan referensi lain yang kalian pakai
