# ecoplast-web

Company profile statis **Ecoplast Solutions** — jasa produksi tali plastik & biji
plastik PP (polypropylene). Balaraja, Kab. Tangerang, Banten.

- **Live:** https://ecoplastsolutions.id
- **Stack:** HTML + CSS statis (tanpa build step / framework / JS).
- **Hosting:** GitHub Pages (branch `main`, root) di belakang Cloudflare.

Deploy: push ke `main` → live ±1 menit. Setelah itu hard-refresh (`Ctrl+Shift+R`);
domain lewat Cloudflare punya cache.

Dokumentasi lengkap (struktur, sistem desain, aturan) ada di [`CLAUDE.md`](./CLAUDE.md).
Catatan: **jangan ubah/hapus `CNAME`** — pengikat custom domain.
