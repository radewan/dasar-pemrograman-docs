# Products Page

## 1. Overview

| Properti       | Nilai                 |
| -------------- | --------------------- |
| Page name      | Produk                |
| Route          | `/products`           |
| Status         | Ready for Development |
| Priority       | Must                  |
| Authentication | Authenticated         |
| Responsive     | Yes                   |
| Owner          | TBD                   |

### Purpose

Menampilkan daftar seluruh produk warung serta menyediakan fitur pengelolaan produk melalui modal tanpa harus berpindah halaman.

Pengguna dapat menambah produk, mengedit informasi produk, menambah atau mengurangi stok, melihat detail produk, menonaktifkan produk, dan mengekspor data produk.

### User Goals

- Melihat seluruh produk beserta barcode, kategori, harga, stok, dan satuan.
- Mencari produk berdasarkan barcode, nama, atau kategori.
- Mengurutkan produk berdasarkan nama, harga, dan stok.
- Menambahkan produk baru.
- Mengubah informasi produk.
- Menambah stok produk.
- Mengurangi stok produk.
- Melihat detail produk.
- Menonaktifkan produk yang tidak lagi digunakan.
- Mengekspor daftar produk ke PDF atau Excel.

### Preconditions

- User sudah login dan memiliki warung.
- User memiliki akses ke halaman produk.
- Minimal satu kategori sudah tersedia sebelum produk dibuat.

## 2. Entry Points & Navigation

### Entry Points

- Menu "Produk" di sidebar.
- Navigasi dari halaman lain melalui aplikasi.

### Outgoing Navigation

| Trigger                      | Destination     | Conditions                     |
| ---------------------------- | --------------- | ------------------------------ |
| Klik baris / tombol "Detail" | `/products/:id` | Produk tersedia dan aktif      |
| Klik "Tambah Produk"         | `/products`     | Modal tambah terbuka           |
| Klik "Edit"                  | `/products`     | Modal edit terbuka             |
| Klik "Atur Stok"             | `/products`     | Modal Stock Adjustment terbuka |
| Klik "Hapus"                 | `/products`     | Confirmation dialog terbuka    |

### Access Rules

- Hanya user yang sudah login.
- Data produk difilter berdasarkan kepemilikan warung user (multi-tenant).
- User tidak boleh melihat atau mengubah produk milik warung lain.
- Produk yang sudah dinonaktifkan tidak ditampilkan pada daftar utama.
- Produk nonaktif tidak dapat diedit atau dilakukan perubahan stok.

## 3. Layout Structure

### Desktop

- Header halaman berisi judul "Produk".
- Tombol Export (PDF, Excel) dan tombol "Tambah Produk" di kanan atas.
- Search box dan sorting control di atas tabel.
- Tabel produk di bawah search box.
- Pagination di bawah tabel.

### Tablet

- Menggunakan layout tabel seperti desktop.
- Tabel dapat menyesuaikan lebar konten.
- Jika ruang tidak mencukupi, gunakan horizontal scrolling.
- Tombol aksi tetap dapat diakses.

### Mobile

- Tetap menggunakan tabel, bukan card.
- Tabel menggunakan horizontal scrolling (`overflow-x: auto`).
- Header tabel tetap terlihat saat melakukan scrolling horizontal.
- Kolom No dan Aksi dipertahankan agar navigasi tetap mudah.
- Search box menggunakan lebar penuh.
- Sorting control menggunakan lebar penuh atau menyesuaikan ruang.
- Tombol Export menggunakan dropdown.
- Tombol "Tambah Produk" menggunakan lebar penuh.
- Pagination menyesuaikan lebar layar.

**Catatan:**

- Jangan menghilangkan kolom tabel hanya karena ukuran layar kecil.
- Semua kolom tetap tersedia dan dapat dilihat melalui horizontal scrolling.
- Hindari mengecilkan ukuran teks secara berlebihan agar informasi tetap mudah dibaca.

### Content Hierarchy

1. Page header dan tombol tambah produk.
2. Search dan sorting.
3. Daftar produk.
4. Pagination.

## 4. UI Components

| Component              | Description                                     | Required | Reference    |
| ---------------------- | ----------------------------------------------- | -------- | ------------ |
| Page header            | Judul halaman dan tombol utama                  | Yes      | Header       |
| Search box             | Mencari berdasarkan barcode, nama, dan category | Yes      | Input        |
| Sorting control        | Mengurutkan berdasarkan nama, harga, dan stok   | Yes      | Select       |
| Product table          | Menampilkan informasi produk                    | Yes      | Table        |
| Horizontal scrolling   | Memungkinkan tabel digeser pada layar kecil     | Yes      | Overflow     |
| Tombol "Tambah Produk" | Membuka modal tambah produk                     | Yes      | Button       |
| Modal tambah produk    | Form pembuatan produk                           | Yes      | Dialog       |
| Modal edit produk      | Form perubahan produk                           | Yes      | Dialog       |
| Modal Stock Adjustment | Form untuk menambah atau mengurangi stok        | Yes      | Dialog       |
| Stock preview          | Menampilkan perkiraan stok setelah perubahan    | Yes      | Text         |
| Image uploader         | Mengunggah satu gambar produk maksimal 1 MB     | Yes      | File Input   |
| Image preview          | Menampilkan preview gambar yang dipilih         | Yes      | Image        |
| Confirmation dialog    | Konfirmasi sebelum menonaktifkan produk         | Yes      | Alert Dialog |
| Toast notification     | Feedback keberhasilan atau kegagalan operasi    | Yes      | Toast        |
| Pagination             | Navigasi halaman dan jumlah data                | Yes      | Pagination   |
| Tombol Export PDF      | Generate PDF daftar produk                      | Yes      | Button       |
| Tombol Export Excel    | Generate file `.xlsx` daftar produk             | Yes      | Button       |

### Product Table Structure

| No  | Column   | Description                    |
| --- | -------- | ------------------------------ |
| 1   | No       | Nomor urut berdasarkan halaman |
| 2   | Barcode  | Barcode unik produk            |
| 3   | Nama     | Nama produk                    |
| 4   | Category | Kategori produk                |
| 5   | Harga    | Harga jual produk              |
| 6   | Stock    | Jumlah stok tersedia           |
| 7   | Unit     | Satuan produk                  |
| 8   | Aksi     | Detail, Edit, Atur Stok, Hapus |

### Action Buttons

| Action    | Behavior                       |
| --------- | ------------------------------ |
| Detail    | Membuka halaman detail produk  |
| Edit      | Membuka modal edit produk      |
| Atur Stok | Membuka modal Stock Adjustment |
| Hapus     | Membuka confirmation dialog    |

### Create / Edit Modal Fields

| Field     | Type        | Description                           |
| --------- | ----------- | ------------------------------------- |
| Barcode   | Text        | Barcode produk                        |
| Nama      | Text        | Nama produk                           |
| Category  | Select      | Memilih kategori yang tersedia        |
| Harga     | Number      | Harga jual produk                     |
| Stok Awal | Number      | Hanya ditampilkan saat membuat produk |
| Unit      | Text        | Satuan produk                         |
| Image     | File Upload | Mengunggah satu gambar produk         |

**Image Upload Rules:**

- Hanya satu gambar untuk setiap produk.
- Maksimal ukuran file adalah 1 MB.
- Format yang didukung:
  - JPG / JPEG
  - PNG
  - WebP
- Tampilkan preview sebelum gambar disimpan.
- User dapat mengganti gambar yang sudah dipilih.
- Pada modal edit, tampilkan gambar produk saat ini.
- User dapat mengganti gambar dengan gambar baru.
- Jika user tidak mengganti gambar, gambar lama tetap digunakan.

**Catatan:**

- Modal create dan edit menggunakan struktur form yang konsisten.
- Saat edit, seluruh informasi produk sebelumnya ditampilkan.
- Field stok tidak dapat diubah melalui modal edit.
- Perubahan stok dilakukan melalui modal Stock Adjustment atau halaman detail produk.
- Kategori wajib dipilih.

### Stock Adjustment Modal

Modal ini digunakan untuk menambah atau mengurangi stok produk langsung dari Products Page.

#### Modal Fields

| Field         | Type     | Description                                    | Required |
| ------------- | -------- | ---------------------------------------------- | -------- |
| Product Name  | Text     | Nama produk yang dipilih, read-only            | Yes      |
| Current Stock | Text     | Jumlah stok saat ini, read-only                | Yes      |
| Mutation Type | Select   | Memilih jenis perubahan stok                   | Yes      |
| Quantity      | Number   | Jumlah stok yang ingin ditambah atau dikurangi | Yes      |
| Notes         | Textarea | Catatan alasan perubahan stok                  | No       |

#### Mutation Type Options

| Value    | Label        | Description             |
| -------- | ------------ | ----------------------- |
| `masuk`  | Tambah Stok  | Menambahkan stok produk |
| `keluar` | Kurangi Stok | Mengurangi stok produk  |

#### Stock Preview

Modal menampilkan perkiraan jumlah stok setelah perubahan.

Contoh penambahan:

| Information   |  Value |
| ------------- | -----: |
| Current Stock | 20 pcs |
| Mutation Type |  Masuk |
| Quantity      | 10 pcs |
| New Stock     | 30 pcs |

Contoh pengurangan:

| Information   |  Value |
| ------------- | -----: |
| Current Stock | 20 pcs |
| Mutation Type | Keluar |
| Quantity      |  5 pcs |
| New Stock     | 15 pcs |

Perhitungan:

- `masuk`: current_stock + quantity
- `keluar`: current_stock - quantity

Preview diperbarui secara otomatis ketika user mengubah jenis mutasi atau jumlah stok.

#### Modal Actions

| Button           | Behavior                                |
| ---------------- | --------------------------------------- |
| Cancel           | Menutup modal tanpa menyimpan perubahan |
| Simpan Perubahan | Mengirim request ke backend             |

## 5. User Interactions

### Mencari Produk

**Trigger:** Mengetik pada search box.

**Expected behavior:**

1. User dapat mencari berdasarkan barcode, nama, atau category.
2. Pencarian nama dan category menggunakan metode substring dan case-insensitive.
3. Barcode dapat dicari berdasarkan kecocokan teks.
4. Hasil pencarian diperbarui berdasarkan keyword.
5. Query pencarian disimpan dalam URL.
6. Halaman kembali ke halaman pertama ketika keyword berubah.

**Constraints:**

- Pencarian dilakukan di server.
- Debounce sekitar 300ms.
- Keyword kosong menampilkan seluruh produk sesuai filter lainnya.
- Search tidak boleh mengubah sorting yang sedang digunakan.

### Sorting Produk

**Trigger:** User memilih opsi sorting.

**Expected behavior:**

1. User dapat memilih pengurutan berdasarkan nama, harga, atau stok.
2. User dapat memilih arah pengurutan ascending atau descending.
3. Daftar diperbarui berdasarkan pilihan sorting.
4. Query sorting disimpan dalam URL.
5. Pagination kembali ke halaman pertama setelah sorting berubah.

**Sorting Options:**

| Field | Ascending                     | Descending                    |
| ----- | ----------------------------- | ----------------------------- |
| Nama  | A–Z                           | Z–A                           |
| Harga | Harga terendah ke tertinggi   | Harga tertinggi ke terendah   |
| Stock | Stok paling sedikit ke banyak | Stok paling banyak ke sedikit |

**Constraints:**

- Default sorting adalah nama ascending (`name ASC`).
- Sorting dilakukan di server.
- Sorting tidak boleh menghilangkan keyword pencarian.
- Hanya field sorting yang diizinkan yang dapat digunakan.

### Menambah Produk

**Trigger:** Klik tombol "Tambah Produk".

**Expected behavior:**

1. Modal tambah produk terbuka.
2. Form ditampilkan dalam kondisi kosong.
3. User mengisi barcode, nama, category, harga, stok awal, unit, dan image.
4. User dapat melihat preview gambar.
5. User menekan tombol "Simpan".
6. Sistem melakukan validasi.
7. Jika berhasil:
   - Modal tertutup.
   - Daftar produk diperbarui.
   - Toast sukses ditampilkan.
8. Jika gagal:
   - Modal tetap terbuka.
   - Data yang sudah diisi tetap dipertahankan.
   - Error ditampilkan pada field terkait.
   - Toast error ditampilkan untuk kegagalan umum.

**Constraints:**

- Fokus otomatis diarahkan ke field pertama.
- Tombol simpan dinonaktifkan selama request berlangsung.
- Escape dan klik di luar modal menutup modal jika tidak sedang menyimpan.
- Stok awal harus menghasilkan mutasi stok bertipe `initial_stock`.
- Kategori wajib dipilih.
- Image wajib diunggah.
- Image maksimal 1 MB.

### Mengedit Produk

**Trigger:** Klik tombol "Edit".

**Expected behavior:**

1. Modal edit produk terbuka.
2. Sistem mengambil data produk yang akan diedit.
3. Form ditampilkan dengan nilai sebelumnya.
4. User dapat mengubah barcode, nama, category, harga, unit, dan image.
5. User menekan tombol "Simpan Perubahan".
6. Sistem melakukan validasi.
7. Jika berhasil:
   - Modal tertutup.
   - Daftar produk diperbarui.
   - Toast sukses ditampilkan.
8. Jika gagal:
   - Modal tetap terbuka.
   - Data yang sudah diisi tetap dipertahankan.
   - Error ditampilkan pada field terkait.
   - Toast error ditampilkan untuk kegagalan umum.

**Constraints:**

- ID produk tidak dapat diubah.
- Stok tidak dapat diubah melalui modal edit.
- Barcode harus tetap unik dalam satu warung.
- Kategori wajib dipilih.
- Gambar lama tetap digunakan jika user tidak menggantinya.
- Gambar maksimal 1 MB jika diganti.

### Mengatur Stok Produk

**Trigger:** Klik tombol "Atur Stok" pada baris produk.

**Expected behavior:**

1. Modal Stock Adjustment terbuka.
2. Sistem menampilkan nama produk dan stok saat ini.
3. User memilih jenis mutasi:
   - Tambah Stok (`masuk`).
   - Kurangi Stok (`keluar`).
4. User mengisi jumlah stok.
5. User dapat mengisi catatan perubahan stok.
6. Sistem menampilkan preview stok setelah perubahan.
7. User menekan tombol "Simpan Perubahan".
8. Sistem melakukan validasi.
9. Frontend mengirim request ke backend.
10. Jika berhasil:
    - Modal tertutup.
    - Jumlah stok pada tabel diperbarui menggunakan nilai dari backend.
    - Toast sukses ditampilkan.
    - Riwayat mutasi stok tersimpan.
11. Jika gagal:
    - Modal tetap terbuka.
    - Data yang sudah diisi tetap dipertahankan.
    - Error ditampilkan pada field terkait.
    - Toast error ditampilkan untuk kegagalan umum.

**Constraints:**

- Produk harus aktif.
- User hanya dapat mengubah stok produk milik warungnya.
- Quantity harus berupa bilangan bulat positif.
- Pengurangan stok tidak boleh melebihi stok tersedia.
- Backend wajib memeriksa stok terbaru sebelum perubahan.
- Backend tidak boleh hanya mengandalkan hasil preview dari frontend.
- Setiap perubahan stok harus menghasilkan catatan mutasi.
- Perubahan stok dan pencatatan mutasi harus dilakukan dalam satu database transaction.
- Tombol simpan dinonaktifkan selama request berlangsung.
- Modal tidak boleh ditutup ketika request sedang berlangsung.
- Modal ini hanya menyediakan jenis mutasi `masuk` dan `keluar`.
- Penyesuaian stok ke jumlah akhir tertentu dilakukan melalui Product Detail Page.

### Menghapus Produk

**Trigger:** Klik tombol "Hapus".

**Expected behavior:**

1. Confirmation dialog muncul.
2. Dialog menampilkan informasi produk yang akan dinonaktifkan.
3. User dapat memilih "Batal" atau "Hapus".
4. Jika user memilih "Hapus", sistem mengirim request.
5. Jika berhasil:
   - Dialog tertutup.
   - Produk dihapus dari daftar aktif.
   - Toast sukses ditampilkan.
6. Jika gagal:
   - Produk tetap ditampilkan.
   - Dialog ditutup setelah hasil request diterima.
   - Toast error ditampilkan.

**Constraints:**

- Penghapusan menggunakan soft delete.
- Produk yang memiliki riwayat transaksi tidak boleh dihapus permanen.
- Riwayat transaksi dan mutasi stok harus tetap tersedia.
- Produk nonaktif tidak dapat digunakan pada transaksi baru.

### Melihat Detail Produk

**Trigger:** Klik tombol "Detail" atau baris produk.

**Expected behavior:**

1. User diarahkan ke `/products/:id`.
2. Sistem menampilkan informasi produk.
3. User dapat melihat riwayat stok melalui halaman detail.

### Export Data Produk

**Trigger:** Klik "Export PDF" atau "Export Excel".

**Expected behavior:**

1. JavaScript mengirim request ke backend untuk mengambil seluruh data produk berdasarkan filter aktif.
2. Backend mengembalikan data produk dalam format JSON.
3. Browser menerima seluruh data hasil filter, bukan hanya data pada halaman aktif.
4. JavaScript memproses data menggunakan library:
   - PDF: jsPDF.
   - Excel: SheetJS.
5. File di-generate di browser.
6. File langsung diunduh.
7. Tombol menampilkan loading selama proses.
8. Jika gagal, toast error ditampilkan.

**Constraints:**

- Backend bertugas menyediakan data, bukan membuat file PDF atau Excel.
- Browser bertanggung jawab membuat file.
- Export mengikuti keyword pencarian dan sorting yang sedang digunakan.
- Export mencakup seluruh hasil filter.
- Export tidak terbatas pada pagination halaman aktif.
- Library dapat dimuat melalui CDN.
- Tombol export dinonaktifkan selama proses.

### Pagination

**Trigger:** Klik nomor halaman atau mengubah jumlah per halaman.

**Expected behavior:**

1. Query string diperbarui (`page`, `per_page`).
2. Data dimuat ulang sesuai halaman.
3. Search dan sorting yang sedang digunakan tetap dipertahankan.

**Constraints:**

- Pilihan jumlah data: 10, 25, dan 50 per halaman.
- Jika halaman yang diminta melebihi jumlah halaman yang tersedia, sistem mengembalikan halaman valid.

## 6. Page States

### Default

- Daftar produk ditampilkan berdasarkan sorting nama ascending.
- Search dan sorting siap digunakan.
- Tombol tambah dan export tersedia.
- Tombol aksi tersedia untuk setiap produk aktif.

### Loading

- Skeleton ditampilkan saat daftar produk dimuat.
- Modal menampilkan loading saat mengambil data produk untuk edit.
- Modal Stock Adjustment menampilkan loading saat request berlangsung.
- Tombol simpan, Stock Adjustment, dan hapus dinonaktifkan selama request berlangsung.
- Tombol export menampilkan indikator loading.
- Preview stok bukan hasil final sebelum backend mengonfirmasi perubahan.

### Empty

Jika belum ada produk:

"Belum ada produk. Tambahkan produk pertama Anda."

Tampilkan tombol "Tambah Produk".

Jika pencarian tidak menghasilkan data:

"Tidak ada produk yang cocok dengan pencarian Anda."

### Error

- Gagal memuat daftar: tampilkan toast error dan tombol "Coba Lagi".
- Gagal menambah produk: tampilkan error field dan toast error.
- Gagal mengedit produk: tampilkan error field dan toast error.
- Gagal menambah stok: tampilkan error field dan toast error.
- Gagal mengurangi stok: tampilkan error field dan toast error.
- Stok tidak mencukupi: tampilkan error pada field Quantity.
- Gagal menghapus produk: tampilkan toast error.
- Gagal export: tampilkan toast error.

### Success

Gunakan toast notification untuk setiap operasi.

| Action       | Toast Message                 |
| ------------ | ----------------------------- |
| Create       | Produk berhasil ditambahkan   |
| Update       | Produk berhasil diperbarui    |
| Stock In     | Stok berhasil ditambahkan     |
| Stock Out    | Stok berhasil dikurangi       |
| Delete       | Produk berhasil dinonaktifkan |
| Export PDF   | File PDF berhasil dibuat      |
| Export Excel | File Excel berhasil dibuat    |

### Disabled / Permission Denied

- Tombol simpan dinonaktifkan selama request berjalan.
- Tombol hapus dinonaktifkan selama request berjalan.
- Tombol Stock Adjustment dinonaktifkan selama proses penyimpanan stok.
- User tidak dapat mengakses produk milik warung lain.
- Produk nonaktif tidak dapat diedit atau digunakan untuk transaksi baru.
- Produk nonaktif tidak dapat dilakukan perubahan stok.

## 7. Validation & Business Rules

### Form Validation

| Field / Rule  | Requirement                                             | Error message                        |
| ------------- | ------------------------------------------------------- | ------------------------------------ |
| Barcode       | Wajib diisi                                             | Barcode wajib diisi                  |
| Barcode       | Unik dalam satu warung                                  | Barcode sudah digunakan              |
| Barcode       | Format barcode harus valid sesuai standar yang didukung | Format barcode tidak valid           |
| Nama          | Wajib, maksimal 100 karakter                            | Nama produk wajib diisi              |
| Nama          | Unik per warung, case-insensitive                       | Nama produk sudah digunakan          |
| Category      | Wajib memilih kategori aktif yang tersedia              | Kategori wajib dipilih               |
| Harga         | Wajib, bilangan bulat >= 0                              | Harga jual tidak valid               |
| Stok awal     | Wajib saat tambah, bilangan bulat >= 0                  | Stok awal tidak valid                |
| Unit          | Wajib, maksimal 20 karakter                             | Satuan wajib diisi                   |
| Image         | Wajib mengunggah satu gambar                            | Gambar produk wajib diunggah         |
| Image         | Maksimal ukuran 1 MB                                    | Ukuran gambar maksimal 1 MB          |
| Image         | Format JPG, JPEG, PNG, atau WebP                        | Format gambar tidak didukung         |
| Mutation Type | Wajib memilih `masuk` atau `keluar`                     | Jenis mutasi wajib dipilih           |
| Quantity      | Bilangan bulat positif, minimum 1                       | Jumlah stok harus lebih besar dari 0 |
| Quantity      | Tidak boleh melebihi stok saat ini untuk jenis keluar   | Stok tidak mencukupi                 |
| Notes         | Opsional                                                | -                                    |

### Business Rules

- Barcode wajib unik dalam satu warung.
- UUID digunakan sebagai ID internal produk, sedangkan barcode digunakan untuk identifikasi melalui scanner.
- Produk wajib memiliki kategori.
- Kategori menggunakan relasi ke tabel `categories` melalui `category_id`.
- Kategori harus aktif dan dimiliki oleh warung yang sama.
- Produk harus dimiliki oleh warung user yang sedang login.
- Setiap produk hanya memiliki satu gambar.
- Gambar produk wajib diunggah ketika membuat produk.
- Gambar produk maksimal berukuran 1 MB.
- Stok awal saat membuat produk dicatat sebagai mutasi stok bertipe `initial_stock`.
- Setelah produk dibuat, stok tidak dapat diubah melalui modal edit.
- Penambahan stok menggunakan jenis mutasi `masuk`.
- Pengurangan stok menggunakan jenis mutasi `keluar`.
- Penyesuaian stok ke jumlah akhir tertentu dilakukan melalui Product Detail Page.
- Jumlah stok tidak boleh menjadi negatif.
- Backend wajib memvalidasi stok terbaru sebelum perubahan.
- Setiap perubahan stok wajib menghasilkan catatan mutasi.
- Perubahan stok dan pencatatan mutasi harus dilakukan dalam satu database transaction.
- Quantity pada catatan mutasi menyimpan jumlah perubahan stok, bukan jumlah stok akhir.
- Produk yang dinonaktifkan tidak muncul di daftar produk aktif dan tidak dapat digunakan pada transaksi baru.
- Riwayat transaksi dan mutasi stok tidak boleh hilang ketika produk dinonaktifkan.
- Toast digunakan untuk memberikan feedback setelah operasi berhasil atau gagal.

### Image Format Recommendation

| Format     | Status      | Usage                                   |
| ---------- | ----------- | --------------------------------------- |
| JPEG / JPG | Recommended | Foto produk                             |
| WebP       | Recommended | Foto produk dengan ukuran lebih efisien |
| PNG        | Supported   | Gambar produk dengan transparansi       |

## 8. API & Data Integration

> Endpoint berikut merupakan rancangan berdasarkan kebutuhan halaman. Sesuaikan dengan kontrak API backend yang telah disepakati.

| Action                | Method | Endpoint               | API Reference |
| --------------------- | ------ | ---------------------- | ------------- |
| Daftar produk         | GET    | `/products`            | TBD           |
| Detail produk         | GET    | `/products/:id`        | TBD           |
| Tambah produk         | POST   | `/products`            | TBD           |
| Edit produk           | PUT    | `/products/:id`        | TBD           |
| Hapus produk          | DELETE | `/products/:id`        | TBD           |
| Tambah / kurangi stok | POST   | `/products/:id/stock`  | TBD           |
| Data export produk    | GET    | `/api/products/export` | TBD           |
| Daftar kategori       | GET    | `/api/categories`      | TBD           |

### Query Parameters

| Parameter  | Description                            | Example         |
| ---------- | -------------------------------------- | --------------- |
| `q`        | Pencarian barcode, nama, atau category | `q=indomie`     |
| `page`     | Halaman aktif                          | `page=1`        |
| `per_page` | Jumlah data per halaman                | `per_page=10`   |
| `order_by` | Field sorting                          | `order_by=name` |
| `sort`     | Arah sorting                           | `sort=asc`      |

Contoh:

`/products?q=indomie&order_by=price&sort=asc&page=1&per_page=10`

Field `order_by` yang diperbolehkan:

- `name`
- `price`
- `stock`

Field `sort`:

- `asc`
- `desc`

Default:

- `order_by=name`
- `sort=asc`

### Export Query Parameters

Endpoint:

`GET /api/products/export`

Parameter yang didukung:

| Parameter  | Description       |
| ---------- | ----------------- |
| `q`        | Keyword pencarian |
| `order_by` | Field sorting     |
| `sort`     | Arah sorting      |

**Catatan:**

- Endpoint export tidak menggunakan batas pagination.
- Backend mengembalikan seluruh produk yang sesuai filter.
- Data yang dikembalikan digunakan browser untuk membuat file.

### Request Data

**Create Product:**

Content-Type: `multipart/form-data`

| Field           | Type    | Required |
| --------------- | ------- | -------- |
| `barcode`       | String  | Yes      |
| `name`          | String  | Yes      |
| `category_id`   | UUID    | Yes      |
| `price`         | Integer | Yes      |
| `initial_stock` | Integer | Yes      |
| `unit`          | String  | Yes      |
| `image`         | File    | Yes      |

**Update Product:**

Content-Type: `multipart/form-data`

| Field         | Type    | Required |
| ------------- | ------- | -------- |
| `barcode`     | String  | Yes      |
| `name`        | String  | Yes      |
| `category_id` | UUID    | Yes      |
| `price`       | Integer | Yes      |
| `unit`        | String  | Yes      |
| `image`       | File    | No       |

Jika `image` tidak dikirim, gambar lama tetap digunakan.

**Stock Adjustment:**

Content-Type: `application/json`

Tambah stok:

```json
{
  "type": "masuk",
  "quantity": 10,
  "notes": "Penambahan stok dari supplier"
}
```

Kurangi stok:

```json
{
  "type": "keluar",
  "quantity": 5,
  "notes": "Barang rusak"
}
```

Keterangan:

- `type` hanya menerima `masuk` atau `keluar` melalui modal ini.
- `quantity` harus berupa bilangan bulat positif.
- `notes` bersifat opsional.
- Backend harus mengambil stok terbaru sebelum menghitung perubahan.

### Response Data

**Product List:**

- Data produk.
- Informasi pagination.
- Informasi filter aktif.
- Informasi sorting aktif.

**Create / Update:**

- Status operasi.
- Informasi produk yang berhasil disimpan.
- Pesan keberhasilan.

**Stock Adjustment:**

- Status operasi.
- ID produk.
- Stok sebelumnya.
- Jumlah perubahan stok.
- Stok terbaru.
- Jenis mutasi.
- Pesan keberhasilan.

Contoh response:

```json
{
  "success": true,
  "message": "Stock updated successfully",
  "data": {
    "product_id": "product-uuid",
    "previous_stock": 20,
    "quantity": 10,
    "current_stock": 30,
    "type": "masuk"
  }
}
```

**Delete:**

- Status operasi.
- Pesan keberhasilan.
- Informasi produk yang dinonaktifkan.

**Export:**

- Seluruh data produk sesuai filter.
- Data tidak dibatasi pagination.

### Error Handling

| HTTP Status | Condition                                                                     | UI Behavior               |
| ----------- | ----------------------------------------------------------------------------- | ------------------------- |
| 400         | Request tidak valid                                                           | Toast error               |
| 401         | Belum login                                                                   | Redirect ke login         |
| 403         | Tidak memiliki akses                                                          | Tampilkan akses ditolak   |
| 404         | Produk tidak ditemukan                                                        | Toast error               |
| 409         | Barcode atau nama duplikat, stok tidak mencukupi, atau konflik perubahan stok | Tampilkan error terkait   |
| 413         | Ukuran file terlalu besar                                                     | Toast atau error field    |
| 415         | Format gambar tidak didukung                                                  | Tampilkan error image     |
| 422         | Validasi gagal                                                                | Tampilkan error per field |
| 500         | Server error                                                                  | Toast error umum          |

### Data Dependencies

- Produk aktif milik user.
- Kategori aktif milik user.
- Gambar produk.
- Data pagination.
- Data sorting dan filter.
- Stok produk terbaru.
- Riwayat mutasi stok.

## 9. Accessibility

- Search box memiliki label yang jelas.
- Sorting menggunakan label yang dapat dibaca screen reader.
- Tabel memiliki header kolom semantik (`<th scope="col">`).
- Tabel dapat di-scroll secara horizontal menggunakan keyboard.
- Modal menggunakan `role="dialog"` dan `aria-modal="true"`.
- Modal memiliki label judul.
- Fokus terjebak di dalam modal saat terbuka.
- Fokus kembali ke tombol pemicu setelah modal ditutup.
- Image uploader dapat dioperasikan menggunakan keyboard.
- Preview gambar memiliki alternative text.
- Error field dikaitkan dengan input menggunakan `aria-describedby`.
- Tombol aksi memiliki accessible name yang jelas.
- Stock preview diumumkan kepada screen reader ketika nilainya berubah.
- Toast menggunakan `role="status"` untuk keberhasilan dan `role="alert"` untuk error yang perlu segera diketahui.
- Jangan hanya menggunakan warna untuk menunjukkan status.
- Tombol tambah dan kurangi stok memiliki label yang jelas.

## 10. Analytics (Optional)

| Event               | Trigger                       | Properties           |
| ------------------- | ----------------------------- | -------------------- |
| `product_search`    | User mencari produk           | Search type          |
| `product_sort`      | User mengubah sorting         | Field, direction     |
| `product_create`    | Produk berhasil dibuat        | Category ID          |
| `product_update`    | Produk berhasil diperbarui    | Product ID           |
| `product_stock_in`  | Stok berhasil ditambahkan     | Product ID, Quantity |
| `product_stock_out` | Stok berhasil dikurangi       | Product ID, Quantity |
| `product_delete`    | Produk berhasil dinonaktifkan | Product ID           |
| `product_export`    | Export berhasil               | Format               |

## 11. Acceptance Criteria

- [ ] Halaman hanya menampilkan produk milik user yang login.
- [ ] Tabel memiliki kolom No, Barcode, Nama, Category, Harga, Stock, Unit, dan Aksi.
- [ ] Kolom Aksi menyediakan Detail, Edit, Atur Stok, dan Hapus.
- [ ] Desktop menggunakan tabel.
- [ ] Tablet menggunakan tabel.
- [ ] Mobile tetap menggunakan tabel dengan horizontal scrolling.
- [ ] Tidak ada perubahan tampilan menjadi card pada mobile.
- [ ] Search dapat mencari berdasarkan barcode.
- [ ] Search dapat mencari berdasarkan nama.
- [ ] Search dapat mencari berdasarkan category.
- [ ] Search mendukung pencarian case-insensitive untuk nama dan category.
- [ ] Default sorting adalah nama ascending (A–Z).
- [ ] Sorting nama ascending dan descending berfungsi.
- [ ] Sorting harga ascending dan descending berfungsi.
- [ ] Sorting stok ascending dan descending berfungsi.
- [ ] Sorting dan search dapat digunakan bersamaan.
- [ ] Query search, sorting, dan pagination dipertahankan dalam URL.
- [ ] Modal tambah produk dapat dibuka dan ditutup.
- [ ] Produk baru dapat dibuat melalui modal.
- [ ] Form tambah menyediakan field image.
- [ ] Image uploader hanya menerima satu gambar.
- [ ] Image uploader menolak gambar lebih dari 1 MB.
- [ ] Format JPG, JPEG, PNG, dan WebP didukung.
- [ ] Preview gambar ditampilkan sebelum penyimpanan.
- [ ] Kategori wajib dipilih saat membuat produk.
- [ ] Produk tanpa kategori tidak dapat dibuat.
- [ ] Stok awal menghasilkan catatan mutasi stok.
- [ ] Modal edit menampilkan data produk sebelumnya.
- [ ] Produk dapat diedit melalui modal.
- [ ] Stok tidak dapat diubah melalui modal edit.
- [ ] Gambar lama tetap digunakan jika tidak diganti.
- [ ] Barcode duplikat ditolak.
- [ ] Tombol Atur Stok membuka modal Stock Adjustment.
- [ ] Modal Stock Adjustment menampilkan nama produk dan stok saat ini.
- [ ] User dapat memilih Tambah Stok.
- [ ] User dapat memilih Kurangi Stok.
- [ ] User dapat mengisi jumlah stok.
- [ ] User dapat mengisi catatan perubahan stok.
- [ ] Preview stok diperbarui secara otomatis.
- [ ] Quantity wajib berupa bilangan bulat positif.
- [ ] Pengurangan stok tidak boleh melebihi stok tersedia.
- [ ] User dapat membatalkan perubahan stok.
- [ ] Stok diperbarui setelah backend berhasil memproses request.
- [ ] Setiap perubahan stok menghasilkan catatan mutasi.
- [ ] Riwayat mutasi dapat dilihat melalui Product Detail Page.
- [ ] Stok pada tabel diperbarui setelah perubahan berhasil.
- [ ] Backend melakukan validasi stok terbaru.
- [ ] Perubahan stok dan pencatatan mutasi dilakukan secara atomic.
- [ ] Tombol hapus menampilkan confirmation dialog.
- [ ] Produk yang dihapus menggunakan soft delete.
- [ ] Riwayat transaksi dan mutasi stok tetap tersedia.
- [ ] Toast sukses tampil setelah create, update, stock adjustment, dan delete berhasil.
- [ ] Toast error tampil ketika operasi gagal.
- [ ] Error validasi ditampilkan pada field terkait.
- [ ] Loading state ditampilkan saat request berjalan.
- [ ] Pagination mendukung 10, 25, dan 50 data per halaman.
- [ ] Export PDF menghasilkan file yang dapat dibuka.
- [ ] Export Excel menghasilkan file `.xlsx` yang dapat dibuka.
- [ ] Data export diambil dari backend.
- [ ] Pembuatan file export dilakukan di browser.
- [ ] Export mencakup seluruh hasil filter, bukan hanya halaman aktif.
- [ ] Export mengikuti sorting dan pencarian aktif.
- [ ] Tabel dapat dioperasikan dan dibaca pada layar mobile.

## 12. Out of Scope

- Halaman manajemen kategori.
- Import produk melalui CSV atau Excel.
- Pengaturan stok melalui modal edit.
- Penyesuaian stok ke jumlah akhir tertentu melalui Products Page.
- Pengelolaan seluruh riwayat mutasi stok di Products Page.
- Pengelolaan transaksi penjualan.
- Penghapusan permanen produk.
- Pengelolaan supplier.
- Upload lebih dari satu gambar per produk.

## 13. Open Decisions

| Question                                                                                         | Impact                        | Owner | Status |
| ------------------------------------------------------------------------------------------------ | ----------------------------- | ----- | ------ |
| Apakah barcode harus mengikuti standar EAN-13, UPC-A, Code 128, atau mendukung beberapa standar? | Validasi barcode dan scanner  | TBD   | Open   |
| Apakah nama file gambar perlu menggunakan UUID?                                                  | Image storage                 | TBD   | Open   |
| Apakah gambar lama langsung dihapus dari storage ketika diganti?                                 | Image storage dan cleanup     | TBD   | Open   |
| Apakah export PDF perlu menyertakan gambar produk?                                               | Layout dan ukuran file export | TBD   | Open   |

## 14. References

- [Product Detail Page](product-detail-page.md)
- [Stock History Page](product-stock-page.md)
- [Kasir Page](../kasir/kasir-page.md)
- [Category Page](../categories/categories-page.md)
- [Navigation Flow](../../flows/navigation-flow.md)
- [Database Design](../../technical/database-design.md)
- [Frontend Conventions](../../technical/frontend-conventions.md)
- [API Conventions](../../technical/api-conventions.md)
