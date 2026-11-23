# Category Page

## 1. Overview

| Property       | Value                 |
| -------------- | --------------------- |
| Page Name      | Category Page         |
| Route          | `/categories`         |
| Status         | Ready for Development |
| Priority       | Must                  |
| Authentication | Required              |
| Responsive     | Yes                   |

### Purpose

Mengelola kategori produk yang dimiliki oleh user.

### User Goals

- Melihat seluruh kategori produk.
- Mengetahui jumlah produk pada setiap kategori.
- Membuat kategori baru.
- Mengubah nama kategori.
- Menghapus kategori.

## 2. Navigation & Access

### Access Rules

- Hanya user yang sudah login dapat mengakses halaman ini.
- User hanya dapat melihat dan mengelola kategori miliknya sendiri.
- Kategori milik user lain tidak boleh dapat diakses atau dimodifikasi.

## 3. Layout Structure

### Desktop

- Header berisi judul halaman, deskripsi singkat, dan tombol tambah kategori.
- Tabel kategori menggunakan seluruh lebar konten.
- Pagination ditampilkan di bawah tabel jika diperlukan.

### Tablet

- Menggunakan struktur layout seperti desktop.
- Tabel tetap digunakan.
- Lebar tabel menyesuaikan viewport.

### Mobile

- Tetap menggunakan tabel, bukan card.
- Tabel menggunakan horizontal scrolling jika diperlukan.
- Tombol tambah kategori tetap mudah dijangkau.
- Pagination menyesuaikan lebar layar.

## 4. UI Components

### Category Table

| Column        | Description                                      | Example       | Required |
| ------------- | ------------------------------------------------ | ------------- | -------- |
| No.           | Nomor urut kategori pada halaman                 | `1`           | Yes      |
| Nama Category | Nama kategori                                    | `Makanan`     | Yes      |
| Total Product | Jumlah produk yang menggunakan kategori tersebut | `12`          | Yes      |
| Aksi          | Tombol Edit dan Delete                           | Edit / Delete | Yes      |

### Numbering Rules

- Nomor urut dimulai dari `1`.
- Nomor urut mengikuti urutan data pada halaman saat ini.
- Nomor urut di-reset pada setiap halaman pagination.
- Nomor urut tidak disimpan di database.

### Other Components

| Component           | Description                                           | Required    |
| ------------------- | ----------------------------------------------------- | ----------- |
| Page Header         | Menampilkan judul dan deskripsi halaman               | Yes         |
| Add Category Button | Membuka modal Create Category                         | Yes         |
| Category Table      | Menampilkan data kategori                             | Yes         |
| Pagination          | Navigasi antarhalaman jika jumlah data melebihi batas | Conditional |
| Toast Notification  | Memberikan feedback setelah operasi                   | Yes         |

## 5. Create Category Modal

### Modal Structure

- Header berisi judul `Tambah Kategori`.
- Tombol silang (X) untuk menutup modal.
- Form input nama kategori.
- Tombol `Batal`.
- Tombol `Simpan`.

### Form Fields

| Field         | Type       | Required | Validation                                                |
| ------------- | ---------- | -------- | --------------------------------------------------------- |
| Nama Category | Text Input | Yes      | Tidak boleh kosong dan harus unik secara case-insensitive |

### Validation Rules

- Nama kategori wajib diisi.
- Spasi di awal dan akhir nama kategori dihapus sebelum disimpan.
- Nama kategori tidak boleh sama dengan kategori lain milik user yang sama, tanpa membedakan huruf besar dan kecil.
- Contoh: `Makanan`, `makanan`, dan `MAKANAN` dianggap sama.
- Validasi harus dilakukan di frontend dan backend.
- Database harus menjamin keunikan kategori sesuai aturan tersebut untuk mencegah duplikasi akibat request bersamaan.

### Interactions

**Trigger:** Klik tombol `Tambah Kategori`.

**Expected behavior:**

1. Modal Create Category ditampilkan.
2. User mengisi nama kategori.
3. User menekan tombol `Simpan`.
4. Sistem memvalidasi input.
5. Jika valid, kategori disimpan.
6. Modal ditutup dan tabel diperbarui.
7. Toast menampilkan feedback keberhasilan.

**Error Handling:**

- Jika nama kategori kosong, tampilkan pesan validasi.
- Jika nama kategori sudah digunakan, tampilkan pesan `Nama kategori sudah digunakan`.
- Jika terjadi kesalahan server, tampilkan feedback kegagalan tanpa menghapus input yang sudah diisi.

## 6. Edit Category Modal

### Modal Structure

Modal Edit menggunakan struktur dan tampilan yang sama dengan Create Category Modal.

Perbedaannya:

- Header berisi judul `Edit Kategori`.
- Input nama kategori sudah terisi dengan nama saat ini.
- Tombol utama menggunakan label `Simpan Perubahan`.

### Interactions

**Trigger:** Klik tombol `Edit` pada baris kategori.

**Expected behavior:**

1. Modal Edit Category ditampilkan.
2. Input otomatis diisi dengan nama kategori saat ini.
3. User dapat mengubah nama kategori.
4. User menekan tombol `Simpan Perubahan`.
5. Sistem memvalidasi input.
6. Jika valid, perubahan disimpan.
7. Modal ditutup dan tabel diperbarui.
8. Toast menampilkan feedback keberhasilan.

### Validation Rules

- Semua aturan validasi Create Category berlaku.
- Kategori yang sedang diedit boleh mempertahankan nama lamanya.
- Keunikan nama diperiksa terhadap kategori lain milik user yang sama, tidak termasuk kategori yang sedang diedit.
- Perbandingan nama bersifat case-insensitive.

## 7. Delete Category

### Confirmation Dialog

Ketika user menekan tombol `Delete`, sistem menampilkan confirmation dialog.

**Dialog Structure:**

- Judul: `Hapus Kategori?`
- Deskripsi: `Apakah kamu yakin ingin menghapus kategori "[nama kategori]"? Tindakan ini tidak dapat dibatalkan.`
- Tombol `Batal`.
- Tombol `Hapus`.

### Interactions

**Trigger:** Klik tombol `Delete` pada baris kategori.

**Expected behavior:**

1. Confirmation dialog ditampilkan.
2. Nama kategori yang akan dihapus ditampilkan dengan jelas.
3. User memilih `Batal` untuk membatalkan penghapusan.
4. User memilih `Hapus` untuk melanjutkan.
5. Sistem memproses penghapusan.
6. Jika berhasil, dialog ditutup dan tabel diperbarui.
7. Toast menampilkan feedback keberhasilan.

### Delete Rules

- Penghapusan hanya berlaku pada kategori milik user yang sedang login.
- Kategori yang tidak ditemukan atau bukan milik user tidak boleh dihapus.
- Jika kategori masih digunakan oleh produk, sistem harus mencegah penghapusan atau meminta kategori pengganti.

**Open Decision:** Kebijakan penghapusan kategori yang masih digunakan produk belum ditentukan. Jangan menghapus kategori atau mengubah relasi produk secara otomatis sebelum kebijakan ini diputuskan.

## 8. Feedback & Notifications

### Toast Notification

Toast digunakan untuk memberikan feedback setelah operasi Create, Edit, dan Delete.

| Operation | Success Message               | Error Message              |
| --------- | ----------------------------- | -------------------------- |
| Create    | Kategori berhasil ditambahkan | Gagal menambahkan kategori |
| Edit      | Kategori berhasil diperbarui  | Gagal memperbarui kategori |
| Delete    | Kategori berhasil dihapus     | Gagal menghapus kategori   |

### Feedback Rules

- Toast hanya muncul setelah server memberikan hasil operasi.
- Gunakan toast success untuk operasi yang berhasil.
- Gunakan toast error untuk operasi yang gagal.
- Pesan validasi form ditampilkan di dekat input, bukan hanya menggunakan toast.
- Toast tidak boleh menutupi elemen penting atau menghalangi interaksi pengguna.

## 9. States

| State      | Description                                                           |
| ---------- | --------------------------------------------------------------------- |
| Default    | Tabel menampilkan seluruh kategori milik user                         |
| Loading    | Menampilkan skeleton saat data dimuat                                 |
| Empty      | Menampilkan pesan bahwa belum ada kategori dan tombol tambah kategori |
| Creating   | Modal Create sedang dibuka                                            |
| Editing    | Modal Edit sedang dibuka                                              |
| Deleting   | Confirmation dialog sedang dibuka                                     |
| Submitting | Tombol submit dinonaktifkan selama request berlangsung                |
| Error      | Menampilkan feedback jika data gagal dimuat atau operasi gagal        |

## 10. API & Data Integration

### Required Operations

| Method    | Endpoint          | Description               |
| --------- | ----------------- | ------------------------- |
| GET       | `/categories`     | Mengambil daftar kategori |
| POST      | `/categories`     | Membuat kategori          |
| PUT/PATCH | `/categories/:id` | Memperbarui kategori      |
| DELETE    | `/categories/:id` | Menghapus kategori        |

### Data Dependencies

- Category ID
- Category Name
- Total Product
- User ID

### Business Rules

- Kategori dimiliki oleh user.
- Nama kategori harus unik per user secara case-insensitive.
- Total Product merupakan jumlah produk yang terhubung dengan kategori tersebut.
- User hanya dapat melakukan operasi pada kategori miliknya sendiri.
- Validasi backend wajib diterapkan meskipun frontend sudah melakukan validasi.

## 11. Accessibility

- Modal memiliki judul yang jelas.
- Tombol silang memiliki accessible name.
- Fokus keyboard diarahkan ke modal saat dibuka.
- Fokus dikembalikan ke tombol pemicu setelah modal ditutup.
- Tombol Batal dan Simpan memiliki label yang jelas.
- Confirmation dialog dapat diakses menggunakan keyboard.
- Toast menggunakan mekanisme pengumuman yang dapat dibaca screen reader.
- Status tidak hanya dibedakan berdasarkan warna.

## 12. Acceptance Criteria

- [ ] User dapat melihat daftar kategori dalam bentuk tabel.
- [ ] Tabel memiliki kolom No., Nama Category, Total Product, dan Aksi.
- [ ] Nomor urut dimulai dari 1 dan di-reset pada setiap halaman.
- [ ] Total Product menampilkan jumlah produk pada setiap kategori.
- [ ] User dapat membuka modal Create Category.
- [ ] Modal Create memiliki header, tombol silang, input nama kategori, tombol Batal, dan tombol Simpan.
- [ ] Nama kategori wajib diisi.
- [ ] Nama kategori tidak boleh duplikat secara case-insensitive untuk user yang sama.
- [ ] User dapat membuka modal Edit Category.
- [ ] Modal Edit memiliki struktur yang sama dengan modal Create.
- [ ] Modal Edit menampilkan nama kategori saat ini.
- [ ] User dapat menghapus kategori melalui confirmation dialog.
- [ ] User dapat membatalkan penghapusan.
- [ ] User mendapatkan toast setelah operasi berhasil atau gagal.
- [ ] Tabel diperbarui setelah operasi berhasil.
- [ ] User tidak dapat mengelola kategori milik user lain.
- [ ] Kategori yang masih digunakan produk mengikuti kebijakan penghapusan yang telah ditentukan.
- [ ] Halaman responsif di desktop, tablet, dan mobile.
- [ ] Tampilan mobile tetap menggunakan tabel dengan horizontal scrolling jika diperlukan.

## 13. Out of Scope

- Pengelompokan kategori bertingkat (parent-child).
- Upload gambar atau ikon kategori.
- Pengurutan kategori secara manual.
- Bulk delete.
- Pencarian dan filter kategori (dapat ditambahkan jika diperlukan).

## 14. Open Decisions

| Decision                                         | Status | Notes                                                             |
| ------------------------------------------------ | ------ | ----------------------------------------------------------------- |
| Penghapusan kategori yang masih digunakan produk | Open   | Apakah harus menolak penghapusan atau meminta kategori pengganti? |
| Pagination                                       | Open   | Perlu ditentukan batas data per halaman                           |
| Search kategori                                  | Open   | Belum menjadi bagian dari kebutuhan awal                          |
