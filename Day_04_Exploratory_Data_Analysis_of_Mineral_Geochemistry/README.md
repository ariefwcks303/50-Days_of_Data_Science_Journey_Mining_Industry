# ⛏️ Day 4 --- Exploratory Data Analysis of Mineral Geochemistry

**Eksplorasi sifat fisik dan komposisi kimia mineral menggunakan
Python.**

![Project: Data
Exploration](https://img.shields.io/badge/Project-Data%20Exploration-blue)
![Type: Exploratory Data
Analysis](https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple)
![Domain: Mining and
Geology](https://img.shields.io/badge/Domain-Mining%20%26%20Geology-orange)
![Python 3.x](https://img.shields.io/badge/Python-3.x-yellow)

------------------------------------------------------------------------

## 📌 1. Gambaran Proyek

Proyek ini melakukan **Exploratory Data Analysis (EDA)** terhadap
dataset geokimia mineral untuk memahami karakteristik fisik dan
komposisi kimia dari ribuan mineral.

Dalam konteks pertambangan dan geologi, parameter seperti kekerasan
mineral, berat jenis, indeks bias, serta komposisi unsur dapat membantu
proses identifikasi dan karakterisasi mineral.

Eksplorasi ini berfokus pada tiga pertanyaan:

1.  Mineral apa yang memiliki tingkat kekerasan dan berat jenis
    tertinggi dalam dataset?
2.  Bagaimana distribusi nilai *Mohs Hardness*?
3.  Bagaimana hubungan antara sifat fisik mineral dan beberapa unsur
    kimia?

**Tujuan utama:** memahami struktur dataset, mengidentifikasi isu
kualitas data, dan menemukan pola awal melalui statistik deskriptif
serta visualisasi.

## 🎯 2. Tujuan Pembelajaran

-   Memahami dataset dengan jumlah kolom yang besar (*wide dataset*).
-   Melakukan pemeriksaan awal terhadap struktur dan statistik data.
-   Mengidentifikasi nilai `0.0` yang berpotensi merepresentasikan data
    yang tidak tersedia.
-   Mengeksplorasi mineral dengan nilai kekerasan dan berat jenis
    tertinggi.
-   Menganalisis distribusi kekerasan mineral.
-   Mengamati hubungan antara kekerasan dan berat jenis.
-   Menggunakan correlation matrix dan heatmap untuk mengeksplorasi
    hubungan antarvariabel.

## 📂 3. Dataset

Dataset yang digunakan adalah **Comprehensive Database of Minerals**
dari Kaggle.

  -------------------------------------------------------------------------------------------------------------------------------------------------------------
  Informasi                           Keterangan
  ----------------------------------- -------------------------------------------------------------------------------------------------------------------------
  Dataset                             Comprehensive Database of Minerals

  Sumber                              [Kaggle ---
                                      vinven7/comprehensive-database-of-minerals](https://www.kaggle.com/datasets/vinven7/comprehensive-database-of-minerals)

  Jumlah baris                        3.112

  Jumlah kolom                        140

  Domain                              Mineralogi, geologi, dan geokimia
  -------------------------------------------------------------------------------------------------------------------------------------------------------------

### Variabel yang dieksplorasi

  -----------------------------------------------------------------------
  Variabel                            Deskripsi
  ----------------------------------- -----------------------------------
  `Name`                              Nama mineral

  `Crystal Structure`                 Struktur kristal mineral

  `Mohs Hardness`                     Tingkat kekerasan mineral pada
                                      skala Mohs

  `Specific Gravity`                  Berat jenis relatif mineral

  `Refractive Index`                  Indeks bias cahaya

  `Iron`, `Copper`                    Informasi unsur besi dan tembaga

  `Silicon`, `Oxygen`, `Sulfur`       Informasi unsur silikon, oksigen,
                                      dan sulfur

  `Molar Mass`                        Massa molar mineral
  -----------------------------------------------------------------------

Dataset memiliki banyak variabel lain yang tidak seluruhnya digunakan
dalam eksplorasi Day 4 ini.

## 🛠️ 4. Tools & Libraries

  Tools        Penggunaan
  ------------ -----------------------------------------
  Python       Bahasa pemrograman
  Pandas       Membaca, memilih, dan menganalisis data
  NumPy        Komputasi numerik
  Matplotlib   Visualisasi data
  Seaborn      Visualisasi statistik dan heatmap
  Kaggle API   Mengunduh dataset

## 🔄 5. Workflow

``` mermaid
flowchart TD
    A["Download Dataset dari Kaggle"] --> B["Load Dataset dengan Pandas"]
    B --> C["Initial Data Exploration"]
    C --> D["Pemeriksaan Statistik Deskriptif"]
    D --> E["Identifikasi Nilai 0 sebagai Indikasi Missing Data"]
    E --> F["Analisis Mineral dengan Nilai Ekstrem"]
    F --> G["Visualisasi Distribusi Mohs Hardness"]
    G --> H["Analisis Hubungan Hardness dan Specific Gravity"]
    H --> I["Correlation Matrix dan Heatmap"]
    I --> J["Kesimpulan dan Insights"]
```

## 🔍 6. Exploratory Data Analysis

### A. Initial Exploration

Pemeriksaan awal dilakukan menggunakan `head()`, pemilihan kolom, dan
statistik deskriptif melalui `describe()`.

Dataset memiliki 3.112 observasi dan 140 kolom. Eksplorasi kemudian
difokuskan pada empat sifat fisik dan lima unsur kimia.

**Sifat fisik:**

-   `Mohs Hardness`
-   `Specific Gravity`
-   `Refractive Index`
-   `Molar Mass`

**Unsur kimia:**

-   `Iron`
-   `Copper`
-   `Silicon`
-   `Oxygen`
-   `Sulfur`

### B. Data Quality --- Zero Values

Salah satu temuan paling penting adalah perbedaan antara nilai kosong
secara teknis (`NaN`) dan nilai nol (`0.0`).

Pemeriksaan `.isnull()` menunjukkan **0% missing values** pada sembilan
kolom yang diperiksa. Namun, statistik deskriptif memperlihatkan bahwa
nilai kuartil pertama, median, dan kuartil ketiga pada tiga variabel
fisik berikut semuanya bernilai nol.

  ---------------------------------------------------------------------------
  Variabel            Q1 (25%)    Median (50%)        Q3 (75%) Interpretasi
  ------------ --------------- --------------- --------------- --------------
  Mohs                    0.00            0.00            0.00 Nilai nol
  Hardness                                                     sangat dominan

  Specific                0.00            0.00            0.00 Nilai nol
  Gravity                                                      sangat dominan

  Refractive              0.00            0.00            0.00 Nilai nol
  Index                                                        sangat dominan

  Molar Mass            239.42          411.77          747.09 Distribusi
                                                               memiliki
                                                               rentang nilai
                                                               yang terukur
  ---------------------------------------------------------------------------

Pada `Mohs Hardness`, terdapat **2.475 observasi bernilai 0.0**, atau
sekitar **79,5% dari total data**.

Dalam notebook ini, nilai nol pada ketiga variabel fisik tersebut
diperlakukan sebagai indikasi data yang belum tercatat atau *missing
values* terselubung. Interpretasi ini perlu diverifikasi terhadap
dokumentasi dataset sebelum dilakukan imputasi atau penghapusan data.

> **Data quality insight:** `.isnull()` saja tidak cukup untuk
> mendeteksi seluruh masalah kelengkapan data. Makna nilai nol harus
> dipahami berdasarkan konteks setiap variabel.

### C. Mineral dengan Kekerasan Tertinggi

Lima mineral dengan nilai `Mohs Hardness` tertinggi berdasarkan hasil
pengurutan notebook:

  Mineral         Mohs Hardness   Specific Gravity
  ------------- --------------- ------------------
  Stishovite                9.5              0.000
  Bromellite                9.0              3.020
  Corundum                  9.0              4.025
  Chrysoberyl               8.5              3.670
  Eskolaite                 8.0              5.180

**Stishovite** memiliki nilai kekerasan tertinggi dalam hasil
eksplorasi, yaitu 9.5. Namun, nilai `Specific Gravity` yang tercatat
untuk mineral tersebut adalah 0.000, sehingga nilai berat jenisnya tidak
dapat diinterpretasikan sebagai pengukuran valid tanpa verifikasi lebih
lanjut.

### D. Mineral dengan Specific Gravity Tertinggi

  Mineral       Specific Gravity   Mohs Hardness
  ----------- ------------------ ---------------
  Pyrope                   9.593            7.25
  Gananite                 8.928            3.50
  Tantite                  8.550            7.00
  Pinalite                 7.780            0.00
  Ferberite                7.580            4.25

**Pyrope** memiliki nilai `Specific Gravity` tertinggi dalam hasil
pengurutan, yaitu 9.593.

Temuan ini memperlihatkan bahwa mineral dengan kekerasan tertinggi belum
tentu memiliki berat jenis tertinggi. Kekerasan dan berat jenis
merupakan dua karakteristik fisik yang berbeda.

### E. Distribusi Mohs Hardness

Visualisasi histogram menunjukkan distribusi tingkat kekerasan mineral.

Temuan utama:

-   Sebanyak **2.475 mineral (sekitar 79,5%)** memiliki nilai
    `Mohs Hardness` sebesar `0.0`.
-   Nilai kekerasan yang lebih besar dari nol tersebar pada rentang
    sekitar 1 hingga 9.5.
-   Di antara nilai yang tercatat, jumlah mineral relatif banyak berada
    pada kisaran kekerasan 2--6.
-   Mineral dengan kekerasan di atas 8 relatif jarang ditemukan dalam
    dataset.

**Insight:** dominasi nilai nol membuat distribusi keseluruhan tidak
merepresentasikan distribusi kekerasan mineral yang terukur secara utuh.
Analisis lanjutan sebaiknya memisahkan nilai valid dan nilai yang diduga
sebagai data tidak tersedia.

### F. Hubungan Mohs Hardness dan Specific Gravity

Notebook membandingkan dua kelompok observasi:

1.  Data dengan `Mohs Hardness > 0` dan `Specific Gravity > 0`.
2.  Observasi yang memiliki nilai nol pada salah satu atau kedua
    variabel.

Pada kelompok data valid (\> 0), terlihat **tren positif yang lemah**:
mineral dengan kekerasan lebih tinggi cenderung memiliki berat jenis
sedikit lebih besar. Namun, sebaran titik cukup lebar sehingga hubungan
tersebut tidak dapat dianggap kuat.

Pada kelompok yang mengandung nilai nol, terlihat penumpukan titik pada
garis `x = 0` dan `y = 0`. Pola ini memperlihatkan dampak nilai nol
terhadap pembacaan hubungan kedua variabel.

**Insight:** pemisahan nilai nol membantu memperjelas pola hubungan
antara kekerasan dan berat jenis, tetapi tidak membuktikan hubungan
sebab-akibat.

### G. Correlation Analysis --- Physical Properties & Elements

Correlation matrix dibuat menggunakan sembilan variabel yang terdiri
dari empat sifat fisik dan lima unsur kimia.

Temuan korelasi yang dicatat dalam notebook:

  ------------------------------------------------------------------------
  Pasangan Variabel                         Korelasi Interpretasi Notebook
  --------------------- ---------------------------- ---------------------
  Mohs Hardness --                      0.75--0.81\* Korelasi positif kuat
  Specific Gravity                                   antar sifat fisik

  Mohs Hardness --                      0.75--0.81\* Korelasi positif kuat
  Refractive Index                                   antar sifat fisik

  Specific Gravity --                   0.75--0.81\* Korelasi positif kuat
  Refractive Index                                   antar sifat fisik

  Oxygen -- Molar Mass                          0.79 Korelasi positif kuat

  Oxygen -- Silicon                             0.64 Korelasi positif
                                                     cukup kuat

  Silicon -- Molar Mass                         0.45 Korelasi positif
                                                     sedang
  ------------------------------------------------------------------------

*Kisaran 0.75--0.81 merupakan rentang yang dilaporkan untuk hubungan
antar tiga sifat fisik tersebut, bukan nilai spesifik untuk setiap
pasangan.*

Sementara itu, unsur tunggal yang dianalisis memiliki korelasi linear
yang mendekati nol dengan beberapa sifat fisik, dengan kisaran sekitar
-0.11 hingga 0.02.

**Catatan interpretasi:** korelasi tinggi antar sifat fisik perlu dibaca
secara hati-hati karena banyak nilai nol muncul bersamaan pada
kolom-kolom tersebut. Nilai nol yang diduga sebagai missing value dapat
memengaruhi koefisien korelasi.

## 📊 7. Key Findings

  -----------------------------------------------------------------------
  Area                                Temuan Utama
  ----------------------------------- -----------------------------------
  Dataset                             3.112 baris dan 140 kolom

  Data quality                        Nilai nol berpotensi menjadi
                                      representasi missing values

  Mohs Hardness                       2.475 observasi (79,5%) bernilai
                                      0.0

  Mineral terkeras                    Stishovite --- 9.5 Mohs

  Specific Gravity tertinggi          Pyrope --- 9.593

  Hubungan sifat fisik                Tren positif lemah antara hardness
                                      dan specific gravity pada data \> 0

  Korelasi kimia                      Oxygen--Molar Mass: 0.79;
                                      Oxygen--Silicon: 0.64
  -----------------------------------------------------------------------

## ⚠️ 8. Limitations & Data Quality Notes

Beberapa hal perlu diperhatikan sebelum dataset digunakan untuk analisis
lanjutan atau Machine Learning:

1.  **Zero values:** nilai `0.0` pada beberapa variabel fisik diduga
    merepresentasikan data yang tidak tersedia. Asumsi ini perlu
    diverifikasi dengan dokumentasi dataset.
2.  **Missing-value handling:** pemeriksaan `.isnull()` tidak mendeteksi
    nilai nol sebagai missing value.
3.  **Kolom indeks:** notebook menjalankan
    `df.drop(columns=["Unnamed: 0"])` tanpa menyimpan hasilnya kembali
    ke `df` atau menggunakan `inplace=True`. Karena itu, operasi
    tersebut belum benar-benar menghapus kolom dari DataFrame yang
    digunakan.
4.  **Penggunaan `dropna()`:** `dropna()` hanya menghapus nilai `NaN`,
    bukan nilai `0.0`. Seleksi data valid pada scatter plot dilakukan
    secara terpisah dengan kondisi `> 0`.
5.  **Correlation bias:** korelasi dapat terpengaruh oleh nilai nol yang
    muncul bersamaan pada beberapa variabel.
6.  **Scope analisis:** eksplorasi ini bersifat deskriptif dan belum
    mencakup pengujian hipotesis, inferensi statistik, atau pemodelan
    prediktif.

## 🚀 9. Potential Next Steps

-   Memvalidasi arti nilai `0.0` melalui dokumentasi atau sumber
    dataset.
-   Mengubah nilai nol menjadi missing value hanya pada kolom yang
    memang menggunakan nol sebagai penanda data tidak tersedia.
-   Mengukur persentase missing values setelah validasi tersebut.
-   Membandingkan distribusi sebelum dan sesudah penanganan missing
    values.
-   Mengulang analisis korelasi menggunakan observasi yang valid.
-   Mengeksplorasi hubungan struktur kristal, komposisi unsur, dan sifat
    fisik mineral.
-   Mempertimbangkan analisis statistik atau model Machine Learning
    setelah kualitas data memadai.

## 📁 10. Project Structure

``` text
Day_04_Exploratory_Data_Analysis_of_Mineral_Geochemistry/
│
├── Day_04_Exploratory_Data_Analysis_of_Mineral_Geochemistry.ipynb
└── README.md
```

Dataset diunduh melalui Kaggle API pada notebook dan tidak harus
disimpan di repository.

## 🧠 11. Learning Outcomes

Melalui proyek ini, saya mempraktikkan:

-   Membaca dan memahami dataset geokimia dengan banyak variabel.
-   Menggunakan Pandas untuk eksplorasi dan statistik deskriptif.
-   Membedakan missing values teknis (`NaN`) dari nilai nol yang
    berpotensi menjadi placeholder.
-   Menggunakan histogram untuk memahami distribusi data.
-   Menggunakan scatter plot untuk mengeksplorasi hubungan dua variabel.
-   Menggunakan correlation matrix dan heatmap untuk mengamati hubungan
    antarvariabel.
-   Menyusun interpretasi berdasarkan konteks domain pertambangan dan
    geologi.
-   Mengidentifikasi keterbatasan kualitas data sebelum melangkah ke
    analisis lanjutan.

## 🏁 12. Conclusion

Day 4 menunjukkan bahwa proses EDA bukan hanya tentang membuat
visualisasi, tetapi juga memahami kualitas dan konteks data sebelum
menarik kesimpulan.

Dari eksplorasi 3.112 mineral, ditemukan indikasi kuat bahwa nilai nol
pada beberapa sifat fisik merupakan persoalan kualitas data yang perlu
ditangani. Analisis juga mengidentifikasi Stishovite sebagai mineral
dengan nilai kekerasan tertinggi dan Pyrope sebagai mineral dengan
specific gravity tertinggi dalam dataset.

Temuan korelasi memberikan gambaran awal mengenai hubungan antar sifat
fisik dan unsur kimia. Namun, hasil tersebut perlu dievaluasi kembali
setelah penanganan nilai nol dilakukan secara tepat.

**Key takeaway:** *A reliable analysis starts with understanding the
data --- not just visualizing it.*

---
## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

---

<p align="center">
  <em>Day 4 --- Data Exploration in Mining & Geochemistry.</em>
</p>

