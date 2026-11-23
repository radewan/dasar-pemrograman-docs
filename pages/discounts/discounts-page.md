# Discounts Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Diskon |
| Route | `/discounts` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Menampilkan daftar diskon warung dan menyediakan penambahan diskon baru melalui modal.

### User Goals

- Melihat seluruh diskon beserta tipe, nilai, dan statusnya.
- Menambahkan diskon baru.
- Mengaktifkan atau menonaktifkan diskon.

### Preconditions

- User sudah login dan memiliki warung.

## 2. Entry Points & Navigation

### Entry Points

- Menu "Diskon" di sidebar.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Klik "Detail" | `/discounts/:id` | — |
| Simpan diskon baru berhasil | `/discounts` | Modal tertutup, daftar diperbarui |

### Access Rules

- Hanya user yang sudah login.
- Data difilter berdasarkan warung user.

## 3. Layout Structure

### Desktop

- Judul + tombol "Tambah Diskon" di kanan atas.
- Search box.
- Tabel diskon + pagination.

### Tablet

- Sama seperti desktop.

### Mobile

- Daftar sebagai kartu: nama, tipe + nilai, status, aksi.
- Tombol "Tambah Diskon" penuh lebar.

### Content Hierarchy

1. Judul + tombol tambah
2. Search
3. Daftar diskon
4. Pagination

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Search box | Filter berdasarkan nama diskon | Yes | Input |
| Tabel diskon | Nama, tipe, nilai, status, aksi | Yes | Table |
| Tombol "Tambah Diskon" | Membuka modal tambah | Yes | Button |
| Modal tambah diskon | Form diskon baru | Yes | Dialog |
| Toggle status | Mengaktifkan/menonaktifkan diskon | Yes | Switch |
| Pagination | 10/25/50 per halaman | Yes | Pagination |
| Dialog konfirmasi hapus | Konfirmasi sebelum menghapus | Yes | Dialog |
| Alert sukses/error | Feedback aksi | Yes | Alert |

## 5. User Interactions

### Menambah Diskon

**Trigger:** Klik "Tambah Diskon".

**Expected behavior:**
1. Modal terbuka dengan form kosong.
2. Validasi dijalankan di server saat submit.
3. Jika berhasil, modal tertutup, alert sukses tampil, daftar diperbarui.
4. Jika gagal, modal tetap terbuka dengan pesan error per field.

### Mengubah Status Diskon

**Trigger:** Menggeser toggle status pada baris.

**Expected behavior:**
1. Mengirim request untuk mengubah status aktif.
2. Jika berhasil, status pada baris diperbarui.
3. Jika gagal, toggle kembali ke nilai sebelumnya dan alert error tampil.

**Constraints:**
- Diskon nonaktif tidak muncul di selector diskon pada halaman kasir.

### Menghapus Diskon

**Trigger:** Klik aksi "Hapus".

**Expected behavior:**
1. Dialog konfirmasi muncul.
2. Konfirmasi mengirim `POST /discounts/:id/delete`.
3. Diskon dihapus dari daftar.

**Constraints:**
- Diskon yang sudah dipakai transaksi tidak boleh dihapus permanen; nonaktifkan saja. Lihat Open Decisions.

### Pagination

**Trigger:** Klik nomor halaman atau mengubah jumlah per halaman.

**Expected behavior:**
1. Query string diperbarui (`page`, `per_page`).
2. Daftar dimuat ulang.

## 6. Page States

### Default

- Daftar diskon terisi, diurutkan berdasarkan nama.

### Loading

- Skeleton baris; toggle menampilkan state loading saat menyimpan.

### Empty

- Belum ada diskon: "Belum ada diskon. Tambahkan diskon pertama Anda." + tombol "Tambah Diskon".
- Pencarian tanpa hasil: "Tidak ada diskon yang cocok."

### Error

- Gagal memuat: alert error + tombol coba lagi.
- Gagal menyimpan: pesan per field di dalam modal.
- Gagal mengubah status: alert error + toggle dikembalikan.

### Success

- Alert sukses: "Diskon berhasil ditambahkan" / "Status diskon diperbarui".

### Disabled / Permission Denied

- Tombol simpan nonaktif saat request berjalan.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Nama | Wajib, maks. 100 karakter | Nama diskon wajib diisi |
| Tipe | Wajib: `persen` atau `nominal` | Tipe diskon wajib dipilih |
| Nilai | Wajib, bilangan bulat > 0 | Nilai diskon harus lebih dari 0 |
| Nilai (persen) | 1–100 | Persentase diskon harus antara 1 dan 100 |
| Status | Aktif/nonaktif, default aktif | — |

### Business Rules

- Nilai `persen` disimpan sebagai bilangan bulat 1–100.
- Nilai `nominal` dalam Rupiah tanpa desimal.
- Hanya diskon aktif yang dapat dipilih pada transaksi baru.
- Diskon nonaktif tetap tersimpan agar transaksi lama tetap konsisten.

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Daftar diskon | GET | `/discounts` | Mendukung `q`, `page`, `per_page` |
| Tambah diskon | POST | `/discounts` | Form POST, CSRF token wajib |
| Hapus diskon | POST | `/discounts/:id/delete` | Form POST |
| Ubah status diskon | POST | `TBD` | Endpoint toggle belum ditentukan (lihat Open Decisions) |

### Request Data

- `name`, `type`, `value`, `is_active`.

### Response Data

- Halaman daftar dengan alert sukses atau pesan validasi.

### Error Handling

- Error validasi dipetakan ke field masing-masing.
- Error server ditampilkan sebagai alert.

### Data Dependencies

- Diskon milik warung user.

## 9. Accessibility

- Search memiliki label yang terlihat.
- Modal memiliki `role="dialog"` dan `aria-modal="true"`.
- Fokus terjebak di modal dan kembali ke tombol pemicu saat ditutup.
- Toggle status memiliki accessible name yang menyebut nama diskon.
- Status tidak disampaikan hanya lewat warna; sertakan teks "Aktif"/"Nonaktif".
- Error field dikaitkan melalui `aria-describedby`.

## 10. Acceptance Criteria

- [ ] Daftar hanya menampilkan diskon milik warung user.
- [ ] Diskon baru dapat ditambahkan melalui modal.
- [ ] Persentase di luar 1–100 ditolak.
- [ ] Nilai 0 atau negatif ditolak.
- [ ] Toggle status mengubah status diskon dan tersimpan.
- [ ] Diskon nonaktif tidak muncul di selector kasir.
- [ ] Search dan pagination berfungsi.
- [ ] Empty state tampil dengan benar.
- [ ] Status ditampilkan dengan teks, bukan hanya warna.
- [ ] Layout tabel di desktop dan kartu di mobile.

## 11. Out of Scope

- Periode berlaku diskon (tanggal mulai/selesai).
- Minimal belanja untuk memakai diskon.
- Diskon terikat produk/kategori tertentu.
- Diskon otomatis tanpa dipilih di kasir.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Apakah diskon perlu periode berlaku? | Skema diskon dan validasi di kasir | Open |
| Apakah diskon bisa dibatasi ke produk/kategori tertentu? | Model relasi baru | Open |
| Apakah diskon yang sudah dipakai transaksi boleh dihapus permanen? | Integritas data historis | Open — sementara nonaktifkan |
| Endpoint untuk mengubah status diskon dari toggle di daftar? | Route baru atau memakai `POST /discounts/:id` | Open |

## 13. References

- [Discount detail page](discount-detail-page.md)
- [Kasir page](../kasir/kasir-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
