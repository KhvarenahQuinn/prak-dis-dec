#Tugas 0

1. a. SaaS
   Contoh layanan IaaS adalah Amazon Web Services (AWS) dari Amazon.
   AWS menyediakan infrastruktur virtual seperti server, storage, dan jaringan yang dapat digunakan sesuai kebutuhan.
   Service yang diberikan:
   
   * Virtual machine (EC2)
   * Cloud storage (S3)
   * Virtual networking
   * Load balancer
   * Database server
   * Firewall dan keamanan cloud
   Pada SaaS, pengguna hanya menggunakan aplikasi yang telah disediakan tanpa perlu mengelola server, sistem operasi, maupun infrastruktur.

   b. PaaS
   Contoh layanan PaaS adalah Heroku milik Salesforce.
   Heroku menyediakan platform untuk menjalankan aplikasi tanpa perlu mengatur server secara manual.

   Service yang diberikan:
   * Environment untuk deploy aplikasi
   * Runtime berbagai bahasa pemrograman
   * Database add-ons
   * Auto deployment dari GitHub
   * Monitoring aplikasi
   Pada PaaS, developer hanya fokus pada pengembangan aplikasi sedangkan pengelolaan server, middleware, dan deployment ditangani oleh platform.
   
   c. IaaS
   Contoh layanan IaaS adalah Amazon Web Services (AWS) dari Amazon.
   AWS menyediakan infrastruktur virtual seperti server, storage, dan jaringan yang dapat digunakan sesuai kebutuhan.
   Service yang diberikan:
   * Virtual machine (EC2)
   * Cloud storage (S3)
   * Virtual networking
   * Load balancer
   * Database server
   * Firewall dan keamanan cloud
   Pada model IaaS, pengguna memiliki kontrol lebih besar terhadap sistem operasi, konfigurasi server, dan aplikasi yang dijalankan.
     
2. a. SaaS
   `Pengguna -> Browser / Mobile App -> Aplikasi Cloud SaaS -> Server & Database Provider`
   
   Pada arsitektur SaaS, pengguna hanya berinteraksi dengan aplikasi melalui browser atau aplikasi mobile. Seluruh pengelolaan aplikasi, database, server, dan infrastruktur dilakukan oleh provider cloud. Pengguna tidak perlu melakukan instalasi maupun konfigurasi sistem secara langsung.
   
   b. PaaS
   `Developer -> Platform PaaS (Runtime, Middleware, Database) -> Cloud Infrastructure (Server, Storage, Network)`

   Pada arsitektur PaaS, developer fokus pada pembuatan dan deployment aplikasi. Platform menyediakan environment pengembangan seperti runtime, framework, database, dan middleware. Infrastruktur server dan jaringan tetap dikelola oleh provider cloud.
   
   c. IaaS
   User / Administrator -> Virtual Machine / Operating System -> Virtualization Layer -> Physical Infrastructure (Server, Storage, Network)
   
   Pada arsitektur IaaS, provider menyediakan infrastruktur virtual berupa server, storage, dan jaringan. Pengguna memiliki kontrol terhadap sistem operasi, konfigurasi server, serta aplikasi yang dijalankan di atas infrastruktur tersebut.
   
   
# Flask
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/1_1.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/1_2.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/1_3.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/1_4.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/1_5.png" width="400"><br>
</p>

Tahap pertama dimulai dengan melakukan clone repository Flask dari GitHub menggunakan git clone. Repository tersebut berisi source code framework Flask beserta beberapa contoh aplikasi yang dapat digunakan untuk praktikum. Setelah proses clone selesai, direktori `examples/tutorial` di-copy ke folder baru bernama `flask-app` agar project tutorial dapat digunakan dan dimodifikasi secara terpisah tanpa mengubah source asli repository Flask.

Setelah masuk ke direktori flask-app, dilakukan pembuatan virtual environment menggunakan `Python 3.14.4` melalui `uv`. Virtual environment digunakan agar dependency project terpisah dari package bawaan sistem sehingga lingkungan pengembangan menjadi lebih rapi dan terisolasi.

Selanjutnya dependency aplikasi diinstall menggunakan perintah uv pip install `-e .`. Perintah ini akan membaca konfigurasi project dan menginstall seluruh package yang dibutuhkan agar aplikasi Flask dapat dijalankan.

Sebelum aplikasi dijalankan, database terlebih dahulu diinisialisasi menggunakan perintah `flask --app flaskr init-db`. Proses ini akan membuat database SQLite yang digunakan oleh aplikasi tutorial Flask untuk menyimpan data user dan posting.

Aplikasi kemudian dijalankan menggunakan perintah `flask --app flaskr run` dengan tambahan host `0.0.0.0` agar dapat diakses melalui browser pada environment KillerKoda. Setelah server berjalan, aplikasi dapat diakses melalui browser untuk melakukan registrasi akun dan login ke sistem.

#Docker
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/2_1.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/2_2.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/2_3.png" width="400"><br>
</p>

Tahap berikutnya adalah membuat aplikasi Flask menjadi container menggunakan Docker. Langkah awal dilakukan dengan membuat file Dockerfile menggunakan editor nano. File ini berisi instruksi untuk membangun image container, mulai dari menentukan base image Python 3.14, membuat direktori `/app`, menyalin seluruh source code aplikasi, menginstall dependency aplikasi, hingga menjalankan inisialisasi database Flask.

Pada Dockerfile juga ditentukan bahwa aplikasi Flask akan berjalan pada `port 5000` menggunakan flask run dengan host `0.0.0.0` agar dapat menerima koneksi dari luar container.

Setelah file Dockerfile selesai dibuat, service Docker dijalankan terlebih dahulu menggunakan `systemctl start docker`. Selanjutnya dilakukan proses build image menggunakan perintah docker build. Pada tahap ini Docker akan membaca isi Dockerfile dan membuat image container bernama `flaskr:1.0.0`.

Jika build berhasil, container kemudian dijalankan menggunakan docker run dengan pemetaan port `5001:5000`. Artinya port 5000 di dalam container dipetakan ke port `5001` pada host KillerKoda sehingga aplikasi dapat diakses melalui browser menggunakan port `5001`.

#Podman
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/3_1.png" width="400"><br>
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/07/SS/3_2.png" width="400"><br>
</p>

Selain Docker, aplikasi juga dapat dijalankan menggunakan Podman. Perbedaannya, Podman tidak memerlukan daemon seperti Docker sehingga service dockerd tidak perlu dijalankan. Namun file Dockerfile yang sebelumnya dibuat tetap dapat digunakan karena Podman kompatibel dengan format Dockerfile.

Proses dimulai dengan membangun image menggunakan perintah podman build. Podman akan membaca isi Dockerfile, mengunduh image Python yang dibutuhkan, menyalin source code aplikasi, menginstall dependency Flask, dan menjalankan inisialisasi database seperti pada proses Docker sebelumnya.

Setelah image berhasil dibuat, container dijalankan menggunakan podman run dengan pemetaan port 5001:5000. Dengan konfigurasi tersebut, aplikasi Flask yang berjalan di dalam container Podman dapat diakses melalui browser KillerKoda menggunakan port `5001`.
