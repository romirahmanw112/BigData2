# Dokumentasi Proyek
## Ringkasan Singkat
BigData2 adalah kumpulan notebook praktikum PySpark / MLlib yang ditujukan untuk pembelajaran end-to-end alur kerja Big Data sederhana: setup Spark, eksplorasi data, pembersihan, visualisasi, feature engineering, hingga pembuatan dan evaluasi model ML (regresi, klasifikasi, klastering). Notebook siap dijalankan di Google Colab atau lingkungan lokal dengan PySpark.

---

## Tujuan Proyek
- Menyediakan bahan belajar praktis PySpark untuk pemula/intermediate.
- Menunjukkan pipeline dasar pada skala kecil sehingga konsep dapat diuji di Colab.
- Memperlihatkan teknik eksplorasi, transformasi, dan penerapan model MLlib dengan contoh yang mudah diikuti.

---

## Struktur Repositori
- `README.md` — dokumentasi ringkas (file utama).
- `DOCUMENTATION.md` — dokumentasi terperinci (file ini).
- `Praktek_big_data_5.ipynb` — Statistik deskriptif, eksplorasi & visualisasi.
- `Praktek_big_data_6.ipynb` — Feature engineering & pipeline MLlib (regresi, klasifikasi, klastering).
- `Praktek_big_data_7.ipynb` — Contoh integrasi Kafka / streaming (materi opsional / lanjutan).
- (Opsional) dataset contoh disertakan di dalam notebook sebagai list/CSV kecil atau link.

---

## Daftar Isi Dokumentasi
1. Prasyarat & Dependensi  
2. Cara Menjalankan (Colab & Lokal)  
3. Detail Per Modul / Notebook  
   - Praktek_big_data_5.ipynb  
   - Praktek_big_data_6.ipynb  
   - Praktek_big_data_7.ipynb  
4. Penjelasan Logika Umum (Spark / DataFrame / MLlib)  
5. Contoh Data Singkat  
6. Troubleshooting Umum  
7. Rekomendasi Perbaikan & Pengembangan  
8. Cara Berkontribusi & Kontak

---

## 1. Prasyarat / Dependensi
- Python 3.x
- Java (untuk menjalankan Spark lokal)
- Paket Python:
  - pyspark
  - findspark (opsional untuk inisialisasi lokal)
  - pandas, seaborn, matplotlib (untuk visualisasi setelah konversi ke Pandas)
- Instalasi (mis. di Colab atau venv):
```bash
pip install pyspark findspark pandas seaborn matplotlib
```

Catatan:
- Di Colab, instalan PySpark via pip biasanya cukup.
- Untuk Spark lokal, pastikan JAVA_HOME & versi Java kompatibel.

---

## 2. Cara Menjalankan

A. Google Colab (direkomendasikan untuk peserta)
1. Buka notebook `.ipynb` di Colab (Upload atau buka melalui GitHub).
2. Pastikan instalasi paket (jalankan cell instalasi jika ada):
   - `!pip install pyspark findspark pandas seaborn matplotlib`
3. Jalankan cell dari atas ke bawah. Notebook sudah disiapkan agar mudah dieksekusi.
4. Untuk visualisasi, beberapa cell mengonversi Spark DataFrame ke Pandas: `toPandas()` — hati-hati dengan ukuran dataset (gunakan sampling jika besar).

B. Lokal (dengan Spark terpasang)
1. Pastikan Java terpasang & environment variable `JAVA_HOME` benar.
2. Buat virtual environment dan pasang dependensi.
3. Jika ingin menginisialisasi Spark dari script/notebook:
```python
import findspark
findspark.init()
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("BigDataPractikum") \
    .getOrCreate()
```
4. Jalankan notebook via JupyterLab / Jupyter Notebook atau convert ke script.

---

## 3. Detail Per Modul / Notebook

### A. Praktek_big_data_5.ipynb
- Tujuan:
  - Praktik statistik deskriptif, eksplorasi dataset, pembersihan dasar, dan visualisasi.
- Konten Utama:
  1. Setup SparkSession.
  2. Membuat / memuat DataFrame Spark (dari CSV atau contoh dataset).
  3. Eksplorasi: `show()`, `printSchema()`, `describe()`.
  4. Statistik & agregasi: `groupBy`, `agg`, fungsi agregat seperti `count`, `mean`, `stddev`.
  5. Teknik mendapatkan median / modus secara aproksimasi menggunakan Spark (mis. `approxQuantile` atau pengelompokan).
  6. Sampling & konversi ke Pandas untuk visualisasi: `sample()`, `toPandas()`.
  7. Visualisasi dengan seaborn/matplotlib (histogram, boxplot, scatter).
- Logika & Tips:
  - Gunakan operasi DataFrame (Spark) untuk transformasi besar. Hanya konversi ke Pandas bila ukuran sudah aman.
  - Untuk mode: `df.groupBy("kolom").count().orderBy("count", ascending=False).show(1)`
  - Untuk median: `df.approxQuantile("kolom", [0.5], 0.01)`
- Contoh snippet (di notebook):
```python
df.describe().show()
mode_cut = df.groupBy("cut").count().orderBy("count", ascending=False)
mode_cut.show(1)

# sampling untuk visualisasi
sampled_df = df.sample(False, 0.1, seed=42)
viz_df = sampled_df.toPandas()
```
- Output yang Diharapkan:
  - Ringkasan statistik, histogram, boxplot, contoh bar plot dari kategori, dan insight pembersihan data.

---

### B. Praktek_big_data_6.ipynb
- Tujuan:
  - Memperlihatkan pipeline ML sederhana dengan PySpark MLlib: feature engineering, training model regresi/klasifikasi/klastering, prediksi, dan evaluasi dasar.
- Dataset Contoh:
  - Regresi: `data_gaji` (pengalaman, umur, gaji)
  - Klasifikasi: `df_churn` (durasi, komplain, label)
  - Klastering: `data_mall` (pendapatan, skor)
- Langkah Utama:
  1. Setup SparkSession.
  2. Siapkan data (list -> DataFrame atau load CSV).
  3. Feature engineering dengan `VectorAssembler` (gabungkan kolom numerik menjadi `features`).
  4. Bagi dataset (opsional): `randomSplit([0.7, 0.3], seed=42)`
  5. Model:
     - Regresi: `LinearRegression(featuresCol="features", labelCol="gaji")`
     - Klasifikasi: `LogisticRegression(featuresCol="features", labelCol="label")`
     - Klastering: `KMeans(k=3, featuresCol="features")`
  6. `fit()` untuk train, `transform()` untuk prediksi, `select()` untuk menampilkan.
- Penjelasan Logika:
  - VectorAssembler mengubah fitur individu menjadi vektor yang diterima oleh MLlib.
  - `fit()` menghasilkan model yang menyimpan koefisien / centroid.
  - `transform()` menambahkan kolom `prediction` pada DataFrame.
- Contoh snippet (di notebook):
```python
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.regression import LinearRegression

assembler = VectorAssembler(inputCols=["pengalaman", "umur"], outputCol="features")
data_siap_reg = assembler.transform(df_regresi).select("features", "gaji")

lr = LinearRegression(featuresCol="features", labelCol="gaji")
model_lr = lr.fit(data_siap_reg)
hasil_prediksi = model_lr.transform(data_siap_reg)
hasil_prediksi.select("features", "gaji", "prediction").show()
```
- Evaluasi & Interpretasi:
  - Regresi: gunakan RMSE/MAE/R2 (tersedia di evaluator atau hitung manual dari kolom prediction).
  - Klasifikasi: akurasi, confusion matrix (konversi ke Pandas untuk visualisasi jika perlu).
  - Klastering: lihat `clusterCenters()` dan distribusi cluster.
- Tips:
  - Normalisasi / scaling jika fitur berbeda skala (gunakan `StandardScaler`).
  - Untuk dataset kecil di notebook, split train/test cukup sederhana; untuk dataset sebenarnya perlu cross-validation.

---

### C. Praktek_big_data_7.ipynb (Opsional / Streaming)
- Indikasi Konten:
  - Beberapa cell menunjukkan inisialisasi Kafka (Zookeeper/Kafka Broker) dan pembuatan topic `transaksi-toko`.
  - Berguna untuk memperkenalkan konsep streaming dan integrasi sistem messaging.
- Logika Singkat:
  - Setup environment Kafka (pada VM / container / lokal).
  - Buat topic, kirim contoh pesan, baca stream (menggunakan spark streaming / structured streaming).
- Catatan:
  - Notebook ini biasanya lebih kompleks untuk dijalankan di Colab (membutuhkan broker Kafka lokal atau container).
  - Cocok untuk sesi lanjutan / demonstrasi saja.

---

## 4. Penjelasan Logika Umum (Ringkasan Teknik & Pattern)
- SparkSession
  - Titik masuk untuk bekerja dengan Spark: `SparkSession.builder.appName(...).getOrCreate()`
- DataFrame API
  - Operasi transformasi bersifat deklaratif; gunakan chained transformations lalu `show()` atau `collect()` untuk materialisasi.
- Feature Engineering
  - `VectorAssembler` untuk menggabungkan fitur numerik.
  - `StringIndexer` + `OneHotEncoder` untuk fitur kategorikal bila diperlukan.
  - Scaling (`StandardScaler`) bila model sensitif terhadap skala.
- MLlib Workflow
  1. Persiapan data -> `features` + `label`
  2. Split -> train/test
  3. Pilih estimator (LR, Logistic, KMeans)
  4. `fit()` -> `transform()` -> evaluasi
- Konversi ke Pandas
  - Gunakan `toPandas()` pada DataFrame kecil / sampled.
  - Hati-hati Out Of Memory.

---

## 5. Contoh Data Singkat (format & contoh baris)
A. Regresi (data_gaji)
```csv
pengalaman,umur,gaji
1.0,20,5000
2.0,22,6000
3.0,25,7000
4.0,26,8500
5.0,30,10000
```

B. Klasifikasi (df_churn)
```csv
durasi,komplain,label
2.0,5,1
1.0,4,1
10.0,0,0
12.0,1,0
3.0,3,1
```

C. Klastering (data_mall)
```csv
pendapatan,skor
15000,60
25000,40
5000,80
...
```

---
