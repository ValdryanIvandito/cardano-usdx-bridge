# **Gambaran Umum _xReserve_**

**_xReserve_** adalah infrastruktur interoperabilitas yang memungkinkan para pengembang _blockchain_ untuk menerbitkan _stablecoin_ berbasis USDC di jaringan mereka sendiri. Sistem ini didukung oleh pengesahan terprogram (_programmatic attestations_) dan _smart contract_ yang diimplementasikan oleh _Circle_ yang menyimpan USDC sebagai cadangan di _source blockchain_ seperti **_Ethereum_**. _Stablecoin_ berbasis USDC dicetak melalui _xReserve_ dapat saling berinteraksi serta dengan jaringan USDC yang lebih luas.

## **Manfaat Utama**

_xReserve_ menawarkan berbagai manfaat bagi jaringan _blockchain_, para pengembang, dan pengguna akhir:

- **Likuiditas hari pertama:** Token yang dicetak menggunakan _xReserve_ terhubung dengan jaringan USDC yang lebih luas, sehingga pengguna dapat dengan mudah memindahkan token antar _blockchain_.

- **Interoperabilitas:** Pengguna dapat melakukan transfer 1:1 antara _stablecoin_ berbasis USDC dan USDC.

- **Diimplementasikan oleh _Circle_:** _xReserve_ menyimpan cadangan USDC dalam _smart contract_ yang diimplementasikan oleh _Circle_.

- **Asumsi kepercayaan yang diminimalkan:** Mekanisme pengesahan _xReserve_ memverifikasi proses deposit dan pembakaran token (_burn_), sehingga mengurangi ketergantungan pada layanan pihak ketiga untuk mengonversi USDC.

# **Pendahuluan\***

## **Cara Kerja _xReserve_**

_xReserve_ terdiri dari dua layanan utama: _smart contract_ _onchain_ dan sistem pengesahan _offchain_. Secara bersama-sama, kedua layanan ini beserta layanan terkait di _remote blockchain_ memungkinkan pengguna untuk mendepositkan (_deposit_) USDC di _source blockchain_ guna menerima _stablecoin_ berbasis USDC di _remote blockchain_. Selanjutnya, pengguna dapat membakar _stablecoin_ berbasis USDC untuk menarik kembali (_withdrawal_) USDC yang disimpan dalam _xReserve_.

Berikut adalah diagram yang menunjukkan bagaimana _xReserve_ menangani proses _deposit_ dan _withdrawal_.

![xReserve Workflow](./assets/1.png)

## **Proses _Deposit_ ke _xReserve_**

Langkah-langkah berikut terjadi ketika pengguna mendepositkan USDC ke _xReserve_:

1. Pengguna mendepositkan USDC dari aplikasi _wallet_ mereka ke _smart contract_ _xReserve_ di _source blockchain_.
2. Kontrak _xReserve_ memancarkan peristiwa deposit, mengunci dana tersebut, dan menahannya sebagai cadangan.
3. Layanan pengesahan _xReserve_ menghasilkan dan menandatangani bukti pengesahan deposit.
4. Layanan pengesahan _remote blockchain_ mengambil bukti pengesahan deposit yang telah ditandatangani.
5. _remote blockchain_ mencetak _stablecoin_ berbasis USDC di jaringannya dan memancarkan peristiwa mint.
6. Kontrak token di _remote blockchain_ mendepositkan _stablecoin_ yang baru dicetak ke aplikasi _wallet_ pengguna di _remote blockchain_ tersebut.

Setelah proses deposit selesai, pengguna menerima _stablecoin_ berbasis USDC dalam jumlah yang setara di _remote blockchain_.

## **Proses _Withdrawal_ dari _xReserve_**

Selanjutnya, langkah-langkah berikut ini terjadi ketika pengguna menarik USDC dari _xReserve_:

1. Pengguna mengajukan pembakaran _stablecoin_ berbasis USDC di _remote blockchain_ serta _withdrawal_ USDC di _destination blockchain_.
2. Kontrak token yang ada di _remote blockchain_ membakar _stablecoin_ berbasis USDC dan memancarkan peristiwa pembakaran.
3. Layanan pengesahan di _remote blockchain_ menghasilkan dan menandatangani pernyataan niat pembakaran secara _offchain_.
4. Layanan pengesahan di _remote blockchain_ meneruskan niat pembakaran beserta tanda tangannya ke _xReserve_.
5. _xReserve_ memverifikasi pembakaran tersebut dan menerbitkan pengesahan _withdrawal_.
6. _xReserve_ melepaskan USDC ke dompet pengguna di _destination blockchain_.

Setelah proses _withdrawal_ selesai, pengguna menerima USDC di _source blockchain_.

**Catatan:** Sebagai bagian dari proses _withdrawal_ yang sama, _xReserve_ dapat meneruskan dana ke _blockchain_ lain. Hal ini memungkinkan pengguna untuk menarik dana dari blockchain selain _source blockchain_ tanpa melakukan transaksi _crosschain_.

## **_Blockchain_ & Domain yang Didukung _xReserve_**

### **_Blockchain_ yang Didukung**

xReserve beroperasi lintas blockchain. Setiap blockchain yang didukung dapat berperan sebagai satu atau lebih dari peran berikut:

- **_Source chains_** di mana kontrak cerdas _xReserve_ yang diimplementasikan oleh _Circle_ menyimpan **cadangan USDC**.
- **_Remote chains_** di mana stablecoin berbasis USDC dicetak.
- **_Destination chains_** di mana USDC atau stablecoin berbasis USDC pada akhirnya ditarik (_withdraw_).

#### **_Source chains_**

Berikut ini adalah tabel _blockchain_ _mainnet_ dan _testnet_ di mana kontrak cerdas _xReserve_ telah diimplementasikan. Pengguna mendepositkan USDC ke kontrak tersebut untuk menerima stablecoin berbasis USDC dalam jumlah yang setara di _remote blockchain_.

#### **_Mainnet_**

| **_Blockchain_** | **Alamat Token USDC**                                                                                               | **Alamat Kontrak _xReserve_**                                                                                         |
| :--------------- | :------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------- |
| _Ethereum_       | [0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) | [0x8888888199b2Df864bf678259607d6D5EBb4e3Ce](https://etherscan.io/address/0x8888888199b2Df864bf678259607d6D5EBb4e3Ce) |

#### **_Testnet_**

| **_Blockchain_**   | **Alamat Token USDC**                                                                                                       | **Alamat Kontrak _xReserve_**                                                                                                 |
| :----------------- | :-------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| _Ethereum Sepolia_ | [0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238](https://sepolia.etherscan.io/token/0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238) | [0x008888878f94C0d87defdf0B07f46B93C1934442](https://sepolia.etherscan.io/address/0x008888878f94C0d87defdf0B07f46B93C1934442) |
