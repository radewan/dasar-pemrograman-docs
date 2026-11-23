# Profile Page

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | Profil |
| Route | `/profile` |
| Status | Draft |
| Priority | Must |
| Authentication | Authenticated |
| Responsive | Yes |

### Purpose

Memungkinkan user melihat dan mengubah data profilnya sendiri.

### User Goals

- Melihat data profil yang sedang dipakai.
- Mengubah data profil.

## 2. Entry Points & Navigation

### Entry Points

- Dropdown profil di topbar → item "Profil".

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| Simpan berhasil | `/profile` | Perubahan tersimpan |
| Batal | Halaman sebelumnya atau `/profile` | — |

### Access Rules

- Hanya untuk user yang sudah login.
- User hanya dapat melihat dan mengubah profil miliknya sendiri (difilter berdasarkan session).

## 3. Layout Structure

### Desktop

- Halaman dirender di dalam app layout (sidebar + topbar).
- Form profil dalam satu kolom, lebar terbatas agar mudah dibaca.

### Tablet

- Sama seperti desktop, lebar form menyesuaikan.

### Mobile

- Satu kolom penuh, mengikuti app layout mobile.

### Content Hierarchy

1. Judul halaman "Profil"
2. Form data profil
3. Tombol simpan

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| Form profil | Field data user yang dapat diubah | Yes | Input |
| Tombol simpan | Menyimpan perubahan | Yes | Button |
| Pesan sukses | Konfirmasi perubahan tersimpan | Yes | Alert |
| Pesan error | Validasi dan kegagalan simpan | Yes | Alert |

## 5. User Interactions

### Simpan Profil

**Trigger:** User menekan tombol simpan.

**Expected behavior:**
1. Validasi input di server (Pydantic).
2. Jika gagal, tampilkan pesan pada field terkait.
3. Jika berhasil, tampilkan pesan sukses dan tetap di `/profile`.
4. Cegah submit berulang selama request berjalan.

**Constraints:**
- User hanya dapat mengubah profilnya sendiri.

## 6. Page States

### Default

- Field terisi data profil saat ini.

### Loading

- Tombol simpan dinonaktifkan saat request berjalan.

### Error

- Pesan validasi ditampilkan per field; error server ditampilkan sebagai alert.

### Success

- Pesan sukses ditampilkan setelah perubahan tersimpan.

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| TBD | TBD | TBD |

### Business Rules

- TBD — field yang dapat diubah belum diputuskan (lihat Open Decisions).

## 8. API & Data Integration

| Action | Method | Endpoint | Notes |
|---|---|---|---|
| Lihat profil | GET | `/profile` | Render form dengan data user |
| Simpan profil | POST | `/profile` | Form POST, CSRF token wajib |

### Request / Response

- Form dikirim sebagai `application/x-www-form-urlencoded`.
- Validasi via Pydantic schema.
- Error dikembalikan ke halaman yang sama dengan pesan di context Jinja2.

### Data Dependencies

- Data user dan warung dari session yang sedang login.

## 9. Accessibility

- Setiap input memiliki label yang terlihat.
- Error dikaitkan dengan field terkait menggunakan `aria-describedby`.
- Semua kontrol dapat dioperasikan dengan keyboard.
- Fokus keyboard terlihat jelas.

## 10. Acceptance Criteria

- [ ] User dapat membuka `/profile` dari dropdown topbar.
- [ ] Form menampilkan data profil user yang sedang login.
- [ ] User dapat menyimpan perubahan dan melihat pesan sukses.
- [ ] Validasi menampilkan pesan pada field yang bermasalah.
- [ ] User tidak dapat mengakses atau mengubah profil user lain.
- [ ] Halaman responsif di mobile, tablet, dan desktop.

## 11. Out of Scope

- Unggah foto/avatar (avatar tetap inisial huruf pertama username).
- Pengaturan multi-user per warung.
- Hapus akun.

## 12. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Field apa saja yang dapat diubah (nama, username, password)? | Skema form, validasi, dan skema Pydantic | Open |
| Apakah username boleh diubah? | Validasi keunikan dan potensi kebingungan login | Open |
| Apakah ganti password memerlukan password lama? | Alur form dan keamanan | Open |

Jangan membuat asumsi untuk keputusan yang masih terbuka.

## 13. References

- [App layout](../app-layout.md)
- [Navigation flow](../../flows/navigation-flow.md)
- [Authentication flow](../../flows/authentication-flow.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
