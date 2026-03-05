# 1. Pendahuluan

Dokumen ini merupakan terjemahan yang dibuat oleh saya secara pribadi yang bersumber dari dokumentasi xReserve yang diterbitkan oleh Circle. Sumber dokumentasi asli dapat diakses pada [tautan](https://developers.circle.com/xreserve) berikut.

Tujuan dari terjemahan ini adalah untuk membantu:

- pembaca umum yang ingin memahami konsep xReserve
- pengembang blockchain pemula yang mengalami kesulitan memahami dokumentasi asli

**_Catatan: Beberapa istilah teknis dalam ekosistem Web3 dan blockchain sengaja tidak diterjemahkan agar tidak menimbulkan ambiguitas makna, Anda dapat mempelajarinya di bagaian Glossarium._**

# 2. Daftar Isi

1. [Pendahuluan](gambaran-umum-xreserve.md)  
   1.1 [Gambaran Umum dan Manfaat xReserve](gambaran-umum-xreserve.md)

2. Memulai  
   2.1 [Cara Kerja xReserve](cara-kerja-xreserve.md)  
   2.2 [Blockchain dan Domain yang Didukung xReserve](blockchain-domain-xreserve.md)  
   2.3 [Memulai Cepat: Deposit USDC ke xReserve](deposit-usdc-xreserve.md)

3. Withdrawal  
   3.1 [Withdrawal pada Remote Blockchain](withdrawal.md)  
   3.2 [Biaya Withdrawal](biaya-withdrawal.md)

# 3. Glossarium

Dalam dokumentasi ini terdapat beberapa istilah yang tidak diterjemahkan ke dalam bahasa Indonesia. Hal ini dilakukan karena istilah tersebut sudah menjadi standar di industri blockchain dan apabila diterjemahkan justru dapat menyebabkan perubahan makna atau kebingungan.

| Istilah                                        | Penjelasan                                                                                                                                                                                                                                                                        |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Attestation**                                | Pesan kriptografis yang ditandatangani secara digital untuk membuktikan bahwa suatu peristiwa di blockchain telah terjadi, seperti _deposit_ atau _burn_. Attestation digunakan oleh blockchain lain untuk memverifikasi dan menjalankan proses seperti _mint_ atau _withdrawal_. |
| **Blockchain**                                 | Sistem buku besar digital terdistribusi yang mencatat transaksi secara permanen dan transparan pada jaringan komputer terdesentralisasi.                                                                                                                                          |
| **Burn**                                       | Proses menghapus token dari peredaran blockchain secara permanen. Biasanya dilakukan sebelum proses _withdrawal_.                                                                                                                                                                 |
| **Circle**                                     | Perusahaan teknologi finansial yang menerbitkan USDC dan menyediakan berbagai infrastruktur blockchain seperti **xReserve**, **CCTP**, dan **Gateway** untuk mendukung interoperabilitas dan likuiditas antar blockchain.                                                         |
| **Deposit**                                    | Proses menyetor USDC ke kontrak xReserve pada **source blockchain** untuk menerima stablecoin berbasis USDC pada **remote blockchain**.                                                                                                                                           |
| **Deploy**                                     | Proses menerapkan atau mengaktifkan smart contract atau aplikasi blockchain ke suatu jaringan sehingga dapat digunakan oleh pengguna.                                                                                                                                             |
| **Destination Blockchain / Destination Chain** | Blockchain tujuan tempat pengguna menerima aset setelah melakukan proses _withdrawal_ dari xReserve. Blockchain ini dapat berupa **source chain**, **remote chain**, atau blockchain lain yang didukung Gateway atau CCTP.                                                        |
| **Domain**                                     | Identitas numerik yang diterbitkan oleh Circle untuk mengidentifikasi suatu blockchain dalam sistem xReserve. Domain digunakan dalam pesan dan _attestation_ untuk menentukan blockchain sumber atau tujuan.                                                                      |
| **Endpoint**                                   | Alamat API atau URL yang digunakan oleh aplikasi untuk berinteraksi dengan suatu layanan, seperti mengambil informasi domain atau berinteraksi dengan sistem xReserve.                                                                                                            |
| **Gateway**                                    | Infrastruktur yang disediakan oleh Circle untuk menyimpan saldo USDC dan memungkinkan penarikan dana ke berbagai blockchain yang didukung. Dalam sistem xReserve, Gateway membantu menyediakan likuiditas lintas blockchain.                                                      |
| **Mainnet**                                    | Jaringan blockchain utama tempat transaksi nyata terjadi dan memiliki nilai ekonomi.                                                                                                                                                                                              |
| **Mint**                                       | Proses pembuatan token baru pada blockchain setelah sistem memverifikasi _attestation_ dari deposit atau proses lainnya.                                                                                                                                                          |
| **Remote Blockchain / Remote Chain**           | Blockchain mitra yang menerbitkan dan menjalankan stablecoin berbasis USDC setelah proses deposit terjadi pada **source blockchain**. Stablecoin tersebut dicetak berdasarkan _attestation_ dari xReserve.                                                                        |
| **Remote Domain**                              | Domain yang merepresentasikan blockchain tempat stablecoin berbasis USDC diterbitkan dan beredar.                                                                                                                                                                                 |
| **Smart Contract**                             | Program yang berjalan di blockchain dan secara otomatis menjalankan logika atau aturan tertentu ketika kondisi yang ditentukan terpenuhi.                                                                                                                                         |
| **Source Blockchain / Source Chain**           | Blockchain tempat kontrak xReserve berada dan tempat USDC disimpan sebagai cadangan sebelum pengguna menerima stablecoin pada blockchain lain.                                                                                                                                    |
| **Source Domain**                              | Domain yang merepresentasikan blockchain tempat cadangan USDC disimpan dalam kontrak xReserve.                                                                                                                                                                                    |
| **Stablecoin**                                 | Aset kripto yang dirancang untuk memiliki nilai stabil dengan cara dipatok pada aset tertentu, biasanya mata uang fiat seperti dolar AS. Contoh: **USDC** dan **USDT**.                                                                                                           |
| **Testnet**                                    | Jaringan blockchain yang digunakan untuk pengujian aplikasi, smart contract, atau integrasi sebelum digunakan pada mainnet. Token pada testnet tidak memiliki nilai ekonomi nyata.                                                                                                |
| **USDC**                                       | Stablecoin yang diterbitkan oleh Circle dan dipatok dengan nilai 1 USD = 1 USDC. USDC banyak digunakan sebagai aset likuiditas dalam berbagai aplikasi blockchain dan DeFi.                                                                                                       |
| **USDCx**                                      | Stablecoin yang diterbitkan pada suatu blockchain dan didukung 1:1 oleh USDC yang disimpan dalam kontrak xReserve pada **source blockchain**.                                                                                                                                     |
| **Withdrawal**                                 | Proses penarikan USDC atau stablecoin berbasis USDC dengan cara membakar (_burn_) token pada **remote blockchain** dan menerima aset pada blockchain tujuan.                                                                                                                      |
| **xReserve**                                   | Infrastruktur interoperabilitas blockchain yang dikembangkan oleh Circle untuk memungkinkan berbagai blockchain menerbitkan stablecoin yang didukung USDC di jaringan mereka sendiri melalui mekanisme smart contract dan attestation.                                            |
