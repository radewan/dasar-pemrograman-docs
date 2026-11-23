## 1. Overview

### Purpose

Menampilkan rincian satu transaksi penjualan, termasuk informasi transaksi, item yang dibeli, dan ringkasan pembayaran.

### User Goals

- Melihat informasi lengkap suatu transaksi.
- Melihat item apa saja yang terjual.
- Mengetahui harga satuan, kuantitas, dan diskon setiap item.
- Melihat subtotal, diskon, total pembayaran, uang diterima, dan kembalian.
- Mencetak ulang struk.

## 3. Layout Structure

### Desktop

- Header halaman berisi tombol kembali, judul, dan tombol cetak.
- Informasi transaksi berupa nomor transaksi, tanggal, dan waktu.
- Tabel item transaksi menggunakan seluruh lebar konten.
- Ringkasan pembayaran ditampilkan di sisi kanan atau di bawah tabel.

### Tablet

- Menggunakan struktur layout seperti desktop.
- Tabel item dapat menggunakan horizontal scrolling jika diperlukan.
- Ringkasan pembayaran menyesuaikan lebar layar.

### Mobile

- Menggunakan layout satu kolom.
- Urutan konten:
  1. Header dan tombol kembali.
  2. Informasi transaksi.
  3. Tabel item transaksi.
  4. Ringkasan pembayaran.
  5. Tombol cetak.
- Tabel item menggunakan horizontal scrolling (`overflow-x: auto`) jika lebar kolom melebihi viewport.
- Seluruh informasi item tetap dapat diakses melalui horizontal scrolling.
- Ringkasan pembayaran menggunakan lebar penuh.

## 4. UI Components

### Transaction Information

| Component     | Description              | Example            | Required |
| ------------- | ------------------------ | ------------------ | -------- |
| No. Transaksi | Nomor unik transaksi     | `TRX-20261123-001` | Yes      |
| Tanggal       | Tanggal transaksi dibuat | `23 November 2026` | Yes      |
| Waktu         | Waktu transaksi dibuat   | `14:30`            | Yes      |

### Transaction Items Table

| Column       | Description                            | Example          | Required |
| ------------ | -------------------------------------- | ---------------- | -------- |
| No.          | Nomor urut item                        | `1`              | Yes      |
| Produk       | Nama produk yang dibeli                | `Indomie Goreng` | Yes      |
| Harga Satuan | Harga produk saat transaksi dibuat     | `Rp3.500`        | Yes      |
| Qty          | Jumlah produk yang dibeli              | `3`              | Yes      |
| Diskon       | Potongan harga produk                  | `Rp500`          | Yes      |
| Subtotal     | Total harga item setelah diskon produk | `Rp10.000`       | Yes      |

### Numbering Rules

- Nomor urut dimulai dari `1`.
- Nomor urut mengikuti urutan item dalam transaksi.
- Nomor urut tidak disimpan di database karena hanya digunakan untuk tampilan.

### Payment Summary

| Component                      | Description                                    | Example    | Required |
| ------------------------------ | ---------------------------------------------- | ---------- | -------- |
| Subtotal                       | Total harga seluruh item sebelum diskon produk | `Rp50.000` | Yes      |
| Diskon Produk                  | Akumulasi seluruh diskon produk                | `Rp2.000`  | Yes      |
| Subtotal Setelah Diskon Produk | Subtotal setelah potongan harga produk         | `Rp48.000` | Yes      |
| Diskon Transaksi               | Potongan tambahan pada transaksi               | `Rp3.000`  | Yes      |
| Total                          | Total pembayaran setelah seluruh diskon        | `Rp45.000` | Yes      |
| Uang Diterima                  | Jumlah uang yang diterima dari pelanggan       | `Rp50.000` | Yes      |
| Kembalian                      | Selisih uang diterima dengan total pembayaran  | `Rp5.000`  | Yes      |

### Other Components

| Component      | Description                  | Required | Reference |
| -------------- | ---------------------------- | -------- | --------- |
| Tombol Kembali | Kembali ke halaman transaksi | Yes      | Button    |
| Tombol Cetak   | Memicu `window.print()`      | Yes      | Button    |

## 5. User Interactions

### Mencetak Struk

**Trigger:** Klik tombol "Cetak".

**Expected behavior:**

1. Browser membuka dialog print.
2. Halaman menggunakan layout khusus struk.
3. Informasi transaksi, item, diskon, dan pembayaran ditampilkan dalam hasil cetak.
4. Tidak ada perubahan data transaksi.

**Constraints:**

- Menggunakan `window.print()`.
- Menggunakan CSS `@media print` untuk mengatur tampilan cetak.
- Elemen navigasi, tombol, dan komponen yang tidak diperlukan disembunyikan ketika mencetak.
- Hasil cetak harus tetap terbaca pada ukuran kertas struk yang didukung.

### Membuka Produk

**Trigger:** Klik nama produk pada tabel item.

**Expected behavior:**

1. Navigasi ke `/products/:id`.
2. Jika produk masih tersedia, nama produk dapat diklik.
3. Jika produk sudah dihapus atau tidak tersedia, nama produk tetap ditampilkan sebagai teks biasa tanpa tautan.
4. Informasi produk dalam transaksi tetap menggunakan snapshot saat transaksi dibuat.

### Kembali ke Riwayat Transaksi

**Trigger:** Klik tombol "Kembali".

**Expected behavior:**

1. Navigasi kembali ke `/transactions`.
2. Jika memungkinkan, pertahankan filter dan halaman sebelumnya.

## 7. Validation & Business Rules

| Field / Rule      | Requirement                                         | Error message             |
| ----------------- | --------------------------------------------------- | ------------------------- |
| Transaction ID    | Transaksi harus ditemukan dan dimiliki user         | Transaksi tidak ditemukan |
| Transaction Items | Transaksi harus memiliki minimal satu item          | —                         |
| Quantity          | Jumlah item harus sesuai snapshot transaksi         | —                         |
| Payment           | Total pembayaran harus sesuai perhitungan transaksi | —                         |

### Business Rules

- Halaman bersifat read-only.
- Harga dan diskon yang ditampilkan adalah nilai snapshot saat transaksi dibuat, bukan nilai produk saat ini.
- Nama produk menggunakan snapshot saat transaksi dibuat agar tetap tersedia meskipun produk telah dihapus.
- Subtotal merupakan total harga seluruh item sebelum diskon produk.
- Diskon produk dihitung berdasarkan diskon masing-masing item.
- Subtotal setelah diskon produk merupakan subtotal dikurangi akumulasi diskon produk.
- Diskon transaksi diterapkan setelah diskon produk.
- Total = subtotal setelah diskon produk − diskon transaksi.
- Kembalian = uang diterima − total.
- Semua transaksi menggunakan pembayaran tunai.
- Uang diterima dan kembalian selalu ditampilkan.
- Jika tidak ada diskon, tampilkan `Rp0`.
- Semua nominal menggunakan format mata uang Rupiah (IDR).

## 8. API & Data Integration

### Data Dependencies

- Informasi transaksi milik user.
- Nomor transaksi.
- Tanggal dan waktu transaksi.
- Item transaksi beserta snapshot nama produk.
- Snapshot harga satuan.
- Kuantitas setiap item.
- Diskon setiap item.
- Subtotal setiap item.
- Subtotal transaksi.
- Total diskon produk.
- Diskon transaksi.
- Total pembayaran.
- Uang diterima dan kembalian.
- Informasi warung untuk kebutuhan struk.

## 9. Accessibility

- Tabel item memiliki header semantik.
- Ringkasan pembayaran menggunakan markup deskriptif, bukan hanya visual.
- Tombol cetak memiliki accessible name yang jelas.
- Tombol kembali dapat diakses menggunakan keyboard.
- Tabel item pada mobile dapat diakses menggunakan keyboard dan horizontal scrolling.
- Layout print memiliki kontras dan ukuran font yang memadai.
- Informasi transaksi tidak hanya dibedakan menggunakan warna.

## 10. Acceptance Criteria

- [ ] Detail hanya dapat diakses untuk transaksi milik user.
- [ ] Nomor transaksi tampil dengan benar.
- [ ] Tanggal dan waktu transaksi tampil dengan benar.
- [ ] Semua item transaksi tampil.
- [ ] Nomor urut item tampil dengan benar.
- [ ] Nama produk tampil sesuai snapshot transaksi.
- [ ] Harga satuan tampil sesuai snapshot transaksi.
- [ ] Kuantitas dan diskon setiap item tampil dengan benar.
- [ ] Subtotal setiap item tampil dengan benar.
- [ ] Subtotal transaksi tampil sebelum diskon produk.
- [ ] Diskon produk dan diskon transaksi tampil dengan benar.
- [ ] Total pembayaran tampil setelah seluruh diskon.
- [ ] Uang diterima dan kembalian tampil benar.
- [ ] Tombol cetak memicu dialog print browser.
- [ ] Hasil cetak menggunakan layout struk yang sesuai.
- [ ] Nama produk yang sudah dihapus tetap tampil sebagai teks.
- [ ] Akses ke transaksi milik user lain menghasilkan 403/404.
- [ ] Layout responsif di mobile, tablet, dan desktop.
- [ ] Tabel item dapat diakses melalui horizontal scrolling pada mobile.
- [ ] Tidak ada aksi edit atau hapus transaksi.
