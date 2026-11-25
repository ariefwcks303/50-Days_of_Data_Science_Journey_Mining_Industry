<div align="center">

# Day 09 — Missing Values & Outlier Analysis of Flotation Sensor Data

**Analisis kualitas data sensor proses flotasi melalui pemeriksaan missing values, deteksi outlier berbasis Interquartile Range (IQR), serta evaluasi karakteristik dan distribusi data parameter operasional pertambangan.**

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Project-Data%20Science%20Mining-blue" alt="Project">
  <img src="https://img.shields.io/badge/Type-Data%20Cleaning%20%26%20EDA-purple" alt="Type">
  <img src="https://img.shields.io/badge/Focus-Flotation%20Sensor%20%26%20Outlier%20Analysis-green" alt="Focus">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>

## 📌 Project Overview

Pada Day 09, analisis difokuskan pada pemeriksaan kualitas data sensor dalam proses **iron ore flotation**. Data sensor industri dapat mengandung nilai yang tidak lengkap (*missing values*) maupun nilai ekstrem (*outliers*) akibat gangguan instrumen, perubahan kondisi proses, atau variasi operasional.

Sebelum data digunakan untuk analisis lanjutan atau pemodelan Machine Learning, penting untuk memahami kelengkapan data, mengidentifikasi nilai ekstrem, dan mengevaluasi strategi penanganannya.

Pada proyek ini, deteksi outlier dilakukan menggunakan metode **Interquartile Range (IQR)**. Selain itu, dilakukan perbandingan statistik sebelum dan sesudah outlier dikeluarkan, serta visualisasi distribusi sebelum dan sesudah penerapan *clipping* (*winsorizing*).

## 🎯 Learning Objectives

- Memeriksa dan mengidentifikasi *missing values* pada data sensor proses flotasi.
- Memahami karakteristik beberapa variabel sensor yang digunakan dalam proses pengolahan bijih besi.
- Mendeteksi outlier menggunakan metode **Interquartile Range (IQR)**.
- Membuat fungsi Python untuk menandai dan merangkum outlier pada beberapa kolom.
- Memahami pengaruh outlier terhadap nilai rata-rata (*mean*) dan median.
- Membandingkan strategi penanganan outlier dengan membiarkan nilai asli dan menerapkan *clipping*.
- Memvisualisasikan distribusi data menggunakan boxplot, histogram, dan scatter plot.
- Menginterpretasikan potensi anomali sensor serta hubungannya dengan kualitas produk flotasi.

## 📊 Dataset

Dataset yang digunakan adalah **Quality Prediction in a Mining Process**, yang berisi data operasional dari proses pengolahan bijih besi menggunakan metode flotasi.

- **Source:** [Kaggle — Quality Prediction in a Mining Process](https://www.kaggle.com/datasets/edumagalhaes/quality-prediction-in-a-mining-process)
- **Original Dataset Size:** 737,453 rows × 24 columns
- **Sampling Size:** 30,000 rows
- **Sampling Method:** Random Sampling
- **Random State:** 42
- **File Name:** `MiningProcess_Flotation_Plant_Database.csv`

Dataset dibaca menggunakan `decimal=","` untuk menangani format angka dengan koma sebagai pemisah desimal.

### Selected Features

Analisis menggunakan enam variabel yang berkaitan dengan parameter proses dan kualitas hasil flotasi.

| Feature | Description |
|---|---|
| `Ore Pulp pH` | Tingkat keasaman bubur bijih |
| `Ore Pulp Density` | Kepadatan bubur bijih |
| `Starch Flow` | Laju aliran reagen pati |
| `Amina Flow` | Laju aliran reagen amina |
| `Flotation Column 01 Air Flow` | Laju aliran udara pada kolom flotasi pertama |
| `% Silica Concentrate` | Persentase kadar silika pada hasil konsentrat |

## 🛠️ Tools & Libraries

Analisis dilakukan menggunakan Python dan beberapa library untuk pengolahan data serta visualisasi.

- **Python** — Main programming language.
- **Pandas** — Data loading, sampling, data inspection, missing value analysis, dan manipulasi data.
- **NumPy** — Numerical computation.
- **Matplotlib** — Data visualization.
- **Seaborn** — Statistical visualization.
- **Kaggle API** — Dataset downloading.

## 🔍 Analysis Workflow

### 1. Import Libraries

Mengimpor library yang diperlukan untuk proses analisis dan visualisasi.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

### 2. Dataset Loading

Dataset diunduh menggunakan Kaggle API dan dimuat menggunakan Pandas.

```python
df = pd.read_csv(
    '/content/MiningProcess_Flotation_Plant_Database.csv',
    decimal=","
)
```

Pemeriksaan awal dilakukan untuk mengetahui ukuran dataset dan memastikan data berhasil dimuat.

```python
df.shape
df.head()
```

Ukuran dataset asli adalah:

```text
(737453, 24)
```

### 3. Data Sampling

Karena dataset memiliki lebih dari 737 ribu baris, analisis menggunakan sampel sebanyak 30.000 baris agar proses eksplorasi lebih ringan.

```python
df_sample = df.sample(
    n=30000,
    random_state=42
)
```

Hasil sampling:

```text
(30000, 24)
```

Penggunaan `random_state=42` bertujuan agar proses sampling dapat direproduksi.

### 4. Feature Selection

Memilih enam variabel sensor dan kualitas produk yang menjadi fokus analisis.

```python
sensor = [
    "Ore Pulp pH",
    "Ore Pulp Density",
    "Starch Flow",
    "Amina Flow",
    "Flotation Column 01 Air Flow",
    "% Silica Concentrate"
]
```

### 5. Missing Value Analysis

Memeriksa jumlah nilai kosong pada setiap variabel yang telah dipilih.

```python
df_sample[sensor].isnull().sum()
```

Hasil pemeriksaan menunjukkan bahwa seluruh variabel memiliki **0 missing values** pada sampel yang digunakan.

Dengan demikian, tidak diperlukan proses imputasi maupun penghapusan baris akibat nilai kosong pada enam kolom tersebut.

### 6. Outlier Detection Using IQR

Deteksi outlier menggunakan metode **Interquartile Range (IQR)**.

IQR merupakan selisih antara kuartil ketiga (Q3) dan kuartil pertama (Q1).

\[
IQR = Q3 - Q1
\]

Batas bawah dan batas atas ditentukan menggunakan rumus:

\[
Lower\ Bound = Q1 - 1.5 \times IQR
\]

\[
Upper\ Bound = Q3 + 1.5 \times IQR
\]

Nilai yang berada di bawah batas bawah atau di atas batas atas akan ditandai sebagai outlier.

Notebook membuat fungsi `tandai_outlier_iqr()` untuk menghasilkan mask Boolean yang menunjukkan baris dengan nilai ekstrem.

```python
def tandai_outlier_iqr(
    data: pd.DataFrame,
    kolom: str,
    k: float = 1.5
) -> pd.Series:

    seri = data[kolom]

    if seri.dtype == object:
        seri = pd.to_numeric(
            seri.astype(str).str.replace(",", "."),
            errors="coerce"
        )

    q1 = seri.quantile(0.25)
    q3 = seri.quantile(0.75)
    iqr = q3 - q1

    batas_bawah = q1 - k * iqr
    batas_atas = q3 + k * iqr

    return (
        (seri < batas_bawah) |
        (seri > batas_atas)
    )
```

Selanjutnya, fungsi `ringkas_outlier_iqr()` digunakan untuk menghitung jumlah dan persentase outlier pada setiap variabel.

Hasilnya disajikan dalam tabel agar tingkat penyimpangan antarvariabel dapat dibandingkan.

### 7. The Impact of Outliers on Statistics

Menganalisis pengaruh outlier terhadap statistik deskriptif menggunakan variabel `Amina Flow`.

Statistik yang dibandingkan meliputi:

- Mean sebelum menghilangkan outlier.
- Median.
- Mean setelah baris outlier dikeluarkan.

Perbandingan ini bertujuan memahami bagaimana nilai ekstrem dapat memengaruhi representasi nilai rata-rata suatu variabel.

### 8. Exploratory Data Analysis & Visualization

Beberapa visualisasi digunakan untuk memahami distribusi data dan karakteristik outlier.

#### a. Boxplot for Sensor Variables

Menggunakan boxplot untuk mengidentifikasi persebaran data dan titik ekstrem pada:

- `Ore Pulp pH`
- `Ore Pulp Density`
- `% Silica Concentrate`

Boxplot membantu melihat median, rentang interkuartil, dan nilai yang berada di luar batas IQR.

#### b. Histogram Before vs. After Clipping

Membuat fungsi `clip_iqr()` untuk membatasi nilai ekstrem berdasarkan batas IQR.

Nilai yang berada di bawah batas bawah akan disesuaikan ke batas bawah, sedangkan nilai yang berada di atas batas atas akan disesuaikan ke batas atas.

Analisis clipping dilakukan pada `Amina Flow` dengan membandingkan histogram data asli dan data setelah clipping.

Tujuannya adalah memahami perubahan distribusi tanpa menghapus baris data.

#### c. Scatter Plot with Outliers Marked

Membuat scatter plot antara:

- X-axis: `Ore Pulp pH`
- Y-axis: `% Silica Concentrate`

Outlier pada `Ore Pulp pH` ditandai dengan warna berbeda agar dapat dibandingkan dengan kelompok data normal.

Visualisasi ini digunakan untuk mengamati apakah nilai pH yang ekstrem memiliki pola tertentu terhadap kadar silika hasil konsentrat.

## 📈 Key Findings & Insights

### 1. Missing Value Analysis

<details>
<summary>Hasil pemeriksaan kelengkapan data</summary>

Seluruh enam variabel yang dianalisis memiliki nilai kosong sebanyak 0 pada sampel 30.000 baris.

| Feature | Missing Values |
|---|---:|
| Ore Pulp pH | 0 |
| Ore Pulp Density | 0 |
| Starch Flow | 0 |
| Amina Flow | 0 |
| Flotation Column 01 Air Flow | 0 |
| % Silica Concentrate | 0 |

**Insight:** Sampel data yang digunakan lengkap pada seluruh variabel terpilih sehingga tidak memerlukan imputasi missing values.

</details>

### 2. Outlier Detection Results

Hasil deteksi menggunakan metode IQR dengan nilai `k=1.5`:

| Sensor | Outlier Count | Outlier Percentage |
|---|---:|---:|
| Ore Pulp pH | 402 | 1.34% |
| Ore Pulp Density | 1,892 | 6.31% |
| Starch Flow | 40 | 0.13% |
| Amina Flow | 429 | 1.43% |
| Flotation Column 01 Air Flow | 1 | 0.00% |
| % Silica Concentrate | 226 | 0.75% |

**Key Insights:**

- `Ore Pulp Density` memiliki persentase outlier tertinggi, yaitu **6.31% atau 1,892 baris**.
- `Amina Flow` memiliki 429 outlier atau 1.43%.
- `Ore Pulp pH` memiliki 402 outlier atau 1.34%.
- `% Silica Concentrate` memiliki 226 outlier atau 0.75%.
- `Starch Flow` hanya memiliki 40 outlier atau 0.13%.
- `Flotation Column 01 Air Flow` memiliki jumlah outlier paling sedikit, yaitu 1 baris.

**Insight:** Sebagian besar variabel memiliki persentase outlier yang relatif kecil. Namun, `Ore Pulp Density` perlu mendapatkan perhatian lebih lanjut karena memiliki jumlah penyimpangan paling tinggi di antara variabel yang dianalisis.

### 3. Impact of Outliers on Amina Flow

Perbandingan statistik pada variabel `Amina Flow` menghasilkan:

| Statistical Measure | Value |
|---|---:|
| Mean (Original) | 489.04 |
| Median | 504.88 |
| Mean (Without Outliers) | 491.95 |

**Key Insights:**

- Median lebih tinggi daripada mean, menunjukkan adanya nilai rendah yang menarik rata-rata ke bawah.
- Setelah outlier dikeluarkan, mean meningkat dari 489.04 menjadi 491.95.
- Perubahan mean relatif kecil, yaitu sekitar 2.91 poin.

**Insight:** Median dapat digunakan sebagai representasi nilai tipikal yang lebih tahan terhadap pengaruh nilai ekstrem. Sementara itu, mean tetap relevan untuk kebutuhan analisis yang menggunakan rata-rata atau agregasi.

### 4. Boxplot Analysis

Hasil visualisasi boxplot menunjukkan karakteristik distribusi yang berbeda pada tiga variabel.

- **Ore Pulp pH:** Sebagian besar nilai berada di sekitar 9.8, dengan rentang tengah sekitar 9.5–10.1. Terdapat beberapa nilai rendah hingga sekitar 8.75.
- **Ore Pulp Density:** Sebagian besar nilai berada di sekitar 1.70, dengan rentang tengah sekitar 1.65–1.73. Terdapat titik ekstrem di atas maupun di bawah distribusi utama.
- **% Silica Concentrate:** Sebagian besar nilai berada di sekitar 2.0, dengan rentang tengah sekitar 1.45–3.0. Distribusi memiliki nilai tinggi hingga sekitar 5.5.

**Insight:** Ore Pulp pH dan Ore Pulp Density cenderung terkonsentrasi pada rentang tengah, sedangkan `% Silica Concentrate` memperlihatkan penyebaran nilai tinggi yang lebih terlihat.

### 5. Clipping / Winsorizing Analysis

Pada variabel `Amina Flow`, clipping dilakukan menggunakan batas IQR tanpa menghapus baris data.

Hasil perbandingan histogram menunjukkan:

- Batang paling kiri pada histogram setelah clipping menjadi lebih tinggi karena nilai rendah yang berada di luar batas bawah IQR ditahan pada batas tersebut.
- Bagian tengah distribusi, terutama sekitar 530–550, relatif tidak berubah.
- Ekor kanan distribusi tetap relatif sama karena batas atas IQR tidak memotong nilai-nilai tersebut.
- Jumlah baris data tetap utuh karena clipping hanya mengubah nilai ekstrem, bukan menghapus observasi.

**Insight:** Clipping dapat mengurangi pengaruh nilai ekstrem tanpa mengurangi jumlah observasi. Namun, perubahan nilai harus dievaluasi agar informasi penting mengenai kondisi operasional tidak hilang.

### 6. Scatter Plot: Ore Pulp pH vs. Silica Concentrate

Hasil scatter plot menunjukkan:

- Outlier pH terkonsentrasi pada sisi kiri grafik, yaitu sekitar pH 8.75.
- Nilai `% Silica Concentrate` pada kelompok outlier tersebut tetap menyebar dari sekitar 0.8 hingga 5.1.
- Sebagian besar data normal terkonsentrasi pada pH sekitar 9.5–10.2 dan kadar silika sekitar 1.2–3.5.
- Tidak terlihat pola hubungan linear yang kuat secara visual antara pH rendah dan kadar silika.

**Insight:** Nilai pH yang rendah tidak secara langsung menunjukkan kadar silika yang tinggi atau rendah. Analisis multivariabel diperlukan untuk memahami hubungan parameter proses terhadap kualitas konsentrat.

## 💡 Industrial & Operational Insights

Berdasarkan hasil eksplorasi, beberapa hal yang dapat menjadi perhatian dalam pengelolaan proses flotasi adalah:

1. **Monitoring Ore Pulp Density**

   Karena memiliki persentase outlier tertinggi, parameter densitas perlu diperiksa lebih lanjut untuk mengetahui apakah penyimpangan berasal dari kondisi proses aktual, variasi pengukuran, atau gangguan sensor.

2. **Sensor Data Quality Monitoring**

   Pemeriksaan missing values dan outlier sebaiknya menjadi bagian dari proses validasi data sebelum data digunakan untuk analisis maupun pemodelan.

3. **Evaluasi Outlier Sebelum Dihapus**

   Nilai ekstrem tidak selalu merupakan kesalahan data. Outlier dapat mencerminkan perubahan kondisi operasional yang penting sehingga perlu diperiksa berdasarkan konteks proses.

4. **Pemilihan Strategi Penanganan Outlier**

   Mean, median, penghapusan outlier, dan clipping memiliki kegunaan yang berbeda. Strategi penanganan harus disesuaikan dengan karakteristik variabel dan tujuan analisis.

5. **Multivariable Process Analysis**

   Hubungan pH dengan kadar silika tidak cukup dijelaskan menggunakan satu variabel. Parameter seperti Ore Pulp Density, Starch Flow, dan Amina Flow juga perlu dipertimbangkan dalam analisis lanjutan.

## ⚠️ Limitations

Beberapa batasan dari analisis ini adalah:

- Analisis hanya menggunakan sampel 30.000 baris dari dataset asli yang memiliki 737.453 baris.
- Pemeriksaan missing values hanya dilakukan pada enam variabel terpilih dan sampel yang digunakan, bukan seluruh dataset.
- Deteksi outlier menggunakan aturan IQR bersifat statistik dan tidak otomatis menunjukkan kesalahan sensor.
- Batas IQR dapat menandai variasi operasional yang sebenarnya valid sebagai outlier.
- Clipping dilakukan sebagai demonstrasi pada `Amina Flow`; efektivitasnya belum dievaluasi untuk seluruh variabel.
- Hubungan antara `Ore Pulp pH` dan `% Silica Concentrate` diamati secara visual dan belum membuktikan hubungan sebab-akibat.
- Analisis ini merupakan Exploratory Data Analysis (EDA), bukan model prediksi kualitas konsentrat.

## 🚀 Next Steps

Beberapa pengembangan yang dapat dilakukan setelah analisis ini:

- Melakukan pemeriksaan missing values pada seluruh dataset.
- Membandingkan hasil deteksi outlier pada sampel dan dataset lengkap.
- Memeriksa nilai nol dan nilai ekstrem berdasarkan batas fisik atau operasional sensor.
- Membandingkan metode IQR dengan metode deteksi outlier lainnya seperti Z-score.
- Mengevaluasi clipping dan penghapusan outlier terhadap perubahan statistik setiap variabel.
- Melakukan analisis korelasi multivariabel antarparameter proses.
- Menganalisis hubungan parameter sensor terhadap `% Silica Concentrate`.
- Mempersiapkan data yang telah divalidasi untuk tahap Machine Learning.

## 📁 Project Structure

```text
Day_09_Missing_Values_&_Outliers_Flotation_Sensor/
│
├── Day_09_Missing_Values_&_Outliers_Flotation_Sensor.ipynb
├── README.md
└── MiningProcess_Flotation_Plant_Database.csv
```

*Catatan: Dataset CSV diperoleh dari Kaggle dan tidak harus disimpan langsung di dalam repository.*

## ⚙️ Installation & Usage

### 1. Clone Repository

```bash
git clone <repository-url>
cd <repository-folder>
```

### 2. Install Dependencies

```bash
pip install pandas numpy matplotlib seaborn
```

Untuk mengunduh dataset menggunakan Kaggle API:

```bash
pip install kaggle
```

### 3. Download Dataset

Dataset dapat diunduh melalui Kaggle:

[Quality Prediction in a Mining Process](https://www.kaggle.com/datasets/edumagalhaes/quality-prediction-in-a-mining-process)

Jika menggunakan Google Colab, siapkan `kaggle.json` dan jalankan perintah Kaggle API sesuai notebook.

### 4. Run Notebook

Buka file berikut menggunakan Jupyter Notebook atau Google Colab:

```text
Day_09_Missing_Values_&_Outliers_Flotation_Sensor.ipynb
```

Jalankan setiap cell secara berurutan untuk melakukan pemeriksaan missing values, deteksi outlier, evaluasi statistik, dan visualisasi data.

## 📝 Conclusion

Day 09 berfokus pada peningkatan pemahaman mengenai kualitas data sensor dalam proses iron ore flotation, khususnya missing values dan outliers.

Hasil analisis menunjukkan bahwa seluruh enam variabel terpilih tidak memiliki missing values pada sampel 30.000 baris. Namun, deteksi IQR menemukan tingkat penyimpangan yang berbeda antarvariabel, dengan `Ore Pulp Density` memiliki persentase outlier tertinggi sebesar 6.31%.

Analisis `Amina Flow` juga memperlihatkan bahwa outlier dapat memengaruhi nilai mean, sedangkan median relatif lebih tahan terhadap nilai ekstrem. Melalui clipping, nilai ekstrem dapat dibatasi tanpa menghapus observasi, tetapi penerapannya tetap perlu mempertimbangkan karakteristik proses.

Secara keseluruhan, proyek ini menunjukkan bahwa deteksi dan penanganan outlier merupakan bagian penting dari data preprocessing, terutama untuk data sensor industri yang akan digunakan dalam analisis lanjutan dan pengembangan model Machine Learning.

---
## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

---

<p align="center">
  <em>Day 09 --- Analyzing Missing Values and Outliers in Flotation Sensor Data.</em>
</p>