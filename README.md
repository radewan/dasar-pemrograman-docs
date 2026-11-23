# Website Documentation Template

Template dokumentasi untuk merencanakan dan mengembangkan website secara bertahap, termasuk ketika menggunakan AI Agent.

## Cara menggunakan

1. Isi `product/overview.md`, `goals.md`, dan `scope.md` terlebih dahulu.
2. Tentukan design system di folder `design/`.
3. Salin `pages/_page-template.md` untuk setiap halaman baru.
4. Gunakan `pages/account/login-page.md` sebagai contoh spesifikasi yang sudah diisi.
5. Dokumentasikan alur lintas halaman di `flows/`.
6. Lengkapi dokumentasi teknis di `technical/`.
7. Perbarui status dan acceptance criteria ketika implementasi berubah.

## Prinsip

- Satu file untuk satu halaman.
- Bedakan kebutuhan yang sudah diputuskan dengan keputusan yang masih terbuka.
- Jangan mengarang endpoint, response API, atau business logic.
- Hindari mengulang aturan global di setiap spesifikasi halaman.
- Spesifikasi menjelaskan *apa* dan *bagaimana perilakunya*; dokumentasi teknis menjelaskan standar implementasinya.

## Status yang disarankan

`Draft` → `Ready for Design` → `Ready for Development` → `In Progress` → `In Review` → `Done`

Gunakan `Blocked` jika ada keputusan atau dependensi yang menghambat pekerjaan.
# dasar-pemrograman-docs
