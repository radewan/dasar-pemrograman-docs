# Product Goals

## Tujuan

1. Memudahkan pemilik warung mencatat transaksi penjualan secara digital
2. Membantu pemilik warung memantau stok barang secara real-time
3. Memberikan gambaran performa penjualan melalui riwayat transaksi

## Indikator Keberhasilan

| Indikator | Target | Cara mengukur |
|---|---|---|
| Semua fitur Must berjalan tanpa error | 6/6 fitur | Manual testing per fitur |
| Transaksi berhasil dicatat dan muncul di riwayat transaksi | 100% | Verifikasi data di `/transactions` |
| Data antar warung tidak bocor (multi-tenant) | Tidak ada kebocoran | Testing dengan 2 akun berbeda |

## Bukan Tujuan

- Bukan aplikasi untuk skala supermarket atau toko besar
- Bukan aplikasi mobile native (Android/iOS)
- Bukan untuk menggantikan sistem akuntansi lengkap

## Asumsi

| Asumsi | Status | Cara validasi |
|---|---|---|
| Pemilik warung menggunakan perangkat dengan browser modern | Belum tervalidasi | Testing di browser target |
| Koneksi internet tersedia saat export PDF/Excel | Belum tervalidasi | Konfirmasi dengan klien |
