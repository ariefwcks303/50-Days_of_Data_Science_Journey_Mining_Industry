<div align="center">

# Day 02 — Pembersihan Data Harga Komoditas Logam

**Pembersihan dan eksplorasi awal data historis harga komoditas logam untuk kebutuhan analisis industri pertambangan.**

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Proyek-Data%20Science%20Pertambangan-blue" alt="Proyek: Data Science Pertambangan">
  <img src="https://img.shields.io/badge/Jenis-Pembersihan%20Data%20%26%20EDA-purple" alt="Jenis: Pembersihan Data dan EDA">
  <img src="https://img.shields.io/badge/Fokus-Komoditas%20Logam-green" alt="Fokus: Komoditas Logam">
  <img src="https://img.shields.io/badge/Python-3.x-yellow" alt="Python">
</p>

## 📌 Gambaran Umum Proyek

Day 02 berfokus pada proses pembersihan data harga komoditas historis dan persiapan data deret waktu (*time series*) agar dapat digunakan dalam analisis lebih lanjut di industri pertambangan.

Dataset awal berisi harga penutupan harian dari berbagai kontrak berjangka komoditas. Pada notebook ini, analisis dibatasi pada enam jenis logam yang relevan dengan industri pertambangan, yaitu **Emas, Tembaga, Perak, Aluminium, Nikel, dan Seng**.

Tahapan yang dilakukan meliputi pemeriksaan awal data, pemilihan kolom, konversi tanggal, pemeriksaan nilai kosong, penanganan nilai kosong menggunakan *forward fill*, pemeriksaan data berulang, serta visualisasi eksploratif. Tujuan utamanya adalah memahami kualitas data dan menyiapkan deret waktu harga enam komoditas logam tersebut.

## 🎯 Tujuan Pembelajaran

- Memeriksa struktur, ukuran, dan tipe data dari dataset harga komoditas.
- Mengidentifikasi nilai kosong dan membandingkan jumlahnya pada setiap komoditas.
- Memilih kolom komoditas logam yang relevan dengan industri pertambangan.
- Mengubah kolom tanggal menjadi tipe `datetime` dan mengurutkan data secara kronologis.
- Menangani nilai kosong pada data deret waktu menggunakan metode *forward fill* (`ffill`).
- Memeriksa kombinasi nilai harga yang berulang tanpa menyamakannya dengan tanggal duplikat.
- Membuat visualisasi tren harga jangka panjang dan imbal hasil harian emas.

## 📊 Dataset

**Dataset:** Commodity Futures Prices  
**Sumber:** [Kaggle — Commodity Prices](https://www.kaggle.com/datasets/debashish311601/commodity-prices)

Dataset berisi harga penutupan harian dari beberapa kontrak berjangka komoditas, dengan data yang dimulai pada tahun 2000.

| Keterangan | Nilai |
|---|---|
| Ukuran dataset awal | 6.092 baris × 24 kolom |
| Rentang tanggal | 2000-01-03 sampai 2023-08-04 |
| Kolom tanggal | `Date` |
| Komoditas yang dipilih | Gold, Copper, Silver, Aluminium, Nickel, Zinc |
| Ukuran data terpilih sebelum pembersihan | 6.092 baris × 7 kolom, termasuk `Date` |

### Kolom yang Digunakan

| Kolom | Komoditas |
|---|---|
| `GOLD` | Emas |
| `COPPER` | Tembaga |
| `SILVER` | Perak |
| `ALUMINIUM` | Aluminium |
| `NICKEL` | Nikel |
| `ZINC` | Seng |

> **Catatan cakupan:** Nama kolom komoditas mengikuti nama yang tersedia pada dataset. Notebook tidak menetapkan satuan atau mata uang yang sama untuk seluruh seri harga, sehingga README ini tidak mengasumsikan bahwa nilai harga absolut keenam komoditas dapat dibandingkan secara langsung.

## 🧰 Perangkat dan Pustaka

- **Python** — bahasa pemrograman yang digunakan.
- **Pandas** — membaca, memeriksa, memilih, dan membersihkan data.
- **NumPy** — mendukung operasi numerik.
- **Matplotlib** — membuat visualisasi tren dan distribusi.
- **Seaborn** — pustaka visualisasi yang diimpor pada notebook.
- **Kaggle API** — mengunduh dataset.

## 🔍 Tahapan Pengerjaan

### 1. Memuat dan Memeriksa Data Awal

Notebook mengunduh dataset dari Kaggle dan memuat berkas `commodity_futures.csv` ke dalam DataFrame Pandas.

Pemeriksaan awal dilakukan menggunakan:

- `head()` untuk melihat beberapa baris pertama.
- `info()` untuk memeriksa ukuran data, jumlah nilai yang tersedia, dan tipe data.
- `isna().sum()` untuk menghitung nilai kosong pada setiap kolom.

Dataset awal memiliki 6.092 baris dan 24 kolom. Kolom `Date` pada awalnya bertipe `object`, sedangkan kolom harga komoditas bertipe numerik (`float64`).

### 2. Pemeriksaan Nilai Kosong Awal

Pemeriksaan pertama menunjukkan bahwa jumlah nilai kosong berbeda-beda pada setiap komoditas.

Beberapa komoditas hanya memiliki sedikit nilai kosong, sedangkan kolom `GASOLINE` memiliki jumlah nilai kosong yang jauh lebih besar, yaitu 1.491. Kolom Aluminium, Seng, dan Nikel juga menunjukkan jumlah nilai kosong yang hampir sama.

Pemeriksaan ini menjadi dasar untuk memahami kualitas data sebelum memilih kolom logam yang akan dianalisis.

### 3. Memilih Kolom Komoditas Logam

Analisis hanya menggunakan kolom tanggal dan enam komoditas logam berikut:

```python
metal = [
    'Date', 'GOLD', 'COPPER', 'SILVER',
    'ALUMINIUM', 'NICKEL', 'ZINC'
]

df_metal = df[metal].copy()
```

Pemilihan ini mengurangi jumlah kolom dari 24 menjadi 7, termasuk kolom tanggal.

### 4. Mengubah Tanggal dan Mengurutkan Data

Kolom `Date` diubah dari tipe `object` menjadi `datetime`, kemudian data diurutkan berdasarkan tanggal.

```python
df_metal['Date'] = pd.to_datetime(df_metal['Date'])
df_metal = df_metal.sort_values('Date').reset_index(drop=True)
```

Rentang tanggal pada data terpilih adalah **2000-01-03 sampai 2023-08-04**.

Selanjutnya, kolom tanggal dijadikan indeks untuk mendukung pengolahan data deret waktu.

### 5. Menganalisis Nilai Kosong pada Enam Komoditas

Sebelum proses pembersihan, enam kolom komoditas terpilih memiliki jumlah nilai kosong sebagai berikut:

| Komoditas | Jumlah Nilai Kosong | Persentase |
|---|---:|---:|
| Emas | 10 | 0,164% |
| Tembaga | 9 | 0,148% |
| Perak | 13 | 0,213% |
| Aluminium | 47 | 0,772% |
| Nikel | 46 | 0,755% |
| Seng | 46 | 0,755% |

Seluruh kolom komoditas terpilih memiliki persentase nilai kosong di bawah **1%**.

Dua pola utama yang terlihat:

- Emas, Tembaga, dan Perak memiliki persentase nilai kosong yang sangat rendah, yaitu antara 0,148% dan 0,213%.
- Aluminium, Nikel, dan Seng memiliki persentase sedikit lebih tinggi, tetapi nilainya hampir sama, yaitu antara 0,755% dan 0,772%.

Kesamaan jumlah nilai kosong pada Aluminium, Nikel, dan Seng mungkin berkaitan dengan kalender perdagangan atau pola pengumpulan data yang sama. Namun, notebook belum memverifikasi penyebab pastinya.

### 6. Menangani Nilai Kosong dengan Forward Fill

Notebook menggunakan metode *forward fill* (`ffill`) untuk mengisi nilai kosong dengan membawa nilai terakhir yang tersedia ke baris berikutnya.

```python
df_metal_clean = df_metal.set_index('Date').sort_index()
df_metal_clean = df_metal_clean.ffill()
df_metal_clean = df_metal_clean.dropna()
```

Metode ini menggunakan pengamatan sebelumnya, bukan nilai dari masa mendatang. Pendekatan tersebut dapat digunakan untuk merepresentasikan harga terakhir yang tercatat ketika tidak ada pengamatan baru.

**Hasil pembersihan:**

| Tahap | Baris | Kolom |
|---|---:|---:|
| Data terpilih sebelum pembersihan | 6.092 | 7 |
| Data bersih setelah `Date` dijadikan indeks | 6.091 | 6 |

Setelah proses pembersihan, keenam kolom komoditas tidak lagi memiliki nilai kosong.

> **Catatan metode:** *Forward fill* mengasumsikan bahwa penggunaan harga terakhir untuk mengisi tanggal tanpa pengamatan merupakan pendekatan yang sesuai. Asumsi ini perlu ditinjau berdasarkan kalender perdagangan dan tujuan analisis. Untuk pemodelan prediksi, proses prapengolahan juga perlu dirancang berdasarkan urutan waktu agar tidak menggunakan informasi yang belum tersedia pada saat prediksi.

### 7. Memeriksa Data Berulang

Notebook memeriksa baris dengan kombinasi nilai komoditas yang berulang dan tanggal yang berulang secara terpisah.

- `df_metal_clean.duplicated().sum()` menghasilkan **30** kombinasi nilai yang berulang.
- `df_metal_clean.index.duplicated().sum()` menghasilkan **0** tanggal duplikat.
- Jika kedua kemunculan dari kombinasi nilai berulang dihitung, terdapat **60 baris** yang terlibat.

Notebook mengamati bahwa banyak kombinasi nilai yang berulang muncul pada tanggal berbeda, termasuk tanggal di sekitar akhir pekan atau hari libur pasar setelah penerapan *forward fill*. Karena tanggalnya tetap unik, baris-baris tersebut bukan duplikasi tanggal.

Oleh sebab itu, notebook tidak menghapus baris tersebut menggunakan `drop_duplicates()`. Baris tetap dipertahankan agar indeks tanggal dan nilai harga yang dibawa dari pengamatan sebelumnya tidak hilang.

## 📈 Analisis Eksploratif dan Visualisasi

### 1. Tren Harga Emas dan Tembaga

Grafik deret waktu dengan dua sumbu vertikal digunakan untuk melihat pergerakan historis harga Emas dan Tembaga dari tahun 2000 hingga 2023.

**Pengamatan dari notebook:**

- Kedua seri menunjukkan kecenderungan meningkat dalam jangka panjang.
- Tembaga memperlihatkan perubahan yang lebih tajam, termasuk penurunan yang terlihat pada periode 2008–2009, 2015–2016, dan awal 2020.
- Emas terlihat relatif lebih stabil pada beberapa periode.
- Grafik menggunakan sumbu vertikal yang berbeda. Karena itu, perpotongan garis tidak berarti harga Emas dan Tembaga memiliki nilai yang sama.

Perbandingan ini memberikan gambaran awal mengenai perbedaan pola historis antara logam mulia dan logam industri.

### 2. Persentase Nilai Kosong pada Setiap Logam

Diagram batang horizontal digunakan untuk membandingkan persentase nilai kosong keenam komoditas sebelum pembersihan.

Temuan utama:

- Seluruh komoditas terpilih memiliki nilai kosong di bawah 1%.
- Aluminium memiliki persentase tertinggi, yaitu 0,772%.
- Nikel dan Seng masing-masing memiliki persentase 0,755%.
- Tembaga memiliki persentase terendah, yaitu 0,148%.

Visualisasi ini memudahkan perbandingan kelengkapan data antar komoditas.

### 3. Distribusi Imbal Hasil Harian Emas

Notebook menghitung imbal hasil harian Emas menggunakan perubahan persentase (*percentage change*):

```python
df_metal_clean['gold_return'] = (
    df_metal_clean['GOLD'].pct_change() * 100
)
```

Histogram dengan 50 kelompok digunakan untuk memeriksa distribusinya.

**Pengamatan dari notebook:**

- Imbal hasil harian paling banyak terkonsentrasi di sekitar 0%.
- Sebagian besar pengamatan berada pada kisaran kurang lebih -2,5% hingga +2,5%.
- Pengamatan dengan perubahan lebih besar, di luar kisaran sekitar -5% hingga +5%, muncul lebih jarang.
- Notebook menginterpretasikan pengamatan di bagian luar distribusi sebagai pergerakan harga harian yang relatif besar dan tidak sering terjadi.

Analisis ini hanya menggambarkan distribusi perubahan harga historis, bukan meramalkan volatilitas di masa mendatang.

## 💡 Temuan dan Wawasan Utama

### 1. Data Enam Komoditas Terpilih Memiliki Nilai Kosong yang Rendah

Seluruh kolom komoditas terpilih memiliki persentase nilai kosong di bawah 1% sebelum pembersihan. Aluminium, Nikel, dan Seng memiliki persentase nilai kosong yang sedikit lebih tinggi, sedangkan Emas, Tembaga, dan Perak memiliki persentase lebih rendah.

### 2. Forward Fill Menghilangkan Nilai Kosong yang Tersisa

Penerapan *forward fill* menghasilkan dataset enam komoditas tanpa nilai kosong. Data bersih terdiri dari 6.091 baris dan enam kolom harga komoditas, dengan `Date` sebagai indeks.

### 3. Nilai yang Berulang Bukan Berarti Tanggal Duplikat

Terdapat 30 kombinasi nilai komoditas yang berulang, tetapi tidak ada tanggal yang duplikat. Notebook mengaitkan banyak nilai berulang dengan pengisian nilai sebelumnya pada tanggal tanpa pengamatan, seperti akhir pekan dan hari libur pasar. Baris tersebut tetap dipertahankan.

### 4. Emas dan Tembaga Memiliki Pola Historis yang Berbeda

Visualisasi menunjukkan bahwa Emas memiliki pola jangka panjang yang relatif lebih stabil, sedangkan Tembaga memperlihatkan perubahan yang lebih tajam. Temuan ini merupakan pengamatan deskriptif berdasarkan grafik harga historis.

### 5. Imbal Hasil Harian Emas Banyak Berada di Sekitar Nol

Sebagian besar imbal hasil harian Emas berada dekat dengan nol, sementara perubahan positif maupun negatif yang lebih besar terjadi lebih jarang. Histogram memberikan gambaran awal mengenai sebaran perubahan harga harian.

## ⚠️ Keterbatasan dan Hal yang Perlu Diperhatikan

- Analisis hanya berfokus pada enam komoditas logam terpilih, bukan seluruh 24 kolom pada dataset awal.
- Notebook tidak menetapkan satuan atau mata uang yang sama untuk seluruh harga komoditas.
- Penyebab nilai kosong dibahas sebagai kemungkinan, bukan sebagai penyebab yang telah diverifikasi.
- *Forward fill* membawa nilai pengamatan terakhir ke tanggal berikutnya. Asumsi ini belum tentu sesuai untuk setiap kalender perdagangan atau tujuan analisis.
- Kombinasi nilai harga yang berulang pada tanggal berbeda tetap dipertahankan karena bukan tanggal duplikat.
- Harga Emas dan Tembaga ditampilkan menggunakan dua sumbu vertikal berbeda, sehingga perpotongan garis tidak dapat digunakan untuk membandingkan nilai harga absolut.
- Notebook masih berada pada tahap pembersihan data dan eksplorasi deskriptif. Belum dilakukan pemodelan prediksi, analisis korelasi, ataupun penarikan kesimpulan sebab-akibat.

## 🚀 Rencana Pengembangan Berikutnya

Berdasarkan dataset yang telah dibersihkan, beberapa pengembangan yang dapat dilakukan pada tahap selanjutnya:

- Menganalisis hubungan dan korelasi antarharga komoditas logam.
- Membandingkan imbal hasil dan volatilitas keenam komoditas.
- Mengkaji perubahan harga pada periode pasar yang berbeda.
- Melakukan eksperimen peramalan deret waktu setelah menentukan variabel target dan metode evaluasi yang sesuai.
- Menghubungkan pergerakan harga dengan indikator pertambangan atau ekonomi makro lainnya apabila data pendukung tersedia.

Poin-poin tersebut merupakan usulan pengembangan dan belum dikerjakan pada notebook ini.

## 📂 Struktur Proyek

```text
50-Days-of-Data-Science-Journey_Mining-Industry/
│
│├── Day_02_Cleaning_Metal_Commodity_Price/
│   ├── Day_02_Cleaning_Metal_Commodity_Price.ipynb
│   └── README.md
│
└── README.md
```

*Nama folder dan notebook di atas merupakan contoh. Sesuaikan dengan nama yang digunakan pada repository GitHub.*

## 🏁 Kesimpulan

Day 02 menghasilkan dataset deret waktu yang telah dibersihkan untuk enam komoditas logam yang relevan dengan industri pertambangan. Proses yang dilakukan mencakup pemeriksaan data awal, pemilihan kolom logam, konversi tanggal, analisis nilai kosong, pengisian nilai menggunakan *forward fill*, pemeriksaan nilai berulang, serta visualisasi eksploratif.

Hasil akhirnya adalah dataset dengan **6.091 pengamatan dan enam variabel harga komoditas logam**, yang dapat digunakan sebagai dasar untuk analisis eksploratif lanjutan dan pemodelan deret waktu dengan rancangan yang tepat.
---
## 👤 Author

**Arief Wicaksono**

Aspiring AI Engineer | Data Science Student
---

<p align="center">
  <em>Day 02 — Membangun fondasi data yang andal untuk Data Science di industri pertambangan.</em>
</p>
