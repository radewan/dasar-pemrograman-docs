# Color Palette

## Warna Utama

| Token | Nama | Hex | Kegunaan |
|---|---|---|---|
| `--pink-primary` | Pink Sakura | `#F2A7BB` | Warna brand utama, highlight, border aktif |
| `--pink-dark` | Deep Rose | `#C2607A` | Tombol utama, teks aksen, hover state |
| `--pink-light` | Blush | `#FDE8EE` | Background aktif nav, badge pink |

## Warna Netral

| Token | Nama | Hex | Kegunaan |
|---|---|---|---|
| `--bg` | Cream | `#FDF6F0` | Background halaman |
| `--surface` | Cream Light | `#FFFAF5` | Background card, modal, sidebar |
| `--border` | Pink Mist | `#F0D6DC` | Border card, divider, input |
| `--text` | Dark Gray | `#1A1A1A` | Teks utama |
| `--text-muted` | Medium Gray | `#6B7280` | Label, placeholder, teks sekunder |

## Warna Status

| Token | Nama | Hex | Kegunaan |
|---|---|---|---|
| `--success` | Green | `#22C55E` | Transaksi berhasil, stok cukup |
| `--error` | Red | `#EF4444` | Pesan error, validasi gagal |
| `--warning` | Amber | `#F59E0B` | Peringatan stok menipis |

## Aturan Penggunaan

- Tombol aksi utama selalu menggunakan `--pink-dark` sebagai background.
- Jangan menggunakan `--pink-primary` sebagai warna teks di atas background terang — kontrasnya tidak cukup.
- Status color (success, error, warning) hanya untuk feedback dan indikator, bukan dekorasi.
- Background halaman selalu `--bg`; card dan komponen menggunakan `--surface`.
