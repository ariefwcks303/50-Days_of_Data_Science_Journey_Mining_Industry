::: {align="center"}
# Day 08 --- Exploratory Data Analysis of Global Reserve and Ore Distribution

**Eksplorasi distribusi geografis situs bijih dunia, komoditas mineral
utama, dan cadangan batubara berdasarkan negara serta kawasan.**
:::

```{=html}
<p align="center">
```
`<img src="https://img.shields.io/badge/Project-Data%20Science%20Mining-blue" alt="Project">`{=html}
`<img src="https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple" alt="EDA">`{=html}
`<img src="https://img.shields.io/badge/Focus-Global%20Ore%20%26%20Coal%20Reserves-green" alt="Focus">`{=html}
`<img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">`{=html}
```{=html}
</p>
```
## 📌 Gambaran Umum Proyek

Sumber daya mineral dan batubara tersebar di berbagai wilayah dunia
dengan karakteristik geografis dan komoditas yang berbeda. Memahami
persebaran lokasi bijih (*ore sites*), komoditas utama, serta cadangan
batubara dapat menjadi langkah awal untuk mengeksplorasi lanskap sumber
daya pertambangan global.

Pada Day 08, **Exploratory Data Analysis (EDA)** dilakukan menggunakan
dua dataset:

1.  **Mineral Ores Around the World** --- berisi catatan lokasi deposit
    atau situs bijih, negara, komoditas, tipe deposit, dan koordinat
    geografis.
2.  **Coal Reserves by Type** --- berisi data cadangan batubara
    berdasarkan entitas negara atau kawasan dan jenis batubara.

Analisis dataset ore menggunakan sampel acak sebanyak 50.000 baris dari
total 304.632 baris agar proses eksplorasi dan visualisasi lebih ringan.
Dataset cadangan batubara digunakan sebagai analisis pelengkap untuk
melihat distribusi cadangan *Sub-bituminous and lignite*.

Tujuan proyek ini adalah memahami pola distribusi data pertambangan,
mengidentifikasi komoditas yang paling sering tercatat,
memvisualisasikan lokasi geografis, serta mengeksplorasi perbandingan
cadangan batubara.

**Catatan ruang lingkup:** Hasil analisis dataset ore merupakan hasil
dari sampel 50.000 baris, bukan keseluruhan dataset. Jumlah catatan
situs juga tidak sama dengan volume cadangan, kapasitas produksi, atau
tingkat aktivitas tambang.

## 🎯 Tujuan Pembelajaran

-   Memuat dan memeriksa dataset pertambangan berukuran besar.
-   Mengambil sampel acak menggunakan `DataFrame.sample()`.
-   Memahami struktur dan kualitas data melalui inspeksi awal.
-   Mengidentifikasi missing values pada variabel kunci.
-   Menghitung distribusi negara dan komoditas menggunakan
    `value_counts()`.
-   Membuat visualisasi peringkat negara dan komoditas.
-   Memvisualisasikan sebaran geografis menggunakan latitude dan
    longitude.
-   Menggunakan Cartopy untuk menampilkan titik lokasi pada peta dunia.
-   Mengeksplorasi data cadangan batubara berdasarkan jenisnya.
-   Menginterpretasikan hasil EDA dengan mempertimbangkan keterbatasan
    cakupan data.

## 📊 Dataset

### 1. Mineral Ores Around the World

**Sumber:** [Kaggle --- Mineral Ores Around the
World](https://www.kaggle.com/datasets/ramjasmaurya/mineral-ores-around-the-world)

Dataset ini berisi catatan lokasi deposit atau situs bijih di berbagai
negara, termasuk informasi komoditas dan koordinat geografis.

  -----------------------------------------------------------------------
  Atribut                             Keterangan
  ----------------------------------- -----------------------------------
  Ukuran dataset awal                 304.632 baris × 22 kolom

  Data yang dianalisis                50.000 baris

  Metode sampling                     Random sampling

  `random_state`                      `42`

  Fokus analisis                      Negara, komoditas, tipe deposit,
                                      tipe operasi, dan koordinat
  -----------------------------------------------------------------------

Variabel kunci yang dipilih:

  Kolom         Keterangan
  ------------- -----------------------
  `country`     Negara lokasi deposit
  `commod1`     Komoditas utama
  `com_type`    Tipe komoditas
  `dep_type`    Tipe deposit
  `oper_type`   Tipe operasi
  `latitude`    Koordinat lintang
  `longitude`   Koordinat bujur

### 2. Coal Reserves by Type

**Sumber:** [Kaggle --- Coal Reserves by
Type](https://www.kaggle.com/datasets/valchovalev/coalreservesbytype)

Dataset pelengkap yang berisi informasi cadangan batubara berdasarkan
entitas negara atau kawasan dan kategori jenis batubara.

  -----------------------------------------------------------------------
  Atribut                             Keterangan
  ----------------------------------- -----------------------------------
  Ukuran dataset                      47 baris × 5 kolom

  Entitas                             Negara dan agregat kawasan/dunia

  Periode pada data yang ditampilkan  2019

  Variabel numerik                    Cadangan anthracite and bituminous
                                      serta sub-bituminous and lignite

  Fokus analisis notebook             Sub-bituminous and lignite
  -----------------------------------------------------------------------

**Catatan:** Kolom `Entity` mencampurkan negara individual dengan
agregat seperti `World`, `Asia Pacific`, `CIS`, dan `Europe`. Karena
itu, perbandingan harus membedakan entitas negara dan agregat wilayah.

## 🛠️ Tools & Libraries

  Library / Tools   Fungsi
  ----------------- -----------------------------------------------------------
  Python            Bahasa pemrograman utama
  Pandas            Membaca, memilih, mengambil sampel, dan mengolah data
  NumPy             Operasi numerik
  Matplotlib        Membuat visualisasi
  Seaborn           Library visualisasi statistik yang diimpor dalam notebook
  Cartopy           Menampilkan peta geografis dan fitur peta dunia
  Kaggle API        Mengunduh dataset
  Google Colab      Lingkungan pengembangan notebook

### Import Library

``` python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

Untuk visualisasi geografis, notebook menggunakan Cartopy:

``` python
import cartopy.crs as ccrs
import cartopy.feature as cfeature
```

## 🔍 Alur Exploratory Data Analysis

``` mermaid
flowchart TD
    A["Mineral Ores Around the World"] --> B["Load Dataset"]
    B --> C["Initial Inspection"]
    C --> D["Random Sampling: 50,000 Rows"]
    D --> E["Key Column Selection"]
    E --> F["Missing Value Analysis"]
    F --> G["Ore Sites by Country"]
    F --> H["Primary Commodities"]
    F --> I["Geographic Distribution Map"]
    J["Coal Reserves by Type"] --> K["Load Coal Dataset"]
    K --> L["Select Sub-bituminous and Lignite"]
    L --> M["Top 10 Entity Comparison"]
    G --> N["Key Findings & Data Limitations"]
    H --> N
    I --> N
    M --> N
```

### 1. Memuat dan Memeriksa Dataset Ore

Dataset diunduh melalui Kaggle API dan dimuat menggunakan Pandas.

``` python
df = pd.read_csv("Mineral ores round the world.csv")
df.shape
```

Hasil inspeksi menunjukkan dataset memiliki **304.632 baris dan 22
kolom**.

Karena ukuran dataset cukup besar untuk eksplorasi awal, notebook
mengambil sampel acak sebanyak 50.000 baris:

``` python
df_ore = df.sample(n=50000, random_state=42)
```

Penggunaan `random_state=42` membuat proses sampling dapat direproduksi
dengan kondisi dataset dan urutan data yang sama.

### 2. Pemilihan Variabel Kunci

Tujuh kolom dipilih untuk analisis kualitas data dan eksplorasi:

``` python
col_key = [
    "country",
    "commod1",
    "com_type",
    "dep_type",
    "oper_type",
    "latitude",
    "longitude"
]
```

Pemilihan ini berfokus pada informasi negara, komoditas, karakteristik
deposit dan operasi, serta koordinat geografis.

### 3. Missing Value Analysis

Pemeriksaan missing values dilakukan pada tujuh variabel terpilih.

``` python
df_ore[col_key].isnull().mean().mul(100).round(2)
```

**Hasil pemeriksaan:**

  Kolom           Missing Values
  ------------- ----------------
  `country`                0,00%
  `commod1`                6,74%
  `com_type`               1,26%
  `dep_type`              87,87%
  `oper_type`              0,00%
  `latitude`               0,00%
  `longitude`              0,00%

**Interpretasi:**

-   `country`, `oper_type`, `latitude`, dan `longitude` tidak memiliki
    missing values pada sampel.
-   `com_type` memiliki missing values relatif rendah, yaitu 1,26%.
-   `commod1` memiliki 6,74% missing values sehingga sebagian catatan
    tidak memiliki informasi komoditas utama.
-   `dep_type` memiliki 87,87% missing values. Variabel ini perlu
    digunakan dengan hati-hati dan tidak ideal dijadikan syarat wajib
    untuk analisis tanpa penanganan lebih lanjut.

**Catatan proses:** Notebook melakukan pemeriksaan missing values,
tetapi tidak melakukan imputasi atau penghapusan baris secara menyeluruh
pada dataset ore.

## 📈 Exploratory Data Analysis & Visualisasi

### A. Negara dengan Situs Ore Terbanyak

**Visualisasi:** Top 10 Horizontal Bar Chart

Analisis ini menggunakan `value_counts()` pada kolom `country` untuk
mengidentifikasi negara dengan jumlah catatan situs bijih terbanyak
dalam sampel.

``` python
top_negara = (
    df_ore["country"]
    .dropna()
    .value_counts()
    .head(10)
    .sort_values(ascending=True)
)
```

**Hasil utama dari notebook:**

    Peringkat Negara                                                Jumlah Situs
  ----------- --------------------------------------------------- --------------
            1 United States                                               43.875
            2 Mexico                                                         766
            3 Chile                                                          689
            4 Peru                                                           517
        5--10 Argentina, Brazil, Canada, Bolivia, Russia, China           178--?

**Interpretasi:**

United States mendominasi jumlah catatan situs dalam sampel dengan
selisih yang sangat besar dibandingkan negara berikutnya. Mexico berada
di posisi kedua dengan 766 catatan, sedangkan United States memiliki
43.875 catatan.

Dominasi tersebut menunjukkan ketimpangan cakupan pencatatan di dalam
dataset. Pola ini tidak dapat langsung diartikan bahwa jumlah deposit
atau aktivitas pertambangan Amerika Serikat lebih besar daripada seluruh
negara lain.

**Insight:** Cakupan dan sumber pencatatan data perlu diperiksa sebelum
menggunakan jumlah situs sebagai representasi distribusi sumber daya
mineral dunia.

### B. Komoditas Utama yang Paling Sering Tercatat

**Visualisasi:** Top 10 Horizontal Bar Chart

Analisis ini menggunakan kolom `commod1` untuk menghitung frekuensi
komoditas utama.

``` python
top_komod = (
    df_ore["commod1"]
    .dropna()
    .value_counts()
    .head(10)
    .sort_values(ascending=True)
)
```

**10 komoditas utama teratas:**

    Peringkat Komoditas                         Jumlah Situs
  ----------- ------------------------------- --------------
            1 Gold                                     9.343
            2 Sand and Gravel, Construction            7.561
            3 Stone, Crushed/Broken                    2.603
            4 Copper                                   2.495
            5 Iron                                     2.279
            6 Uranium                                  1.529
            7 Silver                                   1.100
            8 Lead                                       999
            9 Clay                                       927
           10 Manganese                                  768

**Interpretasi:**

Gold merupakan komoditas utama yang paling sering tercatat, diikuti oleh
Sand and Gravel, Construction. Daftar sepuluh besar menunjukkan bahwa
catatan dataset tidak hanya mencakup logam berharga dan logam industri,
tetapi juga material konstruksi seperti pasir, kerikil, batu pecah, dan
lempung.

**Insight:** Dataset merekam beragam komoditas, mulai dari mineral logam
hingga bahan galian konstruksi. Frekuensi catatan menunjukkan seberapa
sering komoditas muncul dalam dataset, bukan volume cadangan atau nilai
ekonominya.

### C. Distribusi Geografis Situs Ore

**Visualisasi:** Geographic Scatter Plot menggunakan Cartopy

Koordinat `longitude` dan `latitude` digunakan untuk menampilkan lokasi
situs bijih pada peta dunia.

``` python
geo = df_ore.dropna(
    subset=["latitude", "longitude"]
).copy()

geo = geo[
    geo["latitude"].between(-90, 90)
    & geo["longitude"].between(-180, 180)
]
```

Notebook menggunakan proyeksi `PlateCarree`, fitur daratan, garis
pantai, batas negara, dan scatter plot untuk menampilkan titik lokasi.

**Interpretasi:**

-   Titik situs terlihat sangat padat di Amerika Utara, terutama Amerika
    Serikat dan Meksiko.
-   Amerika Selatan menunjukkan sebaran memanjang di wilayah barat,
    termasuk Chile dan Peru, serta titik-titik di Brasil.
-   Eropa, Afrika, Asia, Asia Tenggara, dan Australia juga memiliki
    catatan lokasi, tetapi kepadatan visualnya lebih rendah dibandingkan
    Amerika Serikat.
-   Pola peta konsisten dengan dominasi United States pada grafik jumlah
    situs per negara.

**Insight:** Peta membantu melihat pola spasial dari catatan lokasi.
Namun, kepadatan titik juga dapat dipengaruhi oleh kelengkapan dan
cakupan sumber data, bukan hanya kondisi geologi atau aktivitas
pertambangan.

### D. Cadangan Batubara Berdasarkan Negara dan Kawasan

**Dataset:** Coal Reserves by Type

Analisis pelengkap menggunakan dataset cadangan batubara. Notebook
memilih kolom numerik terakhir yang tersedia, yaitu cadangan
**Sub-bituminous and lignite**, lalu menampilkan sepuluh entitas dengan
nilai terbesar.

``` python
kol_num = df_coal.select_dtypes(include="number").columns
target_col = kol_num[-1]
```

Nilai cadangan dibagi dengan `1e9` untuk ditampilkan dalam satuan miliar
(*billion*).

**Temuan dari notebook:**

  Entitas           Cadangan Sub-bituminous and lignite
  --------------- -------------------------------------
  World                               320,47 miliar ton
  Asia Pacific                        114,44 miliar ton
  Russia                               90,45 miliar ton
  CIS                                  90,45 miliar ton
  Europe                                            ...
  Australia                            76,51 miliar ton
  Germany                              35,90 miliar ton
  North America                                     ...
  United States                        30,00 miliar ton
  Indonesia                            11,73 miliar ton

**Interpretasi:**

-   `World` merupakan agregat global dan memiliki nilai terbesar pada
    grafik.
-   `Asia Pacific` merupakan agregat kawasan dengan cadangan 114,44
    miliar ton.
-   Di antara entitas negara yang disebutkan dalam interpretasi
    notebook, Russia memiliki cadangan 90,45 miliar ton, diikuti
    Australia 76,51 miliar ton.
-   Indonesia tercatat memiliki 11,73 miliar ton untuk kategori batubara
    yang dianalisis.

**Catatan kualitas dan interpretasi data:**

Kolom `Entity` mencampurkan agregat global/kawasan dan negara
individual. Oleh karena itu, grafik sepuluh entitas teratas tidak boleh
disebut sebagai peringkat sepuluh negara tanpa pemisahan kategori
terlebih dahulu. Nilai `Russia` dan `CIS` yang sama juga perlu
diperhatikan agar tidak terjadi penghitungan ganda ketika membuat
agregasi regional.

## 💡 Kesimpulan Utama dan Insights

### 1. Cakupan Data Ore Sangat Didominasi United States

**Temuan:** United States memiliki 43.875 catatan situs dari 50.000
sampel, jauh di atas Mexico dengan 766 catatan.

**Insight:** Ketimpangan ini mengindikasikan kemungkinan *data coverage
bias*. Dataset dapat merepresentasikan wilayah tertentu secara jauh
lebih rinci dibanding wilayah lain. Jumlah catatan tidak dapat digunakan
sebagai ukuran langsung total cadangan mineral global.

### 2. Gold dan Material Konstruksi Mendominasi Frekuensi Komoditas

**Temuan:** Gold tercatat sebanyak 9.343 situs, sedangkan Sand and
Gravel, Construction sebanyak 7.561 situs.

**Insight:** Dataset memuat catatan yang luas untuk komoditas logam
maupun material konstruksi. Frekuensi ini menggambarkan distribusi
pencatatan komoditas, bukan volume produksi, cadangan, atau nilai
ekonomi.

### 3. Persebaran Geografis Tidak Merata

**Temuan:** Peta menunjukkan konsentrasi titik yang sangat padat di
Amerika Utara, terutama United States.

**Insight:** Visualisasi spasial memperlihatkan pola distribusi catatan
lokasi, tetapi interpretasi geologis memerlukan data tambahan dan
pemahaman terhadap proses pengumpulan dataset.

### 4. Cadangan Sub-bituminous and Lignite Memiliki Konsentrasi Regional yang Besar

**Temuan:** Asia Pacific tercatat sebesar 114,44 miliar ton, sementara
Russia sebesar 90,45 miliar ton pada data yang ditampilkan.

**Insight:** Data cadangan batubara memberikan perspektif tambahan
mengenai distribusi sumber daya energi. Perbandingan negara harus
dilakukan setelah agregat kawasan dipisahkan dari negara individual.

## ⚠️ Keterbatasan dan Hal yang Perlu Diperhatikan

-   **Random sampling:** Analisis dataset ore menggunakan 50.000 dari
    304.632 baris dengan `random_state=42`; hasil frekuensi merupakan
    hasil sampel.
-   **Data coverage bias:** Dominasi United States dapat mencerminkan
    cakupan dan sumber pencatatan dataset.
-   **Missing values:** Kolom `dep_type` memiliki 87,87% missing values,
    sedangkan `commod1` memiliki 6,74%.
-   **Tidak ada imputasi:** Notebook memeriksa missing values tetapi
    tidak melakukan imputasi atau pembersihan menyeluruh terhadap
    dataset ore.
-   **Jumlah situs bukan cadangan:** Frekuensi catatan lokasi tidak
    menunjukkan volume cadangan, produksi, kapasitas, atau nilai
    ekonomi.
-   **Peta bukan validasi geologi:** Titik koordinat hanya menunjukkan
    lokasi yang tercatat dalam dataset dan tidak membuktikan kelengkapan
    lokasi deposit di seluruh dunia.
-   **Campuran entitas coal:** Dataset cadangan batubara mencampurkan
    negara individual dengan agregat kawasan dan dunia.
-   **Pemilihan kolom otomatis:** Notebook menggunakan kolom numerik
    terakhir (`kol_num[-1]`) sebagai target visualisasi cadangan.
    Pemilihan eksplisit nama kolom akan lebih mudah diaudit.
-   **Tidak ada analisis temporal:** Analisis coal menggunakan data yang
    ditampilkan untuk tahun 2019 dan tidak mengeksplorasi tren cadangan
    antarwaktu.
-   **Tidak ada analisis inferensial atau prediktif:** Notebook berfokus
    pada EDA deskriptif dan visualisasi, bukan pengujian statistik atau
    pemodelan Machine Learning.

## 🚀 Pengembangan Selanjutnya (Next Steps)

1.  **Stratified Sampling berdasarkan Negara**

    Mengambil sampel berdasarkan negara atau kawasan agar analisis tidak
    terlalu didominasi oleh satu negara.

2.  **Pemisahan Entitas Negara dan Agregat Kawasan**

    Membersihkan kategori `Entity` pada dataset coal sebelum membuat
    peringkat negara.

3.  **Analisis Komoditas per Negara**

    Membandingkan komoditas utama pada negara atau kawasan tertentu
    menggunakan tabel silang dan visualisasi.

4.  **Analisis Tipe Deposit**

    Mengevaluasi apakah `dep_type` masih dapat digunakan setelah
    penanganan missing values yang tepat.

5.  **Interactive Geospatial Visualization**

    Mengembangkan peta interaktif menggunakan Folium atau GeoPandas
    untuk eksplorasi lokasi berdasarkan negara dan komoditas.

6.  **Analisis Cadangan Batubara yang Lebih Lengkap**

    Membandingkan kategori Anthracite and bituminous dengan
    Sub-bituminous and lignite serta memisahkan data negara dari agregat
    kawasan.

7.  **Integrasi Data Tambahan**

    Menggabungkan dataset dengan sumber data geologi, produksi, atau
    cadangan yang memiliki cakupan geografis dan definisi variabel yang
    lebih konsisten.

Pengembangan tersebut merupakan rencana lanjutan dan belum
diimplementasikan dalam notebook Day 08.

## 📂 Struktur Proyek

``` text
50-Days-of-Data-Science-Journey_Mining-Industry/
│
├── Day_08_Exploratory_Data_Analysis_of_Global_Reserve_and_Ore_Distribution/
│   ├── Day_8_Exploratory_Data_Analysis_of_Global_Reserve_and_Ore_Distribution.ipynb
│   └── README.md
│
└── README.md
```



## 🏁 Kesimpulan

Day 08 menunjukkan bagaimana Exploratory Data Analysis dapat digunakan
untuk mengeksplorasi distribusi situs bijih, komoditas mineral, lokasi
geografis, dan cadangan batubara.

Melalui sampel 50.000 catatan dari dataset Mineral Ores Around the
World, analisis menemukan dominasi catatan situs di United States,
frekuensi tinggi komoditas Gold dan material konstruksi, serta
konsentrasi titik lokasi yang terlihat pada peta dunia.

Dataset Coal Reserves by Type memberikan sudut pandang tambahan mengenai
cadangan *Sub-bituminous and lignite*, termasuk nilai yang tercatat
untuk kawasan Asia Pacific, Russia, Australia, dan Indonesia.

Namun, hasil ini perlu dibaca dengan mempertimbangkan keterbatasan
cakupan data, missing values, penggunaan random sampling, serta
pencampuran entitas negara dan agregat kawasan.

Secara keseluruhan, proyek ini memperkuat pemahaman mengenai **Mining
Data Analytics, exploratory geospatial analysis, dan interpretasi data
sumber daya mineral**. Analisis juga menegaskan bahwa kualitas, cakupan,
dan definisi data merupakan faktor penting sebelum menarik kesimpulan
tentang distribusi sumber daya pertambangan global.

------------------------------------------------------------------------

## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

------------------------------------------------------------------------

```{=html}
<p align="center">
```
`<em>`{=html}Day 08 --- Exploring Global Ore Sites and Coal Reserve
Distribution.`</em>`{=html}
```{=html}
</p>
```
