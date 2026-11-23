# Rekomendasi Struktur Project FastAPI & Panduan Tooling (`uv`)

Dokumen ini berisi rekomendasi struktur direktori untuk **WarungHwa**. Karena FastAPI bersifat **unopinionated** (tidak memaksakan pola folder tertentu seperti Django atau Rails), dokumen ini menetapkan standar arsitektur dan konvensi struktur file yang optimal untuk aplikasi berbasis **FastAPI + Jinja2 (SSR) + SQLModel + MySQL** dengan manajemen dependensi modern menggunakan **`uv`**.

---

## 1. Filosofi & Alasan Pemilihan Struktur

FastAPI memberikan kebebasan penuh kepada developer. Dalam praktiknya, ada 3 pola umum yang sering digunakan di ekosistem FastAPI:

| Pola Arsitektur | Karakteristik | Kesesuaian untuk WarungHwa |
|---|---|---|
| **Single File / Flat** | Semua route, model, dan logic ada di `main.py` atau beberapa file root. | ❌ **Tidak cocok.** Hanya untuk prototipe mini/microservice 1-2 endpoint. Akan menjadi spaghetti code saat menangani 6 domain fitur. |
| **Feature-based (Vertical Slices)** | Folder per fitur (misal `app/products/` berisi `router.py`, `models.py`, `templates/`). | ⚠️ **Kurang optimal untuk SSR.** Sulit mengelola template Jinja2 global (`base.html`), migrasi Alembic terfragmentasi, dan dependensi auth tenant lintas-fitur jadi berulang. |
| **Layered Modular Architecture** | Kode dikelompokkan berdasarkan tanggung jawab teknis (`models/`, `schemas/`, `routers/`, `templates/`, `dependencies/`). | ✅ **Sangat Direkomendasikan.** Memisahkan logika database, validasi HTTP, dan rendering template secara bersih. Sangat alami untuk aplikasi SSR dan tim semester 1. |

**Struktur yang direkomendasikan adalah Layered Modular Architecture di dalam package `app/`.**

---

## 2. Struktur Direktori Lengkap

```text
warunghwa/
├── .venv/                    # Virtual environment lokal (dikelola otomatis oleh uv)
├── alembic/                  # Konfigurasi & versi migrasi database MySQL
│   ├── versions/             # File revisi migrasi ter-generate
│   └── env.py                # Setup runtime Alembic membaca SQLModel metadata
├── app/                      # Package utama aplikasi
│   ├── __init__.py
│   ├── main.py               # Entry point: instansiasi FastAPI, middleware CSRF, static mount, router registration
│   ├── config.py             # Pengaturan aplikasi via pydantic-settings (membaca .env)
│   ├── database.py           # Engine database, koneksi MySQL, generator get_session()
│   │
│   ├── dependencies/         # Reusable FastAPI dependency injection (Depends)
│   │   ├── __init__.py
│   │   └── auth.py           # get_current_user (baca signed cookie itsdangerous, validasi warung/tenant)
│   │
│   ├── models/               # Definisi tabel database (SQLModel Table=True)
│   │   ├── __init__.py       # Re-export semua model untuk registrasi metadata Alembic
│   │   ├── warung.py         # Entitas Warung (Tenant)
│   │   ├── user.py           # Entitas User (Akun login pemilik warung)
│   │   ├── product.py        # Entitas Produk & Kategori
│   │   ├── stock_mutation.py # Entitas Riwayat Mutasi Stok
│   │   ├── discount.py       # Entitas Diskon
│   │   └── transaction.py    # Entitas Transaksi & ItemTransaksi
│   │
│   ├── schemas/              # Validasi Pydantic (Request Form & Response JSON)
│   │   ├── __init__.py
│   │   ├── auth.py           # Schema validasi form login & edit profil
│   │   ├── product.py        # Schema input tambah/edit produk & format export
│   │   ├── discount.py       # Schema input diskon
│   │   └── transaction.py    # Schema payload kasir & format export transaksi
│   │
│   ├── routers/              # Controller HTTP (APIRouter) & handler SSR Jinja2
│   │   ├── __init__.py
│   │   ├── auth.py           # GET/POST /login, POST /logout
│   │   ├── profile.py        # GET/POST /profile
│   │   ├── kasir.py          # GET / (POS Kasir), POST / (Proses transaksi)
│   │   ├── products.py       # GET/POST /products, /{id}, /{id}/delete, /{id}/stock, /api/products/export
│   │   ├── inventory.py      # GET /stock/history (Semua produk)
│   │   ├── discounts.py      # CRUD /discounts, /{id}, /{id}/delete
│   │   └── transactions.py   # GET /transactions, /{id}, /api/transactions/export
│   │
│   ├── templates/            # Template HTML Jinja2 (SSR)
│   │   ├── base.html         # Shell autentikasi (sidebar 5-menu, topbar, toast alert)
│   │   ├── 403.html          # Template halaman forbidden (akses lintas tenant)
│   │   ├── 404.html          # Template halaman not found
│   │   ├── auth/
│   │   │   └── login.html    # Form login publik
│   │   ├── profile/
│   │   │   └── index.html    # Tampilan & form edit profil
│   │   ├── kasir/
│   │   │   └── index.html    # Halaman kasir (grid produk, keranjang, hitung kembalian)
│   │   ├── products/
│   │   │   ├── index.html    # Tabel daftar produk & modal tambah
│   │   │   ├── detail.html   # Detail produk & form mutasi stok
│   │   │   └── stock.html    # Riwayat mutasi stok produk spesifik
│   │   ├── inventory/
│   │   │   └── history.html  # Riwayat mutasi stok semua produk
│   │   ├── discounts/
│   │   │   ├── index.html    # Tabel diskon & modal tambah
│   │   │   └── detail.html   # Detail & edit diskon
│   │   └── transactions/
│   │       ├── index.html    # Tabel riwayat transaksi + filter tanggal & tombol export
│   │       └── detail.html   # Detail transaksi + struk cetak
│   │
│   └── static/               # Asset statis yang di-serve oleh FastAPI
│       ├── css/              # Output Tailwind CSS (dist/tailwind.css)
│       └── js/               # Script client-side (kalkulasi kasir, jsPDF, SheetJS)
│
├── tests/                    # Automated testing suite
│   ├── conftest.py           # Pytest fixtures (DB test in-memory/test MySQL, client httpx)
│   ├── test_auth.py          # Pengujian login, logout, dan cookie
│   ├── test_kasir.py         # Pengujian transaksi & pengurangan stok
│   └── test_products.py      # Pengujian CRUD & isolasi multi-tenant
│
├── .env.example              # Contoh variabel konfigurasi environment
├── alembic.ini               # Konfigurasi file Alembic
├── pyproject.toml            # Definisi project, dependensi, dan setting tool
└── uv.lock                   # Lockfile dependensi biner/versi eksak dari uv
```

---

## 3. Tanggung Jawab Tiap Lapisan (Layer Responsibilities)

1. **`app/main.py` (Application Factory & Setup)**
   - Menginstansiasi `FastAPI()`.
   - Mendaftarkan middleware penting: `starlette-csrf` (keamanan form POST) dan exception handler untuk halaman 404/403.
   - Melakukan mounting asset statis: `app.mount("/static", StaticFiles(directory="app/static"), name="static")`.
   - Mendaftarkan semua router via `app.include_router(...)`.

2. **`app/dependencies/auth.py` (Guard Multi-Tenant)**
   - Mengambil cookie sesi terenkripsi/signed (`itsdangerous`).
   - Memverifikasi apakah session valid; jika tidak valid, melempar redirect ke `/login`.
   - Mengambil data `Warung` yang aktif dan menginjeksikannya ke handler router via `Depends(get_current_user)`.
   - Memastikan tidak ada query yang bocor antar warung.

3. **`app/models/` (Data Persistence / SQLModel)**
   - Mewakili skema tabel di MySQL (`SQLModel, table=True`).
   - Setiap tabel bisnis (`Product`, `Transaction`, `StockMutation`, `Discount`) wajib memiliki kolom `warung_id: int = Field(foreign_key="warung.id")`.

4. **`app/schemas/` (Data Validation / Pydantic)**
   - Memvalidasi data yang dikirim melalui form HTML (`Form(...)` via `python-multipart`).
   - Memvalidasi dan membentuk payload JSON untuk endpoint export (`/api/products/export` & `/api/transactions/export`).

5. **`app/routers/` (HTTP & View Handlers)**
   - Berfungsi sebagai jembatan: menerima input user -> memanggil dependency -> menjalankan query SQLModel berfilter `warung_id` -> mengembalikan `templates.TemplateResponse(...)`.

6. **`app/templates/` (Presentasi SSR Jinja2)**
   - Template bersih yang mewarisi layout `base.html`.
   - Tidak berisi business logic; hanya membaca context yang dikirimkan oleh router.

---

## 4. Panduan Setup Project Menggunakan `uv`

### A. Inisialisasi Baru

```bash
# 1. Buat folder proyek baru tanpa file template bawaan
uv init warunghwa --bare
cd warunghwa

# 2. Pasang FastAPI versi standar (otomatis mencakup uvicorn, jinja2, python-multipart)
uv add "fastapi[standard]"

# 3. Pasang paket inti pendukung database, autentikasi, dan konfigurasi
uv add sqlmodel pymysql alembic itsdangerous starlette-csrf pydantic-settings

# 4. Pasang paket untuk pengujian & kualitas kode (dev tools)
uv add --dev pytest httpx ruff
```

### B. Konfigurasi `pyproject.toml`

Tambahkan blok konfigurasi berikut pada file `pyproject.toml` agar perintah CLI `fastapi` dapat mendeteksi file entry point secara otomatis:

```toml
[tool.fastapi]
entrypoint = "app.main:app"

[tool.pytest.ini_options]
testpaths = ["tests"]
```

---

## 5. Cheat Sheet Perintah Harian (`uv run`)

Semua instruksi dijalankan tanpa perlu mengaktifkan virtual environment secara manual:

| Tugas | Perintah | Fungsi |
|---|---|---|
| **Menjalankan Server Dev** | `uv run fastapi dev` | Menjalankan aplikasi dengan auto-reload pada `localhost:8000` |
| **Menjalankan Server Prod** | `uv run fastapi run` | Menjalankan aplikasi dalam mode produksi tanpa reload |
| **Generate Migrasi Baru** | `uv run alembic revision --autogenerate -m "pesan"` | Mendeteksi perubahan di `app/models/` dan membuat file migrasi |
| **Menjalankan Migrasi DB** | `uv run alembic upgrade head` | Menjalankan perubahan skema ke database MySQL |
| **Menjalankan Unit Test** | `uv run pytest` | Menjalankan semua test di folder `tests/` |
| **Pengecekan Kode (Linting)** | `uv run ruff check .` | Memeriksa kepatuhan kode dan potensi bug |
| **Format Kode Otomatis** | `uv run ruff format .` | Memformat kode sesuai standar PEP 8 secara instan |
| **Sinkronisasi Repo Baru** | `uv sync` | Menyiapkan `.venv` persis sesuai `uv.lock` saat pertama kali clone |

---

## 6. Referensi Terkait

- [Technical Architecture](architecture.md)
- [Frontend Conventions](frontend-conventions.md)
- [Navigation Flow](../flows/navigation-flow.md)
