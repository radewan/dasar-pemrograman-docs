# Frontend Conventions

## General

- Ikuti struktur folder dan pola yang telah disepakati.
- Hindari duplikasi komponen dan business logic.
- Gunakan nama yang jelas dan konsisten.
- Jangan menambahkan dependency tanpa alasan yang jelas.

## Components

- Pisahkan komponen reusable dari komponen khusus halaman.
- Hindari komponen raksasa dengan terlalu banyak tanggung jawab.
- Dokumentasikan komponen kompleks atau yang memiliki API khusus.

## Forms

- Label harus terhubung dengan input.
- Validasi client-side meningkatkan UX, tetapi bukan pengganti validasi server.
- Tampilkan loading, success, dan error state.
- Jangan menyimpan password atau secret di browser storage.

## API

- Ikuti kontrak API yang tersedia.
- Jangan menebak endpoint atau response.
- Pusatkan konfigurasi base URL.
- Tangani network error dan error response secara konsisten.

## Accessibility

- Gunakan semantic HTML.
- Pastikan keyboard navigation.
- Pastikan focus state terlihat.
- Berikan accessible name pada kontrol.

## Testing

- Uji perilaku penting, bukan hanya tampilan.
- Tambahkan test untuk validasi dan alur error.
- Verifikasi acceptance criteria pada spesifikasi halaman.

## Documentation

- Baca spesifikasi sebelum implementasi.
- Catat keputusan yang belum pasti sebagai open decision.
- Perbarui dokumentasi saat requirement berubah.
