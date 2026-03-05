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

## Langkah 3: Mengeksekusi Deposit xReserve

Langkah ini menjelaskan cara mengimplementasikan logika utama untuk memeriksa saldo, menyetujui penggunaan USDC, dan memanggil _xReserve_ di _Ethereum Sepolia_ sehingga _Cardano Preprod_ dapat melakukan _mint_ USDCx yang sesuai.

### 3.1 Menambahkan Fungsi Utama

Tambahkan kode berikut ke file **index.ts**. Kode ini akan:

- Memverifikasi apakah **PRIVATE_KEY** dan **CARDANO_RECIPIENT** telah dikonfigurasi.
- Membuat _Ethereum Sepolia wallet client_ dan mencatat alamat asal.
- Memeriksa saldo ETH untuk biaya gas.
- Menghitung nilai deposit, biaya maksimum, dan payload penerima (USDC menggunakan 6 desimal).
- Memastikan saldo USDC pada _wallet_ mencukupi untuk jumlah deposit.
- Memberikan persetujuan kepada kontrak _xReserve_ untuk menggunakan USDC.
- Memanggil fungsi _depositToRemote_ untuk mengirim permintaan deposit sehingga _Cardano Preprod_ dapat melakukan _mint_ USDCx untuk alamat **CARDANO_RECIPIENT**.

Fungsi bantu **parseCardanoAddress** digunakan untuk memvalidasi tipe alamat dan membangun **remoteRecipient** serta **hookData** untuk fungsi **depositToRemote**. Alamat tipe dasar menggunakan _staking credential_ pada **hookData** sehingga USDCx dikirim ke alamat wallet yang tepat. Alamat tipe _enterprise_ menggunakan nilai **hookData** kosong.

```ts
/* index.ts */

// ============ Parse Cardano address (remoteRecipient + hookData) ============
const CREDENTIAL_TAG = {
  KEY_HASH: "0x00000001",
  SCRIPT_HASH: "0x00000002",
} as const;

const SUPPORTED_ADDRESS_TYPES = {
  BASE: [0, 1, 2, 3], // Base addresses: payment + staking credentials
  ENTERPRISE: [6, 7], // Enterprise addresses: payment credential only
};

function parseCardanoAddress(cardanoAddress: string): {
  remoteRecipient: `0x${string}`;
  hookData: `0x${string}`;
} {
  const decoded = bech32.decode(cardanoAddress, 1000);
  const bytes = bech32.fromWords(decoded.words);
  const headerByte = bytes[0];
  const addressType = headerByte >> 4;

  const allSupported = [
    ...SUPPORTED_ADDRESS_TYPES.BASE,
    ...SUPPORTED_ADDRESS_TYPES.ENTERPRISE,
  ];
  if (!allSupported.includes(addressType)) {
    throw new Error(
      `Unsupported Cardano address type ${addressType}. ` +
        `Use a Base (addr1q...) or Enterprise (addr1v...) address.`,
    );
  }

  // remoteRecipient: [4-byte tag][28-byte payment credential]
  const isScriptPayment = (addressType & 1) === 1;
  const tag = isScriptPayment
    ? CREDENTIAL_TAG.SCRIPT_HASH
    : CREDENTIAL_TAG.KEY_HASH;
  const paymentCredential = bytes.slice(1, 29);
  const credentialHex = Array.from(paymentCredential)
    .map((b) => b.toString(16).padStart(2, "0"))
    .join("");
  const remoteRecipient = `${tag}${credentialHex}` as `0x${string}`;

  // hookData: empty for Enterprise, 95 bytes for Base
  if (SUPPORTED_ADDRESS_TYPES.ENTERPRISE.includes(addressType)) {
    return { remoteRecipient, hookData: "0x" as `0x${string}` };
  }

  // Base address: extract staking credential
  if (bytes.length < 57) {
    throw new Error("Malformed Base address: expected 57 bytes");
  }

  const stakingCredential = bytes.slice(29, 57);
  const isStakingScript = (addressType & 2) === 2;
  const stakingTag = isStakingScript ? "02" : "01";

  // hookData layout (95 bytes):
  // txHash(32) + txIdx(1) + datumTag(1) + datum(32) + stakingTag(1) + stakingCred(28)
  const stakingHex = Array.from(stakingCredential)
    .map((b) => b.toString(16).padStart(2, "0"))
    .join("");
  const hookData =
    `0x${"00".repeat(66)}${stakingTag}${stakingHex}` as `0x${string}`;

  return { remoteRecipient, hookData };
}

// ============ Main function ============
async function main() {
  if (!config.PRIVATE_KEY) {
    throw new Error("PRIVATE_KEY must be set in your .env file");
  }

  if (!config.CARDANO_RECIPIENT) {
    throw new Error("CARDANO_RECIPIENT must be set in your .env file");
  }

  const account = privateKeyToAccount(config.PRIVATE_KEY);
  const client = createWalletClient({
    account,
    chain: sepolia,
    transport: http(config.ETH_RPC_URL),
  });

  const publicClient = createPublicClient({
    chain: sepolia,
    transport: http(config.ETH_RPC_URL),
  });

  console.log(`Ethereum wallet address: ${account.address}`);

  const nativeBalance = await publicClient.getBalance({
    address: account.address,
  });
  console.log(
    `Native balance: ${nativeBalance.toString()} wei (${(
      Number(nativeBalance) / 1e18
    ).toFixed(6)} ETH)`,
  );
  if (nativeBalance === 0n)
    throw new Error("Insufficient native balance for gas fees");

  const value = parseUnits(config.DEPOSIT_AMOUNT, 6);
  const maxFee = parseUnits(config.MAX_FEE, 6);
  const { remoteRecipient, hookData } = parseCardanoAddress(
    config.CARDANO_RECIPIENT,
  );

  console.log(
    `\nDepositing ${config.DEPOSIT_AMOUNT} USDC to Cardano recipient: ${config.CARDANO_RECIPIENT}`,
  );

  const usdcBalance = await publicClient.readContract({
    address: config.SEPOLIA_USDC_CONTRACT,
    abi: ERC20_ABI,
    functionName: "balanceOf",
    args: [account.address],
  });
  console.log(
    `USDC balance: ${usdcBalance.toString()} (${(
      Number(usdcBalance) / 1e6
    ).toFixed(6)} USDC)`,
  );
  if (usdcBalance < value) {
    throw new Error(
      `Insufficient USDC balance. Required: ${(Number(value) / 1e6).toFixed(
        6,
      )} USDC`,
    );
  }

  const approveTxHash = await client.writeContract({
    address: config.SEPOLIA_USDC_CONTRACT,
    abi: ERC20_ABI,
    functionName: "approve",
    args: [config.X_RESERVE_CONTRACT, value],
  });
  console.log("Approval tx hash:", approveTxHash);
  console.log("Waiting for approval confirmation...");

  await publicClient.waitForTransactionReceipt({ hash: approveTxHash });
  console.log("✅ Approval confirmed");

  const depositTxHash = await client.writeContract({
    address: config.X_RESERVE_CONTRACT,
    abi: X_RESERVE_ABI,
    functionName: "depositToRemote",
    args: [
      value,
      config.CARDANO_DOMAIN,
      remoteRecipient,
      config.SEPOLIA_USDC_CONTRACT,
      maxFee,
      hookData,
    ],
  });

  console.log("Deposit tx hash:", depositTxHash);
  console.log(
    "✅ Transaction submitted. You can track this on Sepolia Etherscan.",
  );
}

main().catch((error) => {
  console.error("❌ Error:", error);
  process.exit(1);
});
```

### 3.2 Menjalankan Skrip

Jalankan perintah berikut di terminal:

```bash
npx tsx index.ts
```

### 3.3 Memverifikasi Deposit

Setelah skrip selesai dieksekusi, temukan hash transaksi **deposit** pada keluaran terminal, lalu tempelkan ke [**Sepolia Etherscan**](https://sepolia.etherscan.io/) untuk mengonfirmasi transaksi deposit Anda. Wallet _Cardano Preprod_ penerima akan menerima USDCx yang telah di-_mint_. Anda dapat memverifikasi token pada [**Preprod CardanoScan**](https://preprod.cardanoscan.io/).

[← Previous](blockchain-domain-xreserve.md) | [Table of Contents](README.MD#daftar-isi) | [Next →](withdrawal.md)
