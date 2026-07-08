# CLAUDE.md — Ecoplast Solutions (ecoplast-web)

Company profile statis untuk **Ecoplast Solutions** — jasa produksi tali plastik &
biji plastik PP (polypropylene) di Balaraja, Kab. Tangerang, Banten.
Badan hukum: **PT. Mencoba Bertahan Hidup**.

Live: **https://ecoplastsolutions.id** (GitHub Pages, branch `main`, folder root).

---

## Stack

- **HTML + CSS statis murni.** TANPA build step, TANPA framework.
- **JavaScript minimal & progressive-enhancement** (dua fungsi, keduanya di
  **satu** `<script>` inline di akhir `<body>` tiap halaman):
  1. **Tahun copyright** — mengisi `.js-year` via `getFullYear()` (fallback teks
     `2026` bila JS mati) agar tahun tidak di-hardcode.
  2. **Reveal saat masuk layar (scroll-in)** — `IntersectionObserver` menambah
     kelas `.in` ke elemen target saat masuk viewport (fade + geser naik, ala
     jagopijat.com). Detail di bagian "Reveal scroll-in" pada Sistem desain.
  Menu hamburger tetap **murni CSS** (checkbox hack, tanpa JS). Di luar dua hal
  ini, tidak ada JS lain — pertahankan seminimal mungkin.
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
├─ kebijakan-privasi.html   # Kebijakan privasi (tautan di footer, di luar nav utama)
├─ styles.css          # Base + desktop (TANPA @media)
├─ responsive.css      # Semua @media (mobile/tablet) + prefers-reduced-motion
├─ assets/
│  ├─ logo-mark.png    # Emblem daun "e" (164x140, transparan) — dipakai logo header & footer
│  └─ product/         # Foto produk (.webp): tali.webp, biji1.webp (katalog), biji.webp (bukti Balaraja)
├─ favicon.ico / favicon-32x32.png / apple-touch-icon.png   # favicon + gambar OG
├─ robots.txt          # Allow all + pointer ke sitemap
├─ sitemap.xml         # 5 URL (/, produk, tentang, kontak, kebijakan-privasi)
├─ google5c4d18bf39fc4ecf.html   # Verifikasi Google Search Console — JANGAN dihapus
├─ CNAME               # Pengikat custom domain — JANGAN diubah/dihapus
├─ README.md
└─ CLAUDE.md
```

### Pola per halaman
Semua halaman berbagi markup **header sticky** dan **footer** yang identik (di-inline
di tiap file karena tanpa build step). Yang berbeda per halaman:
- `<title>`, `<meta name="description">`, Open Graph (`og:title/description/url`), `<link rel="canonical">`.
- Penanda nav aktif: `aria-current="page"` pada link nav halaman itu.

Yang **sama di keempat file** (SEO/GEO): geo meta tags (`geo.region=ID-BT`,
`geo.placename`, `geo.position`, `ICBM`) memakai koordinat `-6.2077458;106.4389806`.
`index.html` & `kontak.html` juga punya **JSON-LD `LocalBusiness`** (nama, badan
hukum, `PostalAddress`, `GeoCoordinates`, telepon, `openingHoursSpecification`).
Kalau data bisnis/koordinat berubah, perbarui JSON-LD **dan** geo meta tags.

> Kalau mengedit header/footer/nav, ubah di **kelima** file HTML agar konsisten.
> Untuk blok identik lintas file, aman pakai skrip Python kecil (lihat riwayat commit).

### Sistem desain (`styles.css` + `responsive.css`)
- CSS variables warna: `--bg #F4F6F1`, `--ink #14271D`, `--green #1F7A4D`,
  `--green-deep #12442B`, `--blue #2B5CB8`, `--muted #5C6B60`, `--line #D9E0D6`,
  `--green-light #7BE0A6` (highlight di background gelap).
- **Warna wordmark "Ecoplast Solutions" (kiblat = LOGO ASLI):** logo memenggal warna
  **ECO = hijau** + **PLAST = navy**, dan **SOLUTIONS = navy** (sama dgn PLAST). Token:
  `--brand-green #3E9B37` (ECO) & `--brand-navy #173B5C` (PLAST + SOLUTIONS) — di-eyedrop
  dari file logo (`ECOPLAST SOLUTION.jpeg`). Markup wordmark (header & footer, kelima
  file): `<span class="eco">ECO</span>PLAST SOLUTIONS` — base `.brand__name`/
  `.foot-brand__name` = navy, `.eco` = hijau. **Ada spasi** antara PLAST & SOLUTIONS
  (dulu mendempet "ECOPLASTSOLUTIONS" + SOLUTIONS hijau; kini ikut struktur dua-kata
  logo). Header di latar terang → warna logo langsung; footer di latar gelap → lockup
  dibungkus panel putih (lihat bagian Footer) supaya warna logo tetap tampil apa adanya.
  Kalau logo/warna brand berubah, perbarui kedua token ini.
- **Token elevation & motion (fondasi polish premium — pakai token ini, jangan
  hard-code):**
  - Radius: `--radius 14px` (default), `--radius-lg 20px` (bingkai media besar:
    `.prod__img`, `.map-wrap`), `--radius-sm 10px`. Spasi: `--card-pad`, `--space`.
  - Shadow berlapis low-opacity: `--shadow-xs/sm/md/lg`. Kartu diam = `--shadow-sm`,
    hover naik ke `--shadow-md`/`--shadow-lg`. Hindari drop-shadow berat.
  - Motion: easing `--ease` (umum) & `--ease-out` (masuk), durasi `--dur .22s` &
    `--dur-slow .4s`. Semua transisi memakai token ini agar terasa satu bahasa.
- **Pola pellet**: kelas `.dotted` = `--green-deep` + SVG titik-titik (data-URI inline).
  Dipakai di hero halaman & CTA band. Kedalaman via pseudo: `::before` = ambient
  lighting (glow hijau kiri-atas + aksen biru kanan-atas + vignette bawah, radial),
  `::after` = grain `feTurbulence` SVG inline (`mix-blend-mode: soft-light`, opacity
  rendah). Keduanya `pointer-events:none`; `.container` di atasnya (`z-index:1`).
- **Micro-interaction (CSS-native, tanpa library):** underline nav animatif
  (`.nav__link::after` scaleX), kartu angkat + `.pcard__img` zoom saat hover, chip
  `.feature__num`/`.crow__ico`/`.fc-ico` mengisi hijau saat hover, tombol
  hover/active (press scale), link footer memunculkan panah `::before`. Semua transform
  hover baru wajib dimasukkan ke blok `prefers-reduced-motion` di `responsive.css`.
- **Reveal scroll-in (elemen muncul saat masuk layar — satu-satunya JS selain
  tahun):** pola *progressive enhancement* & **anti-flash**:
  - Skrip sinkron di `<head>` (`document.documentElement.classList.add('has-js')`)
    menandai `<html class="has-js">` **sebelum** body render. CSS menyembunyikan
    target (`opacity:0` + `translateY(22px)`, transisi `--dur-reveal`/`--ease-out`)
    **hanya** saat `.has-js` aktif → tanpa JS = tak ada `.has-js` = konten tampil
    normal, tanpa flash. **Durasi reveal sengaja lambat & elegan**: token khusus
    `--dur-reveal: 1.1s` (bukan `--dur-slow` yang .4s dipakai hover kartu) supaya
    kemunculan terasa mengambang, tidak kaku. Kalau mau lebih lambat/cepat, ubah
    satu angka token ini.
  - Skrip di akhir `<body>` (gabung dengan skrip tahun) menjalankan
    `IntersectionObserver` yang menambah kelas `.in` (→ `opacity:1; transform:none`)
    saat elemen masuk viewport, lalu `unobserve`. **Stagger**: tetangga se-parent
    yang juga target diberi `transition-delay` `index*0.12s` (maks 5) via inline
    style → kartu dalam satu grid muncul berurutan (dilonggarkan dari 0.08s agar
    kaskade lebih terasa). Hero (di atas fold) ter-reveal saat load. Fallback: bila
    `IntersectionObserver` tak ada → semua langsung `.in`.
  - **Daftar selector target ADA DI DUA TEMPAT & wajib sinkron:** blok `.has-js …`
    di `styles.css` **dan** konstanta `SEL` di skrip body tiap HTML. Target:
    `.hero__inner > *`, `.sec-head`, `.pcard`, `.feature`, `.prod`, `.step`,
    `.card`, `.map-block`, `.cta-band .container > *`. Kalau menambah target,
    ubah **kedua** tempat (dan blok reduced-motion di bawah). Hindari menargetkan
    elemen yang ter-nest (mis. `.crow` di dalam `.card`) agar tak dobel-animasi.
  - **`prefers-reduced-motion`** (`responsive.css`): target dipaksa
    `opacity:1 !important; transform:none !important` → konten selalu tampil tanpa
    gerak (aman walau observer tak jalan). Skrip observer dipasang **identik di
    kelima** file HTML.
- **Hero beranda** = `.hero--factory`: background industri **murni CSS/SVG** (siluet
  garis pabrik + lingkaran gulungan + pellet) di atas gradasi gelap. Tanpa foto.
  - **Cahaya berjalan (running light) di skyline:** siluet pabrik = layer statis redup
    dari `background-image` data-URI (tak bisa dianimasi). Di atasnya ada **overlay inline
    SVG** `.hero__skyline` (child pertama di `<section>`, sebelum `.container`) berisi
    **satu** `<path class="hero__skyline-run" pathLength="1000">` yang menelusuri seluruh
    outline pabrik. Overlay diposisikan **identik** dengan layer skyline background
    (`width: min(1180px,150%)`, `left:50%` + `translateX(-50%)`, `bottom:0`, viewBox
    `0 0 1200 230`) sehingga berkas cahaya nempel persis di outline yang sama. Animasi:
    `stroke-dasharray: 90 910` (jumlah = `pathLength` 1000 → hanya satu segmen tampak,
    loop mulus) + `@keyframes skyline-run` menggeser `stroke-dashoffset` 1000→0,
    `linear infinite` (**durasi 20s** — sempat kali diperlambat dari 11s karena terasa
    terlalu cepat), warna `#7ED6A3`, `opacity .62`, glow via `drop-shadow`. `.container`
    (`z-index:1`) selalu di atas cahaya. `.hero--factory` diberi **`overflow: hidden`**
    supaya overlay lebar 150% tak bikin scroll horizontal. **Reduced-motion**
    (`responsive.css`): `.hero__skyline { display:none }` → animasi mati, sisakan siluet
    statis. Hanya `index.html` yang punya `.hero--factory`; kalau kelak halaman lain
    memakai siluet serupa, terapkan overlay yang sama. Murni CSS/SVG, tanpa JS.
- **Foto produk — dua pola berbeda:**
  - **Kartu ringkasan beranda (`.pcard`)**: foto di dalam `.pcard__media` = container
    **full-bleed** ke tepi kartu, **`aspect-ratio: 3/4`** (portrait), `overflow:hidden`;
    `<img class="pcard__img">` `object-fit: cover` + `object-position:center` → mengisi
    penuh tanpa area kosong. Kedua kartu **identik tinggi** (rasio & lebar sama). Sudut
    atas membulat ikut radius kartu (via `overflow:hidden` di `.pcard`). Rasio **3/4
    di semua ukuran** (desktop & mobile 1 kolom sama). **File `tali.webp` (1086×1448)
    & `biji1.webp` (789×1052) keduanya rasio 3:4 PERSIS** — jadi `cover` = tampil utuh,
    tak ada yang terpangkas di ukuran apa pun.
  - **Halaman detail produk (`.prod__img` di produk.html)**: tetap `object-fit: contain`
    + matting lembut (gradasi putih) → produk tampil UTUH tak terpotong untuk tampilan
    detail. Pakai file foto yang sama.
  - Frasa highlight judul dijaga `white-space: nowrap` agar tidak terpecah antar-baris.
  - **Ikon inline SVG dekoratif** (mis. `.feature__ico`) wajib punya atribut
    `width`/`height` eksplisit **selain** ukuran via CSS — jaga-jaga bila `styles.css`
    ke-cache lama (Cloudflare) sementara HTML sudah baru, SVG tak membesar ke default.
- **Header mobile = drawer hamburger** (≤720px, CSS-only, tanpa JS — checkbox hack
  `.nav-toggle` + label `.nav-burger` + label `.nav-overlay`):
  - Meniru gaya **centralcats.id**: panel **geser dari kanan** (`.nav__links`,
    `position: fixed`, `transform: translateX(100%)` → `0`, transisi .35s), background
    `--green-deep`, lebar `min(74vw, 300px)`.
  - `.nav-overlay` = layer gelap; klik untuk menutup. Hamburger 3 garis → X.
  - Header mobile mematikan `backdrop-filter` (agar tidak jadi containing block).
- **Footer (4 kolom, gaya B2B manufaktur):** grid `.foot-cols` (`align-items: start`)
  berisi 4 kolom yang **semua mulai di baseline atas yang sama** (sejajar tepi atas
  logo):
  1. **Perusahaan** (`.foot-info`): **logo = lockup yang sama dengan header** —
     `.foot-brand` berisi emblem `logo-mark.png` (`.foot-brand__mark` height 36px) +
     wordmark `.foot-brand__name`. Karena latar footer gelap, lockup ini diberi
     **panel terang** (`.foot-brand` = chip **gradasi hijau samar** —
     `linear-gradient(135deg,#EFF9F1→#DCF0E1→#C4E7CD)` + border hijau tipis,
     `border-radius: --radius`, `box-shadow: --shadow-sm`, padding) supaya warna
     wordmark tampil **persis logo asli** (ECO hijau + PLAST/SOLUTIONS navy — lihat
     "Warna wordmark" di bawah). Gradasi dibuat **cukup terlihat** (bukan putih polos)
     tapi tetap terang agar navy+hijau wordmark akurat — versi awal `#ffffff→#E4F4E7`
     terlalu samar sehingga terbaca putih.
     Flush-left, emblem **lurus kiri tepat di atas** teks deskripsi
     (`emblem.left == deskripsi.left`). Lalu deskripsi + **ALAMAT** (`.foot-address`).
  2. **Halaman** (`.foot-col`): nav, hover memunculkan panah (`ul a::before`).
  3. **Kontak** (`.foot-col` + `.fc-*`): baris chip-ikon + label + nilai + divider.
  4. **Media Sosial** (`.foot-col` + `.foot-social`/`.fsoc`): Instagram, Facebook,
     LinkedIn, YouTube (inline SVG) dengan label **"Segera hadir"** low-emphasis
     (`opacity .6`) — **placeholder, JANGAN dihapus**; siap jadi tautan asli nanti
     (ganti `<span>` → `<a href>`). Ecoplast belum punya akun sosial.
  - Judul kolom pakai **`<h3>`** (`.foot-col h3`, distyle jadi label mono kecil) — bukan
    `h4`, agar urutan heading valid (WCAG 1.3.1, tak melompati level).
  - Depth latar: `--ink` + gradasi lembut + radial light + grain SVG (`::before`/
    `::after`, `isolation: isolate` + `z-index:-1` agar di belakang konten).
  - Border-top aksen hijau; divider copyright halus (`rgba(255,255,255,.06)`); baris
    copyright **center full-width** (`.foot-bottom`, di luar grid kolom), tahun otomatis
    via `.js-year` + `getFullYear()` (fallback `2026`). Teks: `© <tahun> Ecoplast
    Solutions. Balaraja, …` — pakai **brand "Ecoplast Solutions"**, bukan nama badan
    hukum PT (badan hukum tetap tampil di kolom "Perusahaan" & JSON-LD `legalName`).
  - Responsif (`responsive.css`): desktop 4 kolom → tablet ≤900px **2×2** → mobile
    ≤560px **1 kolom** (urutan: logo, deskripsi, alamat, Halaman, Kontak, Media Sosial,
    copyright). Kalau mengedit footer, ubah di **kelima** file HTML.
- **Kontak**: kartu info pakai baris `.crow` (chip-ikon + label + nilai + divider),
  blok peta `.map-block` (header "Lokasi" + tombol "Buka di Google Maps"). Kartu
  "Informasi kontak" dan "Minta penawaran harga" dibuat **sama lebar & tinggi**. Kartu
  penawaran (`.quote-card`, latar hijau gelap) diawali **chip ikon jabat tangan**
  (`.quote-ico`, inline SVG stroke, 40px, warna `--green-light`, panel putih transparan
  tipis) di atas judul — mengisi ruang kosong & senada chip `.crow__ico`; hover
  mengangkatnya sedikit.
- Aksesibilitas: skip-link, `:focus-visible` jelas, `prefers-reduced-motion` dihormati,
  responsif sampai lebar 360px.

## SEO / GEO (local SEO)

- **Koordinat lokasi (sumber kebenaran): `-6.2077458, 106.4389806`** (dari embed resmi
  listing Google Business "ECOPLAST SOLUTIONS"). Dipakai di: geo meta tags (4 halaman)
  dan JSON-LD `GeoCoordinates` (`index.html` + `kontak.html`).
- **Peta = listing bisnis, bukan pin koordinat mentah.** Embed (`kontak.html`) pakai URL
  `maps/embed?pb=...` resmi listing (menampilkan kartu "ECOPLAST SOLUTIONS"). Tombol
  "Buka di Google Maps" + JSON-LD `hasMap` pakai link listing via CID:
  `https://www.google.com/maps?cid=893561851054800034`. **Jangan** kembalikan ke format
  `search/?api=1&query=LAT,LNG` — itu cuma menjatuhkan pin di titik koordinat (bukan nempel
  ke listing) dan pernah bikin peta meleset ~286 m (longitude salah `106.4415609`).
- **`robots.txt`**: allow all + `Sitemap:` pointer. **`sitemap.xml`**: 5 URL absolut
  (beranda, produk, tentang, kontak, kebijakan-privasi).
- **Google Search Console**: domain terverifikasi via file `google5c4d18bf39fc4ecf.html`
  di root (metode "HTML file"). File ini **JANGAN dihapus** — verifikasi dicek ulang
  berkala; hilang = verifikasi dicabut. Sitemap sudah di-submit (4 halaman kebaca).
- **Cloudflare & robots.txt (penting)**: Cloudflare bisa **menyisipkan blok managed
  robots.txt sendiri** (fitur "Managed robots.txt" / Content Signals) yang menaruh
  `ai-train=no` + `Disallow: /` untuk bot AI (ClaudeBot, GPTBot, CCBot, dll) **di atas**
  file kita — ini pernah bikin robots.txt live ≠ file repo. Setting itu sudah
  **di-OFF-kan** di dashboard Cloudflare agar crawler AI diizinkan; Googlebot search
  tidak pernah terpengaruh. Kalau robots.txt live tampak beda dari repo, cek setting
  ini dulu (Security → Settings → filter "Bot traffic" → Managed robots.txt).

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
- **`google5c4d18bf39fc4ecf.html` JANGAN dihapus** — file verifikasi Google Search
  Console; dihapus = verifikasi dicabut.
- Jangan mengarang klaim/angka (kapasitas ton, tahun berdiri, jumlah klien, testimoni).
  Konten hanya yang faktual/diberikan.
- Pertahankan: tanpa build step, tanpa framework, JS hanya seperlunya (kini: skrip
  tahun + reveal `IntersectionObserver`, keduanya progressive-enhancement).
  `@media` hanya di `responsive.css`.

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
    pakai pengukuran + logika). Semua trik (window kecil, `--headless=new`, DPR ×2)
    tetap terkunci ~504px. Untuk mensimulasikan drawer terbuka, buat salinan HTML
    dengan atribut `checked` pada `#nav-toggle`.
  - **JANGAN percaya screenshot `--headless=new --window-size=<lebar><504>`** — mode
    itu TIDAK meng-clamp seperti `--headless` lama, tapi me-*render layout 504px lalu
    meng-crop* ke lebar gambar. Hasilnya menyesatkan: sisi kanan (mis. hamburger + ikon
    WA di header) terpotong sehingga tampak "rusak"/overflow padahal tidak. Gunakan
    `--headless` (lama) pada `--window-size=504,H` untuk layout mobile yang jujur.
  - **Cek overflow horizontal dengan ANGKA, bukan mata.** Suntik skrip kecil yang
    membandingkan `document.documentElement.scrollWidth` vs `window.innerWidth` (tulis
    hasil ke `document.title`, baca via `--dump-dom`). `scrollWidth <= innerWidth`
    berarti tidak ada overflow — drawer off-canvas (`position:fixed` + `translateX(100%)`)
    memang melar ke kanan tapi Chromium mengecualikannya dari scroll (bukan bug).
  - **Peta Google kosong di headless** (tak ada internet ke Google) — normal; akan
    termuat di live. Tombol "Buka di Google Maps" jadi cadangan.
- Pastikan tautan internal & aset tidak 404: `/styles.css`, `/responsive.css`,
  `/assets/logo-mark.png`, `/assets/product/*.webp`, favicon.
