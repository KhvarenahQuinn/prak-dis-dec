# 3.1 Sinkronisasi Waktu
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/1_6.png" width="400"><br>
  Output Chrony
</p>

Proses dimulai dengan instalasi dan konfigurasi Chrony sebagai NTP client. Setelah service diaktifkan, Chrony membaca file konfigurasi  `etc/chrony/chrony.conf` yang telah diubah untuk menggunakan server:
```
server time1.google.com iburst
```
Opsi `iburst` memerintahkan Chrony untuk mengirim beberapa paket sekaligus di awal koneksi agar sinkronisasi pertama berlangsung lebih cepat.

Pada tahap awal, terlihat dari output `chronyc sources` bahwa terdapat dua entri untuk `time1.google.com`. Entri pertama dengan status `^?` menunjukkan bahwa server belum dapat dijangkau atau masih dalam proses resolusi DNS. Entri kedua dengan status `^*` menunjukkan bahwa server ini telah berhasil dipilih sebagai sumber waktu terbaik (current best), dengan nilai:
```
^* time1.google.com    1   8   377   20   +282us[+448us] +/- 15ms
```
Nilai `Stratum 1` menunjukkan bahwa `time1.google.com` terhubung langsung ke sumber waktu referensi (atomic clock), sehingga akurasinya sangat tinggi. Nilai `Reach 377` dalam format oktal berarti 8 polling terakhir semuanya berhasil, menandakan koneksi ke server stabil. Nilai `Poll 8` berarti interval polling saat ini adalah 2⁸ = 256 detik.

Setelah koneksi stabil, Chrony masuk ke tahap pertukaran timestamp. Klien mengirimkan paket UDP ke port 123 milik `time1.google.com`, dan server membalas dengan empat timestamp T1, T2, T3, T4. Dari keempat timestamp tersebut, Chrony menghitung offset dan round-trip time untuk mengetahui seberapa jauh jam lokal berbeda dari waktu server.

Hasil kalkulasi dapat dilihat pada output `chronyc tracking`:
```
System time     : 0.000062578 seconds slow of NTP time
Last offset     : +0.000166656 seconds
RMS offset      : 0.000979190 seconds
```
Nilai ini menunjukkan bahwa jam lokal hanya terlambat sekitar 0,06 milidetik dari waktu NTP, yang tergolong sangat akurat. `Last offset` sebesar +0,000166 detik adalah hasil koreksi terakhir yang diberikan, sedangkan `RMS offset` sebesar 0,000979 detik adalah rata-rata akurasi dari pengukuran-pengukuran sebelumnya.

Selain offset, Chrony juga mengukur frequency error atau laju penyimpangan jam hardware:
```
Frequency       : 517.844 ppm slow
```
Nilai ini berarti jam hardware komputer berjalan 517,844 bagian per juta lebih lambat dari waktu sebenarnya. Chrony menyimpan nilai ini dan menggunakannya untuk mengkompensasi drift secara otomatis, bahkan sebelum polling berikutnya terjadi.

Proses penyesuaian jam dilakukan melalui mekanisme clock slewing, yaitu Chrony mempercepat atau memperlambat jam secara bertahap, bukan dengan melompat langsung ke waktu yang benar. Hal ini penting agar aplikasi yang sensitif terhadap waktu seperti database dan sistem log tidak mengalami gangguan.

Informasi lebih detail tentang kualitas pengukuran dapat dilihat pada output `chronyc sourcestats`:
```
time1.google.com   27  16   39m   +0.005   1.210   +266ns   1088us
```
Nilai `NP = 27` berarti Chrony telah mengumpulkan 27 sampel pengukuran. Dari 27 sampel tersebut, `NR = 16` adalah jumlah run residual dengan tanda yang sama, yang menunjukkan konsistensi pengukuran. `Span = 39m` berarti data telah dikumpulkan selama 39 menit. `Freq Skew = 1.210 ppm` menunjukkan estimasi error pada kalkulasi frekuensi sudah sangat kecil, dan `Std Dev = 1088us` adalah standar deviasi offset antar sampel yang menggambarkan stabilitas koneksi ke server. Semakin lama Chrony berjalan, nilai-nilai ini akan semakin kecil karena Chrony terus belajar dan menyempurnakan model koreksi jamnya.

#3.2 Vector Clock
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/2_1.png" width="400"><br>
  Output vclocks
</p>
1. Proses dimulai dengan inisialisasi tiga buah proses dalam sistem terdistribusi, yaitu P0, P1, dan P2. Masing-masing proses memiliki vector clock awal `[0, 0, 0]`, yang menunjukkan bahwa belum ada event yang terjadi di sistem.
  
  Pada tahap awal, ketika program dijalankan, output:
  
  ```
  P0: [0, 0, 0]
  P1: [0, 0, 0]
  P2: [0, 0, 0]
  ```
  
  menunjukkan bahwa semua proses masih berada pada kondisi awal tanpa aktivitas. Hal ini sesuai dengan konsep vector clock, dimana setiap proses menyimpan array sepanjang jumlah proses dan diinisialisasi dengan nol.
  
  Selanjutnya, Process 0 melakukan satu event lokal dengan memanggil fungsi `increment()`. Akibatnya, elemen index ke-0 pada vector clock bertambah:
  
  ```
  P0: [1, 0, 0]
  ```
  
  Ini menandakan bahwa P0 telah melakukan satu event. Jika dilakukan secara manual, langkah ini sama dengan menambahkan 1 pada posisi proses tersebut.
  
  Tahap berikutnya adalah komunikasi antar proses. P0 mengirim pesan ke P1 menggunakan `send_message()`. Pada saat pengiriman, clock P0 kembali diincrement terlebih dahulu sehingga menjadi:
  
  ```
  P0: [2, 0, 0]
  ```
  
  Pesan yang dikirim membawa nilai vector clock `[2, 0, 0]`. Saat P1 menerima pesan tersebut melalui `receive_message()`, dilakukan proses merge dengan cara mengambil nilai maksimum tiap elemen:
  
  ```
  max([0,0,0], [2,0,0]) = [2,0,0]
  ```
  
  Kemudian P1 melakukan increment lokal:
  
  ```
  P1: [2, 1, 0]
  ```
  
  Hasil ini menunjukkan bahwa P1 mengetahui seluruh event yang terjadi di P0 hingga saat pesan diterima. Jika dibandingkan dengan perhitungan manual, proses ini identik dengan aturan vector clock: merge → increment.
  
  Selanjutnya, Process 2 melakukan event lokal:
  
  ```
  P2: [0, 0, 1]
  ```
  
  Ini menunjukkan bahwa hanya P2 yang mengalami perubahan tanpa interaksi dengan proses lain.
  
  Kemudian P1 mengirim pesan ke P2. Sama seperti sebelumnya, P1 melakukan increment terlebih dahulu:
  
  ```
  P1: [2, 2, 0]
  ```
  
  Pesan `[2,2,0]` dikirim ke P2. Saat diterima, P2 melakukan merge:
  
  ```
  max([0,0,1], [2,2,0]) = [2,2,1]
  ```
  
  Lalu dilakukan increment:
  
  ```
  P2: [2, 2, 2]
  ```
  
  Hal ini menunjukkan bahwa P2 sekarang mengetahui seluruh event yang terjadi di P0 dan P1 sebelum pesan diterima.
  
  Pada tahap akhir dilakukan pengecekan hubungan kausalitas menggunakan fungsi `happens_before` dan `is_concurrent`.
  
  * `VC0 happens before VC1` bernilai **True**, karena:
  
    ```
    [2,0,0] <= [2,2,0]
    ```
  
    dan ada minimal satu elemen yang lebih kecil → artinya event di P0 terjadi sebelum P1.
  
  * `VC1 happens before VC0` bernilai **False**, karena terdapat elemen P1 yang lebih besar dari P0.
  
  * `VC0 is concurrent with VC2` bernilai **False**, karena:
  
    ```
    [2,0,0] <= [2,2,2]
    ```
  
    sehingga VC0 sebenarnya terjadi sebelum VC2, bukan concurrent.
  
  Jika dibandingkan dengan perhitungan manual, seluruh hasil output program ini sudah sesuai dengan algoritma vector clock, yaitu:
  
  1. Increment saat event lokal
  2. Increment sebelum kirim pesan
  3. Merge (max) saat menerima pesan
  4. Increment setelah menerima pesan


2. Modul
   <p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/2_2.png" width="400"><br>
  vector_clocks.py
  </p>
  <p align="center">
    <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/2_3.png" width="400"><br>
    main.py
  </p>
  <p align="center">
    <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/2_4.png" width="400"><br>
    Output
  </p>

# 3.3 Problem Tanpa Sinkronisasi
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/3_1.png" width="400"><br>
  Output
</p>
   
Proses dimulai dengan pembuatan 5 thread, yaitu Task A, B, C, D, dan E menggunakan modul `threading` di Python. Setiap thread menjalankan fungsi `task()` yang memiliki dua aktivitas utama, yaitu mencetak status mulai (starting), kemudian melakukan `sleep` selama 5 detik untuk mensimulasikan operasi I/O.

Pada tahap awal, ketika semua thread dijalankan menggunakan `start()`, kelima thread langsung dieksekusi secara **bersamaan (concurrent)** oleh scheduler sistem operasi. Hal ini terlihat dari output:

```
Task A starting...
Task B starting...
Task C starting...
Task D starting...
Task E starting...
```

Namun, urutan kemunculan output tersebut **tidak selalu sama** di setiap eksekusi.

Pada percobaan kedua dan ketiga, urutan berubah menjadi:

```
Task A starting...
Task C starting...
Task B starting...
...
```

atau bahkan:

```
Task A starting...
Task E starting...
Task D starting...
...
```

Perbedaan ini terjadi karena thread berjalan secara **asynchronous**, sehingga CPU scheduler menentukan sendiri thread mana yang dijalankan lebih dulu. Tidak ada jaminan urutan eksekusi seperti pada program sekuensial.

---

Setelah semua thread masuk ke kondisi `sleep(5)`, masing-masing thread akan “menunggu” selama 5 detik. Karena delay yang digunakan sama, maka proses selesai hampir bersamaan. Namun, urutan output selesai tetap bisa berbeda, seperti:

```
Task A finished after 5s.
Task E finished after 5s.
Task D finished after 5s.
Task C finished after 5s.
Task B finished after 5s.
```

Hal ini kembali disebabkan oleh **race condition pada output**, dimana beberapa thread mencoba mengakses resource yang sama (stdout/print) tanpa sinkronisasi.

---

Pada tahap akhir, fungsi `join()` memastikan bahwa program utama menunggu semua thread selesai sebelum melanjutkan ke:

```
All tasks completed.
```

Tanpa `join()`, ada kemungkinan program utama selesai lebih dulu sebelum thread-thread tersebut selesai dieksekusi.


**Penyebab Perbedaan Output**

Perbedaan output terjadi karena beberapa faktor utama:

1. **Thread Scheduling oleh OS**
   Sistem operasi menggunakan algoritma penjadwalan (scheduler) untuk menentukan urutan eksekusi thread. Urutan ini bisa berubah setiap kali program dijalankan.

2. **Tidak Ada Sinkronisasi**
   Tidak ada mekanisme seperti `lock`, `semaphore`, atau `mutex` untuk mengatur akses ke resource bersama (misalnya print). Akibatnya, eksekusi menjadi tidak terprediksi.

3. **Eksekusi Concurrent, Bukan Sequential**
   Berbeda dengan program biasa, thread tidak dijalankan satu per satu, melainkan bisa berjalan “bersamaan” (interleaving execution).

## 3.3.1 Data Race / Race Conditions
1. Penjelasan Visual race 01
   <p align="center">
    <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/Foto1.jpeg" width="400"><br>
    Output race 01 dan 02
  </p>
3. Race 02
  <p align="center">
    <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/3_2.png" width="400"><br>
    Output race 01 dan 02
  </p>
  Proses dimulai dengan inisialisasi variabel global `balance = 100` yang digunakan sebagai saldo bersama, serta objek `counter_lock` yang dibuat menggunakan `threading.Lock()` sebagai mekanisme pengaman akses data. Dua thread, yaitu `t1` dan `t2`, kemudian dijalankan secara bersamaan dan masing-masing mengeksekusi fungsi `withdraw(80)`.

Pada saat eksekusi dimulai, kedua thread mencoba mengakses fungsi `withdraw()`. Namun, karena terdapat blok `with counter_lock:`, hanya satu thread yang dapat masuk ke bagian kode tersebut pada satu waktu, sedangkan thread lainnya harus menunggu hingga lock dilepaskan.

Thread yang pertama memperoleh lock akan menjalankan seluruh proses di dalam critical section, dimulai dari pengecekan kondisi `balance >= amount`, kemudian delay menggunakan `time.sleep(0.01)`, hingga akhirnya melakukan pengurangan saldo. Misalnya, thread pertama berhasil mengakses dan mengurangi saldo dari 100 menjadi 20, lalu mencetak output:

```
Mengambil sejumlah Rp 80. Saldo: Rp 20
```

Setelah proses tersebut selesai, lock dilepaskan sehingga thread kedua baru dapat melanjutkan eksekusi.

Ketika thread kedua masuk ke dalam critical section, kondisi saldo sudah berubah menjadi 20. Pada tahap pengecekan `balance >= amount`, kondisi tersebut tidak terpenuhi karena 20 < 80, sehingga thread kedua tidak melakukan pengurangan saldo dan tidak menghasilkan output tambahan.

Hal ini menunjukkan bahwa meskipun thread dijalankan secara concurrent, eksekusi pada bagian penting program tetap berlangsung secara teratur.

Race condition tidak terjadi karena seluruh proses penting, yaitu pengecekan saldo, delay, dan pengurangan saldo, dibungkus dalam satu mekanisme lock yang sama. Akibatnya, tidak ada dua thread yang dapat membaca atau memodifikasi nilai `balance` secara bersamaan. Thread kedua selalu melihat nilai terbaru yang telah diperbarui oleh thread pertama.

Dengan demikian, kondisi dimana kedua thread membaca nilai saldo yang sama sebelum terjadi perubahan tidak pernah terjadi. Operasi yang sebelumnya terpisah (cek → delay → update) kini menjadi satu kesatuan yang tidak dapat disela oleh thread lain, sehingga menghasilkan data yang konsisten dan sesuai dengan logika sistem.

## 3.3.2 Deadlock
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/Foto2.jpeg" width="400"><br>
  Output Deadlock 01
</p>
1. Penjelasan visual deadlock 01

2. Deadlock 02
   <p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/4_2.png" width="400"><br>
  Output Deadlock 02
</p>
Proses dimulai dengan pembuatan dua buah lock bersama, yaitu `lock_a` dan `lock_b`, yang akan digunakan oleh dua thread (`thread1` dan `thread2`). Masing-masing thread mencoba mengakses kedua lock tersebut, namun dengan urutan yang berbeda. Thread 1 mengambil `lock_a` terlebih dahulu, sedangkan Thread 2 mengambil `lock_b` terlebih dahulu.

Pada tahap awal eksekusi, kedua thread dijalankan secara concurrent. Thread 1 berhasil memperoleh `lock_a` dan mencetak:

```
Thread 1: Acquired Lock A
```

Sementara itu, Thread 2 memperoleh `lock_b` dan mencetak:

```
Thread 2: Acquired Lock B
```

Setelah itu, kedua thread melakukan delay selama 1 detik menggunakan `time.sleep(1)`, yang mensimulasikan proses kerja dan meningkatkan kemungkinan konflik seperti pada kasus deadlock sebelumnya.

Selanjutnya, masing-masing thread mencoba memperoleh lock kedua:

* Thread 1 ingin mendapatkan `lock_b`
* Thread 2 ingin mendapatkan `lock_a`

Namun, berbeda dengan kasus deadlock sebelumnya, pada program ini terdapat dua mekanisme penting yang mencegah terjadinya deadlock.

Pertama, penggunaan parameter `timeout=5` pada fungsi `acquire()` membuat thread tidak menunggu selamanya jika lock yang diinginkan tidak tersedia. Jika dalam waktu 5 detik lock tidak berhasil diperoleh, maka thread akan berhenti mencoba dan melanjutkan eksekusi.

Kedua, terdapat blok `finally` yang memastikan bahwa setiap lock yang sudah diperoleh akan selalu dilepaskan (`release()`), bahkan jika thread belum berhasil mendapatkan lock berikutnya. Hal ini menyebabkan resource tidak “ditahan” terlalu lama oleh satu thread.

Sebagai contoh alur eksekusi:

* Thread 1 mengambil `lock_a`, lalu melepaskannya sebelum mencoba `lock_b`
* Thread 2 mengambil `lock_b`, lalu melepaskannya sebelum mencoba `lock_a`

Karena kedua thread **melepaskan lock pertama sebelum mencoba lock kedua**, maka kondisi saling menunggu tidak pernah terjadi.

Deadlock tidak terjadi karena tidak terpenuhinya kondisi utama deadlock, yaitu **circular wait** (saling menunggu secara melingkar). Pada kasus sebelumnya, Thread 1 memegang `lock_a` sambil menunggu `lock_b`, dan Thread 2 memegang `lock_b` sambil menunggu `lock_a`. Namun pada program ini, lock pertama sudah dilepaskan sebelum mencoba mengambil lock kedua, sehingga tidak ada siklus penahanan resource.

Selain itu, adanya timeout juga memastikan bahwa thread tidak akan terjebak dalam kondisi menunggu tanpa batas (infinite waiting).

# 3.4 Algoritma Raft
1. Penjelasan Visualisasi Raft
   <p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/Foto3.jpeg" width="400"><br>
</p>
2. Modul
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/5_2.png" width="400"><br>
  raft_module.py
</p>
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/5_3.png" width="400"><br>
  main.py
</p>
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/03/SS/5_4.png" width="400"><br>
  output main
</p>
