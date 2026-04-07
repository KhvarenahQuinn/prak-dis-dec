# I
## Proses pada Satu Node
### Praktik
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/01.png" width="400">
</p>

1. Gambar di atas menunjukkan proses yang sedang berjalan pada sistem operasi Windows.  
   Pada bagian **Apps** ditampilkan program yang sedang dijalankan oleh pengguna dan memiliki antarmuka (GUI).  
   Sedangkan pada bagian **Background Processes** ditampilkan proses yang berjalan di latar belakang tanpa interaksi langsung dengan pengguna.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/02.png" width="400">
</p>

2. Saat menjalankan aplikasi Calculator, proses program tersebut akan muncul pada kategori **Apps**.  
   Selain itu, sistem juga dapat menjalankan proses tambahan pada **Background Processes** tergantung pada aplikasi yang digunakan.  
   Sebagai contoh, saat menjalankan game Valorant, sistem juga akan menjalankan Vanguard, yaitu sistem anti-cheat yang berjalan bersamaan dengan game tersebut, serta beberapa proses tambahan lainnya.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/03.png" width="400">
</p>

3. Jika aplikasi yang sedang berjalan pada Task Manager diklik kanan, akan muncul berbagai pilihan perintah, salah satunya adalah **End Task**.  
   Perintah **End Task** digunakan untuk menghentikan proses dari aplikasi yang dipilih sehingga aplikasi tersebut berhenti berjalan.  
   Untuk menjalankan kembali aplikasi tersebut, pengguna perlu membuka ulang aplikasi melalui menu Start atau shortcut yang tersedia.


# II
## Komunikasi Antar Proses pada Sistem Terdistribusi
### Tugas
<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_1.png" width="400">
</p>

1. Gambar diatas menunjukan proses instalasi `uv`, yaitu package manager Python modern yang digunakan untuk mengelola versi Python dan virtual environment.  
   Perintah `curl` digunakan untuk mengunduh dan menjalankan script instalasi `uv` secara langsung dari internet.  
   Setelah instalasi selesai, perintah `source` digunakan untuk memuat konfigurasi `uv` ke dalam sesi terminal yang sedang aktif.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_2.png" width="400">
</p>

2. Dibuat direktori `workspace-01` sebagai workspace projek, kemudian masuk ke dalam direktori tersebut.  
   Perintah `uv python list` digunakan untuk melihat daftar versi Python yang tersedia dan dapat digunakan oleh `uv`.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_3.png" width="400">
</p>

3. Versi Python di-*pin* ke `cpython-3.14.3` khusus untuk workspace ini menggunakan perintah `uv python pin`.  
   Jika versi tersebut belum terinstal, `uv` akan mengunduh dan menginstalnya secara otomatis.  
   Setelah berhasil, `uv` membuat file `.python-version` di dalam direktori workspace yang menyimpan informasi versi Python yang digunakan.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_4.png" width="400">
</p>

4. Perintah `ls -la` digunakan untuk memverifikasi bahwa file `.python-version` telah berhasil dibuat di dalam direktori workspace.  
   Perintah `cat .python-version` digunakan untuk melihat isi file tersebut, yang menampilkan versi Python yang telah di-*pin* sebelumnya.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_5.png" width="400">
</p>

5. Virtual environment dibuat menggunakan `uv venv`, kemudian diaktifkan dengan perintah `source .venv/bin/activate`.  
   Paket `strawberry-graphql` beserta dependensinya diinstal menggunakan `uv pip install`.  
   File `schema.py` dibuat langsung menggunakan text editor di Linux, kemudian server GraphQL dijalankan dengan perintah `strawberry server schema`.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_6.png" width="400">
</p>

6. Setelah server berjalan, GraphQL Playground diakses melalui browser pada alamat `http://localhost:8000/graphql` yang kemudian akan diarahkan ke `127.0.0.1/8000/graphql`.
   Query ditulis pada panel sebelah kiri, kemudian tombol **Run** ditekan untuk mengirimkan permintaan ke server.  
   Hasil response dari server ditampilkan pada panel sebelah kanan dalam format JSON, berisi data buku yang telah didefinisikan di dalam `schema.py`.

<p align="center">
  <img src="https://github.com/KhvarenahQuinn/prak-dis-dec/blob/main/02/Picture/2_7.png" width="400">
</p>

7. Kemudian membuka tab terminal baru di sementara server tetap berjalan di terminal pertama.  
   Paket `requests` diinstal menggunakan `uv pip install requests` untuk memungkinkan pengiriman HTTP request dari sisi client.  
   File `client.py` dibuat langsung menggunakan text editor grafis di Linux Mint, kemudian dijalankan dengan perintah `python client.py`.  
   Output yang ditampilkan di terminal merupakan data buku yang diterima dari server melalui komunikasi GraphQL.
   
   `client.py:`
   ```
   import requests
 
    URL = "http://localhost:8000/graphql"
     
    QUERY = """
    {
        books {
            title
            author
        }
    }
    """
     
    def fetch_books():
        response = requests.post(URL, json={"query": QUERY})
     
    if response.status_code == 200:
        data = response.json()
        books = data["data"]["books"]
        print("=" * 35)
        print("  DAFTAR BUKU (GraphQL Client)")
        print("=" * 35)
        for i, book in enumerate(books, 1):
            print(f"{i}. Judul  : {book['title']}")
            print(f"   Penulis: {book['author']}")
            print("-" * 35)
    else:
        print(f"Gagal terhubung. Status: {response.status_code}")
 
    if __name__ == "__main__":
        fetch_books()```
   
