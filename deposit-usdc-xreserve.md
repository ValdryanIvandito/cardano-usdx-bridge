# Panduan Cepat: Deposit USDC ke _xReserve_

Pelajari cara melakukan deposit USDC ke _xReserve_ di _Ethereum Sepolia_.

Panduan cepat ini ditujukan bagi pengembang yang ingin mengintegrasikan _xReserve_ ke dalam aplikasi mereka. Panduan ini membantu Anda menulis skrip _TypeScript_ untuk melakukan deposit USDC ke _xReserve_ di _Ethereum Sepolia_. _Wallet_ penerima akan menerima USDCx di _remote blockchain_ ketika jaringan tersebut mendukung _automatic settlement_.

**Notes:**

**- Perlu mentransfer USDC dari _chain_ lain terlebih dahulu? Gunakan [_Bridge Kit_](https://developers.circle.com/bridge-kit) untuk memindahkan dana _testnet_ ke _Ethereum Sepolia_ sebelum melakukan deposit ke _xReserve_.**
**- Pada dokumentasi ini, _remote blockchain_ yang digunakan adalah _Cardano_.**

## Prasyarat

Sebelum Anda mulai, pastikan Anda telah:

- Menginstal [**Node.js v22+**](https://nodejs.org/en)
- Menyiapkan _wallet Ethereum Sepolia_ dan _wallet Cardano Preprod_, serta mendanainya dengan token _testnet_.

  Perlu _wallet_ atau token _testnet_?
  - Gunakan _wallet provider_ seperti [_MetaMask_](/xreserve/tutorials/deposit-usdc-into-xreserve#need-a-wallet-or-testnet-tokens-2) untuk membuat _wallet Ethereum Sepolia_.
  - Gunakan [_Eternl_](https://eternl.io/) atau [_Lace_](https://www.lace.io/) untuk membuat _wallet Cardano Preprod_.
  - Dapatkan USDC _testnet_ dari [_Circle Faucet_](https://faucet.circle.com/).
  - Dapatkan ETH _testnet_ dari [_Ethereum Sepolia Faucet_](https://cloud.google.com/application/web3/faucet/ethereum/sepolia) publik.

- Menginstal _dependencies_ yang dibutuhkan:
  - [**viem:**](https://viem.sh/docs/getting-started) _library TypeScript_ untuk berinteraksi dengan _Ethereum_.
  - [**@scure/base:**](https://github.com/paulmillr/scure-base) untuk melakukan _bech32 decoding_ pada alamat _Cardano_.
