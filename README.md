# Downscaling Data CHIRPS: Interpolasi IDW (Resolusi 1km)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Research-orange)

## Deskripsi Proyek

Repositori ini memuat implementasi teknis untuk melakukan **Spatial Downscaling** (penurunan skala spasial) pada data curah hujan **CHIRPS v3.0**.

Tujuan utama script ini adalah meningkatkan resolusi spasial data dari **0.05° (~5.5 km)** menjadi **1000 meter (1 km)** untuk analisis hidrometeorologi tingkat lokal di **Provinsi Jambi**. Proses ini melibatkan transformasi sistem koordinat dari Geografis (WGS84) ke Metrik (UTM Zone 48S).

## Metodologi

Proses *downscaling* dilakukan dengan tahapan sebagai berikut:

1.  **Reproyeksi Koordinat**: Mengubah sistem referensi spasial (CRS) input dari `EPSG:4326` (Lat/Lon) menjadi `EPSG:32748` (UTM Zone 48S) agar satuan jarak menjadi **meter**.
2.  **Pembuatan Grid Target**: Membuat grid kosong baru dengan ukuran piksel **1000 x 1000 meter** yang mencakup seluruh wilayah studi.
3.  **Interpolasi IDW (*Inverse Distance Weighting*)**:
    Nilai piksel pada grid target ($Z_{target}$) dihitung berdasarkan nilai piksel tetangga terdekat ($Z_{source}$) menggunakan pembobotan jarak terbalik:
    $$Z_{target} = \frac{\sum (Z_{source} / d^p)}{\sum (1 / d^p)}$$
    * **Power ($p$):** 2
    * **Neighbors ($k$):** 12 titik terdekat
    * **Search Algorithm:** `cKDTree` (Scipy Spatial)
4.  **Geometri Masking**: Memotong (*clipping*) hasil interpolasi sesuai batas administrasi Provinsi Jambi agar data di luar wilayah studi menjadi *NoData*.

## Prasyarat Instalasi

Script ini dikembangkan untuk lingkungan **Google Colab**. Berikut adalah dependensi pustaka yang digunakan:

### Daftar Pustaka
* **Manipulasi Raster:** `rasterio`
* **Manipulasi Vektor:** `geopandas`
* **Transformasi Koordinat:** `pyproj`
* **Interpolasi Spasial:** `scipy` (spatial.cKDTree)
* **Komputasi Numerik:** `numpy`
* **Utilitas:** `tqdm`

### Instalasi
```bash
pip install rasterio geopandas scipy pyproj numpy tqdm

```

## Struktur Direktori Data

Pastikan struktur direktori di Google Drive Anda sesuai dengan konfigurasi berikut:

```text
/Direktori_Project/
├── CHIRPS v3/
│   └── bias_corrected_ga/
│       └── raster_2009_2020/   # Input: File .tif resolusi asli (~5km)
├── Batas Adm Jambi/            # Input: Shapefile batas wilayah (.shp)
└── output/
    └── downscaled_1km_metric/  # Output: File .tif resolusi 1km

```

## Cara Penggunaan

1. **Persiapan Data**: Pastikan file input (CHIRPS terkoreksi bias) dan Shapefile batas administrasi tersedia.
2. **Konfigurasi Path**: Sesuaikan variabel `INPUT_DIR` dan `ADMIN_PATH` pada bagian awal script.
3. **Parameter Grid**:
* `TARGET_RES`: Resolusi output (default: 1000 meter).
* `TARGET_CRS`: Sistem koordinat target (default: EPSG:32748 untuk Jambi).


4. **Eksekusi**: Jalankan script. Proses interpolasi menggunakan *cKDTree* yang efisien, namun waktu pemrosesan bergantung pada jumlah file dan luas wilayah.

## Spesifikasi Output

File output yang dihasilkan memiliki karakteristik:

* **Format:** GeoTIFF (`.tif`)
* **Resolusi:** 1000 meter x 1000 meter
* **CRS:** WGS 84 / UTM Zone 48S (`EPSG:32748`)
* **Tipe Data:** Float32
* **Kompresi:** LZW

## Penulis

**Jariyan Arifudin** Mahasiswa Geografi Lingkungan
Universitas Gadjah Mada (UGM)

## Lisensi & Sitasi

Kode ini didistribusikan di bawah **MIT License**.
Jika Anda menggunakan metode ini untuk penelitian, silakan sitasi repositori ini:

> Arifudin, J. (2026). *Downscaling Data CHIRPS: Interpolasi IDW (Resolusi 1km)*. GitHub Repository.
