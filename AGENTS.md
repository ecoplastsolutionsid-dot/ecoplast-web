# AGENTS.md — Ecoplast Solutions (ecoplast-web)

**Dokumentasi lengkap ada di [`CLAUDE.md`](./CLAUDE.md). Baca file itu sebelum
mengubah apa pun di repo ini.**

File ini sengaja dibuat tipis. Sebelumnya ia adalah SALINAN penuh `CLAUDE.md`
(±21 KB), dan salinan itu berhenti diperbarui pada 30 Juli 2026 sementara
`CLAUDE.md` terus berjalan — jadi ia masih menyatakan "tanpa JS sama sekali",
belum tahu galeri mesin, perbaikan drawer saat Back, reveal scroll-in, maupun
plakat logo footer. Agen yang kebetulan membaca AGENTS.md akan bekerja dengan
aturan yang salah. **Jangan menyalin ulang isi `CLAUDE.md` ke sini** — cukup
pointer ini, supaya tak ada dua sumber kebenaran yang bisa berbeda lagi.

Ringkas, hal yang paling sering menjatuhkan orang di repo ini:

- **Tanpa build step, tanpa framework.** JS hanya tiga fungsi progressive
  enhancement (tahun copyright, tutup drawer saat Back, reveal scroll-in) dalam
  satu `<script>` inline yang **identik di kelima** file HTML.
- **`@media` HANYA di `responsive.css`**, tidak pernah di `styles.css`.
- **Naikkan `?v=` di link stylesheet kelima file setiap kali CSS diubah** —
  Cloudflare menahan CSS 4 jam. Aset baru/rename juga wajib bertanda `?v=`.
- **Header & footer di-inline identik di kelima file HTML** (tanpa build step);
  kalau mengeditnya, edit kelimanya.
- **Jangan hapus/ubah:** `CNAME`, `google5c4d18bf39fc4ecf.html`,
  `BingSiteAuth.xml`, `b3f0fc85243d5aabc07151b1c4324fe5.txt`.
- **Jangan mengarang klaim/angka** (kapasitas, tahun berdiri, jumlah klien,
  testimoni). Hanya yang faktual/diberikan pemilik.
- **Verifikasi dengan angka, bukan mata** — server statis + headless screenshot,
  dan ukur (`getBoundingClientRect`, `scrollWidth`). Catatan jebakan headless
  (clamp 504px Edge, `scroll-behavior: smooth`, `--virtual-time-budget`) ada di
  bagian "Verifikasi lokal" pada `CLAUDE.md`.
