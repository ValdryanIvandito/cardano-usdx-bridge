# **Blockchain & Domain yang Didukung xReserve**

## **Blockchain yang Didukung**

_xReserve_ beroperasi lintas _blockchain_. Setiap _blockchain_ yang didukung dapat berperan sebagai satu atau lebih dari peran berikut:

- **_Source chains_** di mana _smart contract_ _xReserve_ yang di-_deploy_ oleh _Circle_ menyimpan **cadangan USDC**.
- **_Remote chains_** di mana USDCx dicetak.
- **_Destination chains_** di mana USDC atau USDCx pada akhirnya ditarik (_withdraw_).

### **Source Chains**

Berikut ini adalah tabel _blockchain_ _mainnet_ dan _testnet_ di mana _smart contract_ _xReserve_ telah di-_deploy_. Pengguna mendepositkan USDC ke kontrak tersebut untuk menerima USDCx dalam jumlah yang setara di _remote blockchain_.

#### **Mainnet**

| **Blockchain** | **Alamat Token USDC**                                                                                               | **Alamat Kontrak xReserve**                                                                                           |
| :------------- | :------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------- |
| Ethereum       | [0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) | [0x8888888199b2Df864bf678259607d6D5EBb4e3Ce](https://etherscan.io/address/0x8888888199b2Df864bf678259607d6D5EBb4e3Ce) |

#### **Testnet**

| **Blockchain**   | **Alamat Token USDC**                                                                                                       | **Alamat Kontrak xReserve**                                                                                                   |
| :--------------- | :-------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| Ethereum Sepolia | [0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238](https://sepolia.etherscan.io/token/0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238) | [0x008888878f94C0d87defdf0B07f46B93C1934442](https://sepolia.etherscan.io/address/0x008888878f94C0d87defdf0B07f46B93C1934442) |

### **Remote Chains**

Berikut adalah tabel _blockchain_ mitra _xReserve_ pada jaringan _mainnet_ dan _testnet_ di mana USDCx dapat dicetak.

#### **Mainnet**

| **Blockchain** | **Simbol Token** | **Alamat Token atau Pengidentifikasi**                                                                                                                                                                                                                                                   |
| :------------- | :--------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aleo           | USDCx            | [usdcx_stablecoin.aleo](https://explorer.provable.com/program/usdcx_stablecoin.aleo)                                                                                                                                                                                                     |
| Canton         | USDCx            | [12208115f1e168dd7e792320be9c4ca720c751a02a3053c7606e1c1cd3dad9bf60ef](https://api.utilities.digitalasset.com/api/token-standard/v0/registrars/decentralized-usdc-interchain-rep::12208115f1e168dd7e792320be9c4ca720c751a02a3053c7606e1c1cd3dad9bf60ef/registry/metadata/v1/instruments) |
| Cardano        | USDCx            | [1f3aec8bfe7ea4fe14c5f121e2a92e301afe414147860d557cac7e345553444378](https://cardanoscan.io/token/1f3aec8bfe7ea4fe14c5f121e2a92e301afe414147860d557cac7e345553444378)                                                                                                                    |
| Stacks         | USDCx            | [SP120SBRBQJ00MCWS7TM5R8WJNTTKD5K0HFRC2CNE](https://explorer.hiro.so/txid/SP120SBRBQJ00MCWS7TM5R8WJNTTKD5K0HFRC2CNE.usdcx?chain=mainnet)                                                                                                                                                 |

#### **Testnet**

| **Blockchain** | **Simbol Token** | **Alamat Token atau Pengidentifikasi**                                                                                                                                                                                                                                                           |
| :------------- | :--------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Aleo           | USDCx            | [test_usdcx_stablecoin.aleo](https://testnet.explorer.provable.com/program/test_usdcx_stablecoin.aleo)                                                                                                                                                                                           |
| Canton         | USDCx            | [122049e2af8a725bd19759320fc83c638e7718973eac189d8f201309c512d1ffec61](https://api.utilities.digitalasset-staging.com/api/token-standard/v0/registrars/decentralized-usdc-interchain-rep::122049e2af8a725bd19759320fc83c638e7718973eac189d8f201309c512d1ffec61/registry/metadata/v1/instruments) |
| Cardano        | USDCx            | [31dde3db98ad05feb688d4dbb146b3b6054e1246cbcef98c79b0bf665553444378](https://preprod.cardanoscan.io/token/31dde3db98ad05feb688d4dbb146b3b6054e1246cbcef98c79b0bf665553444378)                                                                                                                    |
| Stacks         | USDCx            | [ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM.usdcx](https://explorer.hiro.so/txid/ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM.usdcx?chain=testnet)                                                                                                                                                   |

### **Destination Chains**

Berikut adalah _destination blockchain_ di mana USDC atau USDCx pada akhirnya di-_withdraw_.

- **_Blockchain_ yang didukung oleh _Gateway_:** Karena _xReserve_ menyimpan USDC di _Gateway wallet_, pengguna dapat _withdraw_ USDC pada _blockchain_ mana pun yang didukung oleh _Gateway_, termasuk _source chain_.

- **_Blockchain_ yang didukung oleh CCTP:**  
  Jika suatu _blockchain_ tidak didukung oleh _Gateway_, _xReserve_ akan menggunakan CCTP untuk meneruskan dana ke _blockchain_ lain. Hal ini memungkinkan pengguna untuk _withdraw_ USDC pada _blockchain_ mana pun yang didukung oleh CCTP.

- **_Remote blockchain_ lainnya:** Pengguna dapat membakar USDCx pada satu _remote blockchain_ untuk _withdraw_ USDCx pada _remote blockchain_ lainnya.

## **ID Domain**

Domain adalah ID numerik yang diterbitkan oleh _Circle_ untuk suatu _blockchain_. _xReserve_ menggunakan ID domain dalam pesan dan pengesahan. ID domain tidak dipetakan ke ID _chain_ publik yang ada.

Domain _xReserve_ dibagi menjadi dua kategori:

- **_Source domain_** adalah _blockchain_ tempat USDC disimpan dalam kontrak _xReserve_.
- **_Remote domain_** adalah _blockchain_ tempat USDCx di-_deploy_, di-_mint_, dan beredar.

**Catatan:** Gunakan _endpoint_ [**Get domain information**](https://developers.circle.com/api-reference/xreserve/all/get-info) untuk mendapatkan daftar seluruh _source domain_ dan _remote domain_.

### **Source Domain**

| **Domain** | **Blockchain** |
| :--------- | :------------- |
| 0          | Ethereum       |

### **Remote Domain**

| **Domain** | **Blockchain** |
| :--------- | :------------- |
| 10001      | Canton         |
| 10002      | Aleo           |
| 10003      | Stacks         |
| 10004      | Cardano        |

[← Previous](cara-kerja-xreserve.md) | [Table of Contents](README.md#daftar-isi) | [Next →]()
