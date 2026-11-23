# Navigation Flow

## Global Navigation

Semua route authenticated dilindungi oleh dependency `get_current_user`. Jika session tidak valid, user di-redirect ke `/login`. Setelah login berhasil, user diarahkan ke intended destination yang tervalidasi atau ke `/`.

## Route Map

> Notasi `:id` di tabel ini hanya untuk keterbacaan. Di kode FastAPI, tulis sebagai `{id}` (contoh: `@router.get("/products/{id}")`).

| Method | Route | Fungsi | Access |
|---|---|---|---|
| GET | `/login` | Tampilkan form login | Public |
| POST | `/login` | Proses login | Public |
| POST | `/logout` | Proses logout | Authenticated |
| GET | `/profile` | Halaman profil user | Authenticated |
| POST | `/profile` | Simpan perubahan profil | Authenticated |
| GET | `/` | Halaman kasir | Authenticated |
| POST | `/` | Proses transaksi | Authenticated |
| GET | `/products` | List produk + stok | Authenticated |
| POST | `/products` | Simpan produk baru | Authenticated |
| GET | `/products/:id` | Detail + form edit produk | Authenticated |
| POST | `/products/:id` | Simpan perubahan produk | Authenticated |
| POST | `/products/:id/delete` | Hapus produk | Authenticated |
| GET | `/products/:id/stock` | Riwayat mutasi stok satu produk | Authenticated |
| POST | `/products/:id/stock` | Sesuaikan stok (membuat mutasi baru) | Authenticated |
| GET | `/api/products/export` | JSON data produk + stok (untuk export) | Authenticated |
| GET | `/stock/history` | Riwayat mutasi stok semua produk | Authenticated |
| GET | `/discounts` | List diskon | Authenticated |
| POST | `/discounts` | Simpan diskon baru | Authenticated |
| GET | `/discounts/:id` | Detail + form edit diskon | Authenticated |
| POST | `/discounts/:id` | Simpan perubahan diskon | Authenticated |
| POST | `/discounts/:id/delete` | Hapus diskon | Authenticated |
| GET | `/transactions` | History semua transaksi | Authenticated |
| GET | `/transactions/:id` | Detail satu transaksi | Authenticated |
| GET | `/api/transactions/export` | JSON data transaksi (untuk export) | Authenticated |

## Redirect Rules

- Route authenticated tanpa session valid → redirect ke `/login`
- User sudah login mengakses `/login` → redirect ke `/`
- Setelah login berhasil → redirect ke intended destination yang tervalidasi atau `/`
- Setelah logout → redirect ke `/login`

### Aturan Intended Destination

- Hanya menerima path internal relatif: string yang diawali `/` dan **bukan** `//` (untuk mencegah protocol-relative URL).
- Tolak URL absolut (`http://`, `https://`) dan path yang mengandung skema.
- Jika nilai tidak valid atau kosong → redirect ke `/`.
- Tujuan akhir tetap harus melewati pemeriksaan autentikasi biasa.

## Open Decisions

- Tidak ada route yang belum punya page spec.
