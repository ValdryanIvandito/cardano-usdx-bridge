# **1. Gambaran Umum xReserve**

**_xReserve_** adalah infrastruktur interoperabilitas yang memungkinkan para pengembang _blockchain_ untuk menerbitkan _stablecoin_ berbasis USDC di jaringan mereka sendiri. Sistem ini didukung oleh pengesahan terprogram (_programmatic attestations_) dan _smart contract_ yang diimplementasikan oleh _Circle_ yang menyimpan USDC sebagai cadangan di _source blockchain_ seperti **_Ethereum_**. _Stablecoin_ berbasis USDC dicetak melalui _xReserve_ dapat saling berinteraksi serta dengan jaringan USDC yang lebih luas.

## **1.1 Manfaat Utama**

_xReserve_ menawarkan berbagai manfaat bagi jaringan _blockchain_, para pengembang, dan pengguna akhir:

- **Likuiditas hari pertama:** Token yang dicetak menggunakan _xReserve_ terhubung dengan jaringan USDC yang lebih luas, sehingga pengguna dapat dengan mudah memindahkan token antar _blockchain_.

- **Interoperabilitas:** Pengguna dapat melakukan transfer 1:1 antara _stablecoin_ berbasis USDC dan USDC.

- **Diimplementasikan oleh _Circle_:** _xReserve_ menyimpan cadangan USDC dalam _smart contract_ yang diimplementasikan oleh _Circle_.

- **Asumsi kepercayaan yang diminimalkan:** Mekanisme pengesahan _xReserve_ memverifikasi proses deposit dan pembakaran token (_burn_), sehingga mengurangi ketergantungan pada layanan pihak ketiga untuk mengonversi USDC.

# **2. Pendahuluan**

## **2.1 Cara Kerja xReserve**

_xReserve_ terdiri dari dua layanan utama: _smart contract_ _onchain_ dan sistem pengesahan _offchain_. Secara bersama-sama, kedua layanan ini beserta layanan terkait di _remote blockchain_ memungkinkan pengguna untuk mendepositkan (_deposit_) USDC di _source blockchain_ guna menerima _stablecoin_ berbasis USDC di _remote blockchain_. Selanjutnya, pengguna dapat membakar _stablecoin_ berbasis USDC untuk menarik kembali (_withdrawal_) USDC yang disimpan dalam _xReserve_.

Berikut adalah diagram yang menunjukkan bagaimana _xReserve_ menangani proses _deposit_ dan _withdrawal_.

![xReserve Workflow](./assets/1.png)

## **2.2 Proses Deposit ke xReserve**

Langkah-langkah berikut terjadi ketika pengguna mendepositkan USDC ke _xReserve_:

1. Pengguna mendepositkan USDC dari aplikasi _wallet_ mereka ke _smart contract_ _xReserve_ di _source blockchain_.
2. Kontrak _xReserve_ memancarkan peristiwa deposit, mengunci dana tersebut, dan menahannya sebagai cadangan.
3. Layanan pengesahan _xReserve_ menghasilkan dan menandatangani bukti pengesahan deposit.
4. Layanan pengesahan _remote blockchain_ mengambil bukti pengesahan deposit yang telah ditandatangani.
5. _remote blockchain_ mencetak _stablecoin_ berbasis USDC di jaringannya dan memancarkan peristiwa mint.
6. Kontrak token di _remote blockchain_ mendepositkan _stablecoin_ yang baru dicetak ke aplikasi _wallet_ pengguna di _remote blockchain_ tersebut.

Setelah proses deposit selesai, pengguna menerima _stablecoin_ berbasis USDC dalam jumlah yang setara di _remote blockchain_.

## **2.3 Proses Withdrawal dari xReserve**

Selanjutnya, langkah-langkah berikut ini terjadi ketika pengguna menarik USDC dari _xReserve_:

1. Pengguna mengajukan pembakaran _stablecoin_ berbasis USDC di _remote blockchain_ serta _withdrawal_ USDC di _destination blockchain_.
2. Kontrak token yang ada di _remote blockchain_ membakar _stablecoin_ berbasis USDC dan memancarkan peristiwa pembakaran.
3. Layanan pengesahan di _remote blockchain_ menghasilkan dan menandatangani pernyataan niat pembakaran secara _offchain_.
4. Layanan pengesahan di _remote blockchain_ meneruskan niat pembakaran beserta tanda tangannya ke _xReserve_.
5. _xReserve_ memverifikasi pembakaran tersebut dan menerbitkan pengesahan _withdrawal_.
6. _xReserve_ melepaskan USDC ke dompet pengguna di _destination blockchain_.

Setelah proses _withdrawal_ selesai, pengguna menerima USDC di _source blockchain_.

**Catatan:** Sebagai bagian dari proses _withdrawal_ yang sama, _xReserve_ dapat meneruskan dana ke _blockchain_ lain. Hal ini memungkinkan pengguna untuk menarik dana dari blockchain selain _source blockchain_ tanpa melakukan transaksi _crosschain_.

## **2.4 Blockchain & Domain yang Didukung xReserve**

### **2.4.1 Blockchain yang Didukung**

xReserve beroperasi lintas blockchain. Setiap blockchain yang didukung dapat berperan sebagai satu atau lebih dari peran berikut:

- **_Source chains_** di mana kontrak cerdas _xReserve_ yang diimplementasikan oleh _Circle_ menyimpan **cadangan USDC**.
- **_Remote chains_** di mana stablecoin berbasis USDC dicetak.
- **_Destination chains_** di mana USDC atau stablecoin berbasis USDC pada akhirnya ditarik (_withdraw_).

#### **2.4.1.1 Source Chains**

Berikut ini adalah tabel _blockchain_ _mainnet_ dan _testnet_ di mana kontrak cerdas _xReserve_ telah diimplementasikan. Pengguna mendepositkan USDC ke kontrak tersebut untuk menerima stablecoin berbasis USDC dalam jumlah yang setara di _remote blockchain_.

##### **Mainnet**

| **Blockchain** | **Alamat Token USDC**                                                                                               | **Alamat Kontrak xReserve**                                                                                           |
| :------------- | :------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------- |
| Ethereum       | [0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) | [0x8888888199b2Df864bf678259607d6D5EBb4e3Ce](https://etherscan.io/address/0x8888888199b2Df864bf678259607d6D5EBb4e3Ce) |

##### **Testnet**

| **Blockchain**   | **Alamat Token USDC**                                                                                                       | **Alamat Kontrak xReserve**                                                                                                   |
| :--------------- | :-------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| Ethereum Sepolia | [0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238](https://sepolia.etherscan.io/token/0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238) | [0x008888878f94C0d87defdf0B07f46B93C1934442](https://sepolia.etherscan.io/address/0x008888878f94C0d87defdf0B07f46B93C1934442) |

#### **2.4.1.2 Remote Chains**

Berikut adalah tabel blockchain mitra xReserve pada jaringan mainnet dan testnet di mana stablecoin berbasis USDC dapat dicetak.

##### **Mainnet**

| **Blockchain** | **Simbol Token** | **Alamat Token atau Pengidentifikasi**                                                                                                                                                                                                                                                   |
| :------------- | :--------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aleo           | USDCx            | [usdcx_stablecoin.aleo](https://explorer.provable.com/program/usdcx_stablecoin.aleo)                                                                                                                                                                                                     |
| Canton         | USDCx            | [12208115f1e168dd7e792320be9c4ca720c751a02a3053c7606e1c1cd3dad9bf60ef](https://api.utilities.digitalasset.com/api/token-standard/v0/registrars/decentralized-usdc-interchain-rep::12208115f1e168dd7e792320be9c4ca720c751a02a3053c7606e1c1cd3dad9bf60ef/registry/metadata/v1/instruments) |
| Cardano        | USDCx            | [1f3aec8bfe7ea4fe14c5f121e2a92e301afe414147860d557cac7e345553444378](https://cardanoscan.io/token/1f3aec8bfe7ea4fe14c5f121e2a92e301afe414147860d557cac7e345553444378)                                                                                                                    |
| Stacks         | USDCx            | [SP120SBRBQJ00MCWS7TM5R8WJNTTKD5K0HFRC2CNE](https://explorer.hiro.so/txid/SP120SBRBQJ00MCWS7TM5R8WJNTTKD5K0HFRC2CNE.usdcx?chain=mainnet)                                                                                                                                                 |

##### **Testnet**

| **Blockchain** | **Simbol Token** | **Alamat Token atau Pengidentifikasi**                                                                                                                                                                                                                                                           |
| :------------- | :--------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aleo           | USDCx            | [test_usdcx_stablecoin.aleo](https://testnet.explorer.provable.com/program/test_usdcx_stablecoin.aleo)                                                                                                                                                                                           |
| Canton         | USDCx            | [122049e2af8a725bd19759320fc83c638e7718973eac189d8f201309c512d1ffec61](https://api.utilities.digitalasset-staging.com/api/token-standard/v0/registrars/decentralized-usdc-interchain-rep::122049e2af8a725bd19759320fc83c638e7718973eac189d8f201309c512d1ffec61/registry/metadata/v1/instruments) |
| Cardano        | USDCx            | [31dde3db98ad05feb688d4dbb146b3b6054e1246cbcef98c79b0bf665553444378](https://preprod.cardanoscan.io/token/31dde3db98ad05feb688d4dbb146b3b6054e1246cbcef98c79b0bf665553444378)                                                                                                                    |
| Stacks         | USDCx            | [ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM.usdcx](https://explorer.hiro.so/txid/ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM.usdcx?chain=testnet)                                                                                                                                                   |

#### **2.4.1.3 Destination Chains**

Berikut adalah _destination blockchain_ di mana USDC atau _stablecoin_ berbasis USDC pada akhirnya di-_withdraw_.

- **_Blockchain_ yang didukung oleh _Gateway_:** Karena _xReserve_ menyimpan USDC di dompet _Gateway_, pengguna dapat _withdraw_ USDC pada _blockchain_ mana pun yang didukung oleh _Gateway_, termasuk _source chain_.

- **_Blockchain_ yang didukung oleh CCTP:**  
  Jika suatu _blockchain_ tidak didukung oleh _Gateway_, _xReserve_ akan menggunakan CCTP untuk meneruskan dana ke _blockchain_ lain. Hal ini memungkinkan pengguna untuk _withdraw_ USDC pada _blockchain_ mana pun yang didukung oleh CCTP.

- **_Remote blockchain_ lainnya:** Pengguna dapat membakar _stablecoin_ berbasis USDC pada satu _remote blockchain_ untuk _withdraw_ _stablecoin_ berbasis USDC pada _remote blockchain_ lainnya.

### **2.4.2 ID Domain**

Domain adalah ID numerik yang diterbitkan oleh _Circle_ untuk suatu _blockchain_. _xReserve_ menggunakan ID domain dalam pesan dan pengesahan. ID domain tidak dipetakan ke ID _chain_ publik yang ada.

Domain _xReserve_ dibagi menjadi dua kategori:

- **_Source domain_** adalah _blockchain_ tempat USDC disimpan dalam kontrak _xReserve_.
- **_Remote domain_** adalah _blockchain_ tempat _stablecoin_ berbasis USDC di-_deploy_, dicetak, dan beredar.

**Catatan:** Gunakan _endpoint_ [**Get domain information**](https://developers.circle.com/api-reference/xreserve/all/get-info) untuk mendapatkan daftar seluruh _source domain_ dan _remote domain_.

#### **2.4.2.1 Source Domain**

| **Domain** | **Blockchain** |
| :--------- | :------------- |
| 0          | Ethereum       |

#### **2.4.2.2 Remote Domain**

| **Domain** | **Blockchain** |
| :--------- | :------------- |
| 10001      | Canton         |
| 10002      | Aleo           |
| 10003      | Stacks         |
| 10004      | Cardano        |
