# Typography

## Font

| Peran | Font | Sumber |
|---|---|---|
| Semua teks | Inter | Google Fonts (`https://fonts.google.com/specimen/Inter`) |

Hanya satu font family untuk seluruh aplikasi — cukup untuk aplikasi POS yang simpel dan fungsional.

## Skala Ukuran

| Nama | Ukuran | Weight | Kegunaan |
|---|---|---|---|
| Heading L | 20px | 700 | Judul halaman |
| Heading M | 16px | 700 | Judul section, header card |
| Heading S | 15px | 700 | Sub-judul, total cart |
| Body | 14px | 400 | Teks umum, isi form, item menu |
| Body Medium | 14px | 500 | Label input, username topbar |
| Body Bold | 14px | 600 | Nama produk, item aktif sidebar |
| Small | 13px | 400–500 | Teks sekunder, cart item |
| Caption | 12px | 400–600 | Badge, harga produk, label palette |
| Tiny | 11px | 400–600 | Label swatch, keterangan kecil |

## Aturan Penggunaan

- Gunakan Inter dari Google Fonts via tag `<link>` di `base.html` — tidak perlu install lokal.
- Teks utama selalu `--text` (`#1A1A1A`); teks sekunder dan placeholder gunakan `--text-muted` (`#6B7280`).
- Hindari menggunakan lebih dari dua weight berbeda dalam satu komponen.
- Line height default: 1.5 untuk body, 1.2 untuk heading.
