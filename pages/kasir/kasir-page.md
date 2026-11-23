# Kasir Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Kasir |
| Route | `/` |
| Status | Draft |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Halaman utama untuk mencatat transaksi penjualan: memilih produk, menyusun keranjang, dan menyelesaikan pembayaran.

### User Goals

- Menambahkan produk ke keranjang dengan cepat.
- Melihat total belanja sebelum membayar.
- Menyelesaikan pembayaran dan (opsional) mencetak struk.

### Preconditions

- User sudah login dan memiliki warung.
- Minimal ada satu produk aktif dengan stok tersedia.

## 2. Entry Points & Navigation

### Entry Points

- Menu "Kasir" di sidebar (`/`).
- Redirect setelah login berhasil.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Transaksi selesai | Tetap di `/` dengan keranjang kosong | Pembayaran berhasil |
| Klik nama produk di keranjang | — | Tidak ada navigasi (hanya kontrol qty) |

### Access Rules

- Hanya user yang sudah login.
- Produk yang ditampilkan hanya milik warung user (filter multi-tenant).
- Produk nonaktif tidak ditampilkan. Produk dengan stok 0 tetap tampil tetapi nonaktif dengan label "Habis".

## 3. Layout Structure

### Desktop

- Dua kolom: kiri area produk, kanan panel keranjang.
- Area produk: search box di atas, grid produk di bawahnya.
- Panel keranjang menempel di kanan dan mengisi tinggi area konten.

### Tablet

- Sama seperti desktop, grid produk menyesuaikan jumlah kolom.

### Mobile

- Satu kolom: search + grid produk.
- Keranjang ditampilkan sebagai bottom sheet yang bisa dibuka/ditutup, dengan ringkasan total selalu terlihat.

### Content Hierarchy

1. Search produk
2. Grid produk
3. Keranjang (item, qty, subtotal)
4. Ringkasan pembayaran (diskon, total, uang diterima, kembalian)
5. Tombol "Bayar"

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Search produk | Filter produk berdasarkan nama | Yes | Input |
| Product card | Nama, harga, sisa stok; klik untuk menambah ke keranjang | Yes | Card |
| Cart item | Nama produk, kontrol qty, subtotal | Yes | List item |
| Qty control | Tombol −/+ dan input jumlah | Yes | Input |
| Diskon selector | Memilih satu diskon aktif untuk transaksi | No | Select |
| Ringkasan total | Subtotal, diskon, total | Yes | Summary |
| Uang diterima | Input nominal uang dari pembeli | Yes | Input |
| Kembalian | Hasil hitung otomatis, read-only | Yes | Text |
| Tombol Bayar | Menyimpan transaksi | Yes | Button |
| Dialog sukses | Konfirmasi transaksi berhasil + opsi cetak struk | Yes | Dialog |

## 5. User Interactions

### Menambah Produk ke Keranjang

**Trigger:** Klik product card.

**Expected behavior:**
1. Jika produk belum ada di keranjang, tambahkan dengan qty 1.
2. Jika sudah ada, tambah qty sebanyak 1.
3. Subtotal dan total dihitung ulang.

**Constraints:**
- Qty tidak boleh melebihi stok tersedia.
- Jika stok habis, product card tidak bisa diklik.

### Mengubah Qty

**Trigger:** Klik tombol −/+, atau ubah input qty.

**Expected behavior:**
1. Qty minimal 1; menekan − pada qty 1 menghapus item dari keranjang.
2. Qty maksimal sebesar stok tersedia.
3. Total dihitung ulang.

### Memilih Diskon

**Trigger:** Memilih diskon dari selector.

**Expected behavior:**
1. Hanya diskon aktif yang muncul.
2. Total dihitung ulang setelah diskon diterapkan.
3. Diskon bisa dikosongkan kembali.

### Membayar

**Trigger:** Klik tombol "Bayar".

**Expected behavior:**
1. Validasi: keranjang tidak kosong, uang diterima ≥ total.
2. Nonaktifkan tombol dan tampilkan loading state selama request.
3. Server membuat transaksi dan mutasi stok keluar.
4. Tampilkan dialog sukses berisi total dan kembalian.
5. Keranjang dikosongkan.
6. User dapat memilih mencetak struk (`window.print()`) atau menutup dialog.

**Constraints:**
- Tidak boleh submit ganda selama request berjalan.
- Jika stok berubah dan menjadi tidak cukup di sisi server, transaksi ditolak dengan pesan yang jelas.

## 6. Page States

### Default

- Grid produk terisi, keranjang kosong, tombol "Bayar" nonaktif.

### Loading

- Grid produk menampilkan skeleton saat memuat.
- Tombol "Bayar" menampilkan loading dan nonaktif saat memproses.

### Empty

- Belum ada produk: tampilkan "Belum ada produk. Tambahkan produk terlebih dahulu." dengan link ke `/products`.
- Keranjang kosong: tampilkan "Keranjang masih kosong."

### Error

- Gagal memuat produk: pesan error + tombol coba lagi.
- Gagal menyimpan transaksi: pesan error di panel keranjang, keranjang tetap utuh.

### Success

- Dialog sukses: total, uang diterima, kembalian, opsi cetak struk.

### Disabled / Permission Denied

- Product card stok 0 ditampilkan nonaktif dengan label "Habis".
- Tombol "Bayar" nonaktif jika keranjang kosong atau uang diterima < total.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Keranjang | Minimal 1 item | Keranjang masih kosong |
| Qty | 1 ≤ qty ≤ stok tersedia | Jumlah melebihi stok tersedia |
| Uang diterima | ≥ total | Uang diterima kurang dari total |
| Diskon | Opsional, satu per transaksi | — |

### Business Rules

- Harga produk disimpan sebagai snapshot pada item transaksi; perubahan harga produk tidak mengubah transaksi lama.
- Transaksi berhasil membuat mutasi stok bertipe `keluar` untuk setiap item, dengan referensi ke transaksi.
- Nomor transaksi dibuat otomatis oleh server.
- Semua perhitungan uang memakai Rupiah tanpa desimal.
- Transaksi bersifat final — tidak ada pembatalan/edit setelah tersimpan (lihat Open Decisions).

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Muat produk | GET | `/` | Render grid produk aktif |
| Cari produk | GET | `/?q=<kata>` | Filter berdasarkan nama |
| Simpan transaksi | POST | `/` | Form POST, CSRF token wajib |

### Request Data

- Item transaksi: `product_id`, `qty`.
- `discount_id` (opsional).
- `amount_paid`.

### Response Data

- Halaman yang sama dengan dialog sukses dan ringkasan transaksi.
- Error validasi dikembalikan di context template.

### Error Handling

- Validasi server mengembalikan pesan per field.
- Kegagalan stok mengembalikan pesan "Stok produk ... tidak mencukupi" dan keranjang dipertahankan.

### Data Dependencies

- Produk aktif + stok warung.
- Daftar diskon aktif.

## 9. Accessibility

- Search box memiliki label yang terlihat.
- Product card dapat difokuskan dan diaktifkan dengan keyboard (Enter/Space).
- Tombol −/+ memiliki accessible label ("Kurangi jumlah", "Tambah jumlah").
- Perubahan total diumumkan melalui `aria-live="polite"`.
- Dialog sukses menjebak fokus dan dapat ditutup dengan Escape.
- Semua kontrol memiliki fokus yang terlihat jelas.

## 10. Acceptance Criteria

- [ ] Hanya produk aktif milik warung user yang tampil.
- [ ] Klik product card menambah item ke keranjang.
- [ ] Qty tidak dapat melebihi stok tersedia.
- [ ] Tombol "Bayar" nonaktif saat keranjang kosong atau uang diterima kurang.
- [ ] Kembalian dihitung otomatis dan benar.
- [ ] Transaksi berhasil mengurangi stok dan tercatat di riwayat stok.
- [ ] Transaksi muncul di `/transactions`.
- [ ] Submit ganda dicegah selama loading.
- [ ] Dialog sukses menampilkan total dan kembalian, dengan opsi cetak struk.
- [ ] Keranjang kosong setelah transaksi berhasil.
- [ ] Error stok dari server ditampilkan tanpa menghilangkan keranjang.
- [ ] Layout dua kolom di desktop dan satu kolom + bottom sheet di mobile.
- [ ] Seluruh alur dapat dioperasikan dengan keyboard.

## 11. Out of Scope

- Pembayaran non-tunai (QRIS, kartu, transfer).
- Pembatalan atau edit transaksi setelah tersimpan.
- Split bill / pembayaran sebagian.
- Cetak struk otomatis tanpa konfirmasi.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Metode bayar selain tunai? | Form pembayaran dan skema transaksi | Open |
| Apakah satu transaksi boleh memakai lebih dari satu diskon? | Logika perhitungan total | Open |
| Apakah transaksi boleh dibatalkan/refund? | Route tambahan dan aturan stok | Open |
| Pembulatan total (mis. ke Rp 100 terdekat)? | Perhitungan total | Open |

Jangan membuat asumsi untuk keputusan yang masih terbuka.

## 13. References

- [App layout](../app-layout.md)
- [Products page](../products/products-page.md)
- [Transaction detail page](../transactions/transaction-detail-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
- [Color palette](../../design/color-palette.md)
