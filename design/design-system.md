# Design System

## Prinsip Desain

- Simpel dan fungsional — tidak perlu ornamen berlebihan, yang penting mudah dipakai
- Hangat dan friendly — dekat dengan pengguna pemilik warung
- Konsisten — komponen dan pola yang sama dipakai di seluruh aplikasi

## Platform dan Breakpoint

| Nama | Lebar | Perilaku |
|---|---:|---|
| Mobile | < 640px | Layout satu kolom, sidebar jadi drawer overlay |
| Tablet | 640–1023px | Layout menyesuaikan, sidebar bisa drawer |
| Desktop | ≥ 1024px | Layout dua kolom dengan sidebar permanen |

## Layout

- Max content width: tidak dibatasi — mengisi area konten di kanan sidebar
- Sidebar width: 220px (desktop)
- Spacing scale: mengikuti skala default TailwindCSS (4px base)
- Border radius: 12px untuk card, 8px untuk input dan tombol, 999px untuk badge

## Aturan Umum

- Gunakan token desain dari `color-palette.md` dan `typography.md`, bukan nilai acak.
- Jaga konsistensi jarak, radius, dan hierarki visual.
- Pastikan tampilan responsif di mobile, tablet, dan desktop.
- Prioritaskan keterbacaan — kontras teks harus memenuhi WCAG AA minimum.
