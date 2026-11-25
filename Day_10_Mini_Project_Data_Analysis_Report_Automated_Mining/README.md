<div align="center">

# Day 10 — Mini-Project: Exploratory Data Analysis Report – Automated Mining

**Membangun laporan Exploratory Data Analysis (EDA) otomatis untuk mengeksplorasi direktori tambang mineral berdasarkan wilayah, jenis tambang, sektor industri, dan perusahaan operator.**

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Project-Data%20Science%20Mining-blue" alt="Project">
  <img src="https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple" alt="EDA">
  <img src="https://img.shields.io/badge/Focus-Automated%20Mining%20Report-orange" alt="Automated Mining Report">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>

## 📌 Gambaran Umum Proyek

Day 10 merupakan mini-project sekaligus penutup Modul 1. Pada proyek ini, tahapan yang telah dipelajari sebelumnya—mulai dari memuat data, memilih kolom, memeriksa kualitas data, meringkas informasi, hingga membuat visualisasi—digabungkan menjadi sebuah **laporan EDA otomatis**.

Analisis menggunakan dataset **Operational Mineral Resource Mines**, yaitu direktori tambang mineral di Amerika Serikat yang memuat informasi nama tambang, lokasi administratif, jenis tambang, perusahaan operator, dan klasifikasi sektor industri.

Notebook memfokuskan analisis pada enam atribut inti:

- `NAME` — nama tambang atau lokasi tambang.
- `STATE` — kode negara bagian tempat tambang berada.
- `COUNTY` — wilayah administratif atau county.
- `MINE_TYPE` — kode jenis tambang.
- `COMPNAME` — nama perusahaan atau operator tambang.
- `NAICSDESCR` — deskripsi sektor industri berdasarkan klasifikasi NAICS.

Analisis kemudian dikembangkan menjadi beberapa bagian, yaitu ringkasan EDA otomatis, visualisasi persebaran tambang, komposisi jenis tambang, distribusi sektor industri, peringkat perusahaan operator, serta laporan naratif yang merangkum temuan utama.

**Tujuan utama proyek:** menyusun alur EDA yang reusable sehingga ringkasan dan laporan eksplorasi dapat dihasilkan kembali ketika data diperbarui.

## 🎯 Tujuan Pembelajaran

- Memahami struktur dataset direktori tambang mineral.
- Melakukan inspeksi awal terhadap ukuran dan kolom dataset.
- Memilih fitur inti dari dataset yang memiliki banyak kolom administratif.
- Membuat fungsi reusable untuk menghasilkan ringkasan EDA.
- Memeriksa jumlah duplikasi, missing values, tipe data, dan nilai unik.
- Mengidentifikasi persebaran tambang berdasarkan negara bagian.
- Menganalisis komposisi kode jenis tambang.
- Mengeksplorasi distribusi sektor industri berdasarkan klasifikasi NAICS.
- Mengidentifikasi perusahaan dengan jumlah entitas tambang terbanyak.
- Menyusun laporan otomatis berbasis hasil analisis.
- Menerjemahkan hasil eksplorasi menjadi insight mengenai struktur direktori tambang.

## 📊 Dataset

**Nama Dataset:** Operational Mineral Resource Mines  
**Sumber:** [Kaggle — Operational Mineral Resource Mines](https://www.kaggle.com/datasets/thedevastator/operational-mineral-resource-mines)

Dataset ini berisi direktori tambang mineral di Amerika Serikat beserta informasi lokasi, jenis tambang, perusahaan, dan klasifikasi sektor industri.

### Informasi Dataset

| Atribut | Keterangan |
|---|---|
| Ukuran dataset awal | 2.996 baris × 57 kolom |
| Dataset terpilih | 2.996 baris × 6 kolom |
| Jumlah baris duplikat | 0 |
| Missing values eksplisit pada kolom terpilih | 0 |
| Jumlah negara bagian (`STATE`) | 48 |
| Jumlah county (`COUNTY`) | 548 |
| Fokus analisis | Persebaran tambang, jenis tambang, sektor industri, dan perusahaan |

### Variabel yang Digunakan

| Kolom | Keterangan |
|---|---|
| `NAME` | Nama lokasi atau situs tambang/kuari |
| `STATE` | Kode negara bagian lokasi tambang |
| `COUNTY` | Wilayah administratif tempat tambang berada |
| `MINE_TYPE` | Kode kategori jenis operasional tambang |
| `COMPNAME` | Nama perusahaan atau operator pengelola |
| `NAICSDESCR` | Deskripsi sektor industri berdasarkan klasifikasi NAICS |

**Ruang lingkup analisis:** Notebook menggunakan enam kolom inti dari 57 kolom yang tersedia. Kolom dipilih untuk memfokuskan eksplorasi pada lokasi tambang, kategori operasional, sektor industri, dan perusahaan operator.

## 🛠️ Tools & Libraries

| Library / Tools | Fungsi |
|---|---|
| Python | Bahasa pemrograman utama |
| Pandas | Membaca, memilih, membersihkan, meringkas, dan mengelompokkan data |
| NumPy | Operasi numerik |
| Matplotlib | Membuat visualisasi data |
| Seaborn | Mendukung visualisasi statistik dan palet warna |
| Kaggle API | Mengunduh dataset dari Kaggle |
| Google Colab | Lingkungan pengembangan notebook |

### Import Library

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

## 🔍 Alur Exploratory Data Analysis

Notebook mengikuti tahapan analisis berikut:

```text
Operational Mineral Resource Mines
              |
              v
          Data Loading
              |
              v
       Initial Inspection
              |
              v
        Feature Selection
       (57 kolom → 6 kolom)
              |
              v
       Automated EDA Summary
              |
              v
     Data Quality & Structure
              |
              v
      Exploratory Data Analysis
              |
      +-------+--------+----------------+
      |       |        |                |
      v       v        v                v
   Region  Mine Type  Industry       Company
   (STATE)           Sector         Ranking
      |       |        |                |
      +-------+--------+----------------+
              |
              v
       Automated Narrative
             Report
              |
              v
       Conclusions & Insights
```

### 1. Memuat dan Memeriksa Dataset

Dataset diunduh melalui Kaggle API, kemudian dibaca menggunakan Pandas.

```python
df = pd.read_csv('/content/Mines_and_Mineral_Resources.csv')

df.head()
```

Dataset awal memiliki **2.996 baris dan 57 kolom**. Banyak kolom berisi informasi administratif, sehingga notebook tidak menggunakan seluruh kolom untuk analisis.

### 2. Pemilihan Fitur (Feature Selection)

Enam kolom inti dipilih agar analisis lebih terarah.

```python
core = [
    "NAME",
    "STATE",
    "COUNTY",
    "MINE_TYPE",
    "COMPNAME",
    "NAICSDESCR"
]

df_core = df[core].copy()

print(df_core.shape)
```

Hasil pemilihan fitur:

| Atribut | Hasil |
|---|---:|
| Jumlah baris | 2.996 |
| Jumlah kolom | 6 |
| Kolom yang digunakan | `NAME`, `STATE`, `COUNTY`, `MINE_TYPE`, `COMPNAME`, `NAICSDESCR` |

### 3. Membuat Fungsi Ringkasan EDA Otomatis

Notebook mengembangkan fungsi `ringkas_eda()` untuk menghasilkan ringkasan dataset dalam satu pemanggilan.

Fungsi tersebut menampilkan:

- Jumlah baris dan kolom.
- Jumlah baris duplikat.
- Tipe data setiap kolom.
- Jumlah dan persentase missing values.
- Jumlah nilai unik.
- Statistik deskriptif jika terdapat kolom numerik.
- Frekuensi kategori teratas untuk kolom teks atau kategorikal.
- Peringatan untuk kolom dengan persentase missing value di atas ambang yang ditentukan.

Contoh pemanggilan:

```python
info = ringkas_eda(df_core)
```

Fungsi ini menggunakan parameter `top_n` untuk mengatur jumlah kategori teratas dan `missing_threshold` untuk menentukan ambang peringatan missing value.

### 4. Pemeriksaan Kualitas dan Struktur Data

Berdasarkan output `ringkas_eda()`, dataset terpilih memiliki kondisi berikut:

| Atribut | Hasil |
|---|---:|
| Total baris | 2.996 |
| Total kolom | 6 |
| Baris duplikat identik | 0 |
| Missing values (`NaN`) | 0 |
| Nilai unik `NAME` | 2.992 |
| Nilai unik `STATE` | 48 |
| Nilai unik `COUNTY` | 548 |
| Nilai unik `MINE_TYPE` | 10 |
| Nilai unik `COMPNAME` | 2.246 |
| Nilai unik `NAICSDESCR` | 45 |

Seluruh kolom terpilih memiliki tipe data `object`. Artinya, kolom `MINE_TYPE` yang berisi kode angka seperti `12`, `11`, dan `06` juga masih diperlakukan sebagai kategori teks.

**Temuan kualitas data:** meskipun pemeriksaan standar menunjukkan 0 missing values, kolom `COMPNAME` memiliki 74 entri berupa string kosong atau whitespace. Nilai tersebut tidak otomatis dikenali sebagai `NaN`.

### 5. Analisis Nilai Kosong Tersembunyi pada Nama Perusahaan

Pemeriksaan kategori pada `COMPNAME` menemukan 74 baris berisi string kosong atau spasi.

Untuk analisis peringkat perusahaan, notebook membersihkan whitespace menggunakan `.str.strip()` dan mengubah string kosong menjadi missing value sebelum menghitung frekuensi perusahaan.

```python
seri = df_core["COMPNAME"].astype("string").str.strip()
seri = seri.replace("", pd.NA).dropna()

top_comp = seri.value_counts().head(10)
```

Penanganan ini dilakukan pada proses perhitungan peringkat perusahaan. Hal tersebut perlu dibedakan dari proses pembersihan menyeluruh pada DataFrame utama.

## 📈 Exploratory Data Analysis & Visualisasi

Notebook menghasilkan tiga visualisasi utama dan satu analisis peringkat perusahaan.

### A. Persebaran Tambang Berdasarkan Wilayah (Number of Mines by Region)

**Visualisasi:** Top 10 Horizontal Bar Chart

Analisis ini digunakan untuk mengidentifikasi negara bagian dengan jumlah entitas tambang terbanyak berdasarkan kolom `STATE`.

```python
top_state = df_core["STATE"].value_counts().head(10).sort_values()

fig, ax = plt.subplots(figsize=(9, 5))
bars = ax.barh(top_state.index, top_state.values)

ax.bar_label(bars, padding=5, fmt="{:,.0f}")
ax.set_title("10 Wilayah dengan Tambang Terbanyak")
ax.set_xlabel("Jumlah Tambang")
ax.set_ylabel("STATE")
plt.tight_layout()
plt.show()
```

**Temuan utama:**

| Peringkat | Negara Bagian | Jumlah Tambang |
|---:|---|---:|
| 1 | Kentucky (`KY`) | 514 |
| 2 | West Virginia (`WV`) | 411 |
| 3 | Pennsylvania (`PA`) | 367 |
| 4 | Virginia (`VA`) | 154 |
| 5 | Ohio (`OH`) | 133 |
| 6 | Alabama (`AL`) | 118 |
| 7 | Texas (`TX`) | 116 |
| 8 | Georgia (`GA`) | 71 |
| 9 | Tennessee (`TN`) | 63 |
| 10 | Arizona (`AZ`) | 63 |

**Interpretasi:**

- Kentucky menjadi wilayah dengan jumlah tambang terbanyak, yaitu 514 entitas.
- West Virginia dan Pennsylvania menempati posisi kedua dan ketiga.
- Tiga negara bagian teratas memiliki total 1.292 entitas atau sekitar 43% dari seluruh dataset.
- Terdapat penurunan jumlah yang cukup tajam dari Pennsylvania (367) ke Virginia (154).

**Insight:**

Data menunjukkan konsentrasi direktori tambang yang kuat di Kentucky, West Virginia, dan Pennsylvania. Ketiga wilayah tersebut merupakan bagian dari kawasan pertambangan Appalachian. Konsentrasi ini dapat menjadi titik awal untuk eksplorasi regional lebih lanjut.

### B. Komposisi Jenis Tambang (Composition of Mine Types)

**Visualisasi:** Horizontal Bar Chart

Analisis ini menggunakan kolom `MINE_TYPE` untuk melihat kategori kode jenis tambang yang paling banyak tercatat.

```python
jenis = df_core["MINE_TYPE"].value_counts().head(8).sort_values()

fig, ax = plt.subplots(figsize=(9, 5))
bars = ax.barh(jenis.index.astype(str), jenis.values)

ax.bar_label(bars, padding=5, fmt="{:,.0f}")
ax.set_title("Komposisi Jenis Tambang")
ax.set_xlabel("Jumlah Tambang")
ax.set_ylabel("Jenis Tambang")
plt.tight_layout()
plt.show()
```

**Temuan utama:**

| Kode `MINE_TYPE` | Jumlah |
|---|---:|
| `12` | 964 |
| `11` | 757 |
| `06` | 634 |
| `05` | 366 |
| `04` | 104 |
| Kategori kosong/tidak berlabel | 74 |
| `01` | 69 |
| `02` | 19 |

**Interpretasi:**

- Kode `12` menjadi kategori terbanyak dengan 964 entitas.
- Kode `11` berada di posisi kedua dengan 757 entitas.
- Kode `06` menempati posisi ketiga dengan 634 entitas.
- Tiga kategori teratas berjumlah 2.355 entitas.
- Terdapat kategori dengan label kosong sebanyak 74 baris, yang kemungkinan berkaitan dengan nilai kosong atau whitespace.

**Catatan:** Notebook menampilkan kode `MINE_TYPE`, bukan pemetaan deskripsi resmi untuk setiap kode. Karena itu, interpretasi jenis operasional secara spesifik memerlukan referensi kode kategori yang tervalidasi.

### C. Distribusi Sektor Industri (Distribution of Industrial Sectors)

**Visualisasi:** Pie Chart

Analisis ini menggunakan kolom `NAICSDESCR` untuk mengeksplorasi enam sektor industri dengan jumlah entitas terbanyak.

```python
sektor = df_core["NAICSDESCR"].value_counts().head(6)

fig, ax = plt.subplots(figsize=(10, 7))
ax.pie(
    sektor.values,
    autopct="%1.1f%%",
    startangle=90,
    counterclock=False
)

ax.set_title("6 Sektor Industri Teratas (NAICSDESCR)")
plt.tight_layout()
plt.show()
```

**Enam sektor industri teratas:**

| Sektor Industri | Jumlah | Persentase dari 6 sektor teratas |
|---|---:|---:|
| Bituminous Coal Surface Mining and/or Beneficiating | 885 | 38,7% |
| Bituminous Coal Underground Mining or Mining and Beneficiating | 738 | 32,3% |
| Dimension Stone Mining or Quarrying | 249 | 10,9% |
| Kaolin and Ball Clay Mining | 162 | 7,1% |
| Crushed and Broken Granite Mining and Quarrying | 132 | 5,8% |
| Crushed and Broken Limestone Mining and Quarrying | 120 | 5,2% |
| **Total enam sektor** | **2.286** | **100%** |

**Interpretasi:**

- Sektor batu bara bituminous mendominasi enam kategori teratas.
- Penambangan batu bara terbuka memiliki 885 entitas, sedangkan penambangan batu bara bawah tanah memiliki 738 entitas.
- Kedua sektor batu bara tersebut menyumbang sekitar 71% dari kelompok enam sektor teratas.
- Sektor batu dimensi, kaolin dan ball clay, granit, serta batu kapur membentuk bagian lainnya.

**Catatan interpretasi:** Persentase pada pie chart dihitung dari total enam sektor teratas (2.286 entitas), bukan dari seluruh 2.996 baris dataset.

### D. Perusahaan dengan Jumlah Tambang Terbanyak (Companies with the Most Mines)

**Analisis:** Top 10 Company Ranking

Notebook membuat fungsi `top_perusahaan_tambang()` untuk menghitung perusahaan dengan jumlah entitas tambang terbanyak berdasarkan `COMPNAME`.

Fungsi ini membersihkan spasi di awal dan akhir teks, lalu mengabaikan string kosong sebelum menghitung frekuensi.

**10 perusahaan teratas:**

| Peringkat | Perusahaan | Jumlah Tambang |
|---:|---|---:|
| 1 | Cold Spring Granite Company | 15 |
| 2 | Oxford Mining | 15 |
| 3 | CAM Mining LLC | 13 |
| 4 | Paramont Coal Company Virginia | 13 |
| 5 | Rosebud Mining Company | 12 |
| 6 | Consolidation Coal Company | 11 |
| 7 | Black Beauty Coal Company | 11 |
| 8 | Amfire Mining Company LLC | 10 |
| 9 | Acme Brick Company | 9 |
| 10 | Premier Elkhorn Coal Company | 9 |

**Interpretasi:**

- Cold Spring Granite Company dan Oxford Mining berbagi posisi teratas dengan masing-masing 15 entitas tambang.
- CAM Mining LLC dan Paramont Coal Company Virginia mengikuti dengan masing-masing 13 entitas.
- Jumlah entitas pada sepuluh perusahaan teratas berada pada rentang 9–15.
- Tidak terlihat satu perusahaan yang mendominasi jumlah entitas secara ekstrem dalam daftar sepuluh teratas.

**Catatan:** Perhitungan berdasarkan teks `COMPNAME` yang telah di-strip, tetapi masih menggunakan pencocokan nama secara persis. Variasi penulisan nama perusahaan atau akhiran badan usaha dapat membuat satu grup perusahaan tercatat dalam beberapa nama berbeda.

## 🤖 Automated Report (Narrative Summary)

Sebagai bagian akhir analisis, notebook membuat fungsi `laporan_tambang()` untuk menghasilkan ringkasan naratif singkat dari direktori tambang.

Fungsi tersebut merangkum:

- Total entitas tambang.
- Jumlah negara bagian.
- Negara bagian dengan jumlah tambang terbanyak.
- Kode jenis tambang yang paling dominan.
- Perusahaan dengan jumlah tambang terbanyak setelah mengabaikan nilai kosong.
- Sektor industri dengan jumlah entitas terbanyak.

**Hasil laporan otomatis:**

| Indikator | Hasil |
|---|---|
| Total tambang | 2.996 |
| Jumlah wilayah (`STATE`) | 48 |
| Wilayah terbanyak | KY (514) |
| Jenis tambang dominan | `12` (964) |
| Perusahaan teratas | Cold Spring Granite Company (15) |
| Sektor teratas | Bituminous Coal Surface Mining and/or Beneficiating (885) |

Laporan otomatis ini membuat ringkasan utama dapat dihasilkan kembali melalui satu pemanggilan fungsi ketika dataset diperbarui.

## 💡 Kesimpulan Utama dan Insight

Berdasarkan hasil EDA terhadap 2.996 entitas tambang, terdapat beberapa temuan utama.

### 1. Konsentrasi Geografis di Wilayah Appalachian

**Temuan:**

Kentucky (514), West Virginia (411), dan Pennsylvania (367) merupakan tiga negara bagian dengan jumlah entitas tambang terbanyak. Ketiganya mencakup sekitar 43% dari seluruh data.

**Insight:**

Direktori tambang menunjukkan konsentrasi geografis yang kuat di tiga negara bagian tersebut. Analisis lanjutan dapat memeriksa distribusi komoditas dan jenis tambang secara lebih rinci di setiap wilayah.

### 2. Kategori Kode Jenis Tambang Terkonsentrasi pada Tiga Kode Teratas

**Temuan:**

Kode `12`, `11`, dan `06` masing-masing memiliki 964, 757, dan 634 entitas.

**Insight:**

Sebagian besar catatan berada pada tiga kode kategori tersebut. Namun, pemaknaan operasional setiap kode perlu dikonfirmasi menggunakan dokumentasi resmi `MINE_TYPE` sebelum membuat interpretasi yang lebih spesifik.

### 3. Dominasi Sektor Batu Bara Bituminous

**Temuan:**

Dua sektor batu bara bituminous—surface mining dan underground mining—memiliki jumlah gabungan 1.623 entitas pada enam sektor teratas.

**Insight:**

Komposisi sektor pada dataset menunjukkan besarnya representasi aktivitas pertambangan batu bara bituminous. Kelompok sektor lain yang masuk enam besar meliputi batu dimensi, kaolin dan ball clay, granit, serta batu kapur.

### 4. Jumlah Entitas pada Perusahaan Teratas Relatif Berdekatan

**Temuan:**

Cold Spring Granite Company dan Oxford Mining sama-sama memiliki 15 entitas tambang. Sepuluh perusahaan teratas masing-masing tercatat memiliki 9–15 entitas.

**Insight:**

Daftar sepuluh perusahaan teratas tidak menunjukkan satu nama perusahaan dengan jumlah entitas yang sangat jauh di atas perusahaan lainnya. Namun, hasil ini masih bergantung pada konsistensi penulisan nama perusahaan dalam dataset.

### 5. Pentingnya Pembersihan Missing Value Tersembunyi

**Temuan:**

Pemeriksaan standar menemukan 0 missing value `NaN`, tetapi terdapat 74 string kosong atau whitespace pada kolom `COMPNAME`.

**Insight:**

Pemeriksaan kualitas data tidak cukup hanya menggunakan `isnull()` atau `isna()`. Pembersihan teks seperti `.str.strip()` dan pengubahan string kosong menjadi missing value diperlukan sebelum analisis berbasis nama perusahaan dilakukan.

## ⚠️ Keterbatasan dan Hal yang Perlu Diperhatikan

- **Cakupan dataset:** Dataset merupakan direktori tambang mineral di Amerika Serikat, bukan representasi seluruh industri pertambangan global.
- **Pemilihan fitur:** Analisis hanya menggunakan enam kolom dari 57 kolom yang tersedia. Informasi administratif dan atribut lain tidak dieksplorasi.
- **Tipe data:** Seluruh enam kolom terpilih bertipe `object`, termasuk `MINE_TYPE` yang berisi kode kategori.
- **Kode jenis tambang:** Notebook belum memetakan kode `MINE_TYPE` ke deskripsi resmi. Interpretasi kode memerlukan referensi tambahan.
- **Whitespace pada perusahaan:** Sebanyak 74 string kosong/spasi pada `COMPNAME` tidak terdeteksi sebagai `NaN` pada pemeriksaan awal.
- **Variasi nama perusahaan:** Penghitungan perusahaan masih bergantung pada kecocokan string persis sehingga variasi penulisan dapat memecah pencatatan satu grup usaha.
- **Jumlah baris bukan kapasitas produksi:** Frekuensi entitas tambang tidak menunjukkan volume produksi, cadangan, kapasitas, nilai ekonomi, atau tingkat aktivitas operasional.
- **Tidak ada dimensi waktu:** Analisis dalam notebook berfokus pada distribusi direktori dan tidak membahas tren perubahan jumlah tambang dari waktu ke waktu.
- **Analisis deskriptif:** Hasil visualisasi menunjukkan distribusi dan frekuensi, bukan hubungan sebab-akibat atau pengujian statistik.
- **Tidak ada pemodelan prediktif:** Notebook berfokus pada EDA dan pembuatan laporan otomatis, bukan pengembangan model machine learning.

## 🚀 Pengembangan Selanjutnya (Next Steps)

Beberapa pengembangan yang dapat dilakukan untuk memperdalam analisis:

1. **Pemetaan Kode `MINE_TYPE`**

   Menggabungkan kode jenis tambang dengan dokumentasi resmi agar kategori dapat diinterpretasikan secara lebih jelas.

2. **Pembersihan dan Standardisasi Nama Perusahaan**

   Menormalisasi whitespace, singkatan, akhiran badan usaha, dan variasi penulisan nama operator agar penghitungan perusahaan lebih konsisten.

3. **Analisis Regional yang Lebih Mendalam**

   Membandingkan jenis tambang dan sektor industri berdasarkan `STATE` dan `COUNTY`.

4. **Analisis Hubungan Antarvariabel**

   Mengeksplorasi kombinasi `MINE_TYPE`, `NAICSDESCR`, `STATE`, dan `COMPNAME` untuk memahami pola distribusi kategori.

5. **Eksplorasi Seluruh Kolom Dataset**

   Memeriksa atribut lain dari 57 kolom untuk menemukan informasi tambahan yang relevan dengan lokasi dan karakteristik tambang.

6. **Integrasi Data Spasial**

   Jika tersedia koordinat yang valid, data dapat dikembangkan menjadi peta persebaran tambang.

7. **Pengembangan Dashboard**

   Membuat dashboard interaktif untuk memantau jumlah tambang berdasarkan wilayah, sektor, jenis tambang, dan perusahaan.

8. **Penyempurnaan Automated Report**

   Mengembangkan fungsi laporan agar dapat menghasilkan tabel dan visualisasi secara otomatis setelah dataset diperbarui.

Pengembangan tersebut merupakan rencana lanjutan dan belum diimplementasikan dalam notebook Day 10.

## 📂 Struktur Proyek

```text
50-Days-of-Data-Science-Journey_Mining-Industry/
│
├── Day_10_Mini_Project_Exploratory_Data_Analysis_Report_Automated_Mining/
│   ├── Day_10_Mini_Project_Exploratory_Data_Analysis_Report_Automated_Mining.ipynb
│   └── README.md
│
└── README.md
```

*Nama folder dan file di atas merupakan contoh penamaan; sesuaikan dengan struktur repository yang sebenarnya.*

## 🏁 Kesimpulan

Day 10 menunjukkan bagaimana tahapan Exploratory Data Analysis dapat digabungkan menjadi sebuah **laporan eksplorasi data tambang yang reusable dan otomatis**.

Berdasarkan 2.996 entitas tambang dan enam kolom inti, analisis menemukan bahwa persebaran tambang terkonsentrasi di Kentucky, West Virginia, dan Pennsylvania. Kode jenis tambang `12`, `11`, dan `06` menjadi tiga kategori teratas, sementara sektor batu bara bituminous mendominasi kelompok enam sektor industri dengan jumlah terbanyak.

Analisis perusahaan juga mengidentifikasi Cold Spring Granite Company dan Oxford Mining sebagai dua perusahaan teratas dengan masing-masing 15 entitas tambang. Di sisi kualitas data, ditemukan 74 nilai whitespace pada `COMPNAME` yang perlu diperhatikan karena tidak terdeteksi sebagai missing value standar.

Poin penting yang dipelajari dari proyek ini meliputi:

- Memilih kolom inti dari dataset dengan banyak atribut.
- Membuat fungsi ringkasan EDA yang dapat digunakan kembali.
- Memeriksa kualitas dan struktur data secara sistematis.
- Menghasilkan visualisasi distribusi wilayah, jenis tambang, dan sektor industri.
- Menghitung peringkat perusahaan dengan penanganan string kosong.
- Menyusun laporan naratif otomatis dari hasil eksplorasi.

Secara keseluruhan, proyek ini menjadi penerapan terpadu dari proses **data loading → feature selection → data inspection → automated summary → visualization → reporting**. Hasilnya dapat menjadi dasar untuk eksplorasi direktori tambang yang lebih mendalam pada tahap berikutnya.

---

## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

---

<p align="center">
  <em>Day 10 — Automated Exploratory Data Analysis Report for Mineral Resource Mines.</em>
</p>
