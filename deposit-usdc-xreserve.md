# Panduan Cepat: Deposit USDC ke _xReserve_

Pelajari cara melakukan deposit USDC ke _xReserve_ di _Ethereum Sepolia_.

Panduan cepat ini ditujukan bagi pengembang yang ingin mengintegrasikan _xReserve_ ke dalam aplikasi mereka. Panduan ini membantu Anda menulis skrip _TypeScript_ untuk melakukan deposit USDC ke _xReserve_ di _Ethereum Sepolia_. _Wallet_ penerima akan menerima USDCx di _remote blockchain_ ketika jaringan tersebut mendukung _automatic settlement_.

**Notes:**

- Perlu mentransfer USDC dari _chain_ lain terlebih dahulu? Gunakan [_Bridge Kit_](https://developers.circle.com/bridge-kit) untuk memindahkan dana _testnet_ ke _Ethereum Sepolia_ sebelum melakukan deposit ke _xReserve_.

- Pada dokumentasi ini, _remote blockchain_ yang digunakan adalah _Cardano_.

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

## Langkah 1: Menyiapkan Proyek

Langkah ini menjelaskan cara menyiapkan _workspace Node.js_ baru, menginstal _dependencies_, mengonfigurasi _environment variables_, serta menyiapkan _Ethereum Sepolia wallet_ Anda.

### 1.1 Menyiapkan Development Environment

Buat direktori baru dan instal _dependencies_ yang diperlukan.

```shell
# Set up your directory and initialize the project
mkdir xreserve-cardano-usdcx
cd xreserve-cardano-usdcx
npm init -y

# Install tools and dependencies
npm install viem dotenv @scure/base
```

### 1.2 Mengonfigurasi Environment Variables

Buat file **_.env_** di direktori proyek, lalu tambahkan _wallet private key_ Anda serta alamat penerima pada jaringan _Cardano Preprod_. Gunakan alamat _Cardano bech32_ yang valid.

**Catatan:**

- Jika Anda menggunakan _MetaMask_, ikuti panduan resmi mereka untuk [**menemukan dan mengekspor _private key_ Anda**](https://support.metamask.io/configure/accounts/how-to-export-an-accounts-private-key/).

```shell
cat > .env << EOF
PRIVATE_KEY=<your_ethereum_wallet_private_key>
CARDANO_RECIPIENT=<your_cardano_preprod_recipient_address>
EOF
```

- Penggunaan _private key_ pada contoh ini disederhanakan untuk keperluan demonstrasi. Pada lingkungan _production_, simpan dan kelola _private key_ Anda secara aman dan jangan pernah membagikannya kepada siapa pun.

## Langkah 2: Menyiapkan Skrip

Langkah ini menjelaskan cara membuat skrip dengan mengimpor _dependencies_ serta mendefinisikan konstanta konfigurasi dan _ABI fragments_ yang akan digunakan oleh skrip.

### 2.1 Impor Dependencies

Buat file bernama **index.ts**, lalu tambahkan kode berikut untuk mengimpor _dependencies_ yang diperlukan.

```ts
/* index.ts */

import "dotenv/config";
import { createWalletClient, createPublicClient, http, parseUnits } from "viem";
import { privateKeyToAccount } from "viem/accounts";
import { sepolia } from "viem/chains";
import { bech32 } from "@scure/base";
```

### 2.2 Mendefinisikan Konstanta Konfigurasi

Tambahkan potongan kode berikut ke file **index.ts**. Konfigurasi ini menentukan RPC, _wallet private key_, alamat kontrak, serta parameter khusus _Cardano_ yang akan digunakan oleh skrip.

Variabel **CARDANO_RECIPIENT** dimuat dari file **.env** yang telah Anda buat sebelumnya.

**Catatan:**  
Anda dapat meninjau [_xReserve EVM contracts_](https://github.com/circlefin/evm-xreserve-contracts) di GitHub.

```ts
/* index.ts */

// ============ Configuration constants ============
const config = {
  // Public Ethereum Sepolia RPC and your private wallet key
  ETH_RPC_URL: process.env.RPC_URL || "https://ethereum-sepolia.publicnode.com",
  PRIVATE_KEY: process.env.PRIVATE_KEY as `0x${string}`,

  // Contract addresses on Ethereum Sepolia testnet
  X_RESERVE_CONTRACT:
    "0x008888878f94C0d87defdf0B07f46B93C1934442" as `0x${string}`,
  SEPOLIA_USDC_CONTRACT:
    "0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238" as `0x${string}`,

  // Deposit parameters for Cardano Preprod (works on testnet and mainnet)
  CARDANO_DOMAIN: 10004, // Cardano domain ID
  CARDANO_RECIPIENT: process.env.CARDANO_RECIPIENT || "", // Address to receive USDCx on Cardano Preprod
  DEPOSIT_AMOUNT: "20.00",
  MAX_FEE: "10.00",
};
```

### 2.3 Menyiapkan Contract ABI

Tambahkan potongan kode berikut ke file **index.ts**. Kode ini menambahkan _ABI fragments_ untuk kontrak **xReserve** dan **ERC-20**, yang memberi tahu **viem** bagaimana memformat dan mengirim panggilan kontrak saat skrip dijalankan.

**Catatan:**  
Untuk memahami lebih lanjut mengenai _contract ABI_, lihat panduan dari _QuickNode_ berjudul [**"What is an ABI?"**](https://www.quicknode.com/guides/ethereum-development/smart-contracts/what-is-an-abi).

```ts
/* index.ts */

// ============ Contract ABIs ============
const X_RESERVE_ABI = [
  {
    name: "depositToRemote",
    type: "function",
    stateMutability: "nonpayable",
    inputs: [
      { name: "value", type: "uint256" },
      { name: "remoteDomain", type: "uint32" },
      { name: "remoteRecipient", type: "bytes32" },
      { name: "localToken", type: "address" },
      { name: "maxFee", type: "uint256" },
      { name: "hookData", type: "bytes" },
    ],
    outputs: [],
  },
] as const;

const ERC20_ABI = [
  {
    name: "approve",
    type: "function",
    stateMutability: "nonpayable",
    inputs: [
      { name: "spender", type: "address" },
      { name: "amount", type: "uint256" },
    ],
    outputs: [{ name: "success", type: "bool" }],
  },
  {
    name: "balanceOf",
    type: "function",
    stateMutability: "view",
    inputs: [{ name: "account", type: "address" }],
    outputs: [{ name: "balance", type: "uint256" }],
  },
] as const;
```
