# Discount Detail Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Detail Diskon |
| Route | `/discounts/:id` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Menampilkan detail satu diskon sekaligus memungkinkan user mengubah datanya.

### User Goals

- Melihat detail diskon.
- Mengubah nama, tipe, nilai, atau status diskon.

### Preconditions

- User sudah login.
- Diskon dengan `:id` ada dan milik warung user.

## 2. Entry Points & Navigation

### Entry Points

- Klik "Detail" pada `/discounts`.

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Kembali | `/discounts` | — |
| Simpan perubahan | Tetap di `/discounts/:id` | Berhasil |
| Hapus diskon | `/discounts` | Setelah konfirmasi |

### Access Rules

- Hanya user yang sudah login.
- Diskon milik warung lain → 403/404.

## 3. Layout Structure

### Desktop

- Header: nama diskon + tombol kembali.
- Form data diskon dalam satu kolom lebar terbatas.
- Zona hapus di paling bawah.

### Tablet

- Sama seperti desktop.

### Mobile

- Satu kolom penuh.

### Content Hierarchy

1. Nama diskon + tombol kembali
2. Form data diskon
3. Aksi hapus

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Form data diskon | Nama, tipe, nilai, status | Yes | Input |
| Tombol simpan | Menyimpan perubahan | Yes | Button |
| Tombol hapus | Menghapus diskon | Yes | Button (danger) |
| Dialog konfirmasi hapus | Konfirmasi sebelum menghapus | Yes | Dialog |
| Alert sukses/error | Feedback aksi | Yes | Alert |

## 5. User Interactions

### Mengubah Diskon

**Trigger:** Klik "Simpan".

**Expected behavior:**
1. Validasi di server.
2. Jika berhasil, alert sukses tampil dan data terbaru dirender.
3. Jika gagal, pesan error tampil per field.

**Constraints:**
- Perubahan nilai/tipe berlaku untuk transaksi berikutnya, bukan transaksi lama.

### Menghapus Diskon

**Trigger:** Klik "Hapus".

**Expected behavior:**
1. Dialog konfirmasi muncul.
2. Konfirmasi mengirim `POST /discounts/:id/delete`.
3. Redirect ke `/discounts` dengan alert sukses.

## 6. Page States

### Default

- Semua field terisi data diskon saat ini.

### Loading

- Tombol simpan/hapus menampilkan loading dan nonaktif.

### Empty

- Tidak berlaku.

### Error

- Diskon tidak ditemukan: 404.
- Bukan milik warung user: 403/404.
- Validasi gagal: pesan per field.

### Success

- Alert sukses setelah simpan.

### Disabled / Permission Denied

- Tombol simpan nonaktif saat tidak ada perubahan atau saat request berjalan.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| Nama | Wajib, maks. 100 karakter | Nama diskon wajib diisi |
| Tipe | Wajib: `persen` atau `nominal` | Tipe diskon wajib dipilih |
| Nilai | Wajib, bilangan bulat > 0 | Nilai diskon harus lebih dari 0 |
| Nilai (persen) | 1–100 | Persentase diskon harus antara 1 dan 100 |

### Business Rules

- Perubahan tipe diskon harus diikuti nilai yang valid untuk tipe tersebut.
- Diskon yang sudah dipakai transaksi tetap tersimpan; transaksi lama menyimpan nilai diskon sebagai snapshot.

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Lihat detail | GET | `/discounts/:id` | — |
| Simpan perubahan | POST | `/discounts/:id` | Form POST, CSRF token wajib |
| Hapus | POST | `/discounts/:id/delete` | Form POST |

### Request Data

- `name`, `type`, `value`, `is_active`.

### Response Data

- Halaman detail dengan alert sukses atau pesan validasi.

### Error Handling

- 404 jika tidak ada; 403 jika bukan milik warung user.
- Error validasi dipetakan ke field.

### Data Dependencies

- Diskon milik warung user.

## 9. Accessibility

- Semua input memiliki label yang terlihat.
- Error dikaitkan ke field melalui `aria-describedby`.
- Dialog konfirmasi hapus menjebak fokus dan dapat ditutup dengan Escape.
- Tombol hapus memiliki accessible name yang jelas.

## 10. Acceptance Criteria

- [ ] Detail hanya dapat diakses untuk diskon milik warung user.
- [ ] Data diskon dapat diubah dan tersimpan.
- [ ] Persentase di luar 1–100 ditolak.
- [ ] Nilai 0 atau negatif ditolak.
- [ ] Hapus menampilkan dialog konfirmasi dan diskon tidak lagi tampil di daftar (perilaku akhir mengikuti Open Decisions).
- [ ] Akses ke diskon warung lain menghasilkan 403/404.
- [ ] Layout responsif di mobile, tablet, dan desktop.

## 11. Out of Scope

- Riwayat perubahan diskon.
- Melihat daftar transaksi yang memakai diskon ini.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Apakah perlu menampilkan berapa kali diskon ini sudah dipakai? | Query tambahan dan UI | Open |

## 13. References

- [Discounts page](discounts-page.md)
- [Kasir page](../kasir/kasir-page.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
