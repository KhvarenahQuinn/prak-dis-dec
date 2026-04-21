#4.1 Streaming Replication Menggunakan PostgreSQL
##4.1.1 Prasyarat
1. Instalasi Docker
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/04/SS/1_1.png" width="400"><br>
</p>

```
sudo apt update
sudo apt install -y docker.io
```

Perintah apt update digunakan untuk menyegarkan daftar paket dari repositori agar sistem mengenali versi terbaru yang tersedia. Setelah itu dilakukan instalasi docker.io, yang merupakan paket Docker pada distribusi seperti Kali Linux. Penggunaan nama docker.io diperlukan karena nama docker sendiri sudah digunakan oleh paket lain yang tidak berkaitan dengan container.

Verifikasi instalasi:
```
docker --version
```
Output versi yang ditampilkan menunjukkan bahwa Docker telah terpasang dan siap digunakan pada sistem.

2. Instalasi Docker Compose
```
sudo apt install -y docker-compose
```
Docker Compose dipasang sebagai tool tambahan yang berfungsi untuk menjalankan beberapa container secara bersamaan menggunakan satu file konfigurasi. Dengan adanya Compose, pengelolaan service yang saling terhubung menjadi lebih sederhana dibanding menjalankannya satu per satu secara manual.

3. Mengaktifkan Docker Daemon
```
sudo systemctl start docker
sudo systemctl enable docker
```
Docker membutuhkan service utama berupa daemon (dockerd) yang berjalan di background. Perintah start digunakan untuk menjalankannya secara langsung, sedangkan enable memastikan service tersebut otomatis aktif setiap kali sistem dinyalakan.

Verifikasi status daemon:
```
sudo systemctl status docker
```
Jika status menunjukkan active (running), berarti daemon sudah berjalan dengan normal dan siap menerima perintah dari Docker client.

4. Verifikasi Instalasi
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/04/SS/1_3.png" width="400"><br>
</p>

```
sudo docker run hello-world
```
Perintah ini digunakan sebagai pengujian sederhana untuk memastikan seluruh komponen Docker bekerja dengan baik. Jika image belum tersedia secara lokal, Docker akan mengunduhnya terlebih dahulu, kemudian menjalankannya.

Munculnya output “Hello from Docker!” menandakan bahwa proses instalasi berhasil, termasuk koneksi ke Docker Hub serta komunikasi antara client dan daemon.

##4.1.2 Struktur Direktori dan File

File yang diperlukan adalah sebagai berikut:
```
src/
├── 00_init.sql
├── docker-compose.yaml
└── env.sh

1 directory, 3 files
```
Struktur ini dibuat sederhana agar seluruh konfigurasi berada dalam satu tempat. Direktori src nantinya juga akan berpengaruh pada penamaan container yang dibuat oleh Docker Compose, sehingga setiap container dapat dengan mudah dikenali berdasarkan asal project-nya.

Berikut adalah penjelasan dari masing-masing file tersebut:

1. 00_init.sql
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/04/SS/1_4.png" width="400"><br>
</p>
File ini berisi perintah SQL yang dijalankan secara otomatis saat PostgreSQL pada sisi primary pertama kali diinisialisasi.
```
CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'replicator_password';
SELECT pg_create_physical_replication_slot('replication_slot');
```

Perintah pertama digunakan untuk membuat user khusus bernama replicator yang memiliki hak REPLICATION. User ini tidak digunakan untuk aktivitas database biasa, melainkan hanya untuk proses sinkronisasi data antara primary dan replica.

Perintah kedua membuat sebuah replication slot. Slot ini berfungsi menjaga agar data WAL (Write-Ahead Log) tidak dihapus sebelum sempat dikirim ke replica. Dengan demikian, primary tetap menyimpan log yang diperlukan sehingga replica dapat tetap mengikuti perubahan data tanpa kehilangan informasi.

2. docker-compose.yaml
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/04/SS/1_5.png" width="400"><br>
</p>
File ini merupakan pusat konfigurasi yang digunakan untuk menjalankan beberapa container sekaligus menggunakan Docker Compose.

```
x-postgres-common:
 &postgres-common
 image: postgres:18.3-alpine3.23
 user: postgres
 restart: always
 healthcheck:
  test: 'pg_isready -U zuser --dbname=zdb'
  interval: 10s
  timeout: 5s
  retries: 5
services:
 postgres_primary:
  <<: *postgres-common
  ports:
  - 5432:5432
  environment:
   POSTGRES_USER: zuser
   POSTGRES_DB: zdb
   POSTGRES_PASSWORD: zpass
   POSTGRES_HOST_AUTH_METHOD: "scram-sha-256\nhost replication replicator 0.0.0.0/0 md5"
   POSTGRES_INITDB_ARGS: "--auth-host=scram-sha-256"
  command: |
   postgres
   -c wal_level=replica
   -c hot_standby=on
   -c max_wal_senders=10
   -c max_replication_slots=10
   -c hot_standby_feedback=on
  volumes:
   - ./00_init.sql:/docker-entrypoint-initdb.d/00_init.sql

 postgres_replica:
  <<: *postgres-common
  ports:
  - 5433:5432
  environment:
   PGUSER: replicator
   PGPASSWORD: replicator_password
   PGDATA: /var/lib/postgresql/18/docker
  command: |
   bash -c "
   until pg_basebackup --pgdata=/var/lib/postgresql/18/docker -R \
   --slot=replication_slot --host=postgres_primary --port=5432 -X stream; do
    echo 'pg_basebackup failed. Retrying in 5 seconds ...'
    sleep 5
   done
   echo 'Backup done, starting replica...'
   chmod 0700 /var/lib/postgresql/18/docker
   postgres
   "
  depends_on:
   - postgres_primary
```

Konfigurasi ini mendefinisikan dua service, yaitu primary dan replica. Untuk menghindari pengulangan konfigurasi yang sama, digunakan YAML anchor (x-postgres-common) sebagai template dasar yang berisi pengaturan umum seperti image, user, restart policy, dan healthcheck.

Service postgres_primary berperan sebagai sumber utama data. Beberapa parameter diaktifkan untuk mendukung proses replikasi, seperti wal_level=replica dan max_wal_senders. Selain itu, file 00_init.sql dipasang ke direktori khusus agar dijalankan otomatis saat inisialisasi pertama.

Service postgres_replica bertugas sebagai salinan dari primary. Proses sinkronisasi awal dilakukan menggunakan pg_basebackup, yang menyalin seluruh data dari primary sekaligus menghubungkannya dengan replication slot yang telah dibuat. Perintah dijalankan dalam bentuk loop agar tetap mencoba terhubung jika primary belum siap.

3. env.sh
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/04/SS/1_6.png" width="400"><br>
</p>
File ini berisi kumpulan alias yang digunakan untuk mempermudah interaksi dengan Docker.

```
alias dcu="sudo docker-compose up -d"
alias dcd="sudo docker-compose down"
alias dps="sudo docker ps"
alias der="sudo docker exec -it <nama-direktori>-postgres_replica-1 bash"
alias dep="sudo docker exec -it <nama-direktori>-postgres_primary-1 bash"
alias dlr="sudo docker logs <nama-direktori>-postgres_replica-1"
alias dlp="sudo docker logs <nama-direktori>-postgres_primary-1"
```
Alias ini bersifat opsional, namun cukup membantu karena memperpendek penulisan perintah yang sering digunakan. Nama direktori perlu disesuaikan karena akan menjadi bagian dari nama container yang dibuat oleh Docker Compose. Sebagai contoh, jika direktori bernama src, maka nama container akan diawali dengan src-.

4.1.3 Menjalankan docker-compose
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/04/SS/1_7.png" width="400"><br>
</p>
Catatan: command yang dituliskan di sini memanfaatkan alias yang telah didefinisikan di file env.sh.

```
ls -la
source env.sh
dcu
```
Perintah ls -la digunakan untuk memastikan bahwa seluruh file yang dibutuhkan telah berada pada direktori kerja. Setelah itu, source env.sh dijalankan agar seluruh alias yang telah didefinisikan dapat digunakan pada sesi terminal saat ini.

Perintah dcu merupakan alias dari docker-compose up -d, yang berfungsi untuk menjalankan seluruh service yang didefinisikan pada file docker-compose.yaml dalam mode detached (berjalan di background).

Jika image PostgreSQL belum tersedia di lokal, Docker akan secara otomatis mengunduhnya dari Docker Hub sebelum container dijalankan. Proses ini terlihat dari output “Pull complete” pada masing-masing layer image.

Output yang muncul menunjukkan bahwa Docker berhasil:

  * mengunduh image yang diperlukan
  * membuat network internal (src_default)
  * menjalankan container untuk primary dan replica
  * dps

Perintah ini digunakan untuk melihat daftar container yang sedang berjalan.

Hasil yang diperoleh:
```
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS                                 PORTS                                         NAMES
d14555af781f   postgres:18.3-alpine3.23   "docker-entrypoint.s…"   About a minute ago   Up About a minute (health: starting)   0.0.0.0:5433->5432/tcp                      src-postgres_replica-1
49fdb637a94b   postgres:18.3-alpine3.23   "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)            0.0.0.0:5432->5432/tcp                      src-postgres_primary-1
```
Dari hasil tersebut terlihat bahwa kedua container telah berhasil dijalankan.

Status pada kolom STATUS memberikan informasi kondisi container:

healthy menandakan bahwa service PostgreSQL di dalam container sudah siap menerima koneksi
health: starting menunjukkan bahwa container masih dalam proses inisialisasi

Pada kondisi ini, container primary sudah berada pada status healthy, sedangkan replica masih dalam tahap awal sinkronisasi. Hal ini wajar karena replica perlu melakukan proses pg_basebackup terlebih dahulu sebelum benar-benar siap digunakan.

Jika terjadi masalah atau container tidak mencapai status healthy, proses dapat ditelusuri melalui log menggunakan perintah:
```
dlr
```
Log ini menampilkan detail aktivitas pada container replica, termasuk proses pengambilan data dari primary.

Jika kedua container telah berjalan dengan normal, maka pada tahap ini telah tersedia dua instance PostgreSQL:
```
src-postgres_primary-1
src-postgres_replica-1
```
Instance src-postgres_primary-1 berfungsi sebagai server utama yang menangani seluruh operasi baca dan tulis data. Sementara itu, src-postgres_replica-1 berperan sebagai standby server yang akan menerima aliran data dari primary secara terus-menerus.

Setiap perubahan data yang terjadi pada primary akan direkam dalam bentuk WAL dan kemudian dikirim ke replica. Dengan mekanisme ini, data pada kedua server tetap konsisten tanpa perlu intervensi manual.
