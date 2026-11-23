# Product Stock History Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Riwayat Stok Produk |
| Route | `/products/:id/stock` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Menampilkan seluruh mutasi stok untuk satu produk: kapan stok bertambah, kapan berkurang, dan penyebabnya.

### User Goals

- Menelusuri perubahan stok satu produk dari waktu ke waktu.
- Mengetahui apakah perubahan berasal dari transaksi penjualan atau penyesuaian manual.

### Preconditions

- User sudah login.
- Produk dengan `:id` ada dan milik warung user.

## 2. Entry Points & Navigation

### Entry Points

- Tombol "Riwayat Stok" di `/products/:id`.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Kembali | `/products/:id` | — |
| Klik referensi transaksi | `/transactions/:id` | Mutasi berasal dari transaksi |

### Access Rules

- Hanya user yang sudah login.
- Produk milik warung lain → 403/404.

## 3. Layout Structure

### Desktop

- Header: nama produk + stok saat ini + tombol kembali.
- Filter (tipe, rentang tanggal) di atas tabel.
- Tabel mutasi + pagination.

### Tablet

- Sama seperti desktop.

### Mobile

- Daftar dirender sebagai kartu berisi waktu, tipe, jumlah, alasan.
- Filter ditampilkan dalam baris yang bisa digulir horizontal atau panel lipat.

### Content Hierarchy

1. Nama produk + stok saat ini
2. Filter
3. Daftar mutasi
4. Pagination

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Header produk | Nama + stok saat ini | Yes | Text |
| Filter tipe | masuk / keluar / penyesuaian | Yes | Select |
| Filter tanggal | Rentang tanggal | Yes | Date range |
| Tabel mutasi | Waktu, tipe, jumlah, alasan, referensi | Yes | Table |
| Pagination | 10/25/50 per halaman | Yes | Pagination |

## 5. User Interactions

### Memfilter Mutasi

**Trigger:** Mengubah tipe atau rentang tanggal.

**Expected behavior:**
1. Daftar dimuat ulang sesuai filter.
2. Filter tercermin di query string.
3. Halaman kembali ke 1.

### Melihat Referensi Transaksi

**Trigger:** Klik tautan referensi pada mutasi yang berasal dari transaksi.

**Expected behavior:**
1. Navigasi ke `/transactions/:id`.

## 6. Page States

### Default

- Menampilkan seluruh mutasi produk, terbaru di atas.

### Loading

- Skeleton baris tabel.

### Empty

- Belum ada mutasi: "Belum ada perubahan stok untuk produk ini."

### Error

- Gagal memuat: alert error + tombol coba lagi.
- Produk tidak ditemukan: 404.

### Success

- Tidak ada aksi tulis; halaman read-only.

### Disabled / Permission Denied

- Tidak berlaku.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Rentang tanggal | Tanggal mulai ≤ tanggal akhir | Tanggal akhir tidak boleh sebelum tanggal mulai |

### Business Rules

- Halaman ini read-only — mutasi tidak dapat diedit atau dihapus.
- Mutasi diurutkan dari yang terbaru.
- Jumlah ditampilkan bertanda: `+` untuk masuk, `−` untuk keluar, dan penyesuaian mengikuti arah perubahannya.

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Lihat riwayat | GET | `/products/:id/stock` | Mendukung `type`, `from`, `to`, `page`, `per_page` |

### Data Dependencies

- Produk milik warung user.
- Mutasi stok produk tersebut.

## 9. Accessibility

- Filter memiliki label yang terlihat.
- Tabel memiliki header semantik.
- Tautan referensi transaksi memiliki accessible name yang menyebut nomor transaksi.
- Perubahan hasil filter diumumkan melalui `aria-live="polite"`.

## 10. Acceptance Criteria

- [ ] Hanya mutasi produk milik warung user yang tampil.
- [ ] Mutasi terbaru tampil paling atas.
- [ ] Jumlah masuk dan keluar dibedakan secara visual dan teks (bukan warna saja).
- [ ] Filter tipe dan tanggal berfungsi.
- [ ] Mutasi dari transaksi menampilkan tautan ke detail transaksi.
- [ ] Empty state tampil saat belum ada mutasi.
- [ ] Pagination berfungsi.
- [ ] Halaman read-only, tidak ada aksi ubah/hapus.

## 11. Out of Scope

- Edit atau hapus mutasi.
- Export riwayat mutasi stok.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Apakah penyesuaian ditampilkan sebagai nilai absolut atau selisih? | Format kolom jumlah | Open — bergantung keputusan di detail produk |

## 13. References

- [Product detail page](product-detail-page.md)
- [Stock history page](../inventory/stock-history-page.md)
- [Transaction detail page](../transactions/transaction-detail-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
