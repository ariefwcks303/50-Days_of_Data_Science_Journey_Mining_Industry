# Day 05 — Exploratory Data Analysis Well-Log & Lithology

<p align="center">
  <strong>Well Log & Lithology Data Analysis</strong><br>
  Eksplorasi data log sumur untuk memahami karakteristik fisik batuan berdasarkan kedalaman, distribusi Gamma Ray, serta hubungan antara nilai log dan kelompok litologi.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Project-Data%20Science%20Geoscience-blue" alt="Project: Data Science Geoscience">
  <img src="https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple" alt="Type: Exploratory Data Analysis">
  <img src="https://img.shields.io/badge/Domain-Well%20Logging%20%26%20Lithology-green" alt="Domain: Well Logging and Lithology">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>

## Overview

Pada Day 05, saya mempelajari **exploratory data analysis (EDA) pada data well logging** dari sebuah sumur di Laut Utara. Data well log merekam respons fisik batuan pada berbagai kedalaman, seperti sinar gamma, densitas, porositas neutron, sonic travel time, dan resistivitas.

Fokus notebook ini adalah memahami struktur dataset, memeriksa kelengkapan data, menghapus kolom yang seluruh nilainya kosong, memilih log yang relevan, serta mengeksplorasi karakteristik batuan melalui visualisasi.

Analisis diarahkan pada tiga hal utama: **perubahan nilai log terhadap kedalaman, distribusi Gamma Ray (GR), dan variasi GR pada kelompok litologi (`GROUP`)**. Notebook ini masih berfokus pada data understanding dan EDA; belum dilakukan training model machine learning.

## Learning Objectives

- Memahami konsep **well logging** dan **litologi**.
- Membaca dataset well log menggunakan Pandas.
- Memeriksa struktur data dan contoh observasi menggunakan `head()`.
- Menghitung persentase missing values pada setiap kolom.
- Mengidentifikasi dan menghapus kolom yang seluruh nilainya kosong (*all-NaN columns*).
- Memilih fitur log yang relevan untuk eksplorasi.
- Membaca statistik deskriptif dari parameter well log.
- Membuat track plot log terhadap kedalaman dengan sumbu kedalaman terbalik.
- Menganalisis distribusi nilai Gamma Ray menggunakan histogram.
- Membandingkan distribusi Gamma Ray berdasarkan kelompok litologi menggunakan boxplot.
- Menarik insight awal mengenai variasi karakteristik batuan pada sumur yang dianalisis.

## Dataset

Dataset yang digunakan adalah **Well Logs Dataset for Machine Learning**, yang berisi rekaman log sumur dan label kelompok litologi pada setiap titik kedalaman. Notebook memilih satu file sumur, yaitu `34_10_16_R_well.csv`, yang merepresentasikan sumur **34/10-16 R di Laut Utara**.

- **Source:** [Kaggle — Well Logs Dataset for Machine Learning](https://www.kaggle.com/datasets/faresazzam/well-logs-dataset-for-machine-learning)
- **Selected well:** `34/10-16 R`
- **Selected file:** `34_10_16_R_well.csv`
- **Dataset size:** 25,095 rows × 27 columns
- **Observation unit:** Satu baris merepresentasikan satu titik kedalaman.
- **Depth range:** 227.30–4,042.04 meter.

Notebook mengunduh dataset melalui Kaggle API, mengekstrak file ZIP, lalu membaca file sumur yang dipilih menggunakan Pandas.

### Understanding Well Logging & Lithology

Well logging adalah proses pengukuran karakteristik fisik formasi batuan di sepanjang lubang sumur. Pengukuran dilakukan pada berbagai kedalaman dan menghasilkan kurva log yang dapat membantu interpretasi kondisi bawah permukaan.

Dalam notebook ini, beberapa parameter yang dieksplorasi adalah:

- **Gamma Ray (GR):** Mengukur respons radioaktivitas alami formasi dan sering digunakan sebagai salah satu petunjuk kandungan lempung atau shale.
- **Bulk Density (RHOB):** Menggambarkan densitas bulk batuan.
- **Neutron Porosity (NPHI):** Memberikan indikasi porositas berdasarkan respons neutron.
- **Sonic Travel Time (DTC):** Waktu tempuh gelombang kompresi melalui formasi.
- **Photoelectric Factor (PEF):** Memberikan petunjuk tambahan mengenai karakteristik mineral.
- **Deep Resistivity (RDEP):** Mengukur resistivitas formasi pada investigasi yang lebih dalam.
- **Caliper (CALI):** Menggambarkan diameter lubang bor.
- **Spontaneous Potential (SP):** Respons potensial spontan pada formasi.
- **GROUP:** Label kelompok litologi/formasi yang menyertai data.

Nilai log perlu dibaca bersama kedalaman dan konteks geologi. Interpretasi litologi tidak sebaiknya hanya bergantung pada satu kurva.

### Main Dataset Features

| Feature | Description |
|---|---|
| `WELL` | Identitas sumur |
| `DEPTH_MD` | Kedalaman terukur (*measured depth*) dalam meter |
| `X_LOC` | Koordinat lokasi X |
| `Y_LOC` | Koordinat lokasi Y |
| `Z_LOC` | Koordinat elevasi/kedalaman Z |
| `GROUP` | Label kelompok litologi/formasi |
| `FORMATION` | Nama formasi geologi |
| `CALI` | Diameter lubang bor (*caliper*) |
| `RSHA` | Shallow resistivity |
| `RMED` | Medium resistivity |
| `RDEP` | Deep resistivity |
| `RHOB` | Densitas bulk batuan |
| `NPHI` | Neutron porosity |
| `PEF` | Photoelectric factor |
| `GR` | Gamma Ray |
| `DTC` | Sonic compressional travel time |
| `SP` | Spontaneous Potential |

Dataset asli juga memuat kolom lain yang diperiksa kelengkapannya sebelum pemilihan fitur.

## 1. Data Loading & Initial Inspection

Library yang digunakan:

- `pandas` untuk membaca dan mengolah data.
- `numpy` untuk operasi numerik.
- `matplotlib` untuk membuat visualisasi.
- `seaborn` untuk visualisasi statistik, termasuk boxplot.

Dataset diunduh menggunakan Kaggle API dan diekstrak dari file ZIP. Notebook kemudian membaca file `34_10_16_R_well.csv`:

```python
df = pd.read_csv("34_10_16_R_well.csv")
df.head()
```

Hasil pemeriksaan awal menunjukkan dataset memiliki **25,095 baris dan 27 kolom**. Setiap baris merepresentasikan pengukuran pada satu titik kedalaman. Kolom yang terlihat mencakup identitas sumur, kedalaman, koordinat, kelompok/formasi, serta berbagai kurva well log.

## 2. Missing Value Analysis

Pemeriksaan missing values dilakukan dengan menghitung persentase nilai kosong pada setiap kolom:

```python
df.isnull().mean().mul(100).round(2).sort_values(ascending=False)
```

Hasil pemeriksaan menunjukkan beberapa kolom tidak memiliki data sama sekali, sementara beberapa kolom lainnya hanya memiliki sebagian kecil atau sebagian cukup besar nilai kosong.

### Missing Value Results

| Feature | Missing Values |
|---|---:|
| `RMIC` | 100.00% |
| `BS` | 100.00% |
| `SGR` | 100.00% |
| `ROP` | 100.00% |
| `DTS` | 100.00% |
| `DCAL` | 100.00% |
| `MUDWEIGHT` | 100.00% |
| `ROPA` | 100.00% |
| `NPHI` | 33.77% |
| `FORMATION` | 16.54% |
| `DRHO` | 9.51% |
| `PEF` | 0.79% |
| `RSHA` | 0.57% |
| `RXO` | 0.57% |
| `DTC` | 0.27% |
| `CALI` | 0.14% |

Kolom lain seperti `X_LOC`, `WELL`, `RDEP`, `Y_LOC`, `Z_LOC`, `GROUP`, `DEPTH_MD`, `SP`, `GR`, dan `RHOB` tidak memiliki missing values pada hasil pemeriksaan notebook.

### Initial Observations

- Terdapat **8 kolom dengan missing values 100%**: `RMIC`, `BS`, `SGR`, `ROP`, `DTS`, `DCAL`, `MUDWEIGHT`, dan `ROPA`.
- `NPHI` memiliki persentase missing values paling tinggi di antara kolom yang masih memiliki data, yaitu 33.77%.
- `FORMATION` memiliki 16.54% missing values dan `DRHO` memiliki 9.51%.
- `PEF`, `RSHA`, `RXO`, `DTC`, dan `CALI` memiliki persentase missing values yang relatif kecil.
- Kolom `GR`, `RHOB`, `DEPTH_MD`, dan `GROUP` lengkap pada dataset yang diperiksa.

## 3. Data Cleaning & Feature Selection

Notebook menghapus delapan kolom yang seluruh nilainya kosong, kemudian memilih sepuluh kolom untuk analisis lanjutan.

```python
logam_all_nan = [
    "SGR", "BS", "ROP", "DTS",
    "DCAL", "MUDWEIGHT", "RMIC", "ROPA"
]

df = df.drop(columns=[k for k in logam_all_nan if k in df.columns])

logam_use = [
    "DEPTH_MD", "GR", "RHOB", "NPHI", "DTC",
    "PEF", "RDEP", "CALI", "SP", "GROUP"
]

df_use = df[[k for k in logam_use if k in df.columns]].copy()
```

Hasil pemilihan fitur:

- **Selected dataset:** 25,095 rows × 10 columns.
- **Selected features:** `DEPTH_MD`, `GR`, `RHOB`, `NPHI`, `DTC`, `PEF`, `RDEP`, `CALI`, `SP`, dan `GROUP`.
- Terdapat 9 kolom pengukuran numerik dan 1 kolom kategorikal (`GROUP`).

### Descriptive Statistics

Statistik deskriptif dihitung menggunakan `describe().round(2).T`.

| Feature | Mean | Median | Min | Max |
|---|---:|---:|---:|---:|
| `DEPTH_MD` | 2,134.47 | 2,134.44 | 227.30 | 4,042.04 |
| `GR` | 51.91 | 52.69 | 12.88 | 152.88 |
| `RHOB` | 2.20 | 2.16 | 1.38 | 2.90 |
| `NPHI` | 0.35 | 0.37 | 0.01 | 0.68 |
| `DTC` | 118.40 | 113.19 | 59.23 | 172.71 |
| `PEF` | 9.74 | 4.93 | 1.43 | 382.12 |
| `RDEP` | 3.63 | 1.66 | 0.45 | 499.73 |
| `CALI` | 14.22 | 14.32 | 8.32 | 25.55 |
| `SP` | 65.25 | 69.47 | 33.36 | 86.24 |

`GROUP` tidak muncul pada tabel statistik numerik karena merupakan fitur kategorikal.

### Initial Observations

- `DEPTH_MD` memiliki rentang kedalaman 227.30 hingga 4,042.04 meter.
- `GR` memiliki rata-rata 51.91 dan median 52.69, dengan nilai maksimum 152.88.
- `RHOB` memiliki rata-rata 2.20 dan median 2.16, dengan rentang 1.38–2.90.
- `NPHI` masih memiliki nilai kosong cukup banyak, sehingga jumlah observasi validnya lebih sedikit dibandingkan jumlah baris dataset.
- `PEF` dan `RDEP` memiliki nilai maksimum yang jauh lebih tinggi daripada median. Hal ini mengindikasikan distribusi dengan ekor kanan yang panjang dan nilai ekstrem yang perlu diperiksa lebih lanjut.

## 4. Exploratory Data Analysis & Visualization

Notebook menggunakan tiga visualisasi utama untuk mengeksplorasi perubahan log terhadap kedalaman, distribusi Gamma Ray, dan variasi GR pada kelompok litologi.

### A. GR & RHOB Logs vs. Depth (Track Plot)

Track plot menampilkan kurva `GR` dan `RHOB` terhadap `DEPTH_MD`.

Karakteristik visualisasi:

- Sumbu X menunjukkan nilai log.
- Sumbu Y menunjukkan kedalaman dalam meter.
- Sumbu kedalaman dibalik (*inverted*) sehingga kedalaman dangkal berada di bagian atas dan kedalaman lebih besar berada di bagian bawah.
- Dua kurva yang ditampilkan adalah Gamma Ray dan bulk density.

**Observations:**

- Kurva `GR` berfluktuasi sepanjang kedalaman. Perubahan tersebut menunjukkan variasi respons log pada lapisan-lapisan yang berbeda.
- Nilai GR umumnya berada di kisaran 40–70, dengan beberapa lonjakan yang mencapai sekitar 100–150.
- `RHOB` secara umum terlihat cenderung meningkat pada bagian yang lebih dalam, dari kisaran sekitar 1.8–2.2 di bagian atas menuju sekitar 2.4–2.6 di beberapa bagian lebih dalam.
- Kurva RHOB juga memiliki fluktuasi lokal, termasuk nilai rendah dan tinggi pada kedalaman tertentu.

**Interpretation:**

Track plot membantu melihat perubahan karakteristik fisik batuan sepanjang sumur. Pola GR yang berfluktuasi dapat menjadi petunjuk perubahan lapisan, sedangkan kecenderungan kenaikan RHOB pada kedalaman lebih besar dapat berkaitan dengan pemadatan batuan. Interpretasi tersebut tetap perlu dikaji bersama data log lain dan informasi geologi.

### B. Gamma Ray Distribution (Histogram)

Histogram digunakan untuk melihat distribusi nilai `GR` pada seluruh titik kedalaman.

**Observations:**

- Puncak frekuensi utama berada di sekitar GR 55–60, dengan frekuensi mendekati 2,000 data.
- Sebagian besar nilai GR berada di kisaran 35–75.
- Distribusi memiliki ekor ke kanan yang memanjang melewati GR 80–100 hingga sekitar 150.
- Terlihat juga puncak kecil pada kisaran GR 22–26.

**Interpretation:**

Sebagian besar pengukuran GR berada pada rentang menengah, sementara sejumlah kecil titik memiliki nilai GR yang jauh lebih tinggi. Variasi distribusi ini memperlihatkan bahwa respons Gamma Ray pada sumur tidak seragam di seluruh kedalaman.

### C. Lithological Composition & GR Distribution by Rock Type (Boxplot)

Notebook menghitung jumlah observasi pada setiap kategori `GROUP`, kemudian membandingkan distribusi `GR` pada enam kelompok dengan jumlah data terbanyak menggunakan boxplot.

**Lithology Group Counts:**

| GROUP | Count |
|---|---:|
| `SHETLAND GP.` | 6,026 |
| `NORDLAND GP.` | 5,380 |
| `HORDALAND GP.` | 5,329 |
| `DUNLIN GP.` | 3,517 |
| `BAAT GP.` | 2,000 |
| `ROGALAND GP.` | 1,362 |
| `VIKING GP.` | 1,013 |
| `CROMER KNOLL GP.` | 257 |
| `HEGRE GP.` | 211 |

**Observations:**

- Kelompok dengan jumlah observasi terbanyak adalah `SHETLAND GP.`, `NORDLAND GP.`, dan `HORDALAND GP.`.
- Boxplot dibuat untuk enam kelompok teratas berdasarkan jumlah observasi.
- `DUNLIN GP.` memiliki median GR tertinggi, sekitar 70.
- `HORDALAND GP.` memiliki median GR terendah, sekitar 35.
- `SHETLAND GP.` memiliki sebaran tengah yang relatif terkonsentrasi di sekitar 55–61, tetapi terlihat banyak outlier.
- `NORDLAND GP.`, `BAAT GP.`, dan `ROGALAND GP.` memiliki median GR pada rentang menengah.

**Interpretation:**

Distribusi GR berbeda antar kelompok litologi. Pada data yang dianalisis, `DUNLIN GP.` cenderung memiliki nilai GR lebih tinggi, sedangkan `HORDALAND GP.` memiliki median lebih rendah. Perbedaan ini menunjukkan adanya variasi respons Gamma Ray antar kelompok, tetapi GR saja tidak cukup untuk memastikan jenis batuan secara definitif.

## 5. Key Findings

1. **Dataset contains completely empty columns.** Delapan kolom memiliki missing values 100% dan dihapus sebelum pemilihan fitur.
2. **NPHI is the most incomplete selected log.** `NPHI` memiliki 33.77% missing values, sehingga perlu strategi penanganan khusus jika digunakan pada analisis lanjutan.
3. **The selected dataset contains 25,095 records and 10 features.** Fitur mencakup kedalaman, berbagai log numerik, serta label `GROUP`.
4. **GR varies along the well depth.** Nilai Gamma Ray berfluktuasi dengan beberapa lonjakan tinggi pada kedalaman tertentu.
5. **RHOB tends to increase in deeper intervals.** Track plot memperlihatkan kecenderungan densitas yang lebih tinggi pada bagian sumur yang lebih dalam, meskipun tetap terdapat variasi lokal.
6. **GR distribution is concentrated around 55–60.** Histogram menunjukkan puncak utama di sekitar rentang tersebut dan ekor kanan hingga nilai sekitar 150.
7. **GR distribution differs across lithology groups.** `DUNLIN GP.` memiliki median GR tertinggi di antara enam kelompok yang dibandingkan, sementara `HORDALAND GP.` memiliki median terendah.

## 6. Geological Insights

Berdasarkan hasil eksplorasi, beberapa hal yang dapat menjadi fokus interpretasi dan analisis lanjutan:

### Observe Gamma Ray Changes by Depth

Perubahan nilai GR sepanjang kedalaman dapat digunakan sebagai petunjuk awal untuk mengidentifikasi interval dengan karakteristik formasi yang berbeda. Interval dengan nilai GR tinggi dan rendah dapat ditinjau lebih lanjut bersama log lainnya.

### Compare Lithology Groups

Perbedaan distribusi GR antar `GROUP` menunjukkan bahwa setiap kelompok memiliki karakteristik respons log yang tidak selalu sama. Perbandingan ini dapat menjadi langkah awal dalam memahami variasi litologi.

### Review Density Trends

Kecenderungan kenaikan `RHOB` pada kedalaman yang lebih besar dapat ditelusuri lebih lanjut untuk memahami perubahan densitas dan kemungkinan efek pemadatan batuan.

### Handle Incomplete Logs Carefully

`NPHI` memiliki missing values sekitar 33.77%. Jika fitur ini digunakan pada analisis berikutnya, pola missing values perlu diperiksa berdasarkan kedalaman sebelum menentukan metode imputasi atau penghapusan observasi.

## 7. Limitations & Next Steps

- Analisis hanya menggunakan satu sumur, yaitu `34/10-16 R`; hasilnya tidak otomatis mewakili sumur atau wilayah lain.
- Delapan kolom yang seluruhnya kosong dihapus, tetapi kolom yang masih memiliki missing values sebagian belum melalui proses imputasi.
- `NPHI` masih memiliki 33.77% missing values.
- `PEF` dan `RDEP` memiliki nilai maksimum yang jauh di atas median; nilai ekstrem tersebut belum dianalisis atau ditangani secara khusus.
- Visualisasi yang dibuat berfokus pada `GR`, `RHOB`, dan `GROUP`. Walaupun `NPHI` termasuk fitur terpilih, notebook belum menampilkan visualisasi distribusinya.
- Interpretasi litologi berdasarkan GR bersifat indikatif dan tidak membuktikan jenis batuan secara definitif.
- Notebook ini belum melakukan analisis korelasi, analisis time/depth interval yang lebih mendalam, maupun predictive modeling.

Pengembangan berikutnya yang dapat dilakukan:

- Memeriksa pola missing values berdasarkan kedalaman, terutama pada `NPHI`, `FORMATION`, dan `DRHO`.
- Menentukan strategi imputasi yang sesuai, misalnya interpolasi berdasarkan kedalaman jika secara geologis dan metodologis dapat dipertanggungjawabkan.
- Memeriksa outlier dan distribusi `PEF` serta `RDEP`.
- Membuat visualisasi tambahan untuk `NPHI`, `DTC`, `PEF`, dan `RDEP`.
- Membandingkan lebih banyak kurva log antar kelompok litologi.
- Menganalisis interval kedalaman untuk memahami perubahan litologi secara lebih terperinci.
- Jika tujuan proyek berkembang menjadi klasifikasi litologi, menyiapkan fitur dan label yang sesuai lalu mengevaluasi model machine learning.

## Workflow Summary

1. Import Python libraries.
2. Download the Well Logs Dataset from Kaggle using Kaggle API.
3. Extract the dataset ZIP archive.
4. Load `34_10_16_R_well.csv` into a Pandas DataFrame.
5. Inspect the first rows and dataset structure.
6. Calculate missing-value percentages for each column.
7. Identify and remove eight columns containing only missing values.
8. Select ten relevant well-log and lithology columns.
9. Review descriptive statistics of the selected features.
10. Create a GR and RHOB track plot against measured depth.
11. Visualize the GR distribution using a histogram.
12. Count lithology groups and compare GR distributions using a boxplot.
13. Summarize findings and possible geological insights.

## Tech Stack

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebook / Google Colab
- Kaggle API
- Kaggle Dataset

## Project Structure

```text
Day_05_Exploratory_Data_Analysis_Well_Log_&_Lithology/
│
├── Day_05_Exploratory_Data_Analysis_Well_Log_&_Lithology.ipynb
└── README.md
```

## Conclusion

Day 05 berfokus pada eksplorasi data well logging dari sumur `34/10-16 R` di Laut Utara. Dataset yang digunakan berisi 25,095 baris dan 27 kolom. Setelah pemeriksaan missing values, delapan kolom yang seluruh nilainya kosong dihapus dan sepuluh fitur dipilih untuk analisis lanjutan.

Hasil EDA memperlihatkan bahwa nilai Gamma Ray berfluktuasi sepanjang kedalaman, sementara RHOB menunjukkan kecenderungan meningkat pada interval yang lebih dalam. Distribusi GR paling terkonsentrasi di sekitar 55–60, dengan ekor kanan hingga nilai yang lebih tinggi. Perbandingan berdasarkan kelompok litologi juga memperlihatkan perbedaan median GR, dengan `DUNLIN GP.` memiliki median tertinggi dan `HORDALAND GP.` terendah di antara enam kelompok teratas yang divisualisasikan.

Temuan ini menjadi gambaran awal mengenai variasi karakteristik batuan pada sumur yang dipilih. Analisis berikutnya dapat memperdalam pemeriksaan missing values, nilai ekstrem, dan hubungan antar-log sebelum digunakan untuk interpretasi atau pemodelan litologi.

---

## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student
