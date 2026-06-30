# 1. Instalasi dan Persiapan

## Instalasi Tools

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/1_1.png" width="400">
</p>


Tahap pertama adalah melakukan instalasi seluruh perangkat yang diperlukan untuk pengembangan aplikasi pada blockchain Solana. Perangkat yang diinstal meliputi Rust sebagai bahasa pemrograman utama, Node.js beserta Yarn sebagai runtime dan package manager JavaScript, Solana CLI untuk berinteraksi dengan jaringan Solana melalui terminal, serta Anchor sebagai framework pengembangan smart contract di Solana. Setelah proses instalasi selesai, dilakukan verifikasi untuk memastikan seluruh tools telah berhasil terpasang dan siap digunakan.

## Membuat Wallet Solana

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/1_2.png" width="400">
</p>


Selanjutnya dibuat dua buah wallet Solana menggunakan perintah `solana-keygen new`. Setiap wallet menghasilkan sepasang kunci kriptografi yang terdiri dari public key dan private key. Selain itu, sistem juga menghasilkan seed phrase yang berfungsi sebagai cadangan untuk memulihkan wallet apabila file keypair hilang. Seed phrase ini bersifat rahasia dan tidak boleh dibagikan kepada pihak lain.

## Menampilkan Address Wallet

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/1_3.png" width="400">
</p>


Setelah kedua wallet berhasil dibuat, alamat masing-masing wallet ditampilkan menggunakan perintah `solana address`. Alamat ini merupakan public key yang digunakan sebagai identitas wallet pada jaringan Solana sehingga dapat menerima maupun mengirim aset SOL.

# 3. Memperoleh SOL dari Devnet

## Mengatur Jaringan ke Devnet

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/3_1.png" width="400">
</p>


Konfigurasi Solana CLI diubah agar menggunakan jaringan Devnet melalui perintah `solana config set --url devnet`. Dengan konfigurasi ini, seluruh transaksi dan pengujian akan dilakukan pada jaringan pengembangan sehingga tidak menggunakan aset SOL yang memiliki nilai ekonomi di jaringan utama.

## Meminta SOL Devnet

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/3_2.png" width="400">
</p>


Wallet kemudian memperoleh SOL melalui layanan faucet Devnet. Proses ini memberikan sejumlah SOL uji coba yang dapat digunakan untuk melakukan berbagai transaksi selama proses pengembangan tanpa memerlukan pembelian aset sebenarnya.

## Memeriksa Saldo Wallet

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/3_3.png" width="400">
</p>


Saldo wallet diperiksa menggunakan perintah `solana balance` untuk memastikan bahwa proses airdrop berhasil dilakukan. Informasi saldo ini menunjukkan jumlah SOL yang tersedia dan siap digunakan untuk melakukan transaksi pada jaringan Devnet.

# 4. Alur Konsensus pada Solana Blockchain

## Transfer SOL

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_1.png" width="400">
</p>


Tahap pertama pada proses konsensus adalah melakukan transfer SOL dari wallet pengirim menuju wallet penerima. Setelah transaksi dikirim, jaringan Solana akan memberikan signature transaksi yang berfungsi sebagai identitas unik untuk proses verifikasi dan pelacakan transaksi.

## Menampilkan Slot

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_2.png" width="400">
</p>


Perintah `solana slot` digunakan untuk mengetahui slot terbaru pada jaringan Solana. Slot merupakan satuan waktu dalam mekanisme Proof of History (PoH) yang digunakan untuk mengurutkan transaksi sebelum diproses lebih lanjut oleh validator.

## Menampilkan Daftar Validator

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_3.png" width="400">
</p>


Perintah `solana validators` menampilkan daftar validator yang aktif pada jaringan. Informasi yang ditampilkan meliputi identitas validator, jumlah stake yang dimiliki, serta status validator. Validator dengan stake yang lebih besar memiliki peluang lebih tinggi untuk dipilih sebagai leader dalam proses pembentukan blok.

## Menampilkan Leader Schedule

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_4.png" width="400">
</p>


Leader schedule digunakan untuk mengetahui validator yang bertugas memproduksi blok pada slot tertentu. Informasi ini menunjukkan bagaimana jaringan Solana menentukan validator yang akan memimpin proses pencatatan transaksi sesuai mekanisme Proof of Stake.

## Menampilkan Informasi Vote Account

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_5.png" width="400">
</p>


Data vote account diperoleh melalui pemanggilan API Devnet menggunakan `curl`. Informasi yang ditampilkan memperlihatkan aktivitas voting para validator, termasuk nilai `lastVote`, stake yang dimiliki, serta berbagai informasi lain yang digunakan dalam proses validasi blok dan pencapaian konsensus jaringan.

## Memastikan Status Transaksi

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_6.png" width="400">
</p>


Perintah `solana confirm` digunakan untuk memeriksa status transaksi berdasarkan signature yang diperoleh sebelumnya. Status transaksi menunjukkan bahwa transaksi telah berhasil diproses oleh jaringan dan telah memperoleh konfirmasi dari validator.

## Verifikasi Melalui Solana Explorer

<p align="center">
  <img src="https://raw.githubusercontent.com/KhvarenahQuinn/prak-dis-dec/main/13/SS/4_7.png" width="400">
</p>


Sebagai tahap akhir, transaksi diverifikasi melalui Solana Explorer pada jaringan Devnet menggunakan signature transaksi. Halaman explorer menampilkan informasi lengkap mengenai transaksi, seperti alamat pengirim dan penerima, jumlah SOL yang ditransfer, slot transaksi, serta status **Finalized**. Status tersebut menunjukkan bahwa transaksi telah mencapai konsensus dan telah tercatat secara permanen pada blockchain Solana.
