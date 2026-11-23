# Page Specifications

Setiap halaman memiliki satu file spesifikasi. Salin `_page-template.md`, lalu isi bagian yang relevan.

## Struktur Folder

Satu folder per fitur, nama file `<halaman>-page.md`:

```
pages/
├── app-layout.md          # shell authenticated (cross-cutting)
├── account/               # login, profile
├── kasir/                 # halaman kasir (/)
├── products/              # list, detail, riwayat stok produk
├── inventory/             # riwayat stok semua produk
├── discounts/             # list, detail
├── transactions/          # riwayat, detail transaksi
```

Hanya `README.md`, `_page-template.md`, dan `app-layout.md` yang berada di root `pages/`.

## Aturan

- Cantumkan route dan kebutuhan autentikasi.
- Jelaskan layout desktop dan mobile.
- Daftarkan komponen dan interaksi pengguna.
- Dokumentasikan state penting: default, loading, empty, error, success.
- Referensikan kontrak API yang benar; jangan menebak endpoint atau bentuk response.
- Catat keputusan yang belum selesai di `Open Decisions`.
- Acceptance criteria harus dapat diuji.

## Status

Draft / Ready for Design / Ready for Development / In Progress / In Review / Done / Blocked
