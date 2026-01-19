# Analisis-Faktor-Dominan-dan-Pemodelan-Spasial-untuk-Prediksi-Kejernihan-Air-Danau
## Latar Belakang
Kejernihan air danau merupakan indikator vital bagi kesehatan ekosistem dan sektor pariwisata yang kini terancam oleh tekanan antropogenik seperti urbanisasi dan pertanian, namun keterbatasan sumber daya sering kali menghambat pemantauan kondisi perairan secara menyeluruh. Untuk mengatasi tantangan tersebut, analisis ini menggunakan pendekatan Machine Learning dan pemodelan spasial guna memprediksi tingkat kejernihan (berdasarkan Secchi disk) serta mengidentifikasi faktor lingkungan dominan, sehingga memungkinkan pengambil kebijakan untuk memprioritaskan upaya restorasi dan mengalokasikan anggaran konservasi secara lebih efektif dan tepat sasaran.
## Tujuan Proyek
1. Pengembangan Model Prediksi: Membangun model Machine Learning yang akurat dan efisien untuk memprediksi tingkat kejernihan air danau (Secchi disk depth) dengan memanfaatkan data historis dan variabel lingkungan regional.
2. Identifikasi Faktor Dominan: Menganalisis faktor-faktor lingkungan utama yang memiliki pengaruh paling signifikan terhadap penurunan kualitas air.
3. Optimalisasi Pengambilan Keputusan: Menyediakan landasan berbasis data dan spasial bagi pemangku kepentingan untuk memetakan area rentan, memprioritaskan lokasi restorasi, serta mengefisiensikan alokasi anggaran konservasi dan pariwisata.
## Workflow
### 1. Data Preprocessing and Parsing
Dataset masih sangat "kotor", sehingga perlu dilakukan parsing dan preprocessing data. Beberapa teknik yang digunakan:
- Last Separator Logic: Menangani data yang memiliki titik (.) dan koma (,) sekaligus.
- Regex Grouping: Menangani Ambiguitas ribuan vs desimal.
- Robust Rescaling: Menangani fitur skala angka yang terlalu besar.
- Normalisasi Koordinat.
- Penanganan Outlier Ekstrem.
- Menangani nilai kosong dengan mengisi nilai 0.
- melakukan Sanity Check untuk melihat ke-normalan data.
### 2. Feature Engineering
Membuat beberapa fitur-fitur baru, seperti:
1. log(mean_secchi): Visualisasi menunjukkan mean_secchi sangat right-skewed sehingga transformasi log sangat cocok.
2. log_iws: Melakukan transformasi log dari fitur iws_area_ha
3. ag_intensity: Total % Lahan yang dipakai untuk kegiatan pertanian.
4. depth_ratio: menggambarkan bentuk dasar danau (batimetri).
5. SDI (Shoreline Development Index): Menggambarkan seberapa kompleks bentuk pinggiran danau.
6. Transformasi Fitur Spasial: mengubah data koordinat mentah (Latitude/Longitude) agar lebih mudah dipahami oleh model Machine Learning.
7. Melakukan Transformasi Log terhadap variabel Heavy-Tail.
### 3. CV (Cross-Validation) & Baseline
DIlakukan Cross-Validation dengan menggunakan beberapa teknik:
1. Spatial (Kmeans): Metode pembagian wilayah buatan untuk mengelompokkan danau-danau terdekat ke dalam satu klaster secara otomatis berdasarkan koordinat.
2. Spatial (Blockgrid): Metode pembagian wilayah yang memotong peta menjadi kotak-kotak persegi (grid) beraturan secara kaku untuk memisahkan lokasi data latih dan uji.
3. GroupKFold (HU12): Metode pengelompokan data berdasarkan HU12 untuk memastikan model diuji pada ekosistem yang benar-benar baru dan realistis.
### 4. Model
Menggunakan model LGBMRegressor (LightGBM) untuk mempelajari hubungan kompleks antara fitur lingkungan (seperti urban dan pertanian) dengan tingkat kejernihan air guna menghasilkan prediksi yang jauh lebih akurat dibandingkan model baseline.
## Hasil dan Insight
### Calibration (Spatial CV)
Model memiliki kemampuan generalisasi yang kuat karena berhasil menangkap tren diagonal (membedakan danau jernih vs keruh) secara konsisten di wilayah yang benar-benar baru, meskipun menghadapi variasi data alam yang tinggi.
### Error by Maxdepth Quartile
Prediksi model cenderung lebih akurat pada danau-danau yang dalam dan sedikit lebih meleset pada danau-danau dangkal yang kondisinya fluktuatif.
### Spatial Pattern (Secchi Quartiles)
Kejernihan air (Secchi) memiliki variasi spasial, dengan indikasi wilayah kanan atas lebih jernih (Q3–Q4 high), sementara wilayah lain menunjukkan campuran kategori sehingga pola spasialnya tidak sepenuhnya tersegmentasi tajam.
### SHAP
- Hasil analisis menggunakan SHAP menegaskan pola domain bahwa depth_ratio mendorong prediksi ke atas (jernih), sedangkan % urban/row-crop menarik ke bawah (keruh).
- Terlihat bahwa basis spasial (sin/cos, lat/lon) juga dominan serta selaras dengan EDA yang menunjukkan gradient latitude.
## Kesimpulan
- Danau makin keruh saat area urban/pertanian tinggi, danau dangkal paling rentan.
-  Danau dengan Secchi rendah kurang nyaman untuk berenang/berlayar.
-  Dengan spatial CV dan interval prediksi, kita bisa memilih prioritas dan pakai dana lebih tepat.
## Rekomendasi
- Fokus pemantauan & restorasi di danau dangkal dan wilayah urban tinggi (buffer vegetasi, kurangi run-off).
- perbaiki model dengan bobot nobs, fitur spasial yang lebih halus, serta tambah data mengenai nutrien, musim, dan waktu.
