# Products Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Produk |
| Route | `/products` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Menampilkan daftar produk warung dan menyediakan penambahan produk baru melalui modal.

### User Goals

- Melihat seluruh produk beserta harga dan sisa stok.
- Mencari produk dengan cepat.
- Menambahkan produk baru.

### Preconditions

- User sudah login dan memiliki warung.

## 2. Entry Points & Navigation

### Entry Points

- Menu "Produk" di sidebar.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Klik baris / tombol "Detail" | `/products/:id` | — |
| Simpan produk baru berhasil | `/products` | Modal tertutup, daftar diperbarui |
| Klik "Riwayat Stok" di halaman detail | `/products/:id/stock` | — |

### Access Rules

- Hanya user yang sudah login.
- Data difilter berdasarkan warung user (multi-tenant).

## 3. Layout Structure

### Desktop

- Judul halaman + tombol Export (PDF, Excel) + tombol "Tambah Produk" di kanan atas.
- Search box di atas tabel.
- Tabel produk di bawahnya, pagination di bawah tabel.

### Tablet

- Sama seperti desktop; kolom tabel yang kurang penting disembunyikan.

### Mobile

- Daftar dirender sebagai kartu (bukan tabel) berisi nama, harga, stok, dan aksi.
- Tombol Export menjadi menu dropdown.
- Tombol "Tambah Produk" menjadi tombol penuh di atas daftar.

### Content Hierarchy

1. Judul + tombol tambah
2. Search
3. Daftar produk
4. Pagination

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Search box | Filter berdasarkan nama produk | Yes | Input |
| Tabel / daftar produk | Nama, kategori, harga jual, stok, satuan, aksi | Yes | Table |
| Tombol "Tambah Produk" | Membuka modal tambah | Yes | Button |
| Modal tambah produk | Form produk baru | Yes | Dialog |
| Pagination | 10/25/50 per halaman | Yes | Pagination |
| Tombol Export PDF | Generate PDF daftar produk + stok di browser | Yes | Button |
| Tombol Export Excel | Generate `.xlsx` daftar produk + stok di browser | Yes | Button |
| Dialog konfirmasi hapus | Konfirmasi sebelum menonaktifkan produk | Yes | Dialog |
| Alert sukses/error | Feedback setelah aksi | Yes | Alert |

## 5. User Interactions

### Mencari Produk

**Trigger:** Mengetik di search box.

**Expected behavior:**
1. Daftar difilter berdasarkan nama (case-insensitive, substring).
2. Pencarian dijalankan di server dan memperbarui query string.
3. Hasil tetap dalam halaman pertama.

**Constraints:**
- Debounce ±300ms agar tidak mengirim request setiap ketikan.

### Menambah Produk

**Trigger:** Klik "Tambah Produk".

**Expected behavior:**
1. Modal terbuka dengan form kosong.
2. Validasi dijalankan di server saat submit.
3. Jika berhasil, modal tertutup, alert sukses tampil, daftar diperbarui.
4. Jika gagal, modal tetap terbuka dengan pesan error per field.

**Constraints:**
- Fokus otomatis ke field pertama saat modal terbuka.
- Escape dan klik di luar modal menutup modal tanpa menyimpan.

### Menghapus Produk

**Trigger:** Klik aksi "Hapus".

**Expected behavior:**
1. Dialog konfirmasi muncul.
2. Konfirmasi mengirim `POST /products/:id/delete`.
3. Produk dinonaktifkan (soft delete), tidak lagi tampil di daftar dan kasir.
4. Riwayat transaksi dan mutasi stok lama tetap utuh.

**Constraints:**
- Hapus permanen tidak dilakukan agar riwayat transaksi dan mutasi stok lama tidak rusak.

### Export Data Produk

**Trigger:** Klik "Export PDF" atau "Export Excel".

**Expected behavior:**
1. JavaScript memanggil `/api/products/export` dengan parameter filter yang sama dengan tampilan.
2. File di-generate di browser menggunakan jsPDF atau SheetJS.
3. File langsung diunduh.
4. Tombol menampilkan loading selama proses dan nonaktif.

**Constraints:**
- Export bergantung pada koneksi internet untuk memuat library CDN.
- Jika gagal, tampilkan alert error dan aktifkan kembali tombol.
- Export mencakup seluruh hasil filter, bukan hanya halaman yang sedang tampil.

### Pagination

**Trigger:** Klik nomor halaman atau mengubah jumlah per halaman.

**Expected behavior:**
1. Query string diperbarui (`page`, `per_page`).
2. Daftar dimuat ulang sesuai halaman.

## 6. Page States

### Default

- Daftar produk terisi, diurutkan berdasarkan nama.

### Loading

- Skeleton baris tabel saat memuat; tombol simpan menampilkan loading.

### Empty

- Belum ada produk: "Belum ada produk. Tambahkan produk pertama Anda." + tombol "Tambah Produk".
- Pencarian tanpa hasil: "Tidak ada produk yang cocok dengan '<kata>'."

### Error

- Gagal memuat: alert error + tombol "Coba lagi".
- Gagal menyimpan: pesan per field di dalam modal.
- Gagal menghapus: alert error, produk tetap ada.

### Success

- Alert sukses: "Produk berhasil ditambahkan" / "Produk berhasil dinonaktifkan".

### Disabled / Permission Denied

- Tombol simpan nonaktif saat request berjalan.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Nama | Wajib, maks. 100 karakter | Nama produk wajib diisi |
| Nama | Unik per warung (case-insensitive) | Nama produk sudah digunakan |
| Kategori | Opsional, maks. 50 karakter | Kategori maksimal 50 karakter |
| Harga jual | Wajib, bilangan bulat ≥ 0 | Harga jual wajib diisi dengan angka yang valid |
| Stok awal | Wajib saat tambah, bilangan bulat ≥ 0 | Stok awal tidak boleh negatif |
| Satuan | Opsional, maks. 20 karakter | Satuan maksimal 20 karakter |

### Business Rules

- Stok awal pada saat tambah produk membuat mutasi stok bertipe `masuk` dengan alasan "Stok awal".
- Setelah dibuat, stok tidak diubah langsung dari halaman ini — perubahan stok lewat halaman detail produk.
- Kategori berupa teks bebas dengan saran dari kategori yang sudah ada (belum ada halaman manajemen kategori terpisah).
- Produk yang dinonaktifkan tidak muncul di kasir dan tidak bisa dipilih pada transaksi baru.

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Daftar produk | GET | `/products` | Mendukung `q`, `page`, `per_page` |
| Data export produk | GET | `/api/products/export` | JSON, parameter sama dengan halaman |
| Tambah produk | POST | `/products` | Form POST, CSRF token wajib |
| Hapus (nonaktifkan) | POST | `/products/:id/delete` | Form POST |

### Request Data

- `name`, `category`, `price`, `initial_stock`, `unit`.

### Response Data

- Halaman daftar dengan alert sukses atau pesan validasi.
- Modal tambah dirender ulang dengan nilai yang diisi saat validasi gagal.

### Error Handling

- Error validasi dipetakan ke field masing-masing.
- Error server ditampilkan sebagai alert di atas tabel.

### Data Dependencies

- Produk aktif milik warung user.
- Daftar kategori yang sudah ada (untuk saran).

## 9. Accessibility

- Search box memiliki label yang terlihat.
- Modal memiliki `role="dialog"`, `aria-modal="true"`, dan label judul.
- Fokus terjebak di dalam modal saat terbuka dan kembali ke tombol pemicu saat ditutup.
- Error field dikaitkan dengan input melalui `aria-describedby`.
- Tabel memiliki header kolom yang semantik (`<th scope="col">`).
- Tombol aksi memiliki accessible name yang jelas (bukan hanya ikon).

## 10. Acceptance Criteria

- [ ] Daftar hanya menampilkan produk milik warung user.
- [ ] Search memfilter berdasarkan nama dan memperbarui query string.
- [ ] Pagination berfungsi dengan pilihan 10/25/50 per halaman.
- [ ] Produk baru dapat ditambahkan melalui modal dan langsung muncul di daftar.
- [ ] Stok awal membuat catatan mutasi "Stok awal" yang terlihat di riwayat stok.
- [ ] Nama produk duplikat dalam satu warung ditolak dengan pesan yang jelas.
- [ ] Hapus menampilkan konfirmasi dan menonaktifkan produk.
- [ ] Produk nonaktif hilang dari daftar dan dari kasir, tetapi riwayatnya tetap ada.
- [ ] Empty state dan empty search state tampil dengan benar.
- [ ] Error validasi tampil per field tanpa menutup modal.
- [ ] Layout tabel di desktop dan kartu di mobile.
- [ ] Modal dapat dioperasikan penuh dengan keyboard.
- [ ] Export PDF menghasilkan file yang dapat dibuka.
- [ ] Export Excel menghasilkan file `.xlsx` yang dapat dibuka.
- [ ] Export memakai filter yang sama dan mencakup semua hasil filter, bukan hanya satu halaman.
- [ ] Tombol export nonaktif selama proses dan pulih setelah selesai/gagal.

## 11. Out of Scope

- Halaman manajemen kategori terpisah.
- Impor produk dari file (CSV/Excel).
- Unggah gambar produk.
- Barcode / SKU.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Apakah kategori perlu halaman manajemen sendiri? | Route dan model baru | Open — sementara teks bebas |
| Apakah harga beli (HPP) perlu disimpan? | Skema produk dan laporan laba | Open |
| Apakah perlu field barcode/SKU? | Skema produk dan pencarian di kasir | Open |
| Apakah "hapus" sebaiknya permanen atau tetap soft delete? | Perilaku data historis | Open — sementara soft delete |
| Apakah perlu penanda "stok menipis" dan berapa ambangnya? | Kolom/badge status di daftar | Open |

## 13. References

- [Product detail page](product-detail-page.md)
- [Kasir page](../kasir/kasir-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
