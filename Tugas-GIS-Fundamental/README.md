# Laporan Ringkas: Eksplorasi Data Spasial (Task Day 1 - GIS Fundamental)

**Analis:** Muhammad Caesar Almayda Wira  
**Format Output Spasial:** GeoJSON  
**Sistem Proyeksi Dasar:** WGS 84 / EPSG:4326  

---

### 1. Judul Eksplorasi Data
Analisis Aksesibilitas Isochrone dan Konektivitas Jaringan Ritel Terhadap Infrastruktur Gerbang Tol di Kecamatan Pondok Aren.

### 2. Wilayah Studi
Daerah administrasi yang digunakan adalah Kecamatan Pondok Aren, Kota Tangerang Selatan, Provinsi Banten, dengan fokus area analisis pada sekitar kawasan hunian Bintaro dan Pondok Jaya.

### 3. Sumber dan Jenis Data
*   **Sumber Data:** OpenStreetMap (OSM) yang diekstraksi melalui utilitas Overpass Turbo, kemudian diolah secara terintegrasi menggunakan QGIS dan editor kecerdasan spasial MAPID.
*   **Jenis Data:** Data Vektor Digital berupa fitur titik (*Point*), fitur wilayah (*Polygon*), dan rute jalan (*LineString*).

### 4. Informasi Dasar Data
Berikut adalah daftar *layer* spasial yang digunakan dalam lingkungan proyek (Quantum GIS & MAPID):

*   **Layer 1: sampel_minimarket (Fasilitas Ritel Hasil Olahan)**
    *   *Geometri:* Point (Titik)
    *   *Atribut Utama:* `id`, `name` (Alfamart / Indomaret), `shop` (convenience), `akens_tol`
    *   *Sistem Referensi Koordinat (CRS):* WGS 84 / EPSG:4326
*   **Layer 2: sampel_isokrom (Catchment Area - Eksklusif MAPID)**
    *   *Geometri:* Polygon (Area)
    *   *Atribut Utama:* `id_tool`, `isochrone_profile` (car), `time_limit`, `distance_limit` (300 meter)
    *   *Sistem Referensi Koordinat (CRS):* WGS 84 / EPSG:4326
*   **Layer 3: sampel_route (Shortest Path)**
    *   *Geometri:* LineString (Garis)
    *   *Atribut Utama:* `id`, `start`, `end`, `length_meters`
    *   *Sistem Referensi Koordinat (CRS):* WGS 84 / EPSG:4326
*   **Layer 4: sampel_buffer (Batas Jangkauan Buffer GT 500 Meter)**
    *   *Geometri:* Polygon (Area)
    *   *Atribut Utama:* `id`, `distance` (500 meter)
    *   *Sistem Referensi Koordinat (CRS):* WGS 84 / EPSG:4326
*   **Layer 5: sampel_gt (Infrastruktur Gerbang Tol)**
    *   *Geometri:* Point (Titik)
    *   *Atribut Utama:* `id`, `name` (Gerbang Tol Bintaro 2), `barrier` (toll_booth)
    *   *Sistem Referensi Koordinat (CRS):* WGS 84 / EPSG:4326

### 5. Alur Pengerjaan Secara Singkat
1.  **Akuisisi Data:** Melakukan penarikan data spasial dari database OpenStreetMap berupa sebaran objek minimarket ritel (`sampel_minimarket`), posisi gerbang tol (`sampel_gt`), dan jaringan jalan utama di wilayah administrasi Pondok Aren.
2.  **Identifikasi & Transformasi Koordinat:** Membuka dataset spasial ke dalam lembar kerja QGIS untuk melakukan pengecekan informasi dasar (geometri, tabel atribut) serta memastikan kesesuaian sistem koordinat geografis (EPSG:4326).
3.  **Analisis Aksesibilitas Jaringan:** 
    *   Membuat pemodelan area jangkauan radius garis lurus melingkar sejauh 500 meter dari titik Gerbang Tol Bintaro 2 menggunakan tool `Buffer` untuk menghasilkan layer cakupan `sampel_buffer`.
    *   Kalkulasi rute terpendek (`sampel_route`) dari lokasi sampel toko ritel menuju titik simpul **Gerbang Tol Bintaro 2** menggunakan algoritma *Shortest Path (Layer to Point)* di QGIS untuk mengukur efisiensi jarak tempuh logistik.
    *   Membentuk area jangkauan pasar riil minimarket berbasis rute jaringan jalan (*Catchment Area*) menggunakan analisis Isochrone dengan profil berkendara mobil sejauh 300 meter (`distance_limit: 300`) pada platform MAPID (`sampel_isokrom`).
4.  **Plotting, Styling, & Export:** Mengatur estetika peta visual dengan menerapkan skema warna kontras untuk batas poligon isochrone, menonjolkan garis rute terpendek dengan warna biru tebal, dan memberikan simbol khusus pada titik fasilitas ritel. Data hasil pengerjaan kemudian diekspor ke dalam format GeoJSON.

### 6. Peta Visualisasi Spasial
*(Catatan: Sematkan gambar peta visualisasi "map_visualization.jpg" Anda di bagian ini saat dicetak ke PDF atau diunggah ke GitHub)*

### 7. Catatan Singkat Potensi Penggunaan Data untuk WebGIS
Berkas data spasial berformat GeoJSON yang dihasilkan dari eksplorasi ini memiliki potensi kegunaan yang sangat tinggi dalam pengembangan aplikasi WebGIS interaktif:
*   **Dashboard Analisis Bisnis (Site Selection):** Poligon area jangkauan dari layer `sampel_isokrom` sangat ideal digunakan sebagai basis peta tematik (*catchment area*) pada WebGIS. Hal ini membantu manajemen ritel menganalisis wilayah cakupan pasar secara digital dan menentukan lokasi optimal untuk ekspansi gerai baru.
*   **Layer Sebaran Lokasi & Pop-Up Interaktif:** Fitur titik dari layer `sampel_minimarket` didukung secara *native* oleh *library* pemetaan web modern (seperti Leaflet.js atau Mapbox GL JS). Struktur data teks JSON yang ringan mempercepat proses pemuatan (*loading*) ribuan titik fasilitas pada browser klien, serta memudahkan pemrograman fitur *pop-up* informasi atribut ritel saat titik diklik oleh pengguna.
*   **Visualisasi Jaringan Infrastruktur:** Data rute dari layer `sampel_route` dapat dimanfaatkan sebagai data awal pembuatan dasbor navigasi atau visualisasi keterhubungan fasilitas logistik terhadap akses jalan bebas hambatan secara *real-time*.
