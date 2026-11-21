# Day 01 — Understanding Data from the Iron Ore Flotation Process

## Overview

Pada Day 01, saya mulai mempelajari **data analytics di industri pertambangan** melalui dataset proses flotasi bijih besi. Fokus notebook ini adalah mengenali struktur data industri, memahami parameter proses, melakukan pemeriksaan statistik dasar, membuat filter sederhana, dan menggunakan visualisasi untuk menemukan pola awal pada kualitas hasil flotasi.

Pertanyaan utama yang dieksplorasi adalah: **seberapa sering kadar silika pada hasil konsentrat melewati ambang 3%?** Dalam notebook, ambang 3% digunakan sebagai batas ilustratif untuk mengelompokkan hasil dengan kadar silika tinggi.

Notebook ini berfokus pada **data understanding dan exploratory data analysis (EDA)**. Belum dilakukan training model machine learning.

## Learning Objectives

- Memahami `DataFrame` sebagai struktur data tabular di Python.
- Membaca dataset industri menggunakan Pandas.
- Memeriksa data menggunakan `head()`, `shape`, `info()`, dan `describe()`.
- Memeriksa missing values.
- Membuat kolom kategori berdasarkan ambang kadar silika.
- Membandingkan parameter proses pada kelompok hasil normal dan hasil dengan silika tinggi.
- Membuat visualisasi untuk memahami distribusi dan hubungan antarvariabel.
- Menarik insight awal yang relevan untuk operasional proses flotasi.

## Dataset

Dataset yang digunakan adalah **Quality Prediction in a Mining Process**, yang berisi data nyata dari proses flotasi bijih besi di sebuah pabrik milik Vale, Brasil. Data direkam pada periode Maret–September 2017 dengan interval pencatatan sekitar 20 detik.

- **Source:** [Kaggle — Quality Prediction in a Mining Process](https://www.kaggle.com/datasets/edumagalhaes/quality-prediction-in-a-mining-process)
- **Original dataset:** 737,453 rows × 24 columns
- **Sample used:** 30,000 rows × 24 columns
- **Sampling:** `data.sample(30000, random_state=42)`
- **Missing values in sample:** 0

Notebook membaca file ZIP dengan pengaturan `decimal=","` agar angka desimal terbaca dengan benar.

### Understanding the Flotation Process

Flotasi merupakan proses pemurnian bijih besi. Bijih dicampur dengan air menjadi bubur (*ore pulp*), kemudian ditambahkan reagen kimia dan udara. Dalam konteks dataset:

- **Starch** berfungsi sebagai depressant bijih besi/hematit.
- **Amina** berfungsi sebagai collector silika.
- Udara dialirkan ke kolom flotasi untuk membantu proses pemisahan.
- Hasil akhirnya berupa konsentrat dengan kadar besi lebih tinggi dan kadar silika lebih rendah.

Semakin rendah `% Silica Concentrate`, semakin bersih hasil konsentrat yang dihasilkan.

### Main Dataset Features

| Feature | Description |
|---|---|
| `date` | Timestamp pencatatan proses |
| `% Iron Feed` | Kadar besi pada bijih masuk |
| `% Silica Feed` | Kadar silika pada bijih masuk |
| `Starch Flow` | Aliran reagen starch |
| `Amina Flow` | Aliran reagen amina |
| `Ore Pulp Flow` | Aliran bubur bijih |
| `Ore Pulp pH` | Tingkat keasaman bubur bijih |
| `Ore Pulp Density` | Kepadatan bubur bijih |
| `Flotation Column 01–07 Air Flow` | Aliran udara pada masing-masing kolom |
| `Flotation Column 01–07 Level` | Tinggi cairan pada masing-masing kolom |
| `% Iron Concentrate` | Kadar besi pada hasil konsentrat |
| `% Silica Concentrate` | Kadar silika pada hasil konsentrat; fokus analisis |

## 1. Data Loading & Initial Inspection

Library yang digunakan:

- `pandas` untuk membaca dan mengolah data.
- `numpy` untuk operasi numerik.
- `matplotlib` dan `seaborn` untuk visualisasi.

Dataset dimuat dari file ZIP menggunakan:

```python
df = pd.read_csv(
    "quality-prediction-in-a-mining-process.zip",
    decimal=","
)
```

Ukuran dataset asli adalah **737,453 baris dan 24 kolom**. Untuk eksplorasi awal, notebook mengambil sampel acak sebanyak 30,000 baris dengan `random_state=42`.

Hasil pemeriksaan sampel:

- 30,000 rows × 24 columns.
- 23 kolom numerik (`float64`).
- 1 kolom waktu (`date`) yang masih bertipe `object`.
- Seluruh 30,000 baris terisi pada setiap kolom.

## 2. Descriptive Statistics

Menggunakan `describe().T` untuk melihat jumlah data, mean, standard deviation, minimum, kuartil, median, dan maksimum setiap fitur.

Beberapa statistik penting dari sampel:

| Feature | Mean | Median | Min | Max |
|---|---:|---:|---:|---:|
| `% Iron Feed` | 56.30 | 56.08 | 42.74 | 65.78 |
| `% Silica Feed` | 14.64 | 13.85 | 1.31 | 33.40 |
| `Starch Flow` | 2,889.41 | 3,036.10 | 0.02 | 6,294.94 |
| `Amina Flow` | 489.04 | 504.88 | 241.77 | 739.54 |
| `Ore Pulp pH` | 9.77 | 9.80 | 8.75 | 10.81 |
| `% Iron Concentrate` | 65.06 | 65.22 | 62.05 | 68.01 |
| `% Silica Concentrate` | 2.32 | 1.99 | 0.60 | 5.53 |

### Initial Observations

- Rata-rata kadar besi masuk berada di sekitar 56.3%.
- Kadar silika pada feed cukup bervariasi, dari 1.31% hingga 33.40%.
- Kadar besi konsentrat relatif terkonsentrasi di sekitar 65%.
- Kadar silika konsentrat memiliki median 1.99%, tetapi nilai maksimumnya mencapai 5.53%.
- `Starch Flow` menunjukkan rentang nilai yang lebar.

## 3. Missing Value Check

Pemeriksaan dilakukan menggunakan:

```python
data.isnull().sum().sort_values(ascending=False)
```

Hasilnya menunjukkan **tidak ada missing values** pada seluruh 24 kolom di dalam sampel 30,000 baris.

Karena itu, notebook tidak melakukan imputasi maupun penghapusan baris berdasarkan missing values.

## 4. Filtering: Identifying High Silica Output

Untuk mengidentifikasi kondisi hasil konsentrat dengan kadar silika tinggi, notebook membuat kolom kategori `high_silica` dengan ambang ilustratif 3%.

```python
data["high_silica"] = (
    data["% Silica Concentrate"] > 3
).map({
    True: "Tinggi (>3%)",
    False: "Normal (<=3%)"
})
```

### Result

| Category | Count | Percentage |
|---|---:|---:|
| Normal (<=3%) | 22,581 | 75.27% |
| Tinggi (>3%) | 7,419 | 24.73% |
| **Total** | **30,000** | **100%** |

Sekitar **24.73%** catatan pada sampel memiliki kadar silika konsentrat di atas 3%. Ambang ini merupakan batas ilustratif yang digunakan dalam notebook, bukan klaim standar mutu universal.

## 5. Comparing Process Conditions

Notebook membandingkan rata-rata beberapa parameter proses antara kelompok `Normal (<=3%)` dan `Tinggi (>3%)`.

| Parameter | Normal (<=3%) | Tinggi (>3%) |
|---|---:|---:|
| `% Silica Feed` | 14.50 | 15.06 |
| `% Iron Feed` | 56.41 | 55.97 |
| `Ore Pulp pH` | 9.80 | 9.69 |
| `Starch Flow` | 2,931.66 | 2,760.81 |

### Interpretation

- Rata-rata `% Silica Feed` hanya berbeda 0.56 poin persentase antara kedua kelompok.
- Rata-rata `Ore Pulp pH` sedikit lebih rendah pada kelompok hasil silika tinggi.
- `Starch Flow` rata-rata lebih rendah sekitar 171 unit pada kelompok hasil silika tinggi.

Perbandingan ini bersifat deskriptif dan agregat. Perbedaan rata-rata tersebut menunjukkan pola yang layak ditelusuri, tetapi tidak dengan sendirinya membuktikan hubungan sebab-akibat.

## 6. Exploratory Data Analysis & Visualization

Notebook menggunakan beberapa visualisasi untuk memahami kualitas feed, hasil konsentrat, dan kondisi proses.

### A. Silica Concentrate Histogram

Histogram `% Silica Concentrate` menunjukkan:

- Distribusi condong ke kanan (*right-skewed*).
- Kepadatan terbesar berada di kisaran 1–2%.
- Median berada di sekitar 2%.
- Mean berada di sekitar 2.3%, lebih tinggi daripada median karena adanya nilai-nilai tinggi.
- Terdapat catatan yang melewati ambang ilustratif 3%, dengan nilai maksimum sampel 5.53%.

### B. Iron Feed & Silica Feed Histograms

Histogram feed menunjukkan:

- Sebagian besar `% Iron Feed` berada di kisaran 48–60%, dengan frekuensi tinggi pada nilai sekitar 64%.
- `% Silica Feed` memiliki sebaran lebih lebar, dari sekitar 2% hingga 33%, dengan frekuensi tinggi di sekitar 6%.

Hal ini memperlihatkan bahwa kualitas bahan baku yang masuk bervariasi.

### C. Silica Feed vs Silica Concentrate

Scatter plot digunakan untuk memeriksa apakah kadar silika feed yang lebih tinggi berkaitan dengan kadar silika konsentrat yang lebih tinggi.

Hasil korelasi:

- **Pearson correlation:** 0.062
- **Spearman correlation:** 0.066
- **Linear trend slope:** 0.010

Korelasi tersebut sangat lemah pada sampel ini. Catatan dengan kadar silika konsentrat di atas 3% muncul pada berbagai tingkat `% Silica Feed`, sehingga kadar silika feed saja tidak cukup menjelaskan variasi kualitas hasil.

### D. Silica Concentrate by Ore Pulp pH

Notebook mengelompokkan `Ore Pulp pH` ke dalam empat rentang:

- `< 9.5`
- `9.5–9.8`
- `9.8–10.1`
- `> 10.1`

Boxplot menunjukkan median `% Silica Concentrate` yang cenderung turun seiring meningkatnya kelompok pH:

| Ore Pulp pH | Approx. Median Silica Concentrate |
|---|---:|
| `< 9.5` | 2.2% |
| `9.5–9.8` | 2.1% |
| `9.8–10.1` | 1.9% |
| `> 10.1` | 1.75% |

Pada kelompok pH yang lebih tinggi, sebaran tengah kadar silika konsentrat cenderung lebih rendah. Namun, outlier di atas 4–5% masih terlihat. Temuan ini merupakan asosiasi pada data observasi, bukan bukti bahwa peningkatan pH secara langsung menyebabkan penurunan silika.

## 7. Key Findings

1. **Feed quality varies.** Rata-rata `% Iron Feed` sekitar 56.3%, sementara `% Silica Feed` memiliki rentang yang lebar.
2. **High silica output is not rare in the sample.** Sebanyak 7,419 dari 30,000 catatan (24.73%) memiliki `% Silica Concentrate` di atas ambang ilustratif 3%.
3. **Silica Feed has very weak correlation with final silica.** Pearson correlation sebesar 0.062 dan Spearman sebesar 0.066.
4. **Starch Flow is lower in the high-silica group.** Rata-ratanya 2,760.81 dibanding 2,931.66 pada kelompok normal.
5. **Higher Ore Pulp pH is associated with lower median silica concentrate.** Median turun secara bertahap pada kelompok pH yang lebih tinggi.
6. **Process conditions deserve further investigation.** Hasil eksplorasi mengarahkan perhatian pada parameter proses seperti pH, reagent flow, dan kondisi kolom flotasi, bukan hanya kualitas feed.

## 8. Operational Insights & Recommendations

Berdasarkan interpretasi di notebook, beberapa hal yang dapat menjadi fokus evaluasi operasional:

### Monitor Ore Pulp pH

Notebook mengamati bahwa kelompok pH yang lebih tinggi, khususnya sekitar 9.8–10.1, memiliki median kadar silika konsentrat lebih rendah. Rentang tersebut dapat menjadi fokus investigasi dan pemantauan lebih lanjut, bukan langsung dianggap sebagai set point universal.

### Monitor Starch Flow

Kelompok hasil silika tinggi memiliki rata-rata `Starch Flow` lebih rendah. Konsistensi aliran reagen dapat dievaluasi bersama parameter lain untuk memahami kondisi ketika kadar silika produk meningkat.

### Expand Process Monitoring

Analisis lanjutan dapat menggabungkan pH, reagent flow, air flow, dan level pada tujuh kolom flotasi untuk mengidentifikasi kombinasi kondisi proses yang berkaitan dengan kualitas konsentrat.

### Investigate High-Silica Events

Catatan dengan `% Silica Concentrate` di atas 3% dapat ditelusuri lebih jauh berdasarkan waktu, kondisi feed, dan pengaturan proses agar pola operasionalnya lebih jelas.

## 9. Limitations & Next Steps

- Analisis menggunakan sampel acak 30,000 baris dari 737,453 baris data asli; hasil eksplorasi sampel belum tentu menangkap seluruh variasi dataset.
- Kolom `date` masih bertipe `object` pada pemeriksaan yang ditampilkan; analisis time-series belum dilakukan.
- Ambang 3% untuk `high_silica` bersifat ilustratif.
- Perbandingan kelompok dan korelasi tidak membuktikan hubungan sebab-akibat.
- Notebook ini belum melakukan predictive modeling atau machine learning.

Pengembangan berikutnya yang dapat dilakukan:

- Mengubah `date` menjadi tipe datetime dan menganalisis tren berdasarkan waktu.
- Menganalisis korelasi dan interaksi parameter proses secara lebih mendalam.
- Membandingkan kondisi normal dan high-silica berdasarkan masing-masing kolom flotasi.
- Menguji temuan pada keseluruhan dataset atau sampel yang lebih besar.
- Jika tujuan proyek berkembang menjadi prediksi kualitas, membangun model untuk memperkirakan `% Silica Concentrate` atau mengklasifikasikan kondisi high-silica.

## Workflow Summary

1. Import Python libraries.
2. Download and load the mining flotation dataset.
3. Inspect the first rows and dataset dimensions.
4. Sample 30,000 records from the original dataset.
5. Review data types and descriptive statistics.
6. Check missing values.
7. Create `high_silica` category using the illustrative 3% threshold.
8. Compare feed and process parameters between the two quality groups.
9. Visualize silica concentrate, feed composition, feed-output relationship, and pH groups.
10. Summarize findings and operational recommendations.

## Tech Stack

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebook / Google Colab
- Kaggle API / Kaggle Dataset

## Project Structure

```text
Day_01_Understanding_mine_flotation_data/
│
├── Day_01_Understanding_mine_flotation_data.ipynb
└── README.md
```

## Conclusion

Day 01 menjadi pengenalan terhadap data proses industri melalui dataset flotasi bijih besi. Dari sampel 30,000 catatan, sekitar 24.73% hasil memiliki kadar silika konsentrat di atas ambang ilustratif 3%.

Analisis menunjukkan bahwa hubungan linear antara `% Silica Feed` dan `% Silica Concentrate` sangat lemah. Sementara itu, kelompok hasil dengan silika tinggi memiliki rata-rata `Starch Flow` dan `Ore Pulp pH` yang sedikit lebih rendah, dan boxplot memperlihatkan median kadar silika yang cenderung menurun pada kelompok pH lebih tinggi.

Temuan ini menjadi hipotesis awal untuk eksplorasi lebih lanjut terhadap pengendalian proses flotasi. Pada tahap ini, notebook berfokus pada pemahaman data dan insight deskriptif, belum pada pembangunan model prediksi.

---

**Author:** [Your Name]  
**Project:** Data Analytics / Machine Learning Learning Journey — Day 01
