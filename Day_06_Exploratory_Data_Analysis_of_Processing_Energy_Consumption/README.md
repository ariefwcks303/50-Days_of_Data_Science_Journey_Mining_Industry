# Day 06 — Exploratory Data Analysis of Processing Energy Consumption

<p align="center">
  <strong>Industrial Energy Consumption Analysis</strong><br>
  Analisis konsumsi energi listrik pada industri pemrosesan untuk memahami pola penggunaan energi, kategori beban, variasi operasional mingguan, dan hubungannya dengan emisi CO₂.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Project-Data%20Science%20Mining-blue" alt="Project: Data Science Mining">
  <img src="https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple" alt="Type: Exploratory Data Analysis">
  <img src="https://img.shields.io/badge/Method-Time%20Series%20%26%20Statistical%20Analysis-green" alt="Method: Time Series and Statistical Analysis">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>

## Overview

Pada Day 06, saya mengeksplorasi **data konsumsi energi listrik pada industri** untuk memahami bagaimana energi digunakan sepanjang waktu, bagaimana konsumsi berbeda berdasarkan kategori beban, dan seberapa kuat hubungan antara konsumsi listrik dengan emisi CO₂.

Pengolahan bijih dan mineral membutuhkan energi listrik dalam jumlah besar, misalnya untuk proses penggilingan, flotasi, dan peleburan. Dataset yang digunakan pada notebook ini berasal dari industri baja dan digunakan sebagai **proxy untuk memahami pola konsumsi energi pada fasilitas pemrosesan mineral**, bukan sebagai data langsung dari pabrik pengolahan mineral.

Fokus analisis meliputi pengolahan kolom waktu, agregasi konsumsi energi harian, perbandingan konsumsi berdasarkan `Load_Type`, pola konsumsi berdasarkan hari dalam seminggu, serta hubungan antara `Usage_kWh` dan `CO2(tCO2)`.

Notebook ini berfokus pada **data understanding dan exploratory data analysis (EDA)**. Belum dilakukan training model machine learning.

## Learning Objectives

- Memahami karakteristik dataset konsumsi energi industri.
- Membaca dataset menggunakan Pandas.
- Memeriksa struktur, ukuran, tipe data, dan kelengkapan dataset.
- Mengubah kolom `date` menjadi tipe `datetime`.
- Mengekstrak informasi waktu seperti jam dan tanggal.
- Mengurutkan data berdasarkan waktu.
- Melakukan resampling deret waktu untuk menghitung total konsumsi energi harian.
- Mengidentifikasi puncak dan lembahan konsumsi energi harian.
- Membandingkan distribusi konsumsi energi berdasarkan `Load_Type`.
- Menghitung rata-rata konsumsi energi berdasarkan hari dalam seminggu.
- Menganalisis hubungan konsumsi listrik dengan emisi CO₂.
- Menggunakan korelasi Pearson, Spearman, dan regresi linear sederhana.
- Menarik insight operasional terkait penggunaan energi dan emisi karbon.

## Dataset

Dataset yang digunakan adalah **Steel Industry Energy Consumption**, yang berisi data konsumsi listrik sebuah pabrik industri baja. Data dicatat setiap 15 menit selama satu tahun dan digunakan sebagai proxy untuk eksplorasi pola konsumsi energi pada industri pemrosesan mineral.

- **Source:** [Kaggle — Steel Industry Energy Consumption](https://www.kaggle.com/datasets/csafrit2/steel-industry-energy-consumption)
- **File:** `Steel_industry_data.csv`
- **Dataset size:** 35,040 rows × 11 columns
- **Time coverage:** 1 Januari 2018 – 31 Desember 2018
- **Recording interval:** 15 menit
- **Records per day:** 96
- **Missing values:** 0 pada seluruh kolom berdasarkan pemeriksaan `df.info()`.

### Understanding the Dataset

Setiap baris merepresentasikan satu rekaman konsumsi energi pada interval waktu 15 menit. Dataset mencakup konsumsi listrik, emisi CO₂, parameter daya reaktif, power factor, serta informasi waktu dan kategori beban.

| Feature | Description |
|---|---|
| `date` | Waktu pencatatan konsumsi energi |
| `Usage_kWh` | Konsumsi energi listrik dalam kWh; fokus utama analisis |
| `Lagging_Current_Reactive.Power_kVarh` | Energi daya reaktif pada kondisi lagging |
| `Leading_Current_Reactive_Power_kVarh` | Energi daya reaktif pada kondisi leading |
| `CO2(tCO2)` | Emisi CO₂ terkait konsumsi energi |
| `Lagging_Current_Power_Factor` | Power factor pada kondisi lagging |
| `Leading_Current_Power_Factor` | Power factor pada kondisi leading |
| `NSM` | Jumlah detik sejak tengah malam |
| `WeekStatus` | Kategori hari kerja (`Weekday`) atau akhir pekan (`Weekend`) |
| `Day_of_week` | Nama hari dalam seminggu |
| `Load_Type` | Kategori beban: `Light_Load`, `Medium_Load`, atau `Maximum_Load` |

Konsumsi energi merupakan salah satu komponen penting biaya operasional industri. Penggunaannya juga berkaitan dengan emisi karbon yang dihasilkan dari aktivitas pemakaian listrik.

## 1. Data Loading & Initial Inspection

Library yang digunakan:

- `numpy` untuk operasi numerik.
- `pandas` untuk membaca, membersihkan, dan mengolah data.
- `matplotlib` untuk visualisasi.
- `seaborn` untuk visualisasi statistik.
- `scipy.signal.find_peaks` untuk mendeteksi puncak dan lembahan pada deret waktu.

Dataset diunduh melalui Kaggle API, kemudian file CSV dibaca menggunakan Pandas:

```python
df = pd.read_csv("Steel_industry_data.csv")
df.head()
```

Hasil pemeriksaan awal menggunakan `df.info()` menunjukkan:

- **35,040 rows × 11 columns.**
- 6 kolom bertipe `float64`.
- 1 kolom bertipe `int64`.
- 4 kolom bertipe `object`.
- Seluruh kolom memiliki 35,040 nilai non-null.
- Tidak ditemukan missing values pada pemeriksaan awal.

Kolom `date` masih bertipe `object`, sehingga perlu dikonversi ke `datetime` sebelum digunakan untuk analisis deret waktu.

## 2. DateTime Conversion & Time Feature Extraction

Notebook membuat fungsi `siapkan_kolom_waktu()` untuk menyiapkan data waktu.

```python
def siapkan_kolom_waktu(
    df: pd.DataFrame,
    date_col: str = "date",
    dayfirst: bool = True,
) -> pd.DataFrame:
    data = df.copy()
    data[date_col] = pd.to_datetime(
        data[date_col],
        dayfirst=dayfirst,
        errors="coerce"
    )

    data = (
        data.dropna(subset=[date_col])
        .sort_values(date_col)
        .reset_index(drop=True)
    )

    dt_accessor = data[date_col].dt
    data["jam"] = dt_accessor.hour
    data["tanggal"] = dt_accessor.date

    return data
```

Tahapan yang dilakukan:

1. Mengonversi `date` menjadi tipe `datetime`.
2. Menghapus baris yang gagal dikonversi menjadi tanggal.
3. Mengurutkan data secara kronologis.
4. Membuat kolom `jam` untuk mengekstrak jam pencatatan.
5. Membuat kolom `tanggal` untuk menyimpan tanggal tanpa informasi jam.

### Result

- **Start date:** 2018-01-01 00:00:00
- **End date:** 2018-12-31 23:45:00
- **Time interval:** 15 menit
- **New features:** `jam` dan `tanggal`

Konversi ini memungkinkan analisis konsumsi energi berdasarkan waktu, termasuk agregasi harian dan perbandingan pola antarhari.

## 3. Exploratory Data Analysis & Visualization

Notebook menggunakan empat pendekatan visualisasi dan analisis untuk memahami pola konsumsi energi.

### A. Daily Energy Consumption Trend (Daily Resample)

Notebook mengagregasikan konsumsi energi menjadi total harian menggunakan `resample()`.

```python
def agregasi_energi_harian(
    df: pd.DataFrame,
    date_col: str = "date",
    target_col: str = "Usage_kWh",
    freq: str = "D",
) -> pd.Series:
    return (
        df.set_index(date_col)[target_col]
        .resample(freq)
        .sum()
    )

harian = agregasi_energi_harian(df)
```

Selanjutnya, fungsi `find_peaks()` digunakan untuk mendeteksi puncak dan lembahan pada deret waktu. Parameter `prominence=200` dan `distance=3` digunakan untuk menyaring fluktuasi kecil dan mengatur jarak minimum antartitik ekstrem.

**Observations:**

- Konsumsi listrik harian sepanjang 2018 menunjukkan pola naik-turun yang berulang.
- Terdapat pola puncak dan lembahan yang tajam, yang kemungkinan berkaitan dengan ritme operasional mingguan.
- Puncak tertinggi terjadi sekitar Januari–Februari 2018, dengan konsumsi lebih dari 7,000 kWh/hari.
- Setelah Februari, puncak harian umumnya berada di kisaran 2,500–5,000 kWh/hari.
- Beberapa lembahan berada pada kisaran sekitar 250–400 kWh/hari.

**Interpretation:**

Konsumsi energi tidak konstan sepanjang tahun, melainkan memperlihatkan pola siklikal yang kuat. Perbedaan aktivitas antara hari kerja dan akhir pekan menjadi salah satu faktor yang perlu ditelusuri melalui analisis `Day_of_week` dan `WeekStatus`.

### B. Consumption by Type of Load (Boxplot)

Boxplot digunakan untuk membandingkan distribusi `Usage_kWh` pada tiga kategori `Load_Type`:

- `Light_Load`
- `Medium_Load`
- `Maximum_Load`

**Observations:**

| Load Type | Approx. Median Usage | Observations |
|---|---:|---|
| `Light_Load` | 3–4 kWh | Mayoritas data terkonsentrasi pada nilai rendah, tetapi memiliki banyak outlier hingga sekitar 140 kWh |
| `Medium_Load` | 34 kWh | IQR sekitar 4–61 kWh, dengan whisker atas sekitar 147 kWh |
| `Maximum_Load` | 56 kWh | IQR sekitar 42–75 kWh, dengan outlier hingga sekitar 152 kWh |

**Interpretation:**

- Median konsumsi meningkat dari `Light_Load` ke `Medium_Load`, kemudian `Maximum_Load`.
- `Maximum_Load` memiliki median konsumsi tertinggi.
- `Light_Load` memiliki sebaran outlier ke arah nilai konsumsi yang tinggi.
- Label `Load_Type` tidak sepenuhnya menggambarkan besaran `Usage_kWh` secara linear pada setiap observasi.

Outlier pada kategori `Light_Load` dapat menjadi objek investigasi lebih lanjut, misalnya untuk memeriksa lonjakan konsumsi atau kondisi operasional tertentu. Namun, penyebabnya belum dapat dipastikan hanya dari boxplot.

### C. Average Daily Consumption by Day of the Week

Notebook menghitung rata-rata `Usage_kWh` berdasarkan `Day_of_week`, kemudian menampilkannya menggunakan bar chart.

Urutan hari yang digunakan:

1. Monday
2. Tuesday
3. Wednesday
4. Thursday
5. Friday
6. Saturday
7. Sunday

**Result:**

| Day | Average Usage (kWh per 15-minute record) |
|---|---:|
| Monday | 32.25–35.11 kWh |
| Tuesday | 34.43 kWh |
| Wednesday | 32.25–35.11 kWh |
| Thursday | 35.11 kWh |
| Friday | 32.25–35.11 kWh |
| Saturday | 15.92 kWh |
| Sunday | 7.55 kWh |

**Observations:**

- Rata-rata konsumsi pada hari kerja (Senin–Jumat) berada pada kisaran 32.25–35.11 kWh per interval.
- Kamis menjadi hari dengan rata-rata konsumsi tertinggi, yaitu 35.11 kWh.
- Selasa berada di posisi berikutnya dengan 34.43 kWh.
- Konsumsi pada Sabtu turun menjadi 15.92 kWh.
- Minggu menjadi hari dengan rata-rata terendah, yaitu 7.55 kWh.

**Interpretation:**

Pola tersebut memperlihatkan perbedaan yang jelas antara hari kerja dan akhir pekan. Konsumsi yang lebih rendah pada Sabtu dan Minggu konsisten dengan kemungkinan penurunan aktivitas operasional, tetapi status operasi atau aktivitas pemeliharaan tidak dibuktikan secara langsung oleh analisis ini.

### D. Relationship Between Energy Consumption and CO₂ Emissions

Notebook menganalisis hubungan antara `Usage_kWh` dan `CO2(tCO2)` menggunakan:

- Pearson correlation.
- Spearman correlation.
- Regresi linear sederhana.
- Koefisien determinasi (`R²`).
- Scatter plot dengan garis regresi dan warna berdasarkan `Load_Type`.

**Correlation & Regression Results:**

| Metric | Value |
|---|---:|
| Pearson correlation | 0.9882 |
| Spearman correlation | 0.8815 |
| Linear regression slope | 0.0005 |
| Intercept | -0.0015 |
| R-squared | 0.9765 |

**Observations:**

- Pearson correlation sebesar 0.9882 menunjukkan hubungan linear positif yang sangat kuat.
- Spearman correlation sebesar 0.8815 juga menunjukkan hubungan monotonik positif yang kuat.
- Nilai R² sebesar 0.9765 menunjukkan sekitar 97.65% variasi `CO2(tCO2)` dapat dijelaskan oleh regresi linear terhadap `Usage_kWh` pada data ini.
- Slope sebesar 0.0005 menunjukkan kenaikan emisi sekitar 0.0005 tCO₂ untuk setiap kenaikan 1 kWh konsumsi listrik berdasarkan garis regresi.
- Titik pada scatter plot membentuk pola horizontal bertingkat karena nilai CO₂ tercatat dalam kelipatan 0.01 tCO₂.
- Ketiga kategori `Load_Type` saling tumpang tindih mengikuti pola hubungan energi dan emisi yang sama.

**Interpretation:**

Konsumsi listrik dan emisi CO₂ bergerak hampir searah secara linear. Perbedaan nilai Pearson dan Spearman antara lain dapat berkaitan dengan nilai emisi yang berulang (*ties*) akibat pencatatan CO₂ dalam dua angka desimal.

Hubungan yang sangat kuat ini juga menunjukkan bahwa `CO2(tCO2)` berpotensi menjadi fitur yang redundan jika digunakan bersamaan dengan `Usage_kWh` untuk memprediksi konsumsi energi. Hal ini perlu diperhatikan dalam pengembangan model prediktif.

## 4. Key Findings

1. **Complete dataset with regular time intervals.** Dataset memiliki 35,040 baris dan 11 kolom, mencakup pencatatan setiap 15 menit sepanjang 2018 tanpa missing values pada pemeriksaan awal.
2. **Strong recurring daily consumption pattern.** Total konsumsi energi harian menunjukkan pola puncak dan lembahan yang berulang sepanjang tahun.
3. **Energy usage differs by load category.** Median `Usage_kWh` meningkat dari sekitar 3–4 kWh pada `Light_Load`, 34 kWh pada `Medium_Load`, hingga sekitar 56 kWh pada `Maximum_Load`.
4. **Light_Load contains high-consumption outliers.** Walaupun median konsumsi rendah, kategori ini memiliki outlier hingga sekitar 140 kWh.
5. **Weekday consumption is higher than weekend consumption.** Rata-rata konsumsi hari kerja berada pada kisaran 32.25–35.11 kWh per interval, sedangkan Sabtu 15.92 kWh dan Minggu 7.55 kWh.
6. **Energy consumption is strongly associated with CO₂ emissions.** Pearson correlation mencapai 0.9882 dan R² regresi linear sebesar 0.9765.
7. **CO₂ may be redundant in an energy prediction model.** Hubungan yang sangat kuat antara konsumsi dan emisi perlu diperhatikan ketika menentukan fitur untuk model prediksi.

## 5. Operational Insights & Recommendations

Berdasarkan hasil eksplorasi, beberapa hal yang dapat menjadi fokus evaluasi operasional:

### Review Weekly Energy Consumption

Konsumsi energi rata-rata lebih tinggi pada hari kerja dan turun pada akhir pekan. Pola ini dapat digunakan untuk mengevaluasi jadwal operasi, kebutuhan energi, serta perencanaan aktivitas pemeliharaan.

### Investigate High Usage in Light_Load

Kategori `Light_Load` memiliki outlier konsumsi yang cukup tinggi. Perlu dilakukan pemeriksaan lebih lanjut terhadap waktu kejadian, kondisi mesin, dan aktivitas operasional untuk mengetahui apakah lonjakan tersebut berkaitan dengan startup, standby, atau kondisi lainnya.

### Evaluate Peak Shifting Opportunities

Perbedaan konsumsi antara hari kerja dan akhir pekan dapat menjadi bahan evaluasi penjadwalan proses non-kritis. Pemindahan beban hanya perlu dipertimbangkan setelah memeriksa kapasitas produksi, keselamatan, jadwal pemeliharaan, dan struktur tarif listrik yang berlaku.

### Monitor Energy-Related CO₂ Emissions

Hubungan kuat antara `Usage_kWh` dan `CO2(tCO2)` menunjukkan bahwa pemantauan konsumsi listrik dapat menjadi salah satu pendekatan untuk memantau emisi terkait energi. Pengurangan konsumsi listrik berpotensi menurunkan emisi sesuai faktor emisi yang digunakan dalam dataset.

## 6. Limitations & Next Steps

- Dataset berasal dari industri baja dan digunakan sebagai proxy untuk memahami pola konsumsi energi pada industri pemrosesan mineral; dataset ini bukan pengukuran langsung dari pabrik pengolahan mineral.
- Analisis hanya mencakup satu tahun data, yaitu 2018.
- Tidak ditemukan missing values berdasarkan pemeriksaan awal, tetapi notebook tidak melakukan investigasi kualitas data secara mendalam untuk seluruh fitur.
- Analisis harian menggunakan penjumlahan `Usage_kWh` dari rekaman 15 menit.
- Boxplot memperlihatkan outlier, tetapi notebook belum melakukan investigasi penyebab atau penanganan outlier.
- Penurunan konsumsi pada akhir pekan dapat mengindikasikan penurunan aktivitas operasional, tetapi penyebabnya belum diverifikasi menggunakan data produksi atau jadwal pemeliharaan.
- Korelasi dan regresi menunjukkan asosiasi, bukan hubungan sebab-akibat.
- Nilai `CO2(tCO2)` memiliki pola diskret/bertingkat karena pencatatan hingga dua angka desimal.
- Notebook ini belum melakukan forecasting, predictive modeling, atau machine learning.

Pengembangan berikutnya yang dapat dilakukan:

- Menganalisis konsumsi berdasarkan jam untuk menemukan pola intrahari.
- Membandingkan konsumsi berdasarkan `WeekStatus` dan `Day_of_week` secara lebih mendalam.
- Menginvestigasi tanggal dengan puncak dan lembahan konsumsi ekstrem.
- Menguji outlier pada `Light_Load` berdasarkan waktu dan parameter daya lainnya.
- Menganalisis hubungan `Usage_kWh` dengan power factor dan daya reaktif.
- Mengembangkan forecasting konsumsi energi harian atau per interval.
- Jika membangun model prediksi konsumsi energi, mengevaluasi potensi data leakage dan redundansi fitur `CO2(tCO2)`.

## Workflow Summary

1. Import Python libraries.
2. Download the Steel Industry Energy Consumption dataset using Kaggle API.
3. Extract and load `Steel_industry_data.csv`.
4. Inspect the first rows, dataset dimensions, data types, and missing values.
5. Convert `date` into datetime format.
6. Sort observations chronologically and extract `jam` and `tanggal`.
7. Resample `Usage_kWh` into daily total energy consumption.
8. Detect peaks and troughs in the daily energy time series.
9. Compare energy consumption distributions across `Load_Type`.
10. Calculate and visualize average consumption by day of the week.
11. Analyze the relationship between `Usage_kWh` and `CO2(tCO2)`.
12. Calculate Pearson correlation, Spearman correlation, and linear regression metrics.
13. Summarize findings and operational recommendations.

## Tech Stack

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- SciPy
- Jupyter Notebook / Google Colab
- Kaggle API
- Kaggle Dataset

## Project Structure

```text
Day_06_Exploratory_Data_Analysis_of_Processing_Energy_Consumption/
│
├── Day_06_Exploratory_Data_Analysis_of_Processing_Energy_Consumption.ipynb
└── README.md
```

## Conclusion

Notebook Day 06 mengeksplorasi pola konsumsi energi listrik industri menggunakan dataset Steel Industry Energy Consumption yang mencakup 35.040 catatan sepanjang tahun 2018 dengan interval 15 menit dan tanpa nilai kosong pada pemeriksaan awal.

Hasil EDA menunjukkan adanya pola konsumsi yang berulang antarwaktu, perbedaan sebaran antar kategori Load_Type, serta penurunan rata-rata yang tajam pada akhir pekan dibanding hari kerja. Pada kategori Light_Load, nilai median tergolong rendah, namun terdapat outlier tinggi hingga sekitar 140 kWh yang patut dicermati.

Hubungan antara Usage_kWh dan CO2(tCO2) juga tergolong sangat kuat secara linier (korelasi Pearson = 0,9882, $R^2 = 0,9765$), menandakan bahwa efisiensi konsumsi energi berbanding lurus dengan penurunan emisi karbon sekaligus mengindikasikan potensi redundansi fitur jika keduanya dipakai bersama dalam pemodelan.

Sebagai catatan keterbatasan, karena dataset ini berasal dari industri baja, temuan yang diperoleh berfungsi sebagai proxy awal untuk konteks pemrosesan mineral dan belum dapat langsung mewakili karakteristik beban energi pabrik pengolahan mineral secara penuh.

---

## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

---
<p align="center">
  <em>Day 6 Data Exploration in Processing Energy Consumtion.</em>
</p>

