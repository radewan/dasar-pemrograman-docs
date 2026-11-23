# Authentication Flow

## Tujuan

Mendokumentasikan perjalanan pengguna terkait login dan logout. Registrasi dan pemulihan akun berada di luar scope — akun dibuat manual oleh vendor.

## Login Flow

```mermaid
flowchart TD
    A[Pengguna membuka /login] --> B[Isi username dan password]
    B --> C{Validasi input}
    C -- Tidak valid --> D[Tampilkan pesan validasi di field terkait]
    D --> B
    C -- Valid --> E[Kirim POST /login]
    E --> F{Hasil autentikasi}
    F -- Gagal --> G[Tampilkan pesan error, username dipertahankan]
    G --> B
    F -- Berhasil --> H[Set cookie session]
    H --> I[Redirect ke intended destination yang tervalidasi, atau /]
```

## Logout Flow

```mermaid
flowchart TD
    A[Klik area avatar/username di topbar] --> B[Dropdown profil terbuka]
    B --> C[Klik Logout]
    C --> D[Dialog konfirmasi muncul]
    D -- Batal --> E[Dialog tertutup, tetap di halaman]
    D -- Keluar --> F[POST /logout]
    F --> G[Hapus cookie session]
    G --> H[Redirect ke /login]
```

## Related Pages

- [Login](../pages/account/login-page.md)
- [App Layout](../pages/app-layout.md)

## Open Decisions

- Tidak ada open decisions.
