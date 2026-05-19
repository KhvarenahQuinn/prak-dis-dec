# 1. Prasyarat
1. Install uv, Python, dan dependency
Melakukan instalasi `uv` sebagai package manager modern Python yang digunakan untuk mengelola environment dan dependency proyek. Setelah `uv` berhasil terpasang, dilakukan instalasi Python versi `3.14.4` menggunakan `uv`,kemudian membuat virtual environment agar dependency proyek terisolasi dari sistem utama. Selanjutnya diinstall beberapa package utama yaitu `fastapi[standard]`, `sqlmodel`, dan `uvicorn` yang digunakan untuk membangun REST API berbasis microservices.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/1_1.png" width="400"> </p>

2. Install SQLite
Berikutnya adalah memastikan SQLite telah tersedia pada sistem. SQLite digunakan sebagai database lokal ringan untuk menyimpan data.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/1_2.png" width="400"> </p>

3. Membuat Database dan Tabel Awal
Selanjutnya membuat database `departemen-sdm.db` menggunakan SQLite. Setelah database berhasil dibuat, dibuat tabel `sdm` yang berisi data pegawai dengan atribut `id`, `npp`, dan `nama`. Kemudian beberapa data dimasukkan ke dalam tabel dan diverifikasi menggunakan perintah `SELECT`.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/1_3.png" width="400"> </p>

# 2. Source Code
Membuat file `service.py` yang berisi source code REST API menggunakan FastAPI dan SQLModel. Source code tersebut mendefinisikan model tabel `Sdm`, koneksi database SQLite, dependency session database, serta endpoint `/sdm/` untuk menampilkan seluruh data pegawai dari database dalam format JSON.

<p align="center"> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/2_1.png" width="400"><br> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/2_2.png" width="400"> </p>

# 3. Menjalankan Source Code
Aplikasi FastAPI dijalankan menggunakan Uvicorn sebagai ASGI server dengan host `0.0.0.0` dan port `8002` agar dapat diakses melalui browser maupun preview KillerKoda. Setelah server berjalan, endpoint `/sdm/` diuji menggunakan `curl` dan `browser` untuk memastikan API berhasil mengambil data dari database SQLite.

<p align="center"> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/3_2.png" width="400"><br> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/3_3.png" width="400"><br> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/3_1.png" width="400"> </p>

# 4. Tugas
1. Membuat Database dan Tabel Baru
Membuat database baru bernama `inventaris.db` dengan tabel `barang`. Tabel tersebut memiliki beberapa tipe data berbeda seperti `INTEGER`, `CHAR`, `VARCHAR`, `BOOLEAN`, dan `FLOAT`.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_1.png" width="400"> </p>

2. Membuat Script Python untuk Input Data
Membuat file Python `insert_barang.py` yang digunakan untuk memasukkan lima data barang ke database SQLite secara otomatis menggunakan library bawaan `sqlite3`.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_2.png" width="400"> </p>

3. Menjalankan Script dan Verifikasi Database
Script Python kemudian dijalankan untuk menginput data ke tabel `barang`. Setelah proses berhasil, isi tabel diverifikasi menggunakan SQLite dengan perintah `SELECT * FROM barang`.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_3.png" width="400"> </p>

4. Membuat RESTful API Endpoint
Membuat file `tugas_service.py` yang berisi REST API endpoint `/barang/`. Endpoint tersebut digunakan untuk menampilkan seluruh data barang dari database `inventaris.db` dalam format JSON menggunakan FastAPI dan SQLModel.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_4.png" width="400"> </p>

5. Menjalankan REST API
REST API kemudian dijalankan menggunakan Uvicorn pada port `8003` agar tidak bentrok dengan service sebelumnya. Setelah server aktif, endpoint `/barang/` siap diakses.
<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_5.png" width="400"> </p>

6. Pengujian Endpoint REST API
Melakukan pengujian endpoint `/barang/` menggunakan `curl` dan browser. Hasil pengujian menunjukkan bahwa seluruh data pada tabel barang berhasil ditampilkan dalam format JSON, sehingga REST API berjalan dengan baik.
<p align="center"> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_7.png" width="400"><br> 
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/08/SS/4_6.png" width="400"> </p>
