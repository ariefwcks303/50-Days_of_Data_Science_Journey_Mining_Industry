 <div align="center">

# Day 07 — Exploratory Data Analysis: Keselamatan Kerja dan Insiden Industri Pertambangan

**Analisis data kecelakaan industri untuk mengidentifikasi tingkat keparahan cedera, bagian tubuh yang paling berisiko, pola kecelakaan, dan tren insiden sebagai dasar peningkatan keselamatan kerja.**

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Project-Data%20Science%20Mining-blue" alt="Project">
  <img src="https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple" alt="EDA">
  <img src="https://img.shields.io/badge/Focus-Industrial%20Safety-red" alt="Safety">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>

## 📌 Gambaran Umum Proyek

Keselamatan dan Kesehatan Kerja (K3) merupakan salah satu aspek paling penting dalam industri pertambangan, pengeboran, konstruksi, dan industri berat.

Kecelakaan kerja dapat mengakibatkan cedera serius, cacat permanen, hingga kehilangan nyawa pekerja.

Melalui **Exploratory Data Analysis (EDA)**, catatan kecelakaan kerja dapat dianalisis untuk menemukan pola cedera, bagian tubuh yang paling sering terdampak, jenis kecelakaan yang dominan, serta perubahan jumlah insiden dari waktu ke waktu.

Pada Day 07, analisis menggunakan dataset **OSHA Accident and Injury Data (2015–2017)** untuk mengeksplorasi catatan kecelakaan industri di Amerika Serikat, termasuk aktivitas konstruksi dan industri lainnya yang relevan dengan sektor pertambangan dan pengeboran.

Notebook ini berfokus pada enam variabel utama:

- `Degree of Injury` — tingkat keparahan cedera.
- `Nature of Injury` — jenis atau sifat cedera.
- `Part of Body` — bagian tubuh yang mengalami cedera.
- `Event type` — klasifikasi jenis kejadian.
- `Construction End Use` — konteks atau penggunaan bangunan.
- `Event Date` — tanggal terjadinya insiden.

Tujuan utama proyek ini adalah mengubah catatan kecelakaan menjadi informasi yang bermakna untuk memahami pola risiko dan menentukan prioritas peningkatan keselamatan kerja.

**Catatan ruang lingkup:** Dataset ini mencakup catatan kecelakaan industri dan konstruksi secara lebih luas, bukan dataset yang secara eksklusif berisi kecelakaan pertambangan.

## 🎯 Tujuan Pembelajaran

- Memahami dan melakukan inspeksi terhadap dataset kecelakaan industri.
- Mengidentifikasi nilai kosong pada variabel yang berkaitan dengan keselamatan kerja.
- Mengubah informasi tanggal ke dalam format `datetime`.
- Menganalisis data kategorikal menggunakan `value_counts()`.
- Mengidentifikasi kategori tingkat keparahan cedera yang paling banyak ditemukan.
- Menganalisis bagian tubuh yang paling sering mengalami cedera.
- Mengidentifikasi jenis cedera yang paling dominan.
- Menganalisis pola insiden bulanan menggunakan agregasi data waktu.
- Menerjemahkan hasil analisis menjadi wawasan dan rekomendasi keselamatan kerja.

## 📊 Dataset

**Nama Dataset:** OSHA Accident and Injury Data (2015–2017)  
**Sumber:** [Kaggle — OSHA Accident and Injury Data 15-17](https://www.kaggle.com/datasets/ruqaiyaship/osha-accident-and-injury-data-1517)

Dataset ini berisi catatan kecelakaan dan cedera industri yang dikumpulkan di Amerika Serikat selama periode 2015–2017.

### Informasi Dataset

| Atribut | Keterangan |
|---|---|
| Ukuran dataset awal | 4.847 baris × 29 kolom |
| Dataset terpilih | 4.847 baris × 6 kolom |
| Dataset akhir | 4.845 baris × 6 kolom |
| Periode data | 2015–2017 |
| Tipe data | Kategorikal dan datetime |
| Fokus analisis | Keselamatan industri dan kecelakaan kerja |

### Variabel yang Digunakan

| Kolom | Keterangan |
|---|---|
| `Degree of Injury` | Klasifikasi tingkat keparahan cedera, termasuk fatal dan nonfatal |
| `Nature of Injury` | Jenis atau klasifikasi cedera |
| `Part of Body` | Bagian tubuh yang terdampak insiden |
| `Event type` | Klasifikasi kejadian kecelakaan |
| `Construction End Use` | Konteks atau penggunaan bangunan |
| `Event Date` | Tanggal terjadinya insiden |

**Ruang lingkup analisis:** Notebook memilih enam variabel yang relevan dengan keselamatan kerja, bukan menganalisis seluruh 29 kolom dataset asli.

## 🛠️ Tools & Libraries

Proyek ini menggunakan beberapa library Python untuk manipulasi data dan visualisasi.

| Library | Fungsi |
|---|---|
| Python | Bahasa pemrograman utama |
| Pandas | Membaca, mengolah, membersihkan, dan mengagregasi data |
| NumPy | Operasi numerik |
| Matplotlib | Visualisasi data |
| Seaborn | Library visualisasi statistik |
| Kaggle API | Mengunduh dataset |
| Google Colab | Lingkungan pengembangan notebook |

### Import Library

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## 🔍 Alur Exploratory Data Analysis

Notebook ini mengikuti tahapan analisis yang terstruktur, mulai dari pengambilan dataset hingga penyusunan rekomendasi keselamatan kerja.

```text
OSHA Accident & Injury Dataset
             |
             v
       Data Loading
             |
             v
      Initial Inspection
             |
             v
       Feature Selection
             |
             v
      Missing Value Analysis
             |
             v
       Date Conversion
             |
             v
      Data Cleaning
             |
             v
    Exploratory Data Analysis
             |
     +-------+--------+
     |       |        |
     v       v        v
  Severity Body     Nature
  Cedera   Parts   of Injury
     |       |        |
     +-------+--------+
             |
             v
     Monthly Incident Trend
             |
             v
       Key Findings
             |
             v
   Workplace Safety Insights
             |
             v
    Safety Recommendations
```

### 1. Memuat dan Memeriksa Dataset

Dataset diunduh melalui Kaggle API dan dimuat ke dalam Pandas DataFrame.

```python
df = pd.read_csv(
    "/content/OSHA HSE DATA_ALL ABSTRACTS 15-17_FINAL.csv"
)

df.head()
```

Dataset awal memiliki karakteristik:

- 4.847 baris data.
- 29 kolom.
- Berisi informasi kategorikal, numerik, deskriptif, dan tanggal kejadian.

Pemeriksaan awal dilakukan untuk memahami struktur dataset dan menentukan variabel yang relevan dengan analisis keselamatan kerja.

### 2. Pemilihan Fitur (Feature Selection)

Enam variabel utama dipilih untuk analisis.

```python
fokus = [
    "Degree of Injury",
    "Nature of Injury",
    "Part of Body",
    "Event type",
    "Construction End Use",
    "Event Date"
]

fokus = [k for k in fokus if k in df.columns]
```

Pemilihan ini bertujuan memfokuskan analisis pada tingkat keparahan cedera, klasifikasi cedera, bagian tubuh yang terdampak, jenis kejadian, konteks konstruksi, dan tanggal insiden.

### 3. Analisis Missing Values

Pemeriksaan nilai kosong dilakukan pada enam variabel terpilih.

```python
df[fokus].isnull().mean().mul(100).round(1)
```

Persentase missing values pada pemeriksaan awal dibulatkan menjadi satu angka desimal. Oleh karena itu, dua baris yang memiliki nilai kosong masih terlihat sebagai 0,0% pada ringkasan awal.

Pemeriksaan lebih lanjut menemukan dua baris yang memiliki nilai kosong pada:

- `Nature of Injury`
- `Part of Body`
- `Event type`

Kedua catatan tersebut berkaitan dengan:

- Insiden yang diklasifikasikan sebagai `NO RECORDABLE INJURY`.
- Insiden paparan asbes (*asbestos exposure*).

Kedua baris tersebut kemudian dihapus agar dataset analisis memiliki kelengkapan pada seluruh enam variabel.

```python
kolom_cek = [
    "Nature of Injury",
    "Part of Body",
    "Event type"
]

df[df[kolom_cek].isnull().any(axis=1)]
```

Penghapusan dua baris dilakukan menggunakan:

```python
df = df.drop(index=[1294, 1660])
```

### 4. Konversi Format Tanggal

Kolom `Event Date` dikonversi ke format datetime.

```python
df["Event Date"] = pd.to_datetime(
    df["Event Date"],
    errors="coerce"
)
```

Konversi ini memungkinkan analisis kronologis dan pengelompokan data berdasarkan periode bulanan.

### 5. Pemeriksaan Akhir Data

Setelah proses cleaning, dataset memiliki kondisi sebagai berikut:

| Atribut | Hasil Akhir |
|---|---|
| Total data | 4.845 baris |
| Jumlah kolom | 6 kolom |
| Missing values | 0 |
| Kolom datetime | 1 |
| Kolom kategorikal | 5 |
| Kelengkapan data | 100% |

Dataset akhir telah siap digunakan untuk analisis deskriptif dalam notebook.

## 📈 Exploratory Data Analysis & Visualisasi

Notebook menghasilkan empat analisis visual utama untuk memahami karakteristik insiden kecelakaan kerja.

### A. Tingkat Keparahan Cedera (Severity of Injury)

**Visualisasi:** Horizontal Bar Chart

Analisis ini digunakan untuk mengetahui distribusi tingkat keparahan cedera berdasarkan kolom `Degree of Injury`.

```python
deg = df["Degree of Injury"].value_counts().head(8).sort_values()
```

**Hasil Analisis:**

| Tingkat Cedera | Jumlah Insiden | Persentase |
|---|---:|---:|
| Fatal | 2.964 | 61,2% |
| Nonfatal | 1.881 | 38,8% |
| **Total** | **4.845** | **100%** |

**Interpretasi:**

Kasus Fatal memiliki proporsi sebesar 61,2% dari seluruh catatan yang dianalisis. Jumlah ini lebih besar dibandingkan kasus Nonfatal, dengan selisih sebanyak 1.083 kasus.

Hal tersebut menunjukkan bahwa dataset memiliki proporsi catatan kecelakaan dengan dampak berat dan fatal yang cukup besar.

**Wawasan Keselamatan:**

Program keselamatan kerja perlu memprioritaskan aktivitas berisiko tinggi yang berpotensi menyebabkan kematian, bukan hanya berfokus pada pencegahan cedera ringan.

### B. Bagian Tubuh yang Paling Sering Mengalami Cedera

**Visualisasi:** Top 10 Horizontal Bar Chart

Analisis ini bertujuan mengidentifikasi bagian tubuh yang paling sering terdampak kecelakaan kerja.

```python
body = df["Part of Body"].value_counts().head(10).sort_values()
```

**10 Bagian Tubuh Teratas:**

| Peringkat | Bagian Tubuh | Jumlah | Persentase |
|---:|---|---:|---:|
| 1 | Head | 1.023 | 21,1% |
| 2 | Whole Body | 555 | 11,5% |
| 3 | Fingers | 550 | 11,4% |
| 4 | Internal Injuries | 403 | 8,3% |
| 5 | Heart | 374 | 7,7% |
| 6 | Ribs | 305 | 6,3% |
| 7 | Left Leg | 275 | 5,7% |
| 8 | Hand | 212 | 4,4% |
| 9 | Neck | 157 | 3,2% |
| 10 | Feet | 124 | 2,6% |

**Interpretasi:**

Bagian tubuh Head menempati posisi pertama dengan 1.023 catatan atau 21,1% dari total dataset.

Whole Body dan Fingers berada di posisi berikutnya dengan masing-masing 555 dan 550 catatan.

Cedera internal, jantung, dan tulang rusuk juga termasuk dalam kategori yang cukup dominan.

**Wawasan Keselamatan:**

Perlindungan kepala, tangan, dan jari serta pencegahan benturan fisik yang berat perlu mendapatkan perhatian khusus dalam program keselamatan kerja.

### C. Jenis Cedera yang Paling Sering Terjadi (Nature of Injury)

**Visualisasi:** Top 10 Horizontal Bar Chart

Analisis ini digunakan untuk mengidentifikasi jenis cedera yang paling sering tercatat dalam dataset.

```python
nat = df["Nature of Injury"].value_counts().head(10).sort_values()
```

**10 Jenis Cedera Teratas:**

| Peringkat | Jenis Cedera | Jumlah | Persentase |
|---:|---|---:|---:|
| 1 | Serious Fall/Strike | 1.683 | 34,7% |
| 2 | Fracture, Broken Bones | 852 | 17,6% |
| 3 | Amputation, Crushing | 542 | 11,2% |
| 4 | Laceration | 382 | 7,9% |
| 5 | Head Trauma | 328 | 6,8% |
| 6 | Bruising, Contusion | 291 | 6,0% |
| 7 | Asphyxiation, Drowning | 213 | 4,4% |
| 8 | Electrocution | 199 | 4,1% |
| 9 | Fire Burn | 144 | 3,0% |
| 10 | Dislocation | 64 | 1,3% |

**Interpretasi:**

Serious Fall/Strike menjadi kategori cedera paling dominan dengan 1.683 catatan atau 34,7%.

Fracture/Broken Bones dan Amputation/Crushing juga memiliki jumlah yang cukup besar.

Ketiga kategori tersebut secara keseluruhan mencakup sekitar 63,5% dari seluruh catatan insiden.

**Wawasan Keselamatan:**

Pencegahan kecelakaan akibat jatuh dan benturan, pengendalian risiko patah tulang, perlindungan mesin, serta prosedur penanganan material yang aman menjadi fokus penting dalam upaya mengurangi cedera serius.

### D. Tren Insiden Bulanan (Monthly Incident Trend)

**Visualisasi:** Time-Series Line Chart

Analisis ini digunakan untuk memahami perubahan jumlah catatan insiden berdasarkan waktu.

Kolom `Event Date` digunakan untuk mengelompokkan catatan kecelakaan berdasarkan bulan.

```python
per_bulan = (
    df.dropna(subset=["Event Date"])
      .set_index("Event Date")
      .resample("ME")
      .size()
)
```

**Temuan Utama:**

Notebook mengidentifikasi tiga fase pola insiden:

| Periode | Pola yang Diamati |
|---|---|
| Pertengahan 2015 – Awal 2017 | Relatif stabil, sekitar 111–170 catatan per bulan |
| Februari – Mei 2017 | Terjadi peningkatan signifikan menjadi 573, 566, 586, dan 459 catatan |
| Pertengahan – Akhir 2017 | Terjadi penurunan tajam hingga 117, 4, 1, dan 1 catatan |

**Interpretasi:**

Tren bulanan memperlihatkan pola pelaporan yang tidak biasa.

Terjadi peningkatan tajam pada Februari–Mei 2017, kemudian diikuti penurunan drastis menjelang akhir periode dataset.

Pola tersebut tidak dapat langsung diartikan sebagai peningkatan atau penurunan risiko kecelakaan yang sebenarnya di lapangan.

Beberapa kemungkinan penyebabnya meliputi:

- Pengumpulan atau pelaporan data secara bertahap (*batch reporting*).
- Perubahan cakupan pelaporan.
- Perubahan administratif dalam proses pengumpulan data.
- Batas waktu pengambilan atau penarikan dataset (*data extraction cutoff*).

**Wawasan Keselamatan:**

Sebelum menarik kesimpulan operasional berdasarkan tren insiden bulanan, proses pelaporan dan kelengkapan cakupan dataset perlu diverifikasi terlebih dahulu.

Analisis lanjutan dapat dilakukan dengan membandingkan tren bulanan terhadap `Event type` dan `Construction End Use`.

## 💡 Kesimpulan Utama dan Rekomendasi K3

Berdasarkan hasil analisis dalam notebook, terdapat beberapa wawasan penting yang dapat digunakan sebagai dasar evaluasi keselamatan kerja.

### 1. Tingginya Proporsi Insiden Fatal

**Temuan:**

Sebanyak 2.964 catatan atau 61,2% diklasifikasikan sebagai Fatal.

**Rekomendasi:**

- Memprioritaskan pengendalian aktivitas kerja dengan tingkat risiko tinggi yang berpotensi menyebabkan kematian.
- Menerapkan *Hierarchy of Controls*, dengan mengutamakan eliminasi bahaya dan pengendalian teknis (*engineering controls*).
- Memperkuat prosedur *Permit to Work*.
- Menerapkan *Job Safety Analysis* (JSA) pada aktivitas berisiko tinggi.
- Meningkatkan pengawasan dan pemantauan bahaya di area kerja.

### 2. Perlindungan Kepala dan Tangan Menjadi Prioritas

**Temuan:**

Cedera kepala merupakan kategori bagian tubuh yang paling sering ditemukan, diikuti oleh seluruh tubuh dan jari.

**Rekomendasi:**

- Menegakkan penggunaan helm keselamatan sesuai standar.
- Memasang pelindung atas (*overhead protection*) dan jaring pengaman (*safety nets*) jika diperlukan.
- Meningkatkan perlindungan mesin (*machine guarding*) pada peralatan bergerak dan berputar.
- Menyediakan sarung tangan pelindung yang sesuai dengan jenis pekerjaan.
- Melakukan pemeriksaan APD secara rutin.

### 3. Mengendalikan Risiko Jatuh, Benturan, dan Cedera Struktural

**Temuan:**

Serious Fall/Strike mendominasi dengan proporsi 34,7%, diikuti oleh cedera patah tulang dan amputasi/remuk.

**Rekomendasi:**

- Menerapkan sistem pencegahan dan perlindungan jatuh (*fall protection system*).
- Melakukan inspeksi rutin terhadap *scaffolding*, tangga, dan platform kerja.
- Menerapkan prosedur pengangkatan dan pemindahan material yang aman (*safe lifting and material handling*).
- Menetapkan zona aman di sekitar peralatan bergerak dan material berat.
- Memperkuat pengendalian risiko tertimpa benda dan terjepit mesin.

### 4. Menginvestigasi Pola Pelaporan Insiden yang Tidak Biasa

**Temuan:**

Tren bulanan memperlihatkan peningkatan signifikan pada Februari–Mei 2017, kemudian diikuti penurunan drastis.

**Rekomendasi:**

- Menginvestigasi kemungkinan pelaporan secara bertahap dan masalah sinkronisasi data.
- Memverifikasi konsistensi cakupan pelaporan dataset.
- Membandingkan tren bulanan berdasarkan klasifikasi jenis kejadian.
- Memeriksa apakah konteks konstruksi tertentu berkontribusi terhadap pola yang tidak biasa tersebut.

## ⚠️ Keterbatasan dan Hal yang Perlu Diperhatikan

Beberapa keterbatasan perlu diperhatikan ketika menginterpretasikan hasil analisis.

- **Cakupan dataset:** Dataset mencakup insiden industri dan konstruksi secara luas, bukan hanya kecelakaan pertambangan.
- **Cakupan geografis:** Dataset berkaitan dengan catatan insiden di Amerika Serikat.
- **Periode data:** Analisis hanya mencakup catatan tahun 2015–2017.
- **Proses cleaning:** Dua catatan dihapus karena memiliki nilai kosong pada tiga variabel kategorikal terpilih. Salah satunya diklasifikasikan sebagai tidak memiliki cedera yang dapat dicatat, sedangkan lainnya berkaitan dengan paparan asbes.
- **Potensi bias seleksi:** Penghapusan dua catatan tersebut dapat memengaruhi representasi kategori insiden tertentu.
- **Analisis kategorikal:** Notebook berfokus pada distribusi frekuensi, bukan pengujian signifikansi statistik.
- **Anomali tren bulanan:** Perubahan jumlah catatan bulanan dapat dipengaruhi oleh cakupan pelaporan atau proses administratif, bukan semata-mata perubahan jumlah kecelakaan sebenarnya.
- **Tidak ada analisis kausal:** Visualisasi menunjukkan pola, tetapi tidak membuktikan penyebab kecelakaan kerja.
- **Tidak ada pemodelan prediktif:** Notebook hanya melakukan analisis deskriptif dan belum mengembangkan model prediksi kecelakaan.
- **Tidak menggunakan pengukuran paparan kerja:** Jumlah insiden tidak dinormalisasi berdasarkan jam kerja, jumlah pekerja, atau tingkat paparan operasional. Oleh karena itu, jumlah catatan tidak dapat langsung diartikan sebagai tingkat kecelakaan.
- **Tidak ada perbandingan risiko spesifik industri:** Analisis belum memisahkan catatan pertambangan, pengeboran, dan konstruksi menjadi kelompok industri yang tervalidasi secara terpisah.

## 🚀 Pengembangan Selanjutnya (Next Steps)

Beberapa pengembangan yang dapat dilakukan untuk memperdalam analisis adalah:

1. **Analisis Jenis Kejadian**

   Mengeksplorasi kolom `Event type` untuk mengidentifikasi mekanisme kecelakaan yang paling sering terjadi.

2. **Analisis Konteks Konstruksi**

   Menganalisis `Construction End Use` untuk memahami perbedaan distribusi insiden berdasarkan konteks konstruksi.

3. **Analisis Hubungan Antar Kategori**

   Mengidentifikasi hubungan antara tingkat keparahan cedera, bagian tubuh yang terdampak, dan jenis cedera.

4. **Investigasi Tren Waktu**

   Meneliti anomali pola pelaporan selama tahun 2017 dan memverifikasi kelengkapan data.

5. **Text Mining**

   Mengeksplorasi `Abstract Text`, `Event Description`, dan `Event Keywords` untuk mendapatkan informasi tambahan mengenai pola kecelakaan.

6. **Analisis Khusus Pertambangan**

   Memisahkan catatan yang berkaitan dengan pertambangan dan pengeboran menggunakan klasifikasi industri atau aktivitas yang tervalidasi.

7. **Pengembangan Dashboard K3**

   Membuat dashboard interaktif untuk memantau tingkat keparahan cedera, kategori kecelakaan, dan tren insiden bulanan.

Pengembangan tersebut merupakan rencana analisis lanjutan dan belum diimplementasikan dalam notebook saat ini.

## 📂 Struktur Proyek

```text
50-Days-of-Data-Science-Journey_Mining-Industry/
│
│
│
├── Day_07_Exploratory_Data_Analysis_of_Mine_Industrial_Safety_and_Incidents/
│   ├── Day_07_Exploratory_Data_Analysis_of_Mine_Industrial_Safety_and_Incidents.ipynb
│   └── README.md
│
└── README.md
```

*Nama folder di atas merupakan contoh dan perlu disesuaikan dengan struktur repository yang sebenarnya.*


## 🏁 Kesimpulan

Day 07 menunjukkan bagaimana Exploratory Data Analysis dapat diterapkan pada catatan kecelakaan industri untuk mengidentifikasi pola penting dalam keselamatan kerja.

Berdasarkan 4.845 catatan yang telah melalui proses cleaning, analisis menemukan proporsi kasus fatal yang tinggi, mengidentifikasi bagian tubuh yang paling sering mengalami cedera, serta menunjukkan dominasi cedera akibat jatuh dan benturan serius.

Analisis juga menemukan pola pelaporan bulanan yang tidak biasa selama tahun 2017. Hal ini menegaskan pentingnya memverifikasi cakupan dan kelengkapan data sebelum menarik kesimpulan mengenai tren kecelakaan dari waktu ke waktu.

Beberapa prioritas keselamatan yang dapat diidentifikasi meliputi:

- Mengurangi aktivitas kerja berisiko tinggi yang berpotensi menyebabkan kematian.
- Memperkuat perlindungan kepala, tangan, dan jari.
- Meningkatkan pencegahan kecelakaan akibat jatuh dan benturan.
- Memperkuat perlindungan mesin dan prosedur penanganan material.
- Menginvestigasi pola pelaporan insiden yang tidak biasa.

Secara keseluruhan, proyek ini menunjukkan bagaimana **analisis keselamatan kerja berbasis data** dapat membantu memahami bahaya di lingkungan industri dan menjadi dasar untuk menyusun program K3 yang lebih terarah.

---

## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

---
<p align="center">
  <em>Day 7 Data Exploration in Mine Industrial Safety and Accident.</em>
</p>


