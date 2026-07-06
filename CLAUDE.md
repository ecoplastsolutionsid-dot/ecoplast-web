# CLAUDE.md — Ecoplast Solutions (ecoplast-web)

Company profile statis untuk **Ecoplast Solutions** — jasa produksi tali plastik &
biji plastik PP (polypropylene) di Balaraja, Kab. Tangerang, Banten.
Badan hukum: **PT. Mencoba Bertahan Hidup**.

Live: **https://ecoplastsolutions.id** (GitHub Pages, branch `main`, folder root).

---

## Stack

- **HTML + CSS statis murni.** TANPA build step, TANPA framework.
- **JavaScript minimal**: hanya satu baris `<script>` inline di tiap halaman untuk
  mengisi tahun copyright footer otomatis (elemen `.js-year`, ada fallback teks
  `2026` bila JS mati) — agar tahun tidak di-hardcode. Selain itu tanpa JS
  (termasuk menu hamburger yang murni CSS).
- **Dua stylesheet**, dimuat berurutan di tiap halaman:
  - `styles.css` — sistem desain + layout **desktop/base**.
  - `responsive.css` — **semua `@media`** (breakpoint mobile/tablet + preferensi
    gerak). Dimuat SETELAH styles.css. Pemisahan ini sengaja: aturan desktop dan
    mobile tidak saling mengganggu. **Jangan taruh `@media` di styles.css.**
- Font via Google Fonts CDN: **Archivo** (heading + body, variable — display pakai
  `font-variation-settings: "wdth" 112` + weight 800) dan **IBM Plex Mono**
  (eyebrow/label kecil uppercase, letter-spacing lebar).
- Ikon (WhatsApp, telepon, jam, pin) = inline SVG (bukan file, bukan JS).

## Struktur file

```
/
├─ index.html          # Beranda (hero industri, ringkasan produk, 4 keunggulan, CTA band)
├─ produk.html         # #tali (Tali Plastik PP), #biji (Biji Plastik PP), alur produksi 4 tahap
├─ tentang.html        # Profil + lokasi
├─ kontak.html         # Kartu info kontak, kartu penawaran, blok peta (embed Google Maps)
├─ styles.css          # Base + desktop (TANPA @media)
├─ responsive.css      # Semua @media (mobile/tablet) + prefers-reduced-motion
├─ assets/
│  ├─ logo-mark.png    # Emblem daun "e" (crop dari logo, transparan) — logo header
│  ├─ logo-footer.png  # Logo lengkap versi wordmark PUTIH (emblem warna + teks putih) — footer gelap
│  └─ product/         # Foto produk (.webp): tali.webp, biji1.webp (katalog), biji.webp (bukti Balaraja)
├─ favicon.ico / favicon-32x32.png / apple-touch-icon.png   # favicon + gambar OG
├─ CNAME               # Pengikat custom domain — JANGAN diubah/dihapus
├─ README.md
└─ CLAUDE.md
```

### Pola per halaman
Semua halaman berbagi markup **header sticky** dan **footer** yang identik (di-inline
di tiap file karena tanpa build step). Yang berbeda per halaman:
- `<title>`, `<meta name="description">`, Open Graph (`og:title/description/url`), `<link rel="canonical">`.
- Penanda nav aktif: `aria-current="page"` pada link nav halaman itu.

> Kalau mengedit header/footer/nav, ubah di **keempat** file HTML agar konsisten.
> Untuk blok identik lintas file, aman pakai skrip Python kecil (lihat riwayat commit).

### Sistem desain (`styles.css` + `responsive.css`)
- CSS variables: `--bg #F4F6F1`, `--ink #14271D`, `--green #1F7A4D`,
  `--green-deep #12442B`, `--blue #2B5CB8`, `--muted #5C6B60`, `--line #D9E0D6`,
  `--green-light #7BE0A6` (highlight di background gelap), `--card-pad`, `--radius`.
- **Pola pellet**: kelas `.dotted` = `--green-deep` + SVG titik-titik (data-URI inline).
  Dipakai di hero halaman & CTA band.
- **Hero beranda** = `.hero--factory`: background industri **murni CSS/SVG** (siluet
  garis pabrik + lingkaran gulungan + pellet) di atas gradasi gelap. Tanpa foto.
- **Foto produk**: `object-fit: contain` di bingkai tinggi tetap dengan matting lembut
  → produk tampil UTUH (tidak terpotong), kartu seragam. Frasa highlight judul dijaga
  `white-space: nowrap` agar tidak terpecah antar-baris.
- **Header mobile = drawer hamburger** (≤720px, CSS-only, tanpa JS — checkbox hack
  `.nav-toggle` + label `.nav-burger` + label `.nav-overlay`):
  - Meniru gaya **centralcats.id**: panel **geser dari kanan** (`.nav__links`,
    `position: fixed`, `transform: translateX(100%)` → `0`, transisi .35s), background
    `--green-deep`, lebar `min(74vw, 300px)`.
  - `.nav-overlay` = layer gelap; klik untuk menutup. Hamburger 3 garis → X.
  - Header mobile mematikan `backdrop-filter` (agar tidak jadi containing block).
- **Footer**: pakai `logo-footer.png` (versi putih), blok **ALAMAT**, kolom **Kontak**
  berupa baris chip-ikon + label + nilai + divider (`.fc-*`), border-top aksen hijau,
  dan baris copyright **center full-width** (`.foot-bottom`, di luar grid kolom).
- **Kontak**: kartu info pakai baris `.crow` (chip-ikon + label + nilai + divider),
  blok peta `.map-block` (header "Lokasi" + tombol "Buka di Google Maps"). Kartu
  "Informasi kontak" dan "Minta penawaran harga" dibuat **sama lebar & tinggi**.
- Aksesibilitas: skip-link, `:focus-visible` jelas, `prefers-reduced-motion` dihormati,
  responsif sampai lebar 360px.

## Kontak / data bisnis (sumber kebenaran)

- Badan hukum: **PT. Mencoba Bertahan Hidup** (brand: Ecoplast Solutions).
- WhatsApp/Telepon: **0852-1440-1234** → `tel:+6285214401234`.
- Semua tombol WhatsApp: `https://wa.me/6285214401234?text=<pesan-terurl-encode>`,
  selalu `target="_blank" rel="noopener"`, teks pre-filled sesuai konteks tombol.
- Alamat: Kp. Tobat, Desa Sentul Jaya, Kec. Balaraja, Kab. Tangerang, Banten.
- Jam: Senin–Sabtu, 08.00–17.00 WIB.

## Aturan repo

- **CNAME JANGAN diubah/dihapus** — mengikat domain `ecoplastsolutions.id` ke
  GitHub Pages. Menghapusnya memutus custom domain.
- Jangan mengarang klaim/angka (kapasitas ton, tahun berdiri, jumlah klien, testimoni).
  Konten hanya yang faktual/diberikan.
- Pertahankan: tanpa build step, tanpa framework, JS hanya seperlunya (kini hanya
  skrip tahun). `@media` hanya di `responsive.css`.

## Deploy

1. `git add -A && git commit -m "..."` lalu `git push origin main`.
2. GitHub Pages rebuild otomatis; live dalam **±1 menit**.
3. Domain lewat **Cloudflare** (di depan GitHub Pages) → ada cache. Setelah push,
   **hard-refresh** (`Ctrl+Shift+R`). Banyak "keluhan tampilan" ternyata cache lama —
   selalu hard-refresh dulu. Jika perlu, purge cache di dashboard Cloudflare.

## Verifikasi lokal (cara yang dipakai)

- Jalankan server statis lalu screenshot headless:
  `python -m http.server <port>` di root, buka via `http://localhost:<port>/...`.
- Screenshot: Edge/Chrome `--headless --screenshot=... --window-size=W,H <url>`.
- **Catatan penting saat verifikasi**:
  - Muat lewat **HTTP** (server), bukan `file://` — path absolut `/styles.css`,
    `/responsive.css` hanya benar di server/live.
  - **Edge headless punya lebar window minimum ~504px** — window <504 tidak dihormati,
    jadi screenshot "375px" sebenarnya layout ~504px (mobile sejati sulit dipotret;
    pakai pengukuran + logika). Untuk mensimulasikan drawer terbuka, buat salinan HTML
    dengan atribut `checked` pada `#nav-toggle`.
  - **Peta Google kosong di headless** (tak ada internet ke Google) — normal; akan
    termuat di live. Tombol "Buka di Google Maps" jadi cadangan.
- Pastikan tautan internal & aset tidak 404: `/styles.css`, `/responsive.css`,
  `/assets/logo-mark.png`, `/assets/logo-footer.png`, `/assets/product/*.webp`, favicon.
