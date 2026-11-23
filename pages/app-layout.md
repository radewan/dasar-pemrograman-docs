# App Layout

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | App Layout (Shell) |
| Route | Semua route authenticated `/*` |
| Status | Ready for Development |
| Priority | Must |
| Authentication | Required |
| Responsive | Yes |

### Purpose

Layout utama yang membungkus semua halaman authenticated. Terdiri dari sidebar navigasi dan topbar. Semua halaman (Kasir, Produk, Diskon, Transaksi, Riwayat Stok) dirender di dalam area konten layout ini.

---

## 2. Layout Structure

### Desktop

```
┌──────────────┬──────────────────────────────────────┐
│  WarungHwa   │              [Avatar] [Username] [▾] │
├──────────────┼──────────────────────────────────────┤
│              │                                      │
│ [icon] Kasir │         CONTENT AREA                 │
│ [icon] Produk│                                      │
│ [icon] Diskon│                                      │
│ [icon] Trans.│                                      │
│ [icon] Stok  │                                      │
│              │                                      │
└──────────────┴──────────────────────────────────────┘
```

> Label pada diagram disingkat. Label penuh: Kasir, Produk, Diskon, Transaksi, Riwayat Stok.

- Sidebar memanjang penuh dari atas ke bawah, menimpa area topbar di sisi kiri.
- Header sidebar berisi nama aplikasi "WarungHwa".
- Topbar hanya berada di area kanan (sebelah sidebar), berisi avatar + username + dropdown di kanan.
- Sidebar selalu expanded di desktop — tidak bisa di-collapse.
- Content area mengisi sisa lebar layar di sebelah kanan sidebar.

### Mobile

```
┌─────────────────────────────┐
│ TOPBAR                      │
│ [☰]          [Avatar] [▾]  │
├─────────────────────────────┤
│                             │
│       CONTENT AREA          │
│                             │
└─────────────────────────────┘
```

- Sidebar tersembunyi secara default di mobile.
- Topbar mobile berisi hamburger di kiri dan avatar+dropdown di kanan.
- Klik hamburger → sidebar muncul sebagai overlay/drawer dari kiri (berisi nama app + menu).
- Klik di luar drawer atau klik item menu → drawer tertutup.
- Username di topbar mobile disembunyikan untuk menghemat ruang.

---

## 3. Topbar

### Struktur

```
┌──────────────┬──────────────────────────────────────┐
│  WarungHwa   │              [Avatar] [Username] [▾] │
└──────────────┴──────────────────────────────────────┘
```

| Elemen | Posisi | Deskripsi |
|---|---|---|
| Nama aplikasi "WarungHwa" | Header sidebar (kiri atas) | Selalu tampil di desktop |
| Hamburger button (☰) | Kiri topbar (mobile only) | Membuka/menutup sidebar drawer — hanya tampil di mobile |
| Avatar | Kanan topbar | Lingkaran berisi huruf pertama username (kapital), bukan gambar |
| Username | Kanan topbar (setelah avatar) | Nama user yang sedang login — tampil di desktop, disembunyikan di mobile |
| Dropdown arrow (▾) | Paling kanan topbar | Membuka dropdown menu profil |

### Dropdown Profil

Muncul saat user klik area avatar/username/arrow. Berisi:

| Item | Aksi |
|---|---|
| Profil | Navigasi ke `/profile` |
| Laporkan Masalah | Membuka `https://wa.me/<nomor-vendor>` dengan pesan template (medium: WhatsApp) |
| Logout | Menampilkan dialog konfirmasi sebelum logout |

#### Dialog Konfirmasi Logout

- Muncul sebagai modal/dialog di tengah layar.
- Konten: *"Apakah Anda yakin ingin keluar?"*
- Tombol: **Batal** (tutup dialog) dan **Keluar** (proses logout).
- Setelah logout berhasil, redirect ke `/login`.

---

## 4. Sidebar

### Menu Navigasi

| Urutan | Icon (Heroicons) | Label | Route |
|---|---|---|---|
| 1 | `shopping-cart` | Kasir | `/` |
| 2 | `cube` | Produk | `/products` |
| 3 | `tag` | Diskon | `/discounts` |
| 4 | `document-text` | Transaksi | `/transactions` |
| 5 | `arrows-right-left` | Riwayat Stok | `/stock/history` |

> Catatan: Icon menggunakan **Heroicons** (outline, 24px) via SVG inline, dirender langsung di template Jinja2.

### Sidebar States

| State | Desktop | Mobile |
|---|---|---|
| Expanded | Icon + label tampil (satu-satunya state di desktop) | — |
| Drawer open | — | Sidebar muncul sebagai overlay dari kiri |
| Drawer closed | — | Sidebar tersembunyi |

### Active State

- Menu yang sedang aktif (halaman yang sedang dibuka) diberi highlight visual yang jelas (warna background berbeda atau indikator garis).

---

## 5. User Interactions

### Hamburger Button

- **Desktop:** Tidak ada — sidebar selalu visible dan tidak bisa di-collapse.
- **Mobile:** Tampil di kiri topbar. Klik → buka/tutup sidebar sebagai drawer overlay.

### Dropdown Profil

- Klik area avatar/username/arrow → dropdown muncul.
- Klik di luar dropdown → dropdown tertutup.
- Klik item menu → aksi dijalankan dan dropdown tertutup.

### Navigasi Sidebar

- Klik item menu → navigasi ke route yang sesuai.
- **Mobile:** Drawer tertutup otomatis setelah item menu diklik.

---

## 6. Accessibility

- Hamburger button memiliki `aria-label="Toggle sidebar"` dan `aria-expanded` yang diupdate sesuai state.
- Sidebar memiliki `role="navigation"` dan `aria-label="Main navigation"`.
- Item menu aktif memiliki `aria-current="page"`.
- Dropdown profil dapat dioperasikan dengan keyboard (Tab, Enter, Escape).
- Dialog konfirmasi logout menjebak fokus keyboard saat terbuka (focus trap).
- Semua kontrol interaktif memiliki fokus yang terlihat jelas.

---

## 7. Acceptance Criteria

- [ ] Topbar tampil di semua halaman authenticated.
- [ ] Sidebar selalu visible di desktop, tidak bisa di-collapse.
- [ ] Hamburger hanya tampil di mobile, berfungsi membuka/menutup drawer.
- [ ] Klik di luar drawer menutup drawer.
- [ ] Avatar menampilkan huruf pertama username (kapital).
- [ ] Username tampil di topbar desktop, tersembunyi di mobile.
- [ ] Klik avatar/username/arrow membuka dropdown profil.
- [ ] Klik di luar dropdown menutup dropdown.
- [ ] Dropdown berisi: Profil, Laporkan Masalah, Logout.
- [ ] Klik Logout memunculkan dialog konfirmasi.
- [ ] Konfirmasi logout berhasil redirect ke `/login`.
- [ ] Sidebar menampilkan 5 menu: Kasir, Produk, Diskon, Transaksi, Riwayat Stok.
- [ ] Setiap menu memakai icon Heroicons sesuai tabel Menu Navigasi.
- [ ] Menu aktif memiliki highlight visual yang jelas.
- [ ] Navigasi sidebar berfungsi ke route yang benar.
- [ ] Di mobile, klik menu sidebar menutup drawer.
- [ ] Layout responsive dan mobile friendly.

---

## 8. Open Decisions

| Question | Impact | Status |
|---|---|---|
| Nomor WhatsApp vendor untuk menu "Laporkan Masalah" belum ditentukan | Nilai `wa.me/<nomor>` di link | Open — ditunda sampai nomor tersedia |

---

## 9. References

- [Design system](../design/design-system.md)
- [Frontend conventions](../technical/frontend-conventions.md)
- [Navigation flow](../flows/navigation-flow.md)
