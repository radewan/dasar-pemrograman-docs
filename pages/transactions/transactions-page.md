# Transactions Page

## 1. Overview

| Properti       | Nilai                 |
| -------------- | --------------------- |
| Page name      | Transaksi             |
| Route          | `/transactions`       |
| Status         | Ready for Development |
| Priority       | Must                  |
| Authentication | Authenticated         |
| Responsive     | Yes                   |

### Purpose

Menampilkan riwayat seluruh transaksi penjualan warung.

### User Goals

- Melihat daftar transaksi yang pernah terjadi.
- Mencari transaksi berdasarkan nomor.
- Membuka detail transaksi.
- Mengunduh daftar transaksi sebagai PDF atau Excel.

### Preconditions

- User sudah login dan memiliki warung.

## 2. Entry Points & Navigation

### Entry Points

- Menu "Transaksi" di sidebar.

### Outgoing Navigation

| Trigger                      | Destination         | Conditions |
| ---------------------------- | ------------------- | ---------- |
| Klik baris / tombol "Detail" | `/transactions/:id` | —          |

### Access Rules

- Hanya user yang sudah login.
- Data difilter berdasarkan user.
- Halaman read-only: tidak ada pembuatan atau penghapusan transaksi dari halaman ini.

## 3. Layout Structure

### Desktop

- Judul halaman + tombol Export (PDF, Excel) di kanan atas.
- Search nomor transaksi + filter rentang tanggal.
- Tabel transaksi + pagination.

### Tablet

- Menggunakan layout tabel seperti desktop.
- Tabel dapat menggunakan horizontal scrolling jika lebar layar tidak mencukupi.
- Search dan filter menyesuaikan lebar layar.
- Tombol Export tetap tersedia.

### Mobile

- Tetap menggunakan tabel, bukan kartu.
- Tabel menggunakan horizontal scrolling (`overflow-x: auto`) apabila lebar kolom melebihi viewport.
- Seluruh kolom informasi transaksi tetap tersedia dan dapat diakses melalui horizontal scrolling.
- Kolom aksi menggunakan tombol "Detail".
- Search menggunakan lebar penuh.
- Filter tanggal ditempatkan dalam panel lipat.
- Tombol Export ditempatkan dalam menu dropdown.
- Header tabel tetap terlihat ketika tabel di-scroll secara vertikal jika memungkinkan.
- Pagination menyesuaikan lebar layar.

### Content Hierarchy

1. Judul
2. Filter
3. Daftar transaksi
4. Pagination

## 4. UI Components

### Transaction Table

| Column          | Description                                | Example              | Required |
| --------------- | ------------------------------------------ | -------------------- | -------- |
| No.             | Nomor urut transaksi pada halaman saat ini | `1`                  | Yes      |
| No. Transaksi   | Nomor unik transaksi                       | `TRX-20261123-001`   | Yes      |
| Tanggal & Waktu | Waktu transaksi dibuat                     | `23 Nov 2026, 14:30` | Yes      |
| Jumlah Item     | Total kuantitas barang dalam transaksi     | `5 item`             | Yes      |
| Subtotal        | Total harga sebelum diskon                 | `Rp150.000`          | Yes      |
| Diskon          | Total potongan harga transaksi             | `Rp15.000`           | Yes      |
| Total           | Total pembayaran setelah diskon            | `Rp135.000`          | Yes      |
| Aksi            | Tombol untuk membuka detail transaksi      | `Detail`             | Yes      |

### Numbering Rules

- Nomor urut dimulai dari `1` pada setiap halaman.
- Nomor urut mengikuti urutan transaksi yang ditampilkan.
- Nomor urut diperbarui ketika pengguna berpindah halaman.
- Nomor urut tidak disimpan di database karena hanya digunakan untuk kebutuhan tampilan.

### Other Components

| Component           | Description                                                  | Required | Reference   |
| ------------------- | ------------------------------------------------------------ | -------- | ----------- |
| Search nomor        | Filter berdasarkan nomor transaksi                           | Yes      | Input       |
| Start Date          | Menentukan tanggal awal transaksi                            | No       | Date Picker |
| End Date            | Menentukan tanggal akhir transaksi                           | No       | Date Picker |
| Tabel transaksi     | Menampilkan seluruh informasi transaksi sesuai kolom di atas | Yes      | Table       |
| Pagination          | 10/25/50 per halaman                                         | Yes      | Pagination  |
| Tombol Export PDF   | Generate PDF daftar transaksi di browser                     | Yes      | Button      |
| Tombol Export Excel | Generate `.xlsx` daftar transaksi di browser                 | Yes      | Button      |

### Date Filter Rules

- Start Date dan End Date menggunakan date picker.
- Kedua tanggal bersifat opsional.
- Jika hanya Start Date diisi, tampilkan transaksi sejak tanggal tersebut.
- Jika hanya End Date diisi, tampilkan transaksi sampai tanggal tersebut.
- Jika keduanya diisi, tampilkan transaksi dalam rentang tanggal tersebut.
- Tanggal awal dan akhir termasuk dalam hasil filter.
- End Date tidak boleh lebih awal daripada Start Date.
- Jika kedua tanggal dikosongkan, tampilkan seluruh transaksi.

## 5. User Interactions

### Memfilter Transaksi

**Trigger:** Mengetik nomor atau mengubah rentang tanggal.

**Expected behavior:**

1. Daftar dimuat ulang sesuai filter.
2. Filter tercermin di query string.
3. Halaman kembali ke 1.

**Constraints:**

- Search di-debounce ±300ms.

### Export Daftar Transaksi

**Trigger:** Klik "Export PDF" atau "Export Excel".

**Expected behavior:**

1. JavaScript memanggil `/api/transactions/export` dengan parameter filter yang sama dengan tampilan.
2. File di-generate di browser menggunakan jsPDF atau SheetJS.
3. File langsung diunduh.
4. Tombol menampilkan loading selama proses dan nonaktif.

**Constraints:**

- Export bergantung pada koneksi internet untuk memuat library CDN.
- Jika gagal, tampilkan alert error dan aktifkan kembali tombol.
- Export mencakup seluruh hasil filter, bukan hanya halaman yang sedang tampil.
- Data export mencakup seluruh kolom transaksi yang ditampilkan pada tabel, kecuali kolom Aksi.

### Membuka Detail

**Trigger:** Klik baris atau tombol "Detail".

**Expected behavior:**

1. Navigasi ke `/transactions/:id`.

## 6. Page States

### Default

- Daftar transaksi terbaru di atas.

### Loading

- Skeleton baris tabel.

### Empty

- Belum ada transaksi: "Belum ada transaksi."
- Filter tanpa hasil: "Tidak ada transaksi yang cocok dengan filter."

### Error

- Gagal memuat: alert error + tombol coba lagi.

### Success

- Tidak ada aksi tulis.

### Disabled / Permission Denied

- Tidak berlaku.

## 7. Validation & Business Rules

| Field / Rule    | Requirement                   | Error message                                   |
| --------------- | ----------------------------- | ----------------------------------------------- |
| Rentang tanggal | Tanggal mulai ≤ tanggal akhir | Tanggal akhir tidak boleh sebelum tanggal mulai |

### Business Rules

- Transaksi diurutkan dari yang terbaru.
- Transaksi bersifat final dan tidak dapat diubah dari halaman ini.
- Subtotal merupakan total harga seluruh item sebelum diskon.
- Diskon merupakan total potongan harga yang diterapkan pada transaksi.
- Total yang ditampilkan adalah total setelah seluruh diskon.
- Jumlah item merupakan total kuantitas seluruh produk dalam transaksi, bukan jumlah jenis produk.
- Jika transaksi tidak mendapatkan diskon, tampilkan `Rp0`.
- Kolom Aksi hanya menyediakan navigasi ke halaman detail transaksi.

## 8. API & Data Integration

| Action                | Method | Endpoint                   | Notes                                           |
| --------------------- | ------ | -------------------------- | ----------------------------------------------- |
| Daftar transaksi      | GET    | `/transactions`            | Mendukung `q`, `from`, `to`, `page`, `per_page` |
| Data export transaksi | GET    | `/api/transactions/export` | JSON, parameter sama dengan halaman             |

### Data Dependencies

- Transaksi milik user beserta detail item dan jumlah item.
- Subtotal transaksi.
- Total diskon transaksi.
- Total pembayaran setelah diskon.
- Tanggal dan waktu transaksi.

## 9. Accessibility

- Filter memiliki label yang terlihat.
- Tabel memiliki header semantik.
- Baris yang dapat diklik juga dapat diaktifkan dengan keyboard dan memiliki accessible name.
- Tombol "Detail" memiliki accessible name yang jelas dan dapat mengidentifikasi transaksi terkait.
- Tabel pada mobile dapat diakses menggunakan keyboard dan horizontal scrolling.
- Perubahan hasil filter diumumkan melalui `aria-live="polite"`.

## 10. Acceptance Criteria

- [ ] Hanya transaksi milik user yang tampil.
- [ ] Transaksi terbaru tampil paling atas.
- [ ] Search nomor dan filter tanggal berfungsi.
- [ ] Nomor transaksi tampil dengan benar.
- [ ] Tanggal dan waktu transaksi tampil dengan benar.
- [ ] Jumlah item tampil dengan benar.
- [ ] Subtotal tampil sebelum diskon.
- [ ] Diskon tampil dengan benar.
- [ ] Total tampil setelah diskon.
- [ ] Transaksi tanpa diskon menampilkan `Rp0`.
- [ ] Klik baris atau tombol Detail membuka halaman detail transaksi.
- [ ] Empty state dan empty filter state tampil dengan benar.
- [ ] Pagination berfungsi.
- [ ] Tidak ada aksi ubah/hapus transaksi.
- [ ] Layout menggunakan tabel pada desktop, tablet, dan mobile.
- [ ] Tabel pada mobile dapat di-scroll secara horizontal tanpa merusak layout halaman.
- [ ] Seluruh kolom informasi transaksi tetap dapat diakses pada mobile.
- [ ] Export PDF menghasilkan file yang dapat dibuka.
- [ ] Export Excel menghasilkan file `.xlsx` yang dapat dibuka.
- [ ] Export memakai filter yang sama dan mencakup semua hasil filter, bukan hanya satu halaman.
- [ ] Export mencakup seluruh informasi transaksi dan tidak menyertakan kolom Aksi.
- [ ] Tombol export nonaktif selama proses dan pulih setelah selesai/gagal.

## 11. Out of Scope

- Membuat transaksi dari halaman ini (dilakukan di kasir).
- Edit atau pembatalan transaksi.

## 12. Open Decisions

| Question                                                  | Impact               | Status |
| --------------------------------------------------------- | -------------------- | ------ |
| Apakah perlu filter berdasarkan diskon atau metode bayar? | Opsi filter tambahan | Closed|

## 13. References

- [Transaction detail page](transaction-detail-page.md)
- [Kasir page](../kasir/kasir-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
