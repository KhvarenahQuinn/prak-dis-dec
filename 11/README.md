# Tugas 1
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/11/SS/1_1.png" width="400">
</p>
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/11/SS/1_2.png" width="400">
</p>
Script yang dijalankan menggunakan algoritma SHA-256 dari library `hashlib` untuk menghasilkan hash dari dua buah string, yaitu **"UTDI"** dan **"Fakultas Teknologi Informasi"**. Hasil yang diperoleh berupa deretan karakter heksadesimal yang berbeda untuk masing-masing input.

Output yang dihasilkan:

```
SHA-256 Hash: e26d7d497c24e33ca2027102df133770c250847dd64280218fb7cb38a5127e64

SHA-256 Hash: 55763386b23c14645c89874760dd19a42023e7b67b080e4d0d142ac82ef608fa
```

Nilai hash yang dihasilkan memiliki panjang yang sama meskipun panjang teks masukan berbeda. Selain itu, setiap input menghasilkan hash yang unik sehingga perubahan sekecil apa pun pada data akan menghasilkan nilai hash yang berbeda. Karena sifatnya yang satu arah (*one-way function*), nilai hash tidak dapat digunakan untuk memperoleh kembali data aslinya.

SHA-256 menghasilkan representasi unik dari suatu data dalam bentuk hash dengan panjang yang tetap. Setiap perubahan pada data akan menghasilkan hash yang berbeda sehingga algoritma ini sangat cocok digunakan untuk memverifikasi integritas data dan menjadi salah satu komponen utama dalam teknologi blockchain.



# Tugas 2 
<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/11/SS/1_3.png" width="400">
</p>
## CoreBlockchain.py

File ini berisi definisi kelas `CoreBlockchain` yang digunakan untuk merepresentasikan sebuah blok dalam blockchain.

Method `__init__()` digunakan untuk menginisialisasi atribut yang dimiliki setiap blok, yaitu:

* `idx` sebagai nomor urut blok.
* `timestamp` sebagai waktu pembuatan blok.
* `data` sebagai informasi atau transaksi yang disimpan.
* `previous_hash` sebagai hash dari blok sebelumnya.
* `nonce` sebagai angka yang umumnya digunakan dalam proses kriptografi dan mining.
* `hash` sebagai identitas unik blok yang dihasilkan melalui proses hashing.

Method `count_hash()` bertugas menggabungkan seluruh informasi dalam blok menjadi satu string, kemudian menghasilkan nilai hash menggunakan algoritma SHA-256. Hash yang dihasilkan akan menjadi identitas unik bagi blok tersebut.

## UtdiBlockchain.py

File ini digunakan untuk mengelola rangkaian blok sehingga membentuk sebuah blockchain.

Pada method `__init__()`, dibuat sebuah list bernama `chain` yang berfungsi untuk menyimpan seluruh blok. Setelah itu dipanggil method `init_genesis_block()` untuk membuat Genesis Block sebagai blok pertama dalam blockchain.

Method `init_genesis_block()` membuat blok awal dengan indeks `0`, data `"Blok Genesis / Awal"`, dan nilai `previous_hash` sebesar `"0"` karena belum ada blok sebelumnya.

Method `add_block()` digunakan untuk menambahkan blok baru ke dalam blockchain. Data yang diberikan akan disimpan pada blok baru dan nilai `previous_hash` diisi menggunakan hash dari blok terakhir pada rantai blockchain.

## blockchain_demo_01.py

File ini digunakan untuk menguji implementasi blockchain yang telah dibuat.

Program pertama kali membuat objek blockchain menggunakan kelas `UtdiBlockchain`. Setelah itu ditambahkan dua data transaksi:

* Bambang transfer ke Zaky sebesar 5 koin.
* Zaky transfer ke Didik sebesar 10 koin.

Selanjutnya dilakukan perulangan terhadap seluruh blok dalam blockchain untuk menampilkan informasi berupa indeks blok, data yang tersimpan, hash blok sebelumnya, dan hash blok saat ini.

Berdasarkan output yang dihasilkan, blockchain berhasil membentuk tiga blok yang saling terhubung melalui mekanisme hash. Setiap blok menyimpan hash dari blok sebelumnya sehingga membentuk rantai data yang saling terkait. Mekanisme ini menjaga integritas data karena perubahan pada satu blok akan memengaruhi hash blok tersebut dan menyebabkan hubungan dengan blok-blok berikutnya menjadi tidak valid.

# Tugas 3

## 1. Istilah
### a. DApps (Decentralized Applications)

DApps merupakan aplikasi yang berjalan di atas jaringan blockchain dan tidak bergantung pada server pusat. Berbeda dengan aplikasi konvensional yang dikendalikan oleh satu perusahaan atau organisasi, DApps memanfaatkan smart contract untuk menjalankan logika bisnisnya secara terdesentralisasi. Dengan pendekatan ini, data dan proses aplikasi dapat lebih transparan serta sulit dimanipulasi oleh pihak tertentu.

Contoh DApps yang populer adalah Uniswap dan OpenSea.

### b. NFT (Non-Fungible Token)

NFT merupakan aset digital unik yang disimpan dan diverifikasi menggunakan teknologi blockchain. Berbeda dengan mata uang kripto seperti Bitcoin atau Ether yang bersifat fungible (dapat dipertukarkan dengan nilai yang sama), setiap NFT memiliki identitas dan metadata yang berbeda sehingga tidak dapat digantikan oleh NFT lainnya.

NFT banyak digunakan untuk merepresentasikan kepemilikan aset digital seperti karya seni, musik, item permainan, sertifikat digital, dan koleksi virtual.

### c. DEX (Decentralized Exchange)

DEX adalah platform pertukaran aset kripto yang beroperasi secara terdesentralisasi tanpa perantara atau otoritas pusat. Seluruh transaksi dilakukan langsung antar pengguna melalui smart contract yang berjalan di blockchain.

Keunggulan DEX meliputi kontrol penuh pengguna terhadap asetnya, transparansi transaksi, serta tidak adanya kebutuhan untuk menyerahkan dana kepada pihak ketiga. Contoh DEX yang banyak digunakan adalah Uniswap, PancakeSwap, dan SushiSwap.

### d. Tokenization

Tokenization adalah proses mengubah suatu aset menjadi token digital yang tersimpan di blockchain. Aset yang ditokenisasi dapat berupa aset fisik maupun nonfisik, seperti properti, kendaraan, saham, karya seni, atau hak kepemilikan tertentu.

Dengan tokenization, aset dapat diperdagangkan secara lebih mudah, transparan, dan efisien. Selain itu, aset bernilai tinggi juga dapat dibagi menjadi kepemilikan yang lebih kecil sehingga lebih mudah diakses oleh banyak investor.

### e. Stablecoins

Stablecoin merupakan aset kripto yang dirancang untuk memiliki nilai yang relatif stabil dibandingkan mata uang kripto biasa. Nilainya biasanya dipatok terhadap aset tertentu, seperti mata uang fiat, emas, atau kombinasi aset lainnya.

Tujuan utama stablecoin adalah mengurangi volatilitas harga yang umum terjadi pada aset kripto. Stablecoin sering digunakan sebagai media transaksi, penyimpanan nilai, dan sarana transfer dana antar platform blockchain.

Contoh stablecoin yang populer adalah Tether, USD Coin, dan DAI.

## 2. Peranti Pengembangan untuk Membangun DApps di Ethereum

Jika membangun DApps pada Ethereum, peranti pengembangan yang dipilih adalah **Hardhat**.

Hardhat merupakan framework pengembangan Ethereum yang menyediakan berbagai fitur untuk membuat, menguji, melakukan debugging, serta melakukan deployment smart contract. Framework ini menggunakan bahasa JavaScript atau TypeScript sehingga mudah dipadukan dengan ekosistem pengembangan web modern.

Beberapa alasan memilih Hardhat antara lain:

1. **Mudah digunakan**
2. **Mendukung Smart Contract Solidity**
3. **Local Development Network**
4. **Fitur Debugging yang Baik**
5. **Integrasi dengan Testing Framework**
6. **Mendukung Deployment ke Testnet dan Mainnet**
7. **Komunitas dan Ekosistem yang Besar**

Hardhat merupakan pilihan yang tepat untuk pengembangan DApps di Ethereum karena menyediakan lingkungan pengembangan yang lengkap, mendukung pembuatan dan pengujian smart contract, memiliki fitur debugging yang baik, serta memudahkan proses deployment ke berbagai jaringan blockchain Ethereum. Dengan fitur-fitur tersebut, proses pengembangan DApps dapat dilakukan secara lebih cepat, aman, dan efisien.

