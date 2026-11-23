# Login Page

## 1. Overview

| Properti              | Nilai                                  |
| --------------------- | -------------------------------------- |
| Page name             | Login                                  |
| Route                 | `/login`                               |
| API Endpoint          | `/api/auth/login`                      |
| Status                | Ready for Development                  |
| Priority              | Must                                   |
| Authentication        | Public                                 |
| Responsive            | Yes                                    |
| Frontend              | Vite + React + TypeScript              |
| Backend               | FastAPI                                |
| Authentication Method | Server-side Session + HTTP-only Cookie |

### Purpose

Memungkinkan pengguna yang sudah memiliki akun untuk masuk ke aplikasi menggunakan username dan password.

### User Goals

- Login menggunakan username dan password.
- Memperpanjang masa login menggunakan Remember Me.
- Mendapatkan pesan yang jelas ketika proses login gagal.

---

## 2. Entry Points & Navigation

### Entry Points

- Pengguna dapat mengakses halaman melalui route `/login`.
- Semua route yang memerlukan autentikasi akan mengarahkan pengguna ke `/login` jika tidak memiliki session yang valid.
- Pengguna yang sudah login tidak dapat mengakses halaman login.

### Outgoing Navigation

| Trigger        | Destination                   | Conditions             |
| -------------- | ----------------------------- | ---------------------- |
| Login berhasil | `/` atau intended destination | Autentikasi berhasil   |
| Login gagal    | Tetap di `/login`             | Kredensial tidak valid |

### Access Rules

- Halaman dapat diakses tanpa login.
- Jika pengguna sudah memiliki session valid, arahkan langsung ke `/`.
- Validasi intended destination: hanya path internal relatif yang diawali `/` dan bukan `//`. Selain itu, redirect ke `/`.
- Tidak ada halaman register atau forgot password. Akun dikelola oleh vendor secara manual.
- Pemeriksaan autentikasi dilakukan melalui API backend, bukan hanya berdasarkan state React.

---

## 3. Layout Structure

### Desktop

- Layout dua kolom.
- Kolom kiri berisi branding: logo, nama aplikasi, dan tagline:

  "Kelola warung, catat penjualan, pantau stok — semua dalam satu tempat."

- Kolom kanan berisi card form login.
- Form menjadi fokus utama.

### Mobile

- Layout satu kolom.
- Branding dipersingkat (logo + nama aplikasi saja di bagian atas).
- Form login menjadi konten utama.

---

## 4. UI Components

| Component            | Description                         | Required | Reference      |
| -------------------- | ----------------------------------- | -------- | -------------- |
| Brand logo           | Identitas aplikasi                  | Yes      | Design system  |
| Username input       | Username akun                       | Yes      | Input          |
| Password input       | Password akun                       | Yes      | Password input |
| Password toggle      | Menampilkan/menyembunyikan password | Yes      | Password input |
| Remember me checkbox | Memperpanjang durasi session        | Yes      | Checkbox       |
| Login button         | Mengirim request login              | Yes      | Button         |
| Form error message   | Menjelaskan kegagalan login         | Yes      | Alert          |

---

## 5. User Interactions

### Username Input

- Pengguna dapat memasukkan username.
- Field wajib diisi.
- Jangan menghapus username ketika login gagal.

### Password Input

- Pengguna dapat memasukkan password.
- Password disembunyikan secara default.
- Terdapat icon eye di dalam field.
- Klik icon untuk menampilkan password.
- Klik kembali untuk menyembunyikan password.
- Toggle tidak mengubah nilai password.
- Field wajib diisi.

### Remember Me Checkbox

- Jika dicentang, session diperpanjang selama 30 hari.
- Backend mengatur masa berlaku session dan cookie.
- Jika tidak dicentang, cookie menjadi session cookie tanpa atribut `Max-Age` atau `Expires`.
- Status checkbox tidak memengaruhi proses verifikasi kredensial.

### Login Button

1. Validasi input di frontend.
2. Jika validasi gagal, tampilkan pesan pada field terkait.
3. Jika valid, kirim HTTP POST request ke backend.
4. Sertakan CSRF token pada request.
5. Sertakan credentials agar browser dapat menerima cookie dari backend.
6. Cegah pengiriman berulang selama request berjalan (disable tombol + loading state).
7. Jika berhasil, simpan informasi user yang dikembalikan backend ke authentication state React.
8. Redirect ke intended destination yang tervalidasi atau `/`.
9. Jika gagal, tampilkan pesan error yang aman.

---

## 6. Page States

### Default

- Field kosong.
- Password tersembunyi.
- Remember Me tidak dicentang.
- Tombol login aktif.

### Loading

- Tampilkan indikator loading pada tombol.
- Nonaktifkan tombol submit untuk mencegah request berulang.

### Validation Error

- Tampilkan pesan pada field yang bermasalah.
- Arahkan fokus ke field pertama yang error.
- Tidak perlu mengirim request ke backend jika validasi dasar gagal.

### Authentication Error

Tampilkan pesan berikut ketika kredensial tidak valid:

> Username atau password yang Anda masukkan salah. Jika Anda lupa kredensial login, silakan hubungi kami untuk mendapatkan bantuan.

- Pertahankan username yang telah dimasukkan.
- Jangan tampilkan stack trace atau detail internal server.
- Jangan membocorkan apakah username terdaftar atau tidak.

### Network Error

Jika backend tidak dapat dihubungi:

> Tidak dapat terhubung ke server. Silakan periksa koneksi internet Anda dan coba kembali.

- Pertahankan input username.
- Jangan menghapus data form.

### Success

- Browser menerima session cookie dari backend.
- Authentication state React diperbarui.
- Redirect ke intended destination yang tervalidasi atau `/`.

---

## 7. Validation & Business Rules

| Field    | Requirement | Error Message        |
| -------- | ----------- | -------------------- |
| Username | Required    | Username wajib diisi |
| Password | Required    | Password wajib diisi |

### Business Rules

- Tidak ada fitur register mandiri.
- Tidak ada fitur forgot password.
- Akun dibuat dan dikelola oleh vendor.
- Batas percobaan login dan rate limiting ditangani backend.
- Password tidak boleh disimpan di localStorage, sessionStorage, maupun cookie.
- Session ID tidak boleh disimpan dalam state React sebagai pengganti cookie.
- Session ID harus menggunakan nilai acak yang aman secara kriptografis.
- Session harus dapat dicabut ketika pengguna logout.
- Backend harus memvalidasi session pada setiap endpoint yang memerlukan autentikasi.

---

## 8. API & Data Integration

### 8.1 API Endpoints

| Action           | Method | Endpoint           | Authentication |
| ---------------- | ------ | ------------------ | -------------- |
| Get CSRF Token   | GET    | `/api/auth/csrf`   | Public         |
| Login            | POST   | `/api/auth/login`  | Public         |
| Get Current User | GET    | `/api/auth/me`     | Session Cookie |
| Logout           | POST   | `/api/auth/logout` | Session Cookie |

### 8.2 Login Request

**Endpoint:** `POST /api/auth/login`

**Content-Type:** `application/json`

**Request Body:**

```json
{
  "username": "rangga",
  "password": "user-password",
  "remember_me": true
}
```

| Field       | Type    | Required | Description               |
| ----------- | ------- | -------- | ------------------------- |
| username    | string  | Yes      | Username pengguna         |
| password    | string  | Yes      | Password pengguna         |
| remember_me | boolean | Yes      | Menentukan durasi session |

**Headers:**

```http
Content-Type: application/json
X-CSRF-Token: <csrf-token>
```

Frontend wajib menggunakan `credentials: "include"` pada request agar browser dapat mengirim dan menerima cookie.

### 8.3 Login Success Response

**HTTP Status:** `200 OK`

```json
{
  "message": "Login berhasil.",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "username": "rangga"
  }
}
```

**Response Headers:**

Backend mengirim session cookie melalui header `Set-Cookie`, bukan melalui response JSON.

Contoh ketika Remember Me aktif:

```http
Set-Cookie: session=<opaque-session-id>; HttpOnly; Secure; SameSite=Lax; Path=/; Max-Age=2592000
```

Contoh ketika Remember Me tidak aktif:

```http
Set-Cookie: session=<opaque-session-id>; HttpOnly; Secure; SameSite=Lax; Path=/
```

Catatan:

- `HttpOnly`: JavaScript tidak dapat membaca cookie.
- `Secure`: Cookie hanya dikirim melalui HTTPS. Untuk development HTTP lokal, pengaturan ini perlu disesuaikan.
- `SameSite=Lax`: Membatasi pengiriman cookie pada konteks cross-site tertentu.
- `Path=/`: Cookie berlaku untuk seluruh path aplikasi.
- `Max-Age=2592000`: Masa berlaku 30 hari.
- Tanpa `Max-Age` dan `Expires`, cookie menjadi session cookie.

Session ID tidak boleh dikembalikan di dalam response JSON.

### 8.4 Login Error Response

**HTTP Status:** `401 Unauthorized`

```json
{
  "detail": "Username atau password yang Anda masukkan salah. Jika Anda lupa kredensial login, silakan hubungi kami untuk mendapatkan bantuan."
}
```

Backend harus menggunakan pesan yang sama untuk username yang tidak ditemukan maupun password yang salah.

### 8.5 Validation Error Response

**HTTP Status:** `422 Unprocessable Entity`

Contoh:

```json
{
  "detail": [
    {
      "loc": ["body", "username"],
      "msg": "Field required",
      "type": "missing"
    }
  ]
}
```

Frontend dapat menerjemahkan error validasi backend menjadi pesan yang mudah dipahami pengguna.

### 8.6 Rate Limit Response

**HTTP Status:** `429 Too Many Requests`

```json
{
  "detail": "Terlalu banyak percobaan login. Silakan coba kembali nanti."
}
```

Jika tersedia, backend dapat menyertakan header `Retry-After`.

### 8.7 Get CSRF Token

**Endpoint:** `GET /api/auth/csrf`

Digunakan frontend untuk memperoleh CSRF token sebelum mengirim request yang mengubah state server.

Contoh response:

```json
{
  "csrf_token": "<csrf-token>"
}
```

Ketentuan:

- Backend menyediakan token melalui endpoint ini.
- Frontend menyimpan token dalam memory React, bukan localStorage.
- Token dikirim melalui header `X-CSRF-Token`.
- Cookie CSRF (jika menggunakan pola double-submit) harus dibedakan dari session cookie dan tidak berisi session ID.
- Token harus divalidasi backend pada request yang memerlukan perlindungan CSRF.

### 8.8 Get Current User

**Endpoint:** `GET /api/auth/me`

Digunakan untuk memeriksa apakah session pengguna masih valid dan mengambil informasi pengguna yang sedang login.

**Request Headers:**

```http
Cookie: session=<opaque-session-id>
```

Browser mengelola pengiriman cookie secara otomatis ketika menggunakan `credentials: "include"`.

**Success Response:**

**HTTP Status:** `200 OK`

```json
{
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "username": "rangga"
  }
}
```

**Unauthenticated Response:**

**HTTP Status:** `401 Unauthorized`

```json
{
  "detail": "Authentication required."
}
```

### 8.9 Logout

**Endpoint:** `POST /api/auth/logout`

Digunakan untuk mengakhiri session pengguna.

**Request Headers:**

```http
X-CSRF-Token: <csrf-token>
```

**Success Response:**

**HTTP Status:** `200 OK`

```json
{
  "message": "Logout berhasil."
}
```

Backend wajib:

1. Memvalidasi session pengguna.
2. Menghapus atau mencabut session dari penyimpanan server.
3. Mengirim cookie penghapusan dengan nama, path, dan atribut cookie yang sesuai.
4. Mengembalikan response sukses.

---

## 9. Authentication & Cookie Security

### Session Management

- Gunakan server-side session.
- Session ID berupa random opaque identifier dengan tingkat entropi yang memadai.
- Simpan session di database atau penyimpanan server-side yang sesuai.
- Jangan menyimpan password atau informasi sensitif di cookie.
- Session ID harus diregenerasi setelah autentikasi berhasil untuk mencegah session fixation.
- Session harus dihapus ketika logout.
- Session yang kedaluwarsa harus ditolak backend.

### Cookie Configuration

| Attribute | Value                 | Purpose                                    |
| --------- | --------------------- | ------------------------------------------ |
| HttpOnly  | true                  | Mencegah JavaScript membaca session cookie |
| Secure    | true (production)     | Mengharuskan HTTPS                         |
| SameSite  | Lax                   | Membatasi pengiriman cookie cross-site     |
| Path      | `/`                   | Berlaku untuk seluruh aplikasi             |
| Max-Age   | 2592000 (Remember Me) | Masa berlaku 30 hari                       |

### CSRF Protection

- Semua request yang mengubah state harus dilindungi CSRF.
- Termasuk login, logout, create, update, dan delete.
- Gunakan CSRF token melalui header `X-CSRF-Token`.
- Backend harus memvalidasi token sebelum memproses request.
- Jangan mengandalkan `SameSite` sebagai satu-satunya perlindungan CSRF.

### CORS Configuration

Karena frontend dan backend berjalan pada origin berbeda, FastAPI harus menyediakan konfigurasi CORS.

Contoh development:

- Frontend: `http://localhost:5173`
- Backend: `http://localhost:8000`

Ketentuan:

- Gunakan `allow_credentials=True`.
- Gunakan daftar origin yang eksplisit.
- Jangan menggunakan wildcard `*` bersama credentials.
- Izinkan header `Content-Type` dan `X-CSRF-Token`.
- Izinkan method HTTP yang dibutuhkan aplikasi.

Frontend wajib menggunakan `credentials: "include"` pada request yang memerlukan cookie.

Untuk production, utamakan frontend dan backend berada pada satu site melalui reverse proxy agar konfigurasi cookie dan CSRF lebih sederhana.

---

## 10. Frontend Integration

### Authentication State

Frontend menggunakan React Context atau state management yang sesuai untuk mengelola status autentikasi.

Minimal state:

| State           | Type         | Description                |
| --------------- | ------------ | -------------------------- |
| user            | User \| null | Informasi pengguna         |
| isAuthenticated | boolean      | Status autentikasi         |
| isLoading       | boolean      | Status pemeriksaan session |

### Application Initialization

Ketika aplikasi pertama kali dibuka:

1. React memeriksa status autentikasi melalui `GET /api/auth/me`.
2. Browser mengirim session cookie secara otomatis melalui Axios.
3. Jika response `200`, simpan informasi user ke authentication state.
4. Jika response `401`, ubah status menjadi unauthenticated.
5. Jika terjadi network error, tampilkan kondisi error yang sesuai dan jangan menganggap pengguna sudah logout.
6. Setelah pemeriksaan selesai, tampilkan halaman berdasarkan status autentikasi.

### Protected Routes

- Route yang memerlukan autentikasi harus menggunakan Protected Route.
- Selama pemeriksaan session berlangsung, tampilkan loading state.
- Jika pengguna belum login, redirect ke `/login`.
- Sertakan intended destination melalui query parameter, misalnya `/login?next=/products`.
- Setelah login berhasil, kembalikan pengguna ke intended destination yang sudah divalidasi.
- Backend tetap wajib memvalidasi autentikasi pada setiap endpoint privat.

### Axios Configuration

Gunakan Axios sebagai HTTP client utama untuk seluruh komunikasi antara frontend dan backend.

Buat satu instance Axios terpusat agar konfigurasi request konsisten.

Contoh struktur:

```text
src/
├── lib/
│   └── axios.ts
├── services/
│   └── auth.service.ts
├── contexts/
│   └── auth.context.tsx
└── pages/
    └── login/
        └── login.page.tsx
```

### Axios Instance

```typescript
// src/lib/axios.ts

import axios from "axios";

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  withCredentials: true,
  headers: {
    "Content-Type": "application/json",
  },
});
```

Environment variable:

```env
VITE_API_URL=http://localhost:8000/api/
```

Ketentuan:

- `withCredentials: true` wajib digunakan agar browser dapat mengirim dan menerima cookie.
- Jangan menyimpan session ID di konfigurasi Axios.
- Jangan menggunakan `localStorage` atau `sessionStorage` untuk menyimpan session ID.
- Semua request API aplikasi menggunakan instance `api` yang sama.

### CSRF Token Integration

- Frontend memperoleh CSRF token melalui `GET /auth/csrf`.
- Simpan CSRF token dalam memory React.
- Kirim token melalui header `X-CSRF-Token`.
- Gunakan Axios interceptor atau konfigurasi request terpusat untuk menambahkan CSRF token pada request yang mengubah state server.
- Jangan mengirim CSRF token pada request yang tidak memerlukannya.
- Backend tetap bertanggung jawab memvalidasi token.

### Authentication Service

Pisahkan proses komunikasi autentikasi dari komponen React.

Contoh:

```typescript
// src/services/auth.service.ts

import { api } from "@/lib/axios";

interface LoginPayload {
  username: string;
  password: string;
  remember_me: boolean;
}

interface AuthUser {
  id: string;
  username: string;
}

interface LoginResponse {
  message: string;
  user: AuthUser;
}

export const authService = {
  async login(payload: LoginPayload) {
    const response = await api.post<LoginResponse>("/auth/login", payload);

    return response.data;
  },

  async me() {
    const response = await api.get<{ user: AuthUser }>("/auth/me");

    return response.data;
  },

  async logout(csrfToken: string) {
    const response = await api.post(
      "/auth/logout",
      {},
      {
        headers: {
          "X-CSRF-Token": csrfToken,
        },
      },
    );

    return response.data;
  },

  async getCsrfToken() {
    const response = await api.get<{ csrf_token: string }>("/auth/csrf");

    return response.data;
  },
};
```

### Login Request Example

```typescript
const result = await authService.login({
  username,
  password,
  remember_me: rememberMe,
});

setUser(result.user);
navigate(intendedDestination);
```

### Error Handling

Gunakan `AxiosError` untuk menangani error dari backend.

| HTTP Status   | Handling                                                         |
| ------------- | ---------------------------------------------------------------- |
| 401           | Tampilkan pesan kredensial salah pada login.                     |
| 403           | Tampilkan pesan CSRF atau akses ditolak sesuai response backend. |
| 422           | Tampilkan pesan validasi input.                                  |
| 429           | Tampilkan pesan rate limit.                                      |
| 500           | Tampilkan pesan kesalahan server yang umum.                      |
| Network Error | Tampilkan pesan koneksi server gagal.                            |

Jangan menampilkan response internal backend atau stack trace kepada pengguna.

### Important Notes

- Jangan melakukan redirect otomatis ke `/login` untuk setiap response `401` tanpa pengecekan konteks request.
- Response `401` dari endpoint login berarti kredensial salah, bukan berarti harus melakukan redirect.
- Response `401` dari endpoint privat dapat digunakan untuk memperbarui authentication state.
- Hindari request `/auth/me` berulang kali pada setiap render React.
- Jangan membuat instance Axios baru di setiap komponen.
- Backend harus mengizinkan credentials melalui konfigurasi CORS FastAPI.

## 11. Accessibility

- Setiap input memiliki label yang terlihat.
- Gunakan `type="text"` untuk username.
- Gunakan `type="password"` untuk password secara default.
- Icon toggle password memiliki accessible label (`aria-label`) dan state (`aria-pressed`).
- Semua kontrol dapat digunakan dengan keyboard.
- Error dikaitkan dengan field terkait menggunakan `aria-describedby`.
- Fokus keyboard terlihat jelas.
- Gunakan semantic HTML pada form dan button.

---

## 12. Acceptance Criteria

### UI & Interaction

- [ ] Pengguna dapat mengisi username dan password.
- [ ] Validasi field kosong berfungsi dengan pesan yang tepat.
- [ ] Password dapat ditampilkan dan disembunyikan.
- [ ] Submit menampilkan loading state.
- [ ] Submit tidak dapat dilakukan berulang saat loading.
- [ ] Username dipertahankan saat login gagal.
- [ ] Layout dua kolom tampil di desktop.
- [ ] Layout satu kolom tampil di mobile.
- [ ] Kontrol utama dapat diakses dengan keyboard.

### Authentication

- [ ] Login menggunakan endpoint FastAPI.
- [ ] Login berhasil menghasilkan session cookie.
- [ ] Session cookie menggunakan HttpOnly.
- [ ] Session cookie menggunakan Secure pada production.
- [ ] Session cookie menggunakan SameSite=Lax.
- [ ] Remember Me memperpanjang durasi session hingga 30 hari.
- [ ] Tanpa Remember Me, browser menggunakan session cookie.
- [ ] Login gagal menampilkan pesan aman.
- [ ] Session ID tidak dikembalikan dalam response JSON.
- [ ] Session ID diregenerasi setelah login.
- [ ] Logout mencabut session dan menghapus cookie.
- [ ] Session yang kedaluwarsa ditolak backend.

### Frontend Integration

- [ ] React memeriksa session melalui `/api/auth/me`.
- [ ] Protected Route menunggu pemeriksaan session selesai.
- [ ] Pengguna tanpa session valid diarahkan ke `/login`.
- [ ] Pengguna yang sudah login diarahkan ke `/`.
- [ ] Intended destination divalidasi sebelum redirect.
- [ ] Request menggunakan `credentials: "include"`.
- [ ] CSRF token dikirim pada request yang memerlukan perlindungan CSRF.
- [ ] CORS mengizinkan origin frontend yang ditentukan.

---

## 13. Out of Scope

- Pembuatan akun (register).
- Pemulihan password (forgot password).
- Login via OAuth / Google.
- Multi-factor authentication.
- Pengelolaan beberapa perangkat dan session melalui halaman khusus.

---

## 14. Open Decisions

| Question                                                                              | Impact                | Status |
| ------------------------------------------------------------------------------------- | --------------------- | ------ |
| Apakah session disimpan menggunakan database atau Redis?                              | Infrastruktur backend | Open   |
| Berapa lama session berlaku tanpa Remember Me di sisi server?                         | Session management    | Open   |
| Apakah session menggunakan idle timeout selain batas maksimal masa berlaku?           | Security              | Open   |
| Apakah production frontend dan backend menggunakan satu origin melalui reverse proxy? | CORS dan cookie       | Open   |
| Apakah menggunakan `starlette-csrf` atau implementasi CSRF double-submit token?       | CSRF protection       | Open   |

---

## 15. References

- [Authentication flow](../../flows/authentication-flow.md)
- [Design system](../../design/design-system.md)
- [Frontend conventions](../../technical/frontend-conventions.md)
- [Backend conventions](../../technical/backend-conventions.md)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [MDN - Using HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies)
- [MDN - CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)
