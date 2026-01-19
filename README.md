# BigData2

## Ringkasan
BigData2 adalah kumpulan notebook praktikum PySpark / MLlib (Jupyter / Google Colab) untuk mengenal alur kerja Big Data dan machine learning sederhana. Projek ini berisi contoh eksplorasi data, transformasi, feature engineering, serta penerapan model regresi, klasifikasi, dan klastering menggunakan PySpark.

---

## Daftar Isi
- [Ringkasan](#ringkasan)  
- [Struktur Repositori](#struktur-repositori)  
- [Tujuan Proyek](#tujuan-proyek)  
- [Fitur Utama](#fitur-utama)  
- [Prerequisites / Dependensi](#prerequisites--dependensi)  
- [Cara Menjalankan (Colab & Lokal)](#cara-menjalankan-colab--lokal)  
- [Detail Per Modul / Notebook](#detail-per-modul--notebook)  
  - [Praktek_big_data_5.ipynb](#praktek_big_data_5ipynb)  
  - [Praktek_big_data_6.ipynb](#praktek_big_data_6ipynb)  
- [Contoh Data Singkat](#contoh-data-singkat)  
- [Rekomendasi Perbaikan & Pengembangan](#rekomendasi-perbaikan--pengembangan)  
- [Cara Berkontribusi](#cara-berkontribusi)  
- [Kontak / Penulis](#kontak--penulis)

---

## Struktur Repositori
- `README.md` — (file ini) dokumentasi terstruktur.
- `Praktek_big_data_5.ipynb` — notebook: eksplorasi data, statistik deskriptif, pembersihan & visualisasi.
- `Praktek_big_data_6.ipynb` — notebook: feature engineering + contoh MLlib (regresi, klasifikasi, klastering).

> Catatan: Semua materi disajikan sebagai notebook interaktif (Colab-ready).

---

## Tujuan Proyek
- Menjadi bahan pembelajaran praktis PySpark untuk pemula/intermediate.
- Menunjukkan alur end-to-end sederhana: membuat SparkSession → pemrosesan DataFrame → feature engineering → membangun model MLlib → evaluasi/prediksi.
- Mudah dijalankan di Google Colab agar peserta tidak perlu konfigurasi Spark lokal secara manual.

---

## Fitur Utama
- Setup PySpark di Colab / lokal.
- Membuat dan mengeksplor DataFrame Spark.
- Transformasi data & feature engineering (`VectorAssembler`).
- Model MLlib: Linear Regression, Logistic Regression, KMeans.
- Menampilkan prediksi dan interpretasi sederhana.

---

## Prerequisites / Dependensi
- Python 3.x  
- Java (untuk menjalankan Spark secara lokal)  
- Paket Python:
  - pyspark
  - findspark (opsional untuk inisialisasi lokal)
  - pandas, seaborn, matplotlib (untuk konversi & visualisasi)
  
Instalasi contoh (Colab / lingkungan baru):
```bash
pip install pyspark findspark pandas seaborn matplotlib
```

---

## Cara Menjalankan (Colab & Lokal)

1. Google Colab (direkomendasikan)
   - Buka notebook yang ingin dijalankan, mis. klik "Open in Colab" pada masing-masing notebook.
   - Jalankan sel instalasi dependensi (`!pip install pyspark -q`) jika ada.
   - Jalankan sel-sel dari atas ke bawah.

2. Lokal (Jupyter / JupyterLab)
   - Pastikan Java terpasang (Spark membutuhkan Java).
   - Buat virtual environment dan pasang dependensi di atas.
   - Jalankan `jupyter notebook` atau `jupyter lab`, lalu buka file `.ipynb` dan jalankan sel per sel.

Contoh inisialisasi Spark di notebook:
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("Latihan_MLlib") \
    .getOrCreate()
```

---

## Detail Per Modul / Notebook

### Praktek_big_data_5.ipynb
Tujuan: praktik statistik deskriptif, eksplorasi & pembersihan data, serta visualisasi.

Konten utama:
- Setup SparkSession (dengan `findspark` jika perlu).
- Membuat DataFrame dari dataset contoh atau CSV (diperagakan).
- Eksplorasi: `show()`, `printSchema()`, `describe()`.
- Transformasi: pembuatan kolom baru, `groupBy`, agregasi.
- Teknik statistik: median aproksimasi, mode, dsb.
- Visualisasi: convert ke Pandas dan plot dengan seaborn/matplotlib.
- Beberapa utilitas styling untuk tampilan DataFrame di Colab.

Contoh snippet:
```python
df.describe().show()
mode_cut = df.groupBy("cut").count().orderBy("count", ascending=False)
mode_cut.show(1)
```

Penjelasan logika: gunakan Spark untuk operasi skala data dan konversi sebagian data ke Pandas bila perlu untuk visualisasi.

---

### Praktek_big_data_6.ipynb
Tujuan: menunjukkan pipeline ML sederhana menggunakan PySpark MLlib (feature engineering, training model, prediksi).

Konten utama:
1. Setup SparkSession.
2. Menyiapkan dataset contoh langsung di notebook:
   - Contoh regresi (`data_gaji`): (pengalaman, umur, gaji).
   - Contoh klasifikasi (`df_churn`): (durasi, komplain, label).
   - Contoh klastering (`data_mall`): (pendapatan, skor).
3. Feature engineering dengan VectorAssembler:
```python
from pyspark.ml.feature import VectorAssembler

assembler = VectorAssembler(inputCols=["pengalaman", "umur"], outputCol="features")
data_siap_reg = assembler.transform(df_regresi).select("features", "gaji")
```
4. Regresi Linear:
```python
from pyspark.ml.regression import LinearRegression
lr = LinearRegression(featuresCol="features", labelCol="gaji")
model_lr = lr.fit(train_data)
hasil_prediksi = model_lr.transform(test_data)
hasil_prediksi.select("features","gaji","prediction").show()
```
5. Klasifikasi (Logistic Regression) dan tampilan prediksi.
6. Klastering (KMeans) dan pengecekan pusat cluster.

Penjelasan logika:
- `VectorAssembler` menyatukan kolom numerik ke satu kolom vektor fitur.
- `fit()` untuk training, `transform()` untuk menghasilkan kolom `prediction`.
- KMeans digunakan untuk menemukan pola cluster pada data pelanggan.

---

## Contoh Data Singkat (yang dipakai di notebook)
- data_gaji (regresi):
  ```python
  data_gaji = [
      (1.0, 20, 5000),
      (2.0, 22, 6000),
      (3.0, 25, 7000),
      (4.0, 26, 8500),
      (5.0, 30, 10000),
      (6.0, 31, 11500)
  ]
  ```
- data_mall (klastering):
  ```python
  data_mall = [
      (15000, 39),
      (12000, 81),
      (20000, 6),
      (25000, 77),
      (30000, 40)
  ]
  ```
- df_churn (klasifikasi) misalnya berisi kolom `durasi`, `komplain`, `label` (0/1).

---

