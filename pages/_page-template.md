# [Page Name]

## 1. Overview

| Properti | Nilai |
|---|---|
| Page name | [Nama halaman] |
| Route | [/path] |
| Status | Draft |
| Priority | Must / Should / Could |
| Authentication | Public / Authenticated / Role-based |
| Responsive | Yes / No |
| Owner | [Opsional] |

### Purpose

[Tujuan halaman.]

### User Goals

- [Hal yang ingin pengguna capai]

### Preconditions

- [Kondisi yang harus terpenuhi sebelum halaman digunakan]

## 2. Entry Points & Navigation

### Entry Points

- [Dari mana pengguna dapat membuka halaman ini]

### Outgoing Navigation

| Trigger | Destination | Conditions |
|---|---|---|
| [Aksi] | [/route] | [Kondisi] |

### Access Rules

- [Siapa yang boleh mengakses]
- [Perilaku jika tidak memiliki izin]

## 3. Layout Structure

### Desktop

- [Susunan layout]
- [Lebar/posisi konten jika sudah ditentukan]

### Tablet

- [Perubahan layout]

### Mobile

- [Perubahan layout]

### Content Hierarchy

1. [Elemen utama]
2. [Elemen pendukung]

## 4. UI Components

| Component | Description | Required | Reference |
|---|---|---|---|
| [Nama] | [Fungsi] | Yes / No | [Dokumen komponen] |

## 5. User Interactions

### [Nama komponen/aksi]

**Trigger:** [Apa yang dilakukan pengguna]

**Expected behavior:**
1. [Langkah]
2. [Langkah]

**Constraints:**
- [Batasan/perilaku khusus]

Ulangi bagian ini untuk setiap interaksi penting.

## 6. Page States

### Default

- [Tampilan awal]

### Loading

- [Indikator dan kontrol yang dinonaktifkan]

### Empty

- [Tampilan jika belum ada data]

### Error

- [Pesan dan tindakan pemulihan]

### Success

- [Konfirmasi dan langkah berikutnya]

### Disabled / Permission Denied

- [Jika relevan]

## 7. Validation & Business Rules

| Field / Rule | Requirement | Error message |
|---|---|---|
| [Field] | [Aturan] | [Pesan] |

### Business Rules

- [Aturan bisnis yang sudah disepakati]

## 8. API & Data Integration

> Isi berdasarkan kontrak API yang telah disepakati. Jika belum tersedia, tulis `TBD` dan catat sebagai open decision.

| Action | Method | Endpoint | API Reference |
|---|---|---|---|
| [Action] | [GET/POST/...] | [Path atau TBD] | [Link/file] |

### Request Data

- [Referensi schema request]

### Response Data

- [Referensi schema response]

### Error Handling

- [Pemetaan error API ke UI]

### Data Dependencies

- [Data yang diperlukan halaman]

## 9. Accessibility

- [Label untuk input]
- [Keyboard navigation]
- [Focus visibility]
- [Screen reader behavior]
- [Contrast]

## 10. Analytics (Optional)

| Event | Trigger | Properties |
|---|---|---|
| [event_name] | [Trigger] | [Data non-sensitif] |

## 11. Acceptance Criteria

- [ ] [Kriteria yang dapat diuji]
- [ ] [Kriteria responsif]
- [ ] [Kriteria interaksi]
- [ ] [Kriteria error/loading]
- [ ] [Kriteria aksesibilitas yang relevan]

## 12. Out of Scope

- [Hal yang tidak dikerjakan dalam halaman ini]

## 13. Open Decisions

| Question | Impact | Owner | Status |
|---|---|---|---|
| [Pertanyaan] | [Dampak] | [Owner] | Open |

Jangan membuat asumsi untuk keputusan yang masih terbuka.

## 14. References

- [Design link]
- [Related page]
- [API contract]
- [Related flow]
