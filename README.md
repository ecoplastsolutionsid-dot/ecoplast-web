# ecoplast-web

Company profile statis **Ecoplast Solutions** — jasa produksi tali plastik & biji
plastik PP (polypropylene). Balaraja, Kab. Tangerang, Banten.

- **Live:** https://ecoplastsolutions.id
- **Stack:** HTML + CSS statis, tanpa build step & tanpa framework. JavaScript
  hanya seperlunya (progressive enhancement): tahun copyright otomatis, menutup
  drawer menu saat tombol Back ditekan, dan reveal saat elemen masuk layar.
- **Hosting:** GitHub Pages (branch `main`, root) di belakang Cloudflare.
- **Halaman:** `index.html`, `produk.html`, `tentang.html`, `kontak.html`,
  `kebijakan-privasi.html` — header & footer di-inline identik di kelimanya.
- **CSS:** `styles.css` (base + desktop) lalu `responsive.css` (semua `@media`).

Deploy: push ke `main` → live ±1 menit. Setelah itu hard-refresh (`Ctrl+Shift+R`);
domain lewat Cloudflare punya cache.

> **Kalau `styles.css`/`responsive.css` diubah, naikkan `?v=` pada link stylesheet
> di kelima file HTML.** Cloudflare menahan CSS 4 jam, jadi lupa menaikkannya =
> perubahan tak tampil di live sampai cache kedaluwarsa.

Dokumentasi lengkap (struktur, sistem desain, aturan) ada di [`CLAUDE.md`](./CLAUDE.md).
Catatan: **jangan ubah/hapus `CNAME`** — pengikat custom domain.
