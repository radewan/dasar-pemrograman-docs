# Architecture

## Ringkasan

Website POS (Point of Sale) multi-tenant untuk warung di Indonesia. Dibangun dengan FastAPI sebagai backend framework menggunakan server-side rendering (SSR) via Jinja2. Setiap warung memiliki akun sendiri dengan data yang terisolasi antar tenant. Satu akun per warung merangkap fungsi kasir dan pengelola stok. Styling menggunakan TailwindCSS.

## Technology Stack

| Area | Technology | Version | Status |
|---|---|---|---|
| Framework | FastAPI | 0.111+ | Approved |
| Language | Python | 3.11+ | Approved |
| Database | MySQL | 8.0+ | Approved |
| ORM | SQLModel | 0.0.19+ | Approved |
| Migrasi DB | Alembic | 1.13+ | Approved |
| Styling | TailwindCSS | 3.x | Approved |
| Routing | FastAPI router | (bawaan FastAPI) | Approved |
| Template Engine | Jinja2 (`Jinja2Templates`) | 3.x | Approved |
| Auth | Session-based via cookie + `itsdangerous` | — | Approved |
| CSRF | `starlette-csrf` | — | Approved |
| Form parsing | Pydantic + `python-multipart` | — | Approved |
| Testing | pytest + `httpx` | — | Proposed |
| Tooling & Dependency | `uv` | 0.4+ | Approved |

## Application Structure

Aplikasi menggunakan struktur paket `app/` yang modular dan dikelola menggunakan **`uv`**. Detail lengkap mengenai fungsi per layer, konfigurasi `pyproject.toml`, dan panduan setup tersedia di **[Project Structure](project-structure.md)**.

```
warunghwa/
├── alembic/               # Migrasi database
│   └── versions/
├── app/
│   ├── main.py            # Entry point FastAPI app
│   ├── config.py          # Konfigurasi (pydantic-settings, env vars)
│   ├── database.py        # Koneksi database, session SQLModel
│   ├── dependencies/      # FastAPI dependencies (auth guard, get_current_user)
│   ├── models/            # SQLModel table definitions
│   ├── schemas/           # Pydantic schemas (validasi form/JSON)
│   ├── routers/
│   │   ├── auth.py        # /login, /logout
│   │   ├── profile.py     # /profile
│   │   ├── kasir.py       # Kasir (/)
│   │   ├── products.py    # CRUD produk; /products/{id}/stock; /api/products/export
│   │   ├── inventory.py   # /stock/history (riwayat mutasi stok semua produk)
│   │   ├── discounts.py   # CRUD diskon
│   │   └── transactions.py# /transactions, /{id}, /api/transactions/export
│   ├── templates/         # HTML templates (Jinja2)
│   │   ├── base.html
│   │   ├── 403.html
│   │   ├── 404.html
│   │   ├── auth/
│   │   ├── profile/
│   │   ├── kasir/
│   │   ├── products/
│   │   ├── inventory/
│   │   ├── discounts/
│   │   └── transactions/
│   └── static/
│       ├── css/           # Output TailwindCSS
│       └── js/            # JavaScript: interaksi UI, export PDF/Excel (client-side)
├── tests/                 # Unit & integration tests
├── pyproject.toml         # Konfigurasi dependensi uv & FastAPI entrypoint
└── uv.lock                # Lockfile dependensi uv
```

**Multi-tenant:** Setiap model data utama (produk, stok, transaksi) memiliki foreign key ke `Warung` (tenant). Query selalu difilter berdasarkan warung dari user yang sedang login sehingga data antar warung tidak bocor.

## Data Flow

### Halaman Umum (SSR)
```
User (Browser)
    │
    ▼
FastAPI Router (request → dependency auth → validasi → query)
    │
    ▼
SQLModel → MySQL Database
    │
    ▼
Jinja2Templates (render HTML)
    │
    ▼
User (Browser) ← Response HTML
```

### Export PDF / Excel (Client-side)
```
User klik tombol Export
    │
    ▼
JavaScript fetch → FastAPI endpoint (JSON)
    │
    ▼
FastAPI Router → SQLModel → MySQL
    │
    ▼
Response JSON (data produk/transaksi)
    │
    ▼
JavaScript generate PDF/Excel di browser
    │
    ▼
File langsung di-download oleh user
```

- Halaman utama menggunakan SSR penuh via Jinja2.
- Export data produk dan transaksi (PDF/Excel) menggunakan client-side rendering: server hanya mengembalikan data JSON, file di-generate di browser oleh JavaScript.
- Cetak struk bersifat opsional — user bisa memilih print atau tidak setelah transaksi selesai, menggunakan `window.print()` di browser.
- JavaScript juga digunakan untuk interaksi UI sederhana (misalnya: auto-hitung total belanja di halaman kasir).

## Authentication

- Session-based menggunakan signed cookie (`itsdangerous.TimestampSigner`).
- Login menghasilkan cookie terenkripsi yang menyimpan ID user/warung.
- Semua route protected menggunakan FastAPI dependency (`get_current_user`) yang membaca dan memverifikasi cookie.
- Jika cookie tidak ada atau tidak valid, dependency redirect ke `/login`.
- Setiap warung memiliki satu akun `User` yang terhubung ke objek `Warung`.
- Registrasi akun baru dilakukan secara manual oleh vendor (tidak ada self-register).
- CSRF protection diterapkan via middleware `starlette-csrf` untuk semua form POST.

## Error Handling

- **404 / 403:** FastAPI exception handler custom yang me-render template `404.html` / `403.html`.
- **Form validation:** Validasi dilakukan via Pydantic di layer schema; error dikembalikan ke template Jinja2 dan ditampilkan di halaman form.
- **Database error:** Menggunakan `try/except` pada operasi kritis (misalnya: proses transaksi), dengan pesan error yang ramah ditampilkan ke user.
- **Akses lintas tenant:** Setiap route memverifikasi bahwa objek yang diakses milik warung user yang sedang login; jika tidak, kembalikan 403 atau redirect.

## Client-side Libraries

| Kebutuhan | Library | Cara pakai |
|---|---|---|
| Export PDF | jsPDF | Via CDN, generate di browser |
| Export Excel | SheetJS (xlsx) | Via CDN, generate file `.xlsx` di browser |
| Cetak struk | `window.print()` | Bawaan browser, opsional |
| Icon | Heroicons | Via CDN SVG inline atau copy-paste langsung ke template Jinja2 |

## Open Decisions

- Deploy: belum diputuskan — scope saat ini adalah final project semester 1; deployment bisa dijalankan secara lokal.
