## 1 & 2. Download dan Install Hadoop
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/1_1.png" width="400"><br>
</p>

Proses dimulai dengan mengunduh distribusi Apache Hadoop versi 3.5.0 menggunakan perintah `wget`. File yang diunduh berupa arsip `.tar.gz` yang berisi seluruh komponen Hadoop. Tahap ini bertujuan untuk memperoleh source distribusi yang akan digunakan pada proses instalasi selanjutnya.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/2_1.png" width="400"><br>
</p>

File Hadoop yang telah diunduh kemudian diekstraksi menggunakan perintah `tar`. Hasil ekstraksi ditempatkan pada direktori `~/software/storage/`. Selanjutnya dibuat symbolic link dengan nama `hadoop` yang mengarah ke folder `hadoop-3.5.0`. Penggunaan symbolic link ini bertujuan untuk mempermudah pengelolaan direktori instalasi tanpa perlu mengubah konfigurasi ketika terjadi pergantian versi Hadoop.

## 3. Instalasi pdsh

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/3_1.png" width="400"><br>
</p>

Instalasi pdsh dilakukan menggunakan package manager `apt`. pdsh merupakan tool yang digunakan untuk menjalankan perintah secara paralel pada beberapa node. Setelah instalasi, dilakukan pengujian menggunakan perintah `pdsh -V` untuk memastikan bahwa pdsh telah terpasang dengan benar dan siap digunakan dalam lingkungan Hadoop.

## 4. Konfigurasi Apache Hadoop

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/4_1.png" width="400"><br>
</p>

Pada tahap ini dilakukan konfigurasi beberapa file utama Hadoop, yaitu `hdfs-site.xml`, `core-site.xml`, `mapred-site.xml`, dan `yarn-site.xml`.  
Konfigurasi tersebut mencakup pengaturan jumlah replikasi data, alamat filesystem HDFS, framework MapReduce menggunakan YARN, serta pengaturan resource pada NodeManager. Konfigurasi ini memungkinkan Hadoop berjalan dalam mode pseudo-distributed, di mana seluruh komponen berjalan dalam satu mesin namun tetap mensimulasikan sistem terdistribusi.

## 5. Format NameNode

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/5_1.png" width="400"><br>
</p>

Format NameNode dilakukan menggunakan perintah `hdfs namenode -format`. Proses ini bertujuan untuk menginisialisasi struktur penyimpanan pada Hadoop Distributed File System (HDFS). Proses format hanya dilakukan satu kali, karena jika dilakukan kembali akan menghapus seluruh data yang telah tersimpan.

## 6. Mengatur Konfigurasi JAVA_HOME

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/6_1.png" width="400"><br>
</p>

Pada tahap ini dilakukan pengaturan variabel lingkungan `JAVA_HOME` pada file `hadoop-env.sh`. Konfigurasi ini diperlukan agar Hadoop dapat menemukan lokasi instalasi Java yang digunakan oleh sistem. Kesalahan dalam pengaturan ini dapat menyebabkan Hadoop gagal dijalankan.

## 7. Menjalankan Daemon untuk HDFS
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/7_2.png" width="400"><br>
</p>
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/06/SS/7_1.png" width="400"><br>
</p>

Setelah seluruh konfigurasi selesai, daemon Hadoop dijalankan menggunakan perintah `start-dfs.sh`. Perintah ini akan mengaktifkan komponen utama HDFS, yaitu NameNode, DataNode, dan SecondaryNameNode. Untuk memastikan bahwa seluruh layanan berjalan dengan baik, dilakukan pengecekan menggunakan perintah `jps`.
