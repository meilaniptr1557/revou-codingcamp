# Technical Design — Cat Clicker Web App

**Versi:** 1.0.0  
**Tanggal:** 15 Juni 2026  
**Status:** Draft — Menunggu Review

---

## 1. Stack Teknologi

| Lapisan | Teknologi | Keterangan |
|---|---|---|
| Markup | HTML5 | Semantic elements, ARIA attributes |
| Styling | Tailwind CSS v3 (CDN) | Utility-first, tidak ada build step |
| Logika | Vanilla JavaScript (ES6+) | Tanpa framework, tanpa bundler |
| Font | Google Fonts — Nunito | Di-load via `@import` CSS |
| Foto API | cataas.com | REST GET, no-auth, returns JPEG |

**Keputusan Arsitektur:** Single HTML file (`index.html`) — tidak ada build process, tidak ada dependency manager (npm/yarn). Semua berjalan langsung di browser.

---

## 2. Struktur File

```
revou-codingcamp/
├── index.html              # Seluruh aplikasi (HTML + CSS inline + JS)
├── README.md               # Deskripsi proyek
└── docs/
    ├── requirements.md     # Dokumen requirements ini
    └── technical-design.md # Dokumen technical design ini
```

> Untuk v2.0, pertimbangkan memisahkan ke `style.css` dan `app.js` agar lebih mudah di-maintain.

---

## 3. Arsitektur Aplikasi

Aplikasi menggunakan pola **Event-Driven + State-View Separation** sederhana:

```
[User Interaction]
       │
       ▼
[Event Listener] ──► [State Update] ──► [UI Render (updateUI)]
       │
       └──► [Side Effect: spawnReaction, loadNewCat]
```

### Komponen Utama

```
┌─────────────────────────────────────────────┐
│                  index.html                  │
│                                             │
│  ┌──────────┐   ┌───────────┐   ┌────────┐ │
│  │  Data    │   │   State   │   │   DOM  │ │
│  │  Layer   │   │  (clicks) │   │ Elements│ │
│  └────┬─────┘   └─────┬─────┘   └───┬────┘ │
│       │               │              │      │
│  ┌────▼───────────────▼──────────────▼────┐ │
│  │           Logic Layer                  │ │
│  │  clickCat() │ updateUI() │ spawnReaction│ │
│  │  loadNewCat() │ getCurrentLevel()       │ │
│  └────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

---

## 4. Data Model

### 4.1 State

```javascript
// Satu-satunya state aplikasi
let clicks = 0;  // Integer, bertambah 1 setiap klik
```

### 4.2 Data Statik — Reactions Pool

```javascript
// Array of objects, dipilih secara acak saat klik
const reactions = [
  { emoji: String, text: String },
  // ... 15 item total
];
```

### 4.3 Data Statik — Level Config

```javascript
// Array of level objects, diiterasi untuk menentukan level saat ini
const levels = [
  { min: Number, max: Number, label: String, emoji: String },
  // ... 6 level, level terakhir max = Infinity
];
```

---

## 5. Alur Kerja (Flow)

### 5.1 Flow: Klik Kucing

```
User klik / touchstart pada #cat-container
         │
         ▼
   clickCat(event)
         │
         ├─► clicks++ 
         │
         ├─► updateUI()
         │       ├─► Set #click-count text
         │       ├─► getCurrentLevel()
         │       ├─► Hitung progress %
         │       ├─► Update #progress-bar width
         │       └─► Update #level-label & #level-badge
         │
         ├─► Ambil koordinat (mouse / touch)
         │
         ├─► spawnReaction(x, y)
         │       ├─► getRandom(reactions) → pilih reaksi
         │       ├─► Buat <div class="reaction-bubble">
         │       ├─► Posisikan relatif ke #cat-area
         │       ├─► getRandom(colors) → set warna
         │       ├─► Append ke DOM (#cat-area)
         │       ├─► Update #mood-box text
         │       └─► addEventListener('animationend') → remove dari DOM
         │
         └─► Flash effect pada #cat-img
                 └─► Tambah class 'clicked', hapus setelah 200ms
```

### 5.2 Flow: Ganti Kucing

```
User klik #new-cat-btn
         │
         ▼
   loadNewCat()
         │
         ├─► Set src: https://cataas.com/cat?ts={Date.now()}
         │   (timestamp mencegah browser cache)
         │
         └─► Update #mood-box: "🔄 Kucing baru datang!"
```

### 5.3 Flow: Inisialisasi

```
DOMContentLoaded / script di akhir body
         │
         ▼
   updateUI()              ← Render state awal (clicks = 0)
         │
   addEventListener()      ← Pasang event listeners
         │
   cataas.com/cat          ← Browser load foto via src attribute
```

---

## 6. Komponen UI & DOM Elements

| Element ID | Tag | Fungsi |
|---|---|---|
| `#cat-area` | `<div>` | Container relatif untuk positioning bubble reaksi |
| `#cat-container` | `<button>` | Target klik utama, wraps foto kucing |
| `#cat-img` | `<img>` | Menampilkan foto kucing dari API |
| `#mood-box` | `<div>` | Menampilkan reaksi terakhir kucing |
| `#click-count` | `<span>` | Menampilkan angka total klik |
| `#progress-bar` | `<div>` | Progress bar animasi (width diubah via JS) |
| `#level-label` | `<span>` | Teks label level + progress klik |
| `#level-badge` | `<span>` | Badge nama level saat ini |
| `#new-cat-btn` | `<button>` | Trigger ganti foto kucing |
| `.reaction-bubble` | `<div>` (dynamic) | Bubble reaksi, dibuat & dihapus secara dinamis |

---

## 7. Styling & Animasi

### 7.1 CSS Custom (di luar Tailwind)

| Selector | Deskripsi |
|---|---|
| `#cat-container:active` | Scale down 0.93 saat ditekan (press feedback) |
| `.reaction-bubble` | Absolute positioned, animasi `floatUp` |
| `@keyframes floatUp` | Bubble naik 110px sambil fade out dalam 1.2s |
| `#cat-img.clicked` | Brightness + pink glow saat diklik (200ms) |
| `.progress-bar-inner` | Transisi smooth width menggunakan cubic-bezier |
| `body` | Background gradient pink-to-purple |

### 7.2 Tailwind Classes Utama

| Komponen | Classes |
|---|---|
| Card wrapper | `bg-white/80 backdrop-blur-md rounded-3xl shadow-2xl` |
| Foto kucing | `rounded-full overflow-hidden border-4 border-pink-200` |
| Progress bar bg | `bg-pink-100 rounded-full h-3 overflow-hidden` |
| Progress bar fill | `bg-gradient-to-r from-pink-400 to-purple-400` |
| Level badge | `bg-gradient-to-r from-pink-100 to-purple-100 rounded-full` |
| Tombol ganti | `bg-pink-400 hover:bg-pink-500 active:scale-95 rounded-full` |

---

## 8. External API

### cataas.com

| Properti | Detail |
|---|---|
| Endpoint | `GET https://cataas.com/cat` |
| Auth | Tidak diperlukan |
| Response | JPEG image (binary) |
| Cache busting | Query param `?ts={timestamp}` |
| Fallback | ❌ Belum ada (planned v2.0) |

---

## 9. Pertimbangan Performa

| Isu | Solusi Saat Ini |
|---|---|
| Memory leak dari bubble DOM | `animationend` event menghapus element otomatis |
| Browser cache foto kucing | Cache-busting via `?ts=Date.now()` |
| Render blocking font | Google Fonts di-load async via `@import` |
| Tailwind CDN size | Diterima untuk v1.0; untuk produksi gunakan PurgeCSS build |

---

## 10. Known Limitations & Tech Debt

| No | Isu | Prioritas | Solusi Rencana (v2.0) |
|---|---|---|---|
| TD-01 | State tidak persisten (reset saat refresh) | Medium | Simpan `clicks` ke `localStorage` |
| TD-02 | Tidak ada fallback jika cataas.com down | High | Sediakan set foto kucing lokal sebagai fallback |
| TD-03 | Semua kode dalam satu file HTML | Low | Pisahkan ke `style.css` dan `app.js` |
| TD-04 | Tailwind via CDN (tidak di-purge) | Low | Setup build dengan Tailwind CLI + PurgeCSS |
| TD-05 | Tidak ada unit test | Medium | Setup test dengan Jest atau Vitest |
| TD-06 | Reaksi bisa muncul duplikat berturut-turut | Low | Tambah logika "no repeat last" pada `getRandom` |

---

## 11. Rencana Iterasi

### v1.0 (Saat Ini) — MVP
- ✅ Single file HTML
- ✅ Foto kucing dari API
- ✅ Reaksi acak dengan animasi
- ✅ Sistem level (6 level)
- ✅ Mobile support

### v1.1 — Persistensi & Polish
- [ ] Simpan progress klik ke `localStorage`
- [ ] Fallback image jika API down
- [ ] Efek suara opsional (muted by default)
- [ ] Animasi level-up notification

### v2.0 — Expansion
- [ ] Pisahkan HTML / CSS / JS ke file terpisah
- [ ] Multiple kucing dengan nama masing-masing
- [ ] Sistem item / upgrades (membeli makanan kucing, dll.)
- [ ] Leaderboard lokal (multi-session via localStorage)
