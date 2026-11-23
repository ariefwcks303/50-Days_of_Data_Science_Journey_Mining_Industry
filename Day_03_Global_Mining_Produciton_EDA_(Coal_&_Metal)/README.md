<div align="center">

# Day 03 — Global Mining Production EDA (Coal & Metals)

**Exploratory data analysis of global mine production records, focusing on annual ore production, mined materials, and mining facilities.**

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Project-Data%20Science%20Mining-blue" alt="Project: Data Science Mining">
  <img src="https://img.shields.io/badge/Type-Exploratory%20Data%20Analysis-purple" alt="Type: Exploratory Data Analysis">
  <img src="https://img.shields.io/badge/Focus-Global%20Mining%20Production-green" alt="Focus: Global Mining Production">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>



## 📌 Ringkasan Proyek

Hari 03 berfokus pada eksplorasi data produksi pertambangan global dari fasilitas tambang batu bara dan logam. *Notebook* ini meneliti seberapa banyak bijih yang ditambang dari waktu ke waktu, kode material mana yang menyumbang volume produksi terbesar, serta bagaimana distribusi produksi di berbagai fasilitas pertambangan.

Analisis ini menggunakan *dataset* `Open Database on Global Coal and Metal Mining`. Setelah memeriksa catatan awalnya, *notebook* memfokuskan analisis utamanya pada baris dengan `type == "Ore mined"`.

Alur kerjanya mencakup pemuatan *dataset*, inspeksi struktur, eksplorasi kategori, penyaringan (*filtering*), agregasi dengan `groupby`, dan visualisasi. Tiga sudut pandang analisis utama dikembangkan: tren produksi tahunan, 10 material teratas yang ditambang, dan distribusi produksi di tingkat fasilitas.

> **Catatan cakupan:** Meskipun *dataset* sumber mencakup pertambangan batu bara dan logam global, analisis dalam *notebook* ini secara khusus berfokus pada kategori `Ore mined`. *Notebook* ini tidak melakukan analisis terpisah khusus batu bara.

## 🎯 Tujuan Pembelajaran

* Memeriksa struktur, dimensi, tipe data, dan kelengkapan *dataset* produksi pertambangan.
* Memahami kategori produksi dan kode material yang tersedia.
* Menyaring catatan agar berfokus pada `Ore mined`.
* Mengagregasi tonase produksi berdasarkan tahun, material, dan fasilitas menggunakan `groupby`.
* Mengidentifikasi pola produksi tahunan dan material yang ditambang dengan volume tertinggi.
* Membuat peringkat fasilitas pertambangan berdasarkan total produksi `Ore mined` yang tercatat.
* Menggunakan grafik garis, grafik batang, dan *boxplot* logaritmik untuk mengeksplorasi data produksi.
* Menafsirkan temuan analisis sambil mempertimbangkan kemungkinan keterbatasan pelaporan data.

## 📊 Dataset

**Dataset:** Open Database on Global Coal and Metal Mining

**Sumber:** [Kaggle — Open Database on Global Coal and Metal Mining](https://www.kaggle.com/datasets/simonjasansky/open-database-on-global-coal-and-metal-mining)

*Dataset* ini merupakan kumpulan catatan produksi dari fasilitas tambang batu bara dan logam di seluruh dunia. Catatan mencakup tahun pelaporan, jenis produksi, kode material, pengenal fasilitas, dan kuantitas produksi dalam ton.

### Ringkasan Dataset

| Atribut | Deskripsi |
| --- | --- |
| Ukuran awal | 12.292 baris × 12 kolom |
| Rentang tahun | 2000–2020 |
| Satuan produksi | Ton (`value_tonnes`) |
| *Subset* analisis utama | `type == "Ore mined"` |
| Jumlah baris pada *subset* `Ore mined` | 5.324 |
| Fasilitas unik pada `Ore mined` | 524 |
| Jumlah jenis produksi | 4 |
| Jumlah kode material | 44 |

### Fitur Utama Dataset

| Kolom | Deskripsi |
| --- | --- |
| `id` | Pengenal catatan |
| `facility_id` | Pengenal fasilitas pertambangan |
| `year` | Tahun pelaporan produksi |
| `type` | Kategori produksi, seperti `Ore mined` atau `Ore processed` |
| `material` | Kode material yang terkait dengan catatan produksi |
| `value_tonnes` | Kuantitas produksi yang tercatat dalam ton |
| `overall_grade_ppm` | Informasi kadar keseluruhan, jika tersedia |
| `amount_sold_tonnes` | Jumlah terjual dalam ton, jika tersedia |
| `mine_processing` | Informasi pemrosesan tambang, jika tersedia |
| `reporting_period` | Informasi periode pelaporan, jika tersedia |
| `source_id` | Pengenal catatan sumber |
| `comment` | Komentar tambahan pada catatan, jika tersedia |

*Notebook* ini terutama menggunakan `year`, `type`, `material`, `value_tonnes`, dan `facility_id`. Beberapa kolom deskriptif memiliki banyak nilai kosong dan tidak diperlukan untuk analisis produksi utama.

### Kategori Produksi

*Dataset* awal memiliki empat nilai pada kolom `type`:

| Jenis Produksi | Jumlah Catatan |
| --- | --- |
| `Ore mined` | 5.324 |
| `Ore processed` | 5.313 |
| `Concentrate` | 1.612 |
| `NM mineral` | 43 |
| **Total** | **12.292** |

Analisis utama pada *notebook* ini hanya menggunakan `Ore mined`.

### Kelengkapan Data

Inspeksi awal menunjukkan bahwa kolom identifikasi dan produksi inti sudah lengkap, sedangkan beberapa kolom detail opsional hanya terisi sebagian kecil.

| Kolom | Catatan Non-Null |
| --- | --- |
| `id` | 12.292 |
| `facility_id` | 12.292 |
| `year` | 12.292 |
| `type` | 12.292 |
| `material` | 12.292 |
| `value_tonnes` | 12.292 |
| `overall_grade_ppm` | 408 |
| `amount_sold_tonnes` | 542 |
| `mine_processing` | 483 |
| `reporting_period` | 1.960 |
| `source_id` | 12.292 |
| `comment` | 655 |

> **Pertimbangan data:** *Notebook* ini memperlakukan tonase produksi, tahun, fasilitas, jenis, dan material sebagai kolom utama untuk EDA ini. Nilai kosong pada kolom detail opsional tidak diimputasi.

## 🧰 Peralatan & Pustaka

* **Python** — bahasa pemrograman utama.
* **Pandas** — pemuatan data, inspeksi, penyaringan, pengelompokan, dan agregasi.
* **NumPy** — operasi numerik, termasuk transformasi logaritmik.
* **Matplotlib** — pembuatan dan pemformatan grafik.
* **Seaborn** — visualisasi statistik, termasuk grafik batang dan *boxplot*.
* **Kaggle API** — pengunduhan *dataset*.

## 🔍 Alur Kerja

### 1. Pengunduhan & Pemuatan Dataset

*Notebook* menginstal dan mengonfigurasi Kaggle API, mengunduh *dataset*, mengekstrak arsip, dan memuat `minerals.csv` ke dalam Pandas DataFrame.

Langkah pemuatan utamanya adalah:

```python
df = pd.read_csv("/content/data/minerals.csv")

```

*Notebook* ini menggunakan berkas kredensial Kaggle bernama `kaggle.json` untuk autentikasi unduhan.

### 2. Inspeksi Data Awal

*Dataset* diperiksa menggunakan:

```python
df.head(2)
df.info()

```

Inspeksi ini mengonfirmasi:

* **12.292 catatan** dan **12 kolom**.
* Tiga kolom bertipe *integer*, dua kolom bertipe *floating-point*, dan tujuh kolom bertipe *object*.
* Kolom produksi dan identifikasi utama terisi penuh.
* Beberapa kolom deskriptif opsional mengandung banyak nilai kosong.

Oleh karena itu, kolom produksi inti dapat digunakan untuk langkah pengelompokan dan agregasi utama pada *notebook* tanpa membuang baris akibat nilai kosong pada kolom-kolom tersebut.

### 3. Eksplorasi Jenis Produksi & Material

*Notebook* memeriksa nilai unik dan frekuensi `type`, mengeksplorasi kode `material` yang tersedia, serta mengidentifikasi rentang tahun.

Catatan tersebut mencakup **tahun 2000 hingga 2020** dan berisi **44 kode material berbeda**. Distribusi jenis dipimpin oleh `Ore mined` (5.324 catatan) dan `Ore processed` (5.313 catatan), diikuti oleh `Concentrate` (1.612) dan `NM mineral` (43).

Contoh kode material yang ada meliputi:

* `O.Fe`, `O.Cu`, `O.Au`, dan `O.Ni` untuk kategori material terkait bijih (*ore*).
* `Con.Cu`, `Con.Fe`, dan `Con.Zn` untuk kategori konsentrat.
* `M.dol`, `M.lim`, dan `M.kao` untuk kategori material lain yang terwakili dalam *dataset*.

*Notebook* mencatat bahwa mengelompokkan kode material berdasarkan awalannya dapat membantu menyederhanakan agregasi berikutnya. Taksonomi kode material secara penuh tidak diterapkan dalam analisis ini.

### 4. Penyaringan Catatan `Ore mined`

Analisis utama dibatasi pada catatan dengan `type` sama dengan `Ore mined`.

```python
ore_mined = df[df["type"] == "Ore mined"].copy()
ore_mined = ore_mined.dropna(
    subset=["value_tonnes", "year"]
)

ore_mined["year"] = ore_mined["year"].astype(int)

```

**Hasil penyaringan:**

| Atribut | Hasil |
| --- | --- |
| Catatan `Ore mined` sebelum penyaringan | 5.324 |
| Catatan setelah penyaringan kolom wajib | 5.324 |
| Catatan terhapus karena `value_tonnes` atau `year` kosong | 0 |

Seluruh 5.324 catatan `Ore mined` tetap bertahan setelah pemeriksaan kolom wajib karena `value_tonnes` dan `year` sudah lengkap pada *subset* ini.

### 5. Agregasi Produksi Bijih Tahunan

*Notebook* mengelompokkan catatan `Ore mined` berdasarkan tahun dan menjumlahkan `value_tonnes` untuk menghitung total produksi yang tercatat pada tiap tahun.

```python
annual_production = (
    ore_mined.groupby("year")["value_tonnes"].sum()
)

```

*Series* yang dihasilkan divisualisasikan sebagai grafik garis. Nilai produksi dikonversi dari ton menjadi **miliar ton** agar lebih mudah dibaca.

**Pengamatan utama yang tercatat di *notebook*:**

* Produksi tahunan sekitar **1,40 miliar ton** pada tahun 2000.
* Produksi meningkat dalam jangka panjang dan mencapai sekitar **4,13 miliar ton** pada tahun 2018.
* Produksi kemudian menurun menjadi sekitar **2,51 miliar ton** pada tahun 2019.
* Total tahun 2020 turun lebih jauh menjadi sekitar **0,43 miliar ton**.

*Notebook* menyoroti kenaikan yang kuat hingga 2018 dan penurunan tajam pada dua tahun terakhir. Penurunan di akhir periode ini disarankan untuk ditelusuri lebih lanjut karena dapat berkaitan dengan kondisi operasional atau kelengkapan pelaporan. Penyebabnya tidak ditetapkan di dalam *notebook*.

### 6. Top 10 Material Berdasarkan Total Produksi Ditambang

Untuk mengidentifikasi material dengan tonase tambang tercatat terbesar, *notebook* mengelompokkan `Ore mined` berdasarkan `material`, menjumlahkan `value_tonnes`, dan mengurutkan totalnya secara menurun.

```python
top_10_materials = (
    ore_mined.groupby("material")["value_tonnes"]
    .sum()
    .sort_values(ascending=False)
)

```

Sepuluh total teratas dikonversi ke miliar ton dan ditampilkan dalam grafik batang horizontal.

| Peringkat | Kode Material | Total Produksi (Miliar Ton) |
| --- | --- | --- |
| 1 | `O.Fe` | 17,93 |
| 2 | `O.Cu` | 10,61 |
| 3 | `O.bulk` | 8,51 |
| 4 | `O.Au` | 7,76 |
| 5 | `O.oxi` | 3,02 |
| 6 | `O.Al` | 1,71 |
| 7 | `O.sul` | 0,76 |
| 8 | `O.Ni` | 0,45 |
| 9 | `O.pgm` | 0,15 |
| 10 | `O.Zn` | 0,13 |

**Pengamatan utama:** `O.Fe` memiliki produksi kumulatif terbesar pada catatan yang dipilih, diikuti oleh `O.Cu`, `O.bulk`, dan `O.Au`. Terdapat selisih yang cukup besar antara empat kategori terdepan ini dan material lainnya di jajaran 10 besar.

### 7. Analisis Produksi Tingkat Fasilitas

*Notebook* mengagregasi `value_tonnes` berdasarkan `facility_id` untuk menghitung total produksi `Ore mined` yang tercatat bagi tiap fasilitas di seluruh catatan yang tersedia.

```python
fac_count = (
    ore_mined.groupby("facility_id")["value_tonnes"].sum()
)

```

Sepuluh fasilitas dengan produksi agregat tertinggi dipilih dan divisualisasikan dalam grafik batang horizontal.

**Pengamatan fasilitas teratas yang tercatat di *notebook*:**

| ID Fasilitas | Total Produksi (Miliar Ton) |
| --- | --- |
| `COM00520.00` | 2,19 |
| `COM01155.00` | 1,92 |
| `COM00178.00` | 1,74 |
| `COM00421.00` | 1,55 |
| `COM01290.00` | 1,38 |
| `COM00744.00` | 1,10 |
| `COM00276.00` | 1,08 |
| `COM01390.00` | 1,08 |
| `COM00747.00` | 1,05 |
| `COM00840.00` | 1,04 |

Sepuluh fasilitas teratas berkisar antara sekitar **1,04 hingga 2,19 miliar ton** dalam total produksi tercatat. `COM00520.00` merupakan penyumbang terbesar di antara fasilitas-fasilitas ini.

*Notebook* juga menghitung jumlah fasilitas unik pada *subset* `Ore mined`:

* **524 ID fasilitas unik**.

### 8. Distribusi Nilai Produksi (Skala Logaritmik)

Besaran nilai produksi sangat bervariasi. Agar distribusinya lebih mudah diamati, *notebook* menghapus nilai tonase yang kosong dan tidak positif, menerapkan transformasi logaritma berbasis 10, lalu membuat *boxplot*.

```python
tonase_valid = ore_mined["value_tonnes"].dropna()
tonase_valid = tonase_valid[tonase_valid > 0]

log_tonase = np.log10(tonase_valid)

sns.boxplot(x=log_tonase)

```

**Pengamatan yang tercatat di *notebook*:**

* 50% data tengah (*interquartile range*) berada kira-kira di antara `log10 = 5,9` dan `log10 = 6,9`.
* Median berada sekitar `log10 = 6,35`, setara dengan sekitar **2,2 juta ton** per catatan.
* Distribusi memiliki ekor bervolume lebih rendah dengan sejumlah *outlier* produksi kecil.
* Kumis (*whisker*) atas memanjang hingga sekitar `log10 = 8,5`, tanpa *outlier* atas yang diplot terpisah.

*Boxplot* ini menggambarkan distribusi catatan produksi individual, bukan total produksi yang diagregasikan untuk masing-masing fasilitas.

## 📈 Analisis Data Eksploratif & Visualisasi

*Notebook* menghasilkan tiga analisis visual utama:

### 1. Total Produksi Tahunan `Ore mined`

Grafik garis menampilkan jumlah tonase bijih yang ditambang untuk setiap tahun dari 2000 hingga 2020.

Grafik ini menyoroti kenaikan jangka panjang hingga 2018 serta penurunan tajam pada 2019–2020. Tahun-tahun akhir memerlukan penelusuran tambahan sebelum penurunan tersebut dapat ditafsirkan sebagai perubahan nyata pada output pertambangan global.

### 2. Top 10 Material Berdasarkan Total Produksi

Grafik batang horizontal memeringkatkan kode material berdasarkan tonase kumulatif `Ore mined`.

`O.Fe`, `O.Cu`, `O.bulk`, dan `O.Au` menempati empat posisi pertama, dengan `O.Fe` memimpin peringkat secara jelas.

### 3. Distribusi Produksi di Seluruh Catatan

*Boxplot* dari `log10(value_tonnes)` memperlihatkan sebaran kuantitas produksi pada tiap catatan `Ore mined`.

Transformasi logaritmik memudahkan pengamatan nilai dengan besaran yang sangat berbeda dan memperlihatkan adanya ekor bervolume lebih kecil pada distribusi.

## 💡 Temuan & Wawasan Utama

### 1. Dataset Mencakup Periode Produksi yang Luas

Catatan ini membentang dari **2000–2020**, menyediakan rentang waktu 21 tahun untuk menelaah nilai produksi yang tersedia di dalam basis data.

### 2. `Ore mined` dan `Ore processed` Mendominasi Jenis Catatan

*Dataset* memuat 5.324 catatan `Ore mined` dan 5.313 catatan `Ore processed`. Kedua kategori ini mencakup sebagian besar dari 12.292 baris.

### 3. Produksi Tahunan Ditambang Mencapai Total Tertinggi Tercatat pada 2018

Agregasi tahunan naik dari sekitar **1,40 miliar ton** pada 2000 menjadi sekitar **4,13 miliar ton** pada 2018. Nilainya kemudian turun tajam pada 2019 dan 2020.

*Notebook* menandai penurunan di akhir periode ini untuk ditelusuri lebih lanjut daripada menetapkan penyebab yang pasti.

### 4. Bijih Besi dan Tembaga Memimpin Peringkat Material

`O.Fe` mencatatkan sekitar **17,93 miliar ton** produksi kumulatif yang ditambang, sementara `O.Cu` mencatatkan sekitar **10,61 miliar ton**. `O.bulk` dan `O.Au` menyusul dengan masing-masing sekitar 8,51 dan 7,76 miliar ton.

### 5. Dataset Mencakup 524 Fasilitas Pertambangan pada Subset Terpilih

Catatan `Ore mined` memuat **524 ID fasilitas unik**. Di antara 10 fasilitas teratas, `COM00520.00` memiliki produksi kumulatif tercatat tertinggi sekitar **2,19 miliar ton**.

### 6. Catatan Produksi Individual Memiliki Rentang Nilai yang Lebar

*Boxplot* berskala log menunjukkan bahwa separuh tengah catatan produksi berada kira-kira antara 0,8 juta dan 8 juta ton, dengan median mendekati 2,2 juta ton. Catatan produksi yang lebih kecil membentuk ekor bervolume lebih rendah.

## ⚠️ Keterbatasan & Pertimbangan

* **Cakupan analisis:** *Notebook* berfokus pada `Ore mined`; analisis terpisah untuk produksi batu bara atau perbandingan seluruh empat jenis produksi tidak dilakukan.
* **Cakupan pelaporan:** Penurunan tajam pada 2019–2020 dapat mencerminkan perubahan operasional, cakupan pelaporan, atau catatan yang belum lengkap. *Notebook* tidak memverifikasi penyebabnya.
* **Kode material:** Analisis menggunakan kode material apa adanya. Pemetaan lengkap yang terverifikasi dari setiap kode ke nama komoditas tidak disediakan.
* **Interpretasi fasilitas:** `facility_id` digunakan sebagai pengenal. *Notebook* tidak memetakan ID fasilitas ke nama fasilitas, negara, atau lokasi geografis.
* **Makna agregasi:** Total material dan fasilitas merupakan penjumlahan lintas catatan dan tahun yang tersedia di *dataset*. Angka tersebut perlu ditafsirkan dalam konteks cakupan pelaporan *dataset*.
* **Kolom opsional jarang terisi:** Kadar, jumlah terjual, pemrosesan, periode pelaporan, dan komentar mengandung banyak nilai kosong dan tidak dipakai pada EDA utama.
* **Tanpa analisis kausal:** Visualisasi menggambarkan pola dan perbedaan; grafik tidak menetapkan mengapa produksi berubah atau mengapa fasilitas memiliki tingkat output berbeda.
* **Tanpa pemodelan prediktif:** *Notebook* ini melakukan EDA deskriptif dan tidak melatih model peramalan maupun *machine learning*.

## 🚀 Langkah Berikutnya

Beberapa arah lanjutan untuk analisis berikutnya antara lain:

* Menelusuri penurunan produksi 2019–2020 dan menilai kelengkapan pelaporan per tahun.
* Membandingkan tren produksi `Ore mined`, `Ore processed`, dan `Concentrate`.
* Membuat pemetaan kode material terdokumentasi dan mengelompokkan material ke kategori komoditas yang lebih luas.
* Menganalisis tren produksi tahunan untuk masing-masing material terdepan secara tersendiri.
* Menelaah tren produksi tingkat fasilitas dari waktu ke waktu, tidak hanya total kumulatif.
* Mengeksplorasi `overall_grade_ppm` dan `amount_sold_tonnes` setelah menilai cakupan dan kesesuaiannya.
* Menambahkan informasi negara atau geografis jika tersedia *dataset* referensi fasilitas yang andal.

Hal-hal ini merupakan usulan perluasan dan belum diimplementasikan di dalam *notebook* ini.

## 📂 Struktur Proyek

```text
50-Days-of-Data-Science-Journey_Mining-Industry/
│
│
├── Day_03_Global_Mining_Production_EDA/
│   ├── Day_03_Global_Mining_Production_EDA_(Coal_&_Metals).ipynb
│   └── README.md
│
└── README.md

```



## 🏁 Kesimpulan

Hari 03 menerapkan analisis data eksploratif pada catatan produksi pertambangan global dengan kajian terfokus pada kategori `Ore mined`. *Notebook* memeriksa *dataset*, menyaring 5.324 catatan bijih yang ditambang, mengagregasi produksi berdasarkan tahun, material, dan fasilitas, serta memvisualisasikan hasilnya.

Analisis ini mengidentifikasi puncak produksi tahunan tercatat sekitar **4,13 miliar ton pada 2018**, menyoroti `O.Fe` dan `O.Cu` sebagai dua kode material terdepan berdasarkan produksi kumulatif, serta menemukan **524 fasilitas unik** pada *subset* terpilih. Hasilnya juga memperlihatkan rentang kuantitas produksi yang lebar pada tiap catatan individual.

Hasil ini memberikan gambaran deskriptif awal terhadap *dataset*. Pemeriksaan lanjutan terkait kelengkapan pelaporan, definisi kode material, dan metadata fasilitas masih diperlukan sebelum menarik kesimpulan yang lebih luas mengenai aktivitas pertambangan global.

---
## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student

---

<p align="center">
  <em>Day 03 — Exploring global mining production through data-driven analysis.</em>
</p>
