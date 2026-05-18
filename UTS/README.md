# 1. Install Podman

Melakukan instalasi Podman pada environment KillerKoda. Setelah proses instalasi selesai, dilakukan pengecekan versi untuk memastikan Podman berhasil terinstall dan siap digunakan sebagai container engine.

<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_1.png" width="400"><br> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_2.png" width="400"><br> </p>

# 2. Membuat Backend Application

Membuat struktur project yang terdiri dari dua backend application dan satu direktori konfigurasi nginx. Pada masing-masing backend dibuat file app.py, requirements.txt, dan Dockerfile.

File app.py digunakan untuk membuat aplikasi Flask sederhana yang menampilkan identitas backend server. File requirements.txt berisi dependency Flask yang diperlukan aplikasi. Sedangkan Dockerfile digunakan untuk membangun image container berbasis Python 3.14.

<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_3.png" width="400"><br> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_4.png" width="400"><br> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_5.png" width="400"><br> </p>

# 3. Build dan Menjalankan Container Backend

Setelah seluruh file backend selesai dibuat, dilakukan proses build image menggunakan Podman pada masing-masing backend application. Setelah image berhasil dibuat, container dijalankan menggunakan port yang berbeda agar setiap backend dapat diakses secara terpisah.

Backend pertama dijalankan pada port 5001, sedangkan backend kedua dijalankan pada port 5002.

<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_6.png" width="400"><br> </p>

# 4. Membuat Konfigurasi Nginx Load Balancer

Membuat file nginx.conf yang digunakan sebagai konfigurasi load balancer. Pada konfigurasi tersebut digunakan directive upstream untuk menghubungkan beberapa backend server sehingga request dari client dapat didistribusikan secara bergantian ke backend yang tersedia.

<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_8.png" width="400"><br> </p>

# 5. Menjalankan Nginx dan Pengujian

Container nginx dijalankan menggunakan Podman dengan memetakan port 8080 pada host ke port 80 pada container nginx. Konfigurasi nginx yang telah dibuat sebelumnya juga di-mount ke dalam container.

Setelah nginx berhasil dijalankan, dilakukan pengujian menggunakan curl localhost:8080 untuk memastikan proses load balancing berjalan dengan baik dan request dapat diteruskan ke backend server.

<p align="center"> <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/UTS/SS/1_7.png" width="400"><br> </p>
