# Requirements — Cat Clicker Web App

**Versi:** 1.0.0  
**Tanggal:** 15 Juni 2026  
**Status:** Draft — Menunggu Review

---

## 1. Gambaran Umum

Cat Clicker adalah single-page web application (SPA) berbasis browser yang memungkinkan pengguna berinteraksi dengan foto kucing secara menyenangkan. Setiap kali pengguna mengklik kucing, kucing menampilkan reaksi acak disertai animasi visual. Aplikasi ini bersifat gamifikasi ringan dengan sistem level berbasis jumlah klik.

---

## 2. Tujuan Produk

- Memberikan pengalaman interaktif yang ringan, lucu, dan menyenangkan.
- Menjadi contoh implementasi SPA sederhana dengan HTML5, Tailwind CSS, dan Vanilla JavaScript.
- Cocok sebagai proyek portofolio atau latihan coding camp.

---

## 3. Target Pengguna

| Segmen | Deskripsi |
|---|---|
| Pengguna Umum | Siapa saja yang ingin bersenang-senang mengklik kucing |
| Pelajar / Mahasiswa | Mempelajari cara kerja SPA dan event-driven programming |
| Instruktur Coding | Digunakan sebagai bahan demonstrasi di kelas |

---

## 4. Functional Requirements

### 4.1 Tampilan Kucing (FR-01)
- [ ] Sistem **HARUS** menampilkan foto kucing nyata saat halaman pertama kali dimuat.
- [ ] Foto kucing diambil dari API eksternal `cataas.com`.
- [ ] Foto ditampilkan dalam format lingkaran (circular crop) di tengah halaman.

### 4.2 Klik Interaksi (FR-02)
- [ ] Pengguna **HARUS** bisa mengklik gambar kucing.
- [ ] Setiap klik menambah counter sebesar 1.
- [ ] Setiap klik memicu satu reaksi yang dipilih secara acak dari pool reaksi yang tersedia.

### 4.3 Reaksi Acak (FR-03)
- [ ] Sistem **HARUS** memiliki minimal 10 reaksi berbeda.
- [ ] Setiap reaksi terdiri dari emoji dan teks pendek dalam Bahasa Indonesia.
- [ ] Reaksi muncul sebagai bubble animasi yang mengambang ke atas di posisi klik.
- [ ] Reaksi terakhir juga ditampilkan di "mood box" di bawah gambar kucing.
- [ ] Warna bubble reaksi dipilih secara acak dari palette yang telah ditentukan.

### 4.4 Counter & Progress (FR-04)
- [ ] Sistem **HARUS** menampilkan total klik secara real-time.
- [ ] Sistem **HARUS** menampilkan progress bar menuju level berikutnya.
- [ ] Label progress menampilkan format: `[emoji] [nama level] — [klik saat ini] / [target klik]`

### 4.5 Sistem Level (FR-05)
- [ ] Sistem **HARUS** memiliki minimal 6 level dengan threshold klik yang berbeda.
- [ ] Level badge diperbarui secara otomatis saat threshold tercapai.
- [ ] Level tertinggi ("DEWA KUCING") tidak memiliki batas atas.

| Level | Nama | Threshold |
|---|---|---|
| 1 | Pemula Menggemaskan 🐱 | 0 – 9 klik |
| 2 | Teman Kucing 🐈 | 10 – 29 klik |
| 3 | Pencinta Kucing 😺 | 30 – 59 klik |
| 4 | Ahli Menggemaskan 🐾 | 60 – 99 klik |
| 5 | Kucing Sejati 👑 | 100 – 199 klik |
| 6 | DEWA KUCING ✨ | 200+ klik |

### 4.6 Ganti Kucing (FR-06)
- [ ] Pengguna **HARUS** bisa memuat foto kucing baru tanpa refresh halaman penuh.
- [ ] Tombol "Ganti Kucing" tersedia dan mudah diakses.
- [ ] Counter klik **TIDAK** direset saat ganti kucing.

### 4.7 Dukungan Sentuhan / Mobile (FR-07)
- [ ] Aplikasi **HARUS** berfungsi di perangkat mobile (touch screen).
- [ ] Event `touchstart` ditangani untuk memicu klik yang sama seperti mouse click.

---

## 5. Non-Functional Requirements

### 5.1 Performa
- [ ] Halaman **HARUS** dapat dimuat dalam < 3 detik pada koneksi broadband normal.
- [ ] Animasi bubble reaksi berjalan di 60fps tanpa frame drop pada perangkat modern.
- [ ] DOM element bubble reaksi **HARUS** dihapus dari DOM setelah animasi selesai (mencegah memory leak).

### 5.2 Kompatibilitas Browser
- [ ] Mendukung Chrome 90+, Firefox 88+, Safari 14+, Edge 90+.
- [ ] Mendukung tampilan di layar mobile (min-width 320px) dan desktop.

### 5.3 Aksesibilitas (a11y)
- [ ] Tombol kucing memiliki atribut `aria-label` yang deskriptif.
- [ ] Gambar kucing memiliki atribut `alt` yang sesuai.
- [ ] Fokus keyboard dapat menjangkau tombol utama.

### 5.4 Ketergantungan Eksternal
- [ ] API foto: `https://cataas.com/cat` (GET, tidak memerlukan autentikasi)
- [ ] CSS framework: Tailwind CSS via CDN `https://cdn.tailwindcss.com`
- [ ] Font: Google Fonts — Nunito

> ⚠️ **Catatan Risiko:** Aplikasi bergantung pada ketersediaan layanan `cataas.com`. Jika API tidak tersedia, foto kucing tidak akan tampil. Perlu fallback image di iterasi berikutnya.

---

## 6. Out of Scope (v1.0)

- Penyimpanan data klik ke server / database
- Sistem autentikasi / login pengguna
- Leaderboard / perbandingan antar pengguna
- Pilihan jenis kucing / filter kucing
- Suara / audio feedback
- Animasi kucing custom (sprite / GIF)

---

## 7. Kriteria Penerimaan (Acceptance Criteria)

| ID | Skenario | Expected Result |
|---|---|---|
| AC-01 | User membuka halaman | Foto kucing tampil, counter = 0, level = "Pemula Menggemaskan" |
| AC-02 | User mengklik kucing 1x | Counter bertambah jadi 1, bubble reaksi muncul dan menghilang |
| AC-03 | User mengklik kucing 10x | Level naik ke "Teman Kucing", progress bar diperbarui |
| AC-04 | User klik tombol "Ganti Kucing" | Foto baru dimuat, counter tidak berubah |
| AC-05 | User membuka di mobile | Layout responsif, klik sentuh berfungsi |
| AC-06 | User klik kucing 200x | Level mencapai "DEWA KUCING", progress bar penuh |
