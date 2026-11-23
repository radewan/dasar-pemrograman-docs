# Product Detail Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Detail Produk |
| Route | `/products/:id` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Menampilkan detail satu produk sekaligus memungkinkan user mengubah datanya dan menyesuaikan stok.

### User Goals

- Melihat informasi lengkap produk dan sisa stok.
- Mengubah data produk.
- Menambah atau mengurangi stok dengan alasan yang tercatat.
- Melihat riwayat mutasi stok produk ini.

### Preconditions

- User sudah login.
- Produk dengan `:id` ada dan milik warung user.

## 2. Entry Points & Navigation

### Entry Points

- Klik baris/tombol "Detail" pada `/products`.
- Link langsung ke `/products/:id`.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Kembali | `/products` | — |
| Klik "Riwayat Stok" | `/products/:id/stock` | — |
| Simpan perubahan | Tetap di `/products/:id` | Berhasil |
| Hapus produk | `/products` | Setelah konfirmasi |

### Access Rules

- Hanya user yang sudah login.
- Produk milik warung lain → 403 atau 404.

## 3. Layout Structure

### Desktop

- Header: nama produk + tombol kembali.
- Dua kolom: kiri form data produk, kanan panel stok (stok saat ini + form penyesuaian stok).
- Tombol "Riwayat Stok" di panel stok.

### Tablet

- Dua kolom tetap, proporsi disesuaikan.

### Mobile

- Satu kolom berurutan: data produk → panel stok → tombol riwayat stok.

### Content Hierarchy

1. Nama produk + tombol kembali
2. Form data produk
3. Stok saat ini
4. Form penyesuaian stok
5. Link riwayat stok
6. Aksi hapus (zona berbahaya, di paling bawah)

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Form data produk | Nama, kategori, harga jual, satuan | Yes | Input |
| Stok saat ini | Nilai read-only | Yes | Text |
| Form penyesuaian stok | Tipe (masuk/keluar/penyesuaian), jumlah, alasan | Yes | Input |
| Tombol "Riwayat Stok" | Navigasi ke `/products/:id/stock` | Yes | Button |
| Tombol simpan | Menyimpan perubahan data produk | Yes | Button |
| Tombol hapus | Menonaktifkan produk | Yes | Button (danger) |
| Dialog konfirmasi hapus | Konfirmasi sebelum menonaktifkan | Yes | Dialog |
| Alert sukses/error | Feedback aksi | Yes | Alert |

## 5. User Interactions

### Mengubah Data Produk

**Trigger:** Klik "Simpan".

**Expected behavior:**
1. Validasi di server.
2. Jika berhasil, alert sukses tampil dan data terbaru dirender.
3. Jika gagal, pesan error tampil per field.

**Constraints:**
- Stok tidak dapat diubah lewat form ini.

### Menyesuaikan Stok

**Trigger:** Submit form penyesuaian stok.

**Expected behavior:**
1. Validasi tipe, jumlah, dan alasan.
2. Server membuat mutasi stok baru dan memperbarui stok produk.
3. Stok saat ini dan riwayat diperbarui.
4. Alert sukses tampil.

**Constraints:**
- Tipe `keluar` tidak boleh membuat stok menjadi negatif.
- Alasan wajib diisi untuk tipe `penyesuaian`.
- Setiap perubahan stok selalu tercatat sebagai mutasi — tidak ada perubahan stok tanpa jejak.

### Melihat Riwayat Stok

**Trigger:** Klik "Riwayat Stok".

**Expected behavior:**
1. Navigasi ke `/products/:id/stock`.

### Menghapus Produk

**Trigger:** Klik "Hapus".

**Expected behavior:**
1. Dialog konfirmasi muncul menjelaskan produk akan dinonaktifkan.
2. Konfirmasi mengirim `POST /products/:id/delete`.
3. Redirect ke `/products` dengan alert sukses.

## 6. Page States

### Default

- Semua field terisi data produk saat ini; stok tampil read-only.

### Loading

- Tombol simpan/penyesuaian menampilkan loading dan nonaktif.

### Empty

- Tidak berlaku (halaman selalu menampilkan satu produk).

### Error

- Produk tidak ditemukan: halaman 404.
- Bukan milik warung user: 403/404.
- Validasi gagal: pesan per field.

### Success

- Alert sukses untuk simpan data dan untuk penyesuaian stok.

### Disabled / Permission Denied

- Tombol simpan nonaktif saat tidak ada perubahan atau saat request berjalan.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Nama | Wajib, maks. 100 karakter, unik per warung | Nama produk wajib diisi |
| Harga jual | Bilangan bulat ≥ 0 | Harga jual wajib diisi dengan angka yang valid |
| Kategori | Opsional, maks. 50 karakter | Kategori maksimal 50 karakter |
| Satuan | Opsional, maks. 20 karakter | Satuan maksimal 20 karakter |
| Tipe mutasi | Salah satu: masuk, keluar, penyesuaian | Tipe mutasi tidak valid |
| Jumlah mutasi | Bilangan bulat > 0 | Jumlah harus lebih dari 0 |
| Alasan | Wajib untuk tipe penyesuaian | Alasan wajib diisi untuk penyesuaian |
| Stok hasil | Tidak boleh negatif | Stok tidak boleh negatif |

### Business Rules

- Stok hanya berubah melalui mutasi; halaman ini tidak menulis stok secara langsung.
- Mutasi `masuk` menambah stok, `keluar` mengurangi, `penyesuaian` menyetel/mengoreksi sesuai jumlah dengan alasan wajib.
- Mutasi dari transaksi kasir bertipe `keluar` dan memiliki referensi transaksi; mutasi manual tidak punya referensi transaksi.

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Lihat detail | GET | `/products/:id` | — |
| Simpan data produk | POST | `/products/:id` | Form POST, CSRF token wajib |
| Sesuaikan stok | POST | `/products/:id/stock` | Membuat mutasi stok baru |
| Hapus (nonaktifkan) | POST | `/products/:id/delete` | Form POST |

### Request Data

- Data produk: `name`, `category`, `price`, `unit`.
- Mutasi: `type`, `quantity`, `reason`.

### Response Data

- Halaman detail dengan alert sukses atau pesan validasi.

### Error Handling

- 404 jika produk tidak ada; 403 jika bukan milik warung user.
- Error validasi dipetakan ke field masing-masing.

### Data Dependencies

- Produk milik warung user.
- Stok terkini (agregat mutasi).

## 9. Accessibility

- Semua input memiliki label yang terlihat.
- Error dikaitkan ke field melalui `aria-describedby`.
- Stok read-only tetap terbaca oleh screen reader.
- Dialog konfirmasi hapus menjebak fokus dan bisa ditutup dengan Escape.
- Tombol hapus memiliki accessible name yang jelas, bukan hanya warna merah.

## 10. Acceptance Criteria

- [ ] Detail produk hanya dapat diakses untuk produk milik warung user.
- [ ] Data produk dapat diubah dan tersimpan.
- [ ] Stok tidak dapat diubah lewat form data produk.
- [ ] Penyesuaian stok membuat catatan mutasi yang muncul di riwayat.
- [ ] Mutasi `keluar` yang melebihi stok ditolak.
- [ ] Alasan wajib untuk tipe penyesuaian.
- [ ] Stok saat ini diperbarui setelah penyesuaian.
- [ ] Tombol "Riwayat Stok" membuka `/products/:id/stock`.
- [ ] Hapus menampilkan konfirmasi dan menonaktifkan produk.
- [ ] Akses ke produk warung lain menghasilkan 403/404.
- [ ] Layout dua kolom di desktop dan satu kolom di mobile.

## 11. Out of Scope

- Edit riwayat mutasi stok yang sudah tercatat.
- Unggah gambar produk.
- Riwayat perubahan harga produk.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Apakah `penyesuaian` menyetel nilai absolut atau menambah/mengurangi seperti masuk/keluar? | Perhitungan stok dan label form | Open |
| Apakah produk boleh dihapus permanen jika belum punya transaksi? | Perilaku hapus | Open — sementara selalu soft delete |

## 13. References

- [Products page](products-page.md)
- [Product stock history page](product-stock-history-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
