# **Cara Kerja xReserve**

_xReserve_ terdiri dari dua layanan utama: _smart contract_ _onchain_ dan _attestation system_ _offchain_. Secara bersama-sama, kedua layanan ini beserta layanan terkait di _remote blockchain_ memungkinkan pengguna untuk mendepositkan USDC di _source blockchain_ guna menerima USDCx di _remote blockchain_. Selanjutnya, pengguna dapat _burn_ USDCx untuk melakukan _withdrawal_ USDC yang disimpan dalam _xReserve_.

Berikut adalah diagram yang menunjukkan bagaimana _xReserve_ menangani proses _deposit_ dan _withdrawal_.

![xReserve Workflow](./assets/1.png)

## **Proses Deposit ke xReserve**

Langkah-langkah berikut terjadi ketika pengguna mendepositkan USDC ke _xReserve_:

1. Pengguna mendepositkan USDC dari aplikasi _wallet_ mereka ke _smart contract_ _xReserve_ di _source blockchain_.
2. Kontrak _xReserve_ mencatat event deposit, mengunci dana tersebut, dan menahannya sebagai cadangan.
3. _Attestation service_ _xReserve_ menghasilkan dan menandatangani bukti _deposit attestation_.
4. _Attestation service_ _remote blockchain_ mengambil bukti _deposit attestation_ yang telah ditandatangani.
5. _remote blockchain_ mencetak USDCx di jaringannya dan mencatat event _mint_.
6. Kontrak token di _remote blockchain_ mendepositkan USDCx yang baru dicetak ke aplikasi _wallet_ pengguna di _remote blockchain_ tersebut.

Setelah proses deposit selesai, pengguna menerima USDCx dalam jumlah yang setara di _remote blockchain_.

## **Proses Withdrawal dari xReserve**

Selanjutnya, langkah-langkah berikut ini terjadi ketika pengguna _withdraw_ USDC dari _xReserve_:

1. Pengguna mengajukan _burn_ USDCx di _remote blockchain_ serta _withdrawal_ USDC di _destination blockchain_.
2. Kontrak token yang ada di _remote blockchain_ melakukan _burn_ USDCx dan mencatat event _burn_.
3. _Attestation service_ di _remote blockchain_ menghasilkan dan menandatangani pernyataan niat _burn_ secara _offchain_.
4. _Attestation service_ di _remote blockchain_ meneruskan niat _burn_ beserta tanda tangannya ke _xReserve_.
5. _xReserve_ memverifikasi proses _burn_ tersebut dan menerbitkan _withdrawal attestation_.
6. _xReserve_ melepaskan USDC ke dompet pengguna di _destination blockchain_.

Setelah proses _withdrawal_ selesai, pengguna menerima USDC di _source blockchain_.

**Catatan:** Sebagai bagian dari proses _withdrawal_ yang sama, _xReserve_ dapat meneruskan dana ke _blockchain_ lain. Hal ini memungkinkan pengguna untuk _withdraw_ dana dari blockchain selain _source blockchain_ tanpa melakukan transaksi _crosschain_.

