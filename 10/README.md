# Praktikum Modul 10 - Data Terdistribusi

# 1. Instalasi


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/1.png" width="400">
</p>

Melakukan pengunduhan YugabyteDB versi LTS. File instalasi diunduh ke direktori yang telah disiapkan `/software`.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/2.png" width="400">
</p>

Setelah proses unduhan selesai, file arsip diekstrak sehingga seluruh komponen YugabyteDB dapat digunakan. Selanjutnya dilakukan pengecekan direktori hasil ekstraksi untuk memastikan proses berjalan dengan baik.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/3.png" width="400">
</p>

Pada langkah ini dibuat symbolic link menuju direktori instalasi YugabyteDB. Penggunaan symbolic link memudahkan akses ke direktori tanpa perlu menggunakan nama versi secara lengkap.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/4.png" width="400">
</p>

Script `post_install.sh` dijalankan untuk melakukan konfigurasi awal dan pengecekan dependensi yang dibutuhkan oleh YugabyteDB. Hasil pengujian menunjukkan seluruh komponen berhasil diverifikasi dengan status berhasil.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/5.png" width="400">
</p>

Konfigurasi `ulimit` dilakukan sesuai kebutuhan YugabyteDB agar sistem dapat menangani jumlah file dan proses yang lebih besar selama menjalankan cluster.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/6.png" width="400">
</p>

Environment variable kemudian dibuat dan diaktifkan sehingga perintah-perintah YugabyteDB dapat dijalankan langsung dari terminal tanpa perlu menuliskan path secara manual.

# 2. Membuat Cluster
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/7.png" width="400">
</p>

Node pertama dijalankan sebagai node awal cluster dengan direktori penyimpanan data pada `node1`. Node ini akan menjadi titik awal bagi node lainnya untuk bergabung ke dalam cluster.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/8.png" width="400">
</p>

Node kedua dijalankan menggunakan parameter `join` yang menghubungkannya ke node pertama. Dengan demikian node kedua berhasil bergabung ke dalam cluster yang telah dibuat.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/9.png" width="400">
</p>

Node ketiga dijalankan dan dikonfigurasi untuk bergabung dengan cluster yang sama. Setelah langkah ini selesai, cluster telah terdiri dari tiga node yang saling terhubung.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/10.png" width="400">
</p>

Konfigurasi *data placement* dilakukan untuk mengatur distribusi data dan replika berdasarkan zona yang telah ditentukan. Pengaturan ini membantu meningkatkan ketersediaan data pada cluster.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/1.png" width="400">
</p>

Pada tahap ini dilakukan verifikasi bahwa layanan YugabyteDB telah aktif dan berjalan pada port yang digunakan oleh cluster.


# 3. Sharding
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/12.png" width="400">
</p>

Tabel `user_range` dibuat menggunakan metode *range sharding* dan kemudian diisi dengan beberapa data contoh. Data yang dimasukkan berhasil disimpan dan dapat ditampilkan kembali menggunakan perintah SELECT.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/13.png" width="400">
</p>

Tabel `users` dibuat menggunakan fitur `SPLIT AT VALUES` sehingga data dibagi ke dalam beberapa tablet. Setelah itu dilakukan pengecekan jumlah tablet untuk memastikan proses pembagian data berhasil dilakukan.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/14_1.png" width="400">
</p>

Pengujian pertama menggunakan `EXPLAIN ANALYZE` dilakukan pada query yang mengambil seluruh data dari tabel `user_range`. Hasilnya menunjukkan bahwa seluruh baris pada tabel dibaca karena query meminta semua data yang tersedia.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/14_2.png" width="400">
</p>

Pengujian berikutnya dilakukan pada query pencarian berdasarkan nilai `id` tertentu. Database menggunakan Index Scan dan hanya membaca satu baris yang sesuai dengan kondisi pencarian.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/14_3.png" width="400">
</p>

Pengujian terakhir pada range sharding menggunakan query rentang nilai. Hasil menunjukkan bahwa hanya data yang berada dalam rentang tersebut yang dipindai sehingga proses pencarian menjadi lebih efisien.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/15.png" width="400">
</p>

Tabel `user_hash` dibuat menggunakan metode *hash sharding* dan diisi dengan data contoh. Karena distribusi data menggunakan fungsi hash, urutan data yang ditampilkan tidak selalu mengikuti urutan nilai `id`.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/16_1.png" width="400">
</p>

Pengujian pertama pada hash sharding dilakukan dengan mengambil seluruh data dari tabel. Hasilnya menunjukkan bahwa seluruh data tetap dibaca untuk memenuhi permintaan query.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/16_2.png" width="400">
</p>

Pengujian kedua menggunakan pencarian berdasarkan satu nilai `id`. Database dapat langsung menemukan data yang dicari sehingga hanya satu baris yang dibaca.


<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/10/SS/16_3.png" width="400">
</p>

Pada pengujian rentang nilai, database tetap melakukan pemindaian terhadap seluruh data sebelum menerapkan filter yang diminta. Hal ini menunjukkan bahwa hash sharding kurang optimal untuk query berbasis rentang.


# 4. Shutdown YugabyteDB

Setelah seluruh pengujian selesai dilakukan, setiap node pada cluster dihentikan secara bertahap menggunakan perintah `yugabyted stop`. Langkah ini memastikan seluruh layanan YugabyteDB berhenti dengan aman dan tidak meninggalkan proses yang masih berjalan.

# 5. Tugas
https://www.credential.net/9bb4b996-1ed2-46cc-9a54-8b88085b006d#acc.WcvyADz6
<p align="center">
  <img src="https://api.accredible.com/v1/frontend/credential_website_embed_image/badge/184189990">
</p>


