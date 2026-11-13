### Discussion

Hasil eksperimen menunjukkan bahwa augmentasi data tidak selalu
meningkatkan performa segmentasi polip menggunakan U-Net. Skenario
tanpa augmentasi menghasilkan nilai Dice rata-rata tertinggi sebesar
0,598 ± 0,018 dan IoU sebesar 0,482 ± 0,018.

Skenario Rotation menghasilkan Dice sebesar 0,590 ± 0,042 dan IoU
sebesar 0,478 ± 0,041. Meskipun terdapat pengulangan dengan performa
yang cukup tinggi, variasi hasil antarulangan lebih besar dibandingkan
baseline. Hal ini menunjukkan bahwa penerapan rotasi belum memberikan
peningkatan yang konsisten.

Sementara itu, skenario Brightness & Contrast memperoleh Dice sebesar
0,562 ± 0,029 dan IoU sebesar 0,451 ± 0,026. Hasil tersebut menunjukkan
bahwa perubahan intensitas kecerahan dan kontras dengan parameter yang
digunakan belum membantu meningkatkan kemampuan segmentasi model.

Berdasarkan peta kesalahan, setiap skenario menunjukkan kemampuan
segmentasi yang berbeda pada masing-masing citra. Terdapat citra yang
berhasil disegmentasi dengan baik, tetapi terdapat pula citra dengan
kesalahan False Positive dan False Negative yang cukup besar.

### Conclusion

Pada konfigurasi eksperimen ini, augmentasi Rotation dan Brightness &
Contrast belum menghasilkan peningkatan performa dibandingkan model
tanpa augmentasi. Baseline memperoleh performa terbaik berdasarkan
rata-rata Dice dan IoU. Dengan demikian, hipotesis bahwa kedua
augmentasi dapat meningkatkan performa segmentasi belum didukung
oleh hasil eksperimen.

Hasil ini terbatas pada dataset, jumlah data latih, arsitektur U-Net,
parameter augmentasi, dan konfigurasi pelatihan yang digunakan.
Pengujian lebih lanjut dengan variasi parameter dan jumlah data yang
lebih besar diperlukan untuk memperoleh kesimpulan yang lebih kuat.
