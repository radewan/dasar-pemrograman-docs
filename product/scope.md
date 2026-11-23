# Product Scope

## In Scope

- Kasir — catat transaksi penjualan
- Produk — kelola produk dan kategori + export data produk/stok (PDF/Excel)
- Inventory/Stok — tambah, kurangi, riwayat stok
- Diskon — kelola diskon
- Riwayat transaksi — daftar transaksi penjualan + export (PDF/Excel)
- Profil pengguna — lihat dan ubah data profil sendiri

## Out of Scope

- Register mandiri
- Forgot password
- Login via OAuth/Google
- Deployment ke server publik
- Multi-user per warung
- Notifikasi stok habis
- Manajemen supplier
- Integrasi payment gateway

## Prioritas

| Fitur | Prioritas | Alasan | Status |
|---|---|---|---|
| Kasir | Must | Fungsi utama aplikasi POS | Planned |
| Produk | Must | Dibutuhkan sebelum kasir bisa berjalan | Planned |
| Inventory/Stok | Must | Dibutuhkan untuk pantau stok barang | Planned |
| Diskon | Must | Bagian dari alur transaksi kasir | Planned |
| Riwayat transaksi | Must | Pemilik warung perlu menelusuri penjualan | Planned |
| Profil pengguna | Must | Diperlukan karena ada menu Profil di dropdown topbar | Planned |

## Batasan

- Hanya bisa dijalankan lokal
- Tidak ada budget untuk server/hosting

## Dependensi

- Library CDN (jsPDF, SheetJS) butuh koneksi internet saat export PDF/Excel
- Akun warung dibuat manual oleh vendor sebelum user bisa login
