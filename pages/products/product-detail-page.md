# Product Detail Page

## 1. Overview

| Properti       | Nilai           |
| -------------- | --------------- |
| Page name      | Product Detail  |
| Route          | `/products/:id` |
| Status         | Draft           |
| Priority       | Must            |
| Authentication | Authenticated   |
| Responsive     | Yes             |

### Purpose

Halaman ini digunakan untuk menampilkan informasi lengkap suatu produk, mengelola stok, dan melihat riwayat perubahan stok.

Pengguna juga dapat mengedit informasi produk melalui modal Edit Product yang tersedia langsung di halaman ini tanpa harus kembali ke Products Page.

### Main Features

- Menampilkan informasi lengkap produk.
- Menampilkan gambar produk.
- Mengedit informasi produk melalui modal.
- Menambah stok produk.
- Mengurangi stok produk.
- Melakukan penyesuaian stok.
- Menampilkan riwayat mutasi stok.
- Memfilter riwayat mutasi berdasarkan jenis dan rentang tanggal.

---

## 2. Page Layout

Halaman terdiri dari beberapa bagian:

1. Product Information
2. Stock Summary
3. Stock Adjustment
4. Product Mutation History

---

## 3. Product Information

### 3.1 Product Data

Menampilkan informasi produk berikut:

| Field      | Description                               |
| ---------- | ----------------------------------------- |
| Image      | Gambar produk                             |
| ID         | UUID produk                               |
| Barcode    | Barcode produk                            |
| Name       | Nama produk                               |
| Category   | Kategori produk                           |
| Price      | Harga produk                              |
| Stock      | Jumlah stok saat ini                      |
| Unit       | Satuan produk                             |
| Created At | Tanggal produk dibuat                     |
| Updated At | Terakhir kali informasi produk diperbarui |

### 3.2 Actions

Tersedia tombol:

| Action       | Description                |
| ------------ | -------------------------- |
| Edit Product | Membuka modal Edit Product |
| Back         | Kembali ke Products Page   |

### 3.3 Edit Product Modal

Modal digunakan untuk mengubah informasi produk secara langsung dari Product Detail Page.

Modal ini juga digunakan oleh Products Page sehingga kedua halaman menggunakan form, validasi, dan proses penyimpanan yang sama.

#### Editable Fields

| Field       | Type   | Required | Description                |
| ----------- | ------ | -------- | -------------------------- |
| Barcode     | String | Yes      | Barcode produk, harus unik |
| Name        | String | Yes      | Nama produk                |
| Category ID | UUID   | Yes      | Kategori produk            |
| Price       | Number | Yes      | Harga produk               |
| Unit        | String | Yes      | Satuan produk              |
| Image       | File   | No       | Gambar produk              |

#### Non-editable Fields

| Field      | Reason                                        |
| ---------- | --------------------------------------------- |
| ID         | Identitas produk tidak boleh diubah           |
| Stock      | Perubahan stok harus melalui Stock Adjustment |
| Created At | Dikelola oleh sistem                          |
| Updated At | Dikelola oleh sistem                          |

#### Modal Behavior

- Modal dibuka ketika pengguna menekan tombol Edit Product.
- Form menampilkan data produk saat ini.
- Gambar produk saat ini ditampilkan sebagai preview.
- Pengguna dapat mengganti gambar produk.
- Pengguna dapat membatalkan perubahan.
- Ketika pengguna menyimpan perubahan, frontend mengirim request ke backend.
- Tombol Save Changes menampilkan loading selama proses penyimpanan.
- Modal ditutup setelah penyimpanan berhasil.
- Informasi produk diperbarui tanpa harus melakukan navigasi ulang.
- Jika penyimpanan gagal, modal tetap terbuka dan menampilkan pesan kesalahan.

#### Validation

| Field       | Validation                                     |
| ----------- | ---------------------------------------------- |
| Barcode     | Required, unique                               |
| Name        | Required                                       |
| Category ID | Required, valid category                       |
| Price       | Required, numeric, minimum 0                   |
| Unit        | Required                                       |
| Image       | Optional, maximum 1 MB, JPG/JPEG, PNG, or WebP |

#### API Endpoint

`PUT /products/:id`

Request menggunakan `multipart/form-data` apabila pengguna mengunggah gambar baru. Field gambar dapat dihilangkan jika tidak ada perubahan gambar.

#### Request Fields

| Field       | Type   | Required |
| ----------- | ------ | -------- |
| barcode     | String | Yes      |
| name        | String | Yes      |
| category_id | UUID   | Yes      |
| price       | Number | Yes      |
| unit        | String | Yes      |
| image       | File   | No       |

#### Success Response

HTTP `200 OK`

```json
{
  "success": true,
  "message": "Product updated successfully",
  "data": {
    "id": "uuid",
    "barcode": "8998866200015",
    "name": "Indomie Goreng",
    "category_id": "category-uuid",
    "price": 3500,
    "stock": 20,
    "unit": "pcs",
    "image": "/uploads/products/product-image.webp",
    "updated_at": "2026-11-23T10:00:00Z"
  }
}
```

#### Error Handling

| HTTP Status | Description                         |
| ----------- | ----------------------------------- |
| 400         | Invalid request data                |
| 401         | User is not authenticated           |
| 403         | User does not have permission       |
| 404         | Product or category not found       |
| 409         | Barcode already exists              |
| 413         | Uploaded image exceeds maximum size |
| 415         | Unsupported image format            |
| 422         | Validation failed                   |
| 500         | Internal server error               |

---

## 3.4 Delete Product

### Purpose

Memungkinkan pengguna menghapus produk langsung dari Product Detail Page.

Penghapusan menggunakan Soft Delete agar riwayat transaksi dan mutasi stok tetap tersimpan.

### Delete Action

- Tombol `Delete Product` tersedia pada Product Detail Page.
- Tombol menggunakan tampilan destructive/danger.
- Ketika diklik, sistem menampilkan Delete Confirmation Modal.
- Produk tidak langsung dihapus sebelum pengguna memberikan konfirmasi.

### Delete Confirmation Modal

**Title:** Delete Product?

**Description:**

"Are you sure you want to delete this product? This action will remove the product from your active product list."

**Buttons:**

| Button         | Action                               |
| -------------- | ------------------------------------ |
| Cancel         | Menutup modal tanpa menghapus produk |
| Delete Product | Melanjutkan proses penghapusan       |

### Delete Behavior

1. Pengguna menekan tombol Delete Product.
2. Sistem menampilkan confirmation modal.
3. Pengguna memilih Cancel atau Delete Product.
4. Jika Cancel, modal ditutup dan tidak ada perubahan data.
5. Jika Delete Product, frontend mengirim request ke backend.
6. Tombol Delete Product menampilkan loading selama proses berlangsung.
7. Jika berhasil, tampilkan success toast.
8. Pengguna diarahkan kembali ke Products Page.
9. Jika gagal, modal tetap terbuka dan tampilkan pesan error.

### API Endpoint

`DELETE /products/:id`

### Success Response

HTTP `200 OK`

```json
{
  "success": true,
  "message": "Product deleted successfully",
  "data": {
    "id": "product-uuid",
    "deleted_at": "2026-11-23T10:00:00Z"
  }
}
```

### Error Handling

| HTTP Status | Description                   |
| ----------- | ----------------------------- |
| 401         | User is not authenticated     |
| 403         | User does not have permission |
| 404         | Product not found             |
| 409         | Product cannot be deleted     |
| 500         | Internal server error         |

### Business Rules

- Penghapusan wajib melalui confirmation modal.
- Produk hanya dapat dihapus oleh pemiliknya.
- Penghapusan menggunakan Soft Delete.
- Produk yang sudah dihapus tidak muncul pada daftar produk aktif.
- Produk yang sudah dihapus tidak dapat diedit atau dilakukan Stock Adjustment.
- Riwayat transaksi dan mutasi stok harus tetap tersimpan.
- Produk yang sudah dihapus tidak dapat dihapus kembali.

## 4. Stock Summary

Menampilkan ringkasan stok produk saat ini.

| Information   | Description                 |
| ------------- | --------------------------- |
| Current Stock | Jumlah stok produk saat ini |
| Unit          | Satuan produk               |

Stock Summary diperbarui setelah pengguna berhasil melakukan Stock Adjustment.

---

## 5. Stock Adjustment

### 5.1 Purpose

Digunakan untuk mengelola jumlah stok produk tanpa mengubah informasi produk lainnya.

### 5.2 Mutation Types

| Type          | Description                                | Example       |
| ------------- | ------------------------------------------ | ------------- |
| `masuk`       | Menambah stok                              | 20 menjadi 30 |
| `keluar`      | Mengurangi stok                            | 20 menjadi 15 |
| `penyesuaian` | Menyesuaikan stok ke jumlah akhir tertentu | 20 menjadi 15 |

### 5.3 Form Fields

| Field    | Type     | Required | Description            |
| -------- | -------- | -------- | ---------------------- |
| Type     | Select   | Yes      | Jenis mutasi stok      |
| Quantity | Number   | Yes      | Jumlah stok            |
| Notes    | Textarea | No       | Catatan perubahan stok |

### 5.4 Business Rules

- Mutasi `masuk` menambahkan stok.
- Mutasi `keluar` mengurangi stok.
- Mutasi `penyesuaian` menetapkan jumlah stok akhir.
- Jumlah stok tidak boleh menjadi negatif.
- Quantity harus lebih besar dari 0.
- Setiap perubahan stok wajib memiliki catatan mutasi.
- Perubahan stok dan penyimpanan riwayat mutasi harus dilakukan dalam satu database transaction.
- Stok tidak boleh diubah melalui Edit Product Modal.

### 5.5 API Endpoint

`POST /products/:id/stock`

#### Request

```json
{
  "type": "masuk",
  "quantity": 10,
  "notes": "Penambahan stok dari supplier"
}
```

Untuk penyesuaian, `quantity` berisi jumlah stok akhir yang diinginkan.

#### Success Response

HTTP `200 OK`

```json
{
  "success": true,
  "message": "Stock updated successfully",
  "data": {
    "product_id": "uuid",
    "previous_stock": 20,
    "quantity_change": 10,
    "current_stock": 30,
    "type": "masuk"
  }
}
```

---

## 6. Product Mutation History

### 6.1 Purpose

Menampilkan seluruh riwayat perubahan stok produk.

### 6.2 Table Columns

| Column        | Description           |
| ------------- | --------------------- |
| No            | Nomor urut            |
| Qty           | Jumlah perubahan stok |
| Mutation Type | Jenis mutasi          |
| Notes         | Catatan perubahan     |
| Date          | Tanggal mutasi        |

### 6.3 Mutation Types

| Type            | Description                  |
| --------------- | ---------------------------- |
| `masuk`         | Penambahan stok              |
| `keluar`        | Pengurangan stok             |
| `penyesuaian`   | Penyesuaian stok             |
| `initial_stock` | Stok awal saat produk dibuat |

### 6.4 Filters

#### Filter by Mutation Type

Pengguna dapat memfilter riwayat berdasarkan jenis mutasi.

Pilihan:

- All Types
- Masuk
- Keluar
- Penyesuaian
- Initial Stock

#### Filter by Date Range

Pengguna dapat memfilter riwayat berdasarkan rentang tanggal.

| Filter    | Description   |
| --------- | ------------- |
| From Date | Tanggal awal  |
| To Date   | Tanggal akhir |

Rules:

- From Date tidak boleh lebih besar daripada To Date.
- Pengguna dapat menggunakan filter jenis mutasi dan tanggal secara bersamaan.
- Tersedia tombol Reset Filter.
- Jika tidak ada data, tampilkan empty state.

### 6.5 Pagination

Pilihan jumlah data per halaman:

- 10
- 25
- 50

Default: 10 records per page.

### 6.6 API Endpoint

`GET /products/:id/mutations`

#### Query Parameters

| Parameter  | Type   | Required | Description             |
| ---------- | ------ | -------- | ----------------------- |
| type       | String | No       | Filter jenis mutasi     |
| start_date | Date   | No       | Tanggal awal            |
| end_date   | Date   | No       | Tanggal akhir           |
| page       | Number | No       | Nomor halaman           |
| per_page   | Number | No       | Jumlah data per halaman |

#### Example

`GET /products/:id/mutations?type=masuk&start_date=2026-11-01&end_date=2026-11-23&page=1&per_page=10`

#### Success Response

HTTP `200 OK`

```json
{
  "success": true,
  "message": "Product mutations retrieved successfully",
  "data": [
    {
      "id": "mutation-uuid",
      "quantity": 10,
      "type": "masuk",
      "notes": "Penambahan stok dari supplier",
      "created_at": "2026-11-23T10:00:00Z"
    }
  ],
  "meta": {
    "current_page": 1,
    "per_page": 10,
    "total": 1,
    "last_page": 1
  }
}
```

---

## 7. Product Detail API

### 7.1 Get Product Detail

`GET /products/:id`

Digunakan untuk mengambil seluruh informasi produk.

#### Success Response

HTTP `200 OK`

```json
{
  "success": true,
  "message": "Product retrieved successfully",
  "data": {
    "id": "uuid",
    "barcode": "8998866200015",
    "name": "Indomie Goreng",
    "category": {
      "id": "category-uuid",
      "name": "Makanan Instan"
    },
    "price": 3500,
    "stock": 20,
    "unit": "pcs",
    "image": "/uploads/products/product-image.webp",
    "created_at": "2026-11-20T10:00:00Z",
    "updated_at": "2026-11-23T10:00:00Z"
  }
}
```

---

## 8. Responsive Behavior

### Desktop

- Informasi produk ditampilkan dalam layout yang terstruktur.
- Tombol Edit Product dan Stock Adjustment mudah diakses.
- Mutation History menggunakan tabel penuh.
- Modal Edit Product ditampilkan di tengah halaman.

### Tablet

- Informasi produk menggunakan layout yang menyesuaikan lebar layar.
- Tabel mutation history tetap menggunakan tabel.
- Modal menyesuaikan lebar layar.

### Mobile

- Informasi produk ditampilkan secara vertikal.
- Tombol aksi menyesuaikan lebar layar.
- Mutation History menggunakan tabel dengan horizontal scrolling.
- Modal Edit Product menggunakan lebar yang sesuai dengan layar.
- Form dapat di-scroll jika kontennya melebihi tinggi layar.

---

## 9. User Interaction

| Action                  | Expected Result                           |
| ----------------------- | ----------------------------------------- |
| Open Product Detail     | Menampilkan informasi produk              |
| Click Edit Product      | Membuka modal Edit Product                |
| Save Product Changes    | Mengirim perubahan ke backend             |
| Cancel Edit             | Menutup modal tanpa menyimpan perubahan   |
| Replace Product Image   | Mengganti gambar produk                   |
| Submit Stock Adjustment | Memperbarui stok dan mencatat mutasi      |
| Filter Mutation Type    | Menampilkan mutasi sesuai jenis           |
| Set Date Range          | Menampilkan mutasi sesuai rentang tanggal |
| Reset Filter            | Menghapus seluruh filter                  |
| Change Pagination       | Menampilkan halaman data berikutnya       |

---

## 10. Business Rules

1. Pengguna harus login untuk mengakses halaman ini.
2. Produk hanya dapat diakses oleh pemiliknya.
3. Produk yang tidak ditemukan menampilkan halaman Not Found.
4. Pengguna hanya dapat mengedit informasi produk miliknya.
5. Edit Product tidak boleh mengubah jumlah stok.
6. Perubahan stok harus melalui Stock Adjustment.
7. Setiap perubahan stok harus tercatat dalam Product Mutation History.
8. Mutasi stok tidak dapat diedit atau dihapus secara langsung.
9. Perubahan informasi produk tidak membuat catatan mutasi stok.
10. Perubahan stok dan pencatatan mutasi harus bersifat atomic.
11. Barcode harus unik dalam lingkup warung atau pemilik produk.
12. Produk yang sudah dihapus tidak dapat diedit atau dilakukan penyesuaian stok.

---

## 11. Out of Scope

Fitur berikut tidak termasuk dalam halaman ini:

- Membuat produk baru.
- Menghapus produk.
- Mengelola kategori.
- Mengelola diskon.
- Mengunggah gambar produk sebagai fitur terpisah.
- Mengubah stok melalui Edit Product Modal.
- Mengedit atau menghapus riwayat mutasi.

---

## 12. Open Decisions

Beberapa keputusan masih perlu ditentukan:

- Apakah catatan mutasi otomatis dibuat untuk perubahan stok dari transaksi POS?
- Apakah riwayat mutasi perlu menampilkan referensi transaksi?
- Apakah timezone tanggal mutasi mengikuti timezone pengguna atau server?
- Apakah perlu batas maksimal jumlah riwayat mutasi yang ditampilkan?
- Apakah perubahan harga produk perlu memiliki riwayat perubahan harga?
