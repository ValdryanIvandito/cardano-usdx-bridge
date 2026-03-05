# **Gambaran Umum xReserve**

**_xReserve_** adalah infrastruktur interoperabilitas yang memungkinkan para pengembang _blockchain_ untuk menerbitkan USDCx di jaringan mereka sendiri. Sistem ini didukung oleh pengesahan terprogram (_programmatic attestations_) dan _smart contract_ yang diimplementasikan oleh _Circle_ yang menyimpan USDC sebagai cadangan (_reserve_) di _source blockchain_ seperti **_Ethereum_**. USDCx dicetak (_mint_) melalui _xReserve_ dapat saling berinteraksi serta dengan jaringan USDC yang lebih luas.

## **Manfaat Utama**

_xReserve_ menawarkan berbagai manfaat bagi jaringan _blockchain_, para pengembang, dan pengguna akhir:

- **Likuiditas hari pertama:** Token yang dicetak menggunakan _xReserve_ terhubung dengan jaringan USDC yang lebih luas, sehingga pengguna dapat dengan mudah memindahkan token antar _blockchain_.

- **Interoperabilitas:** Pengguna dapat melakukan transfer 1:1 antara USDCx dan USDC.

- **Diimplementasikan oleh _Circle_:** _xReserve_ menyimpan cadangan USDC dalam _smart contract_ yang diimplementasikan oleh _Circle_.

- **Asumsi kepercayaan yang diminimalkan:** Mekanisme pengesahan (_attesters_) _xReserve_ memverifikasi proses deposit dan pembakaran token (_burn_), sehingga mengurangi ketergantungan pada layanan pihak ketiga untuk mengonversi USDC.

