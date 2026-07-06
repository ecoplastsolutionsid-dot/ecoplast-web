# CLAUDE.md — Ecoplast Solutions (ecoplast-web)

Company profile statis untuk **Ecoplast Solutions** — jasa produksi tali plastik &
biji plastik PP (polypropylene) di Balaraja, Kab. Tangerang, Banten.

Live: **https://ecoplastsolutions.id** (GitHub Pages, branch `main`, folder root).

---

## Stack

- **HTML + CSS statis murni.** TANPA build step, TANPA framework, TANPA JavaScript.
  Jika suatu saat perlu JS, tambahkan hanya bila memang diminta.
- Satu stylesheet global: `styles.css` (dipakai semua halaman).
- Font via Google Fonts CDN: **Archivo** (heading + body, variable — display pakai
  `font-variation-settings: "wdth" 112` + weight 800) dan **IBM Plex Mono**
  (eyebrow/label kecil uppercase, letter-spacing lebar).
- Ikon WhatsApp = inline SVG (bukan file, bukan JS).

## Struktur file

```
/
├─ index.html          # Beranda (hero, ringkasan produk, 4 keunggulan, CTA band)
├─ produk.html         # #tali (Tali Plastik PP), #biji (Biji Plastik PP), alur produksi 4 tahap
├─ tentang.html        # Profil + lokasi
├─ kontak.html         # Alamat, telp/WA, jam operasional, kartu penawaran, embed Google Maps
├─ styles.css          # Stylesheet tunggal untuk semua halaman
├─ assets/
│  ├─ logo-mark.png    # Emblem daun "e" (crop dari logo, transparan) — logo header/footer
│  └─ product/         # Foto produk (.webp): tali.webp, biji1.webp (katalog), biji.webp (bukti Balaraja)
├─ favicon.ico         # Favicon multi-res
├─ favicon-32x32.png   # Favicon PNG (ketajaman)
├─ apple-touch-icon.png# Ikon iOS + gambar Open Graph
├─ CNAME               # Pengikat custom domain — JANGAN diubah/dihapus
├─ README.md
└─ CLAUDE.md
```

### Pola per halaman
Semua halaman berbagi markup **header sticky** dan **footer** yang identik (di-inline
di tiap file karena tanpa build step). Yang berbeda per halaman:
- `<title>`, `<meta name="description">`, dan Open Graph (`og:title/description/url`).
- `<link rel="canonical">`.
- Penanda nav aktif: `aria-current="page"` pada link nav halaman itu (di-styling
  underline hijau via CSS `.nav__link[aria-current="page"]`).

> Kalau mengedit header/footer/nav, ubah di **keempat** file HTML agar tetap konsisten.

### Sistem desain (di `styles.css`)
- CSS variables: `--bg #F4F6F1`, `--ink #14271D`, `--green #1F7A4D`,
  `--green-deep #12442B`, `--blue #2B5CB8`, `--muted #5C6B60`, `--line #D9E0D6`
  (plus `--green-light #7BE0A6` untuk highlight di atas background gelap).
- Elemen signature: kelas `.dotted` = background `--green-deep` + pola SVG titik-titik
  ("pellet" biji plastik, circle kecil hijau/biru opacity rendah, sebagai data-URI
  inline di CSS). Dipakai di hero dan CTA band.
- Aksesibilitas: skip-link, `:focus-visible` jelas, `prefers-reduced-motion`
  dihormati, responsif sampai lebar 360px.
- Nav responsif tanpa JS: di ≤720px header jadi dua baris — baris 1 brand +
  tombol WhatsApp, baris 2 daftar nav (strip full-width yang bisa di-scroll).
  Diatur via `.nav { display: contents }` + `order` pada media query, jadi nav
  tetap bisa diakses di semua ukuran layar.

## Kontak / data bisnis (sumber kebenaran)

- WhatsApp/Telepon: **0852-1440-1234** → `tel:+6285214401234`
- Semua tombol WhatsApp: `https://wa.me/6285214401234?text=<pesan-terurl-encode>`,
  selalu `target="_blank" rel="noopener"`, dengan teks pre-filled sesuai konteks tombol.
- Alamat: Kp. Tobat, Desa Sentul Jaya, Kec. Balaraja, Kab. Tangerang, Banten.
- Jam: Senin–Sabtu, 08.00–17.00 WIB.

## Aturan repo

- **CNAME JANGAN diubah/dihapus** — file ini yang mengikat domain
  `ecoplastsolutions.id` ke GitHub Pages. Menghapusnya memutus custom domain.
- Jangan mengarang klaim/angka (kapasitas ton, tahun berdiri, jumlah klien,
  testimoni). Konten hanya yang faktual/diberikan.
- Pertahankan: tanpa build step, tanpa framework, tanpa JS (kecuali diminta).

## Deploy

1. `git add -A && git commit -m "..."` lalu `git push origin main`.
2. GitHub Pages membangun ulang otomatis; live dalam **±1 menit**.
3. Domain dilayani lewat **Cloudflare** (di depan GitHub Pages) → ada cache.
   Setelah push, **hard-refresh** (`Ctrl+Shift+R`) untuk melihat versi terbaru.
   Jika masih tertahan, purge cache di dashboard Cloudflare.

## Verifikasi cepat sebelum push

- Buka tiap halaman di browser (lokal: klik file / `Live Server`), cek header, nav
  aktif, tombol WA membuka chat dengan teks benar, dan peta di `kontak.html` muncul.
- Pastikan tautan internal & aset (`/styles.css`, `/assets/logo-mark.png`, favicon)
  tidak 404.
