# Stock History Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Riwayat Stok |
| Route | `/stock/history` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Menampilkan seluruh mutasi stok dari semua produk warung dalam satu daftar, agar user dapat menelusuri perubahan stok secara menyeluruh.

### User Goals

- Melihat semua perubahan stok lintas produk.
- Menemukan mutasi tertentu berdasarkan produk, tipe, atau tanggal.

### Preconditions

- User sudah login dan memiliki warung.

## 2. Entry Points & Navigation

### Entry Points

- Menu "Riwayat Stok" di sidebar.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Klik nama produk | `/products/:id` | — |
| Klik referensi transaksi | `/transactions/:id` | Mutasi berasal dari transaksi |
| Klik "Riwayat" pada baris | `/products/:id/stock` | — |

### Access Rules

- Hanya user yang sudah login.
- Data difilter berdasarkan warung user (multi-tenant).

## 3. Layout Structure

### Desktop

- Judul halaman.
- Baris filter: search produk, tipe, rentang tanggal.
- Tabel mutasi + pagination.

### Tablet

- Sama seperti desktop; kolom referensi bisa disembunyikan.

### Mobile

- Daftar sebagai kartu: waktu, nama produk, tipe, jumlah, alasan.
- Filter dalam panel lipat.

### Content Hierarchy

1. Judul
2. Filter
3. Daftar mutasi
4. Pagination

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Search produk | Filter berdasarkan nama produk | Yes | Input |
| Filter tipe | masuk / keluar / penyesuaian | Yes | Select |
| Filter tanggal | Rentang tanggal | Yes | Date range |
| Tabel mutasi | Waktu, produk, tipe, jumlah, alasan, referensi | Yes | Table |
| Pagination | 10/25/50 per halaman | Yes | Pagination |

## 5. User Interactions

### Memfilter Mutasi

**Trigger:** Mengubah search, tipe, atau rentang tanggal.

**Expected behavior:**
1. Daftar dimuat ulang sesuai filter.
2. Filter tercermin di query string.
3. Halaman kembali ke 1.

**Constraints:**
- Search di-debounce ±300ms.

### Membuka Detail Produk

**Trigger:** Klik nama produk pada baris.

**Expected behavior:**
1. Navigasi ke `/products/:id`.

## 6. Page States

### Default

- Menampilkan seluruh mutasi warung, terbaru di atas.

### Loading

- Skeleton baris tabel.

### Empty

- Belum ada mutasi: "Belum ada perubahan stok."
- Filter tanpa hasil: "Tidak ada mutasi yang cocok dengan filter."

### Error

- Gagal memuat: alert error + tombol coba lagi.

### Success

- Tidak ada aksi tulis; halaman read-only.

### Disabled / Permission Denied

- Tidak berlaku.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Rentang tanggal | Tanggal mulai ≤ tanggal akhir | Tanggal akhir tidak boleh sebelum tanggal mulai |

### Business Rules

- Halaman read-only.
- Mutasi diurutkan dari yang terbaru.
- Jumlah ditampilkan bertanda `+`/`−` disertai teks tipe agar tidak bergantung pada warna.
- Mutasi dari transaksi memiliki referensi; mutasi manual tidak.

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Lihat riwayat | GET | `/stock/history` | Mendukung `q`, `type`, `from`, `to`, `page`, `per_page` |

### Data Dependencies

- Mutasi stok seluruh produk milik warung user.

## 9. Accessibility

- Semua filter memiliki label yang terlihat.
- Tabel memiliki header semantik.
- Tipe mutasi disampaikan dengan teks, bukan hanya warna.
- Perubahan hasil filter diumumkan melalui `aria-live="polite"`.

## 10. Acceptance Criteria

- [ ] Hanya mutasi milik warung user yang tampil.
- [ ] Search produk, filter tipe, dan filter tanggal berfungsi.
- [ ] Mutasi terbaru tampil paling atas.
- [ ] Tipe dan arah perubahan dapat dibaca tanpa bergantung pada warna.
- [ ] Klik nama produk membuka detail produk.
- [ ] Mutasi dari transaksi menampilkan tautan ke detail transaksi.
- [ ] Empty state dan empty filter state tampil dengan benar.
- [ ] Pagination berfungsi.
- [ ] Layout tabel di desktop dan kartu di mobile.

## 11. Out of Scope

- Edit atau hapus mutasi.
- Export riwayat mutasi stok.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Apakah perlu filter "hanya penyesuaian manual"? | Opsi filter tambahan | Open |

## 13. References

- [Product stock history page](../products/product-stock-history-page.md)
- [Product detail page](../products/product-detail-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
