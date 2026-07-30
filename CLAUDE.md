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
- **Cache-buster WAJIB di link stylesheet:** `/styles.css?v=<versi>` &
  `/responsive.css?v=<versi>` (kini `v=20260730d`, sama di kelima file).
  **Naikkan `v` SETIAP KALI `styles.css`/`responsive.css` diubah** — kalau lupa,
  perubahan CSS tak akan tampil di live sampai cache Cloudflare kedaluwarsa.
  Alasannya nyata, pernah kejadian: Cloudflare memberi HTML `max-age=600`
  (10 menit) tapi CSS `max-age=14400` (**4 jam**), jadi setelah push HTML sudah
  baru sementara CSS masih basi → footer sempat merender lockup logo baru dengan
  aturan lama (tinggi 36px, panel putih tak muncul) dan tampak "rusak" berjam-jam.
  Gejalanya: `curl -D -` menunjukkan `last-modified` styles.css **lebih tua** dari
  **JEBAKAN — jangan mengambil URL berversi sebelum deploy selesai.** Pernah
  kejadian dan bikin bingung: `curl .../styles.css?v=X` dijalankan saat GitHub Pages
  masih men-deploy → request itu mengambil `styles.css` versi **lama** dari origin
  dan Cloudflare menyimpannya di cache key **baru** `?v=X` selama 4 jam. Cache-buster
  jadi menunjuk isi basi, dan menaikkan `v` lagi adalah satu-satunya jalan keluar.
  Gejala waktu itu: HTML sudah tanpa `.brand__name` sementara CSS masih punya
  `.brand__mark { width:30px; height:30px; object-fit:contain }`, jadi lockup
  570×124 dimampatkan `contain` menjadi serpihan ±30×6.5px di header.
  **Cara aman mendeteksi deploy sudah selesai:** pakai query buangan, mis.
  `curl ".../styles.css?probe=1" | grep "height: 48px"`. Kalau key buangan itu
  teracuni, tak ada ruginya. Baru setelah cocok, URL `?v=` sungguhan boleh disentuh.
  **Hal yang sama berlaku untuk ASET** (gambar): mengganti NAMA file berisiko —
  HTML baru bisa tayang sebelum aset barunya ter-cache, lalu **404-nya** ikut
  ter-cache (`cf-cache-status: HIT` pada 404) dan tidak sembuh sendiri. Pernah
  terjadi saat `logo-footer.png` → `logo-lockup.png`: kedua logo pecah di live
  padahal file ada di origin (probe query acak mengembalikan 200). Karena itu URL
  logo pun bertanda versi: `/assets/logo-lockup.png?v=1`. Untuk aset baru atau
  rename, sertakan `?v=` **sejak commit pertama**.
  Hard-refresh browser TIDAK menolong — yang
  basi ada di edge Cloudflare, bukan di browser. Query string ini membuat URL-nya
  berbeda sehingga edge wajib mengambil ulang.
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
│  ├─ logo-lockup.png  # Lockup MENDATAR 570x124 transparan (emblem + ECOPLAST/SOLUTIONS
│                      #   bertumpuk) — SATU-SATUNYA logo yang dipakai halaman, di
│                      #   HEADER (.brand, 48px) DAN FOOTER (.foot-brand, 52px, panel
│                      #   putih). Disusun dari potongan logo-full.png; resep di
│                      #   bagian Footer. Dulu bernama logo-footer.png.
│  ├─ logo-mark.png    # Emblem asli (248x218, transparan) — SUDAH TIDAK dipakai di
│                      #   halaman mana pun sejak header ikut memakai logo-lockup.png.
│                      #   Disimpan sebagai acuan crop emblem & pembanding audit OG.
│  ├─ logo-full.png    # Logo lockup resmi 500x500 transparan — sumber ikon + JSON-LD `logo`
│  ├─ og-image.png     # Gambar Open Graph 1200x630 (og:image + JSON-LD `image`)
│  └─ product/         # Foto produk (.webp): tali.webp, biji1.webp (katalog),
│                      #   biji1-full.webp (detail biji), biji.webp (bukti Balaraja)
├─ favicon.ico         # Multi-size 16/32/48 (entri PNG) — EMBLEM saja, bukan lockup
├─ favicon-32x32.png / favicon-48x48.png / apple-touch-icon.png   # lihat bagian "Favicon & gambar OG"
├─ robots.txt          # Allow all + pointer ke sitemap
├─ sitemap.xml         # 5 URL (/, produk, tentang, kontak, kebijakan-privasi) + lastmod
├─ google5c4d18bf39fc4ecf.html   # Verifikasi Google Search Console — JANGAN dihapus
├─ b3f0fc85243d5aabc07151b1c4324fe5.txt   # Kunci IndexNow (Bing/Edge) — JANGAN dihapus/diubah
├─ BingSiteAuth.xml   # Verifikasi Bing Webmaster Tools — JANGAN dihapus/diubah
├─ CNAME               # Pengikat custom domain — JANGAN diubah/dihapus
├─ README.md
└─ CLAUDE.md
```

### Pola per halaman
Semua halaman berbagi markup **header sticky** dan **footer** yang identik (di-inline
di tiap file karena tanpa build step). Yang berbeda per halaman:
- `<title>`, `<meta name="description">`, Open Graph (`og:title/description/url`), `<link rel="canonical">`.
- Penanda nav aktif: `aria-current="page"` pada link nav halaman itu.

Yang **sama di kelima file** (SEO/GEO): geo meta tags (`geo.region=ID-BT`,
`geo.placename`, `geo.position`, `ICBM`) memakai koordinat `-6.2077458;106.4389806`.
Kalau data bisnis/koordinat berubah, perbarui JSON-LD **dan** geo meta tags.

**Structured data (JSON-LD) — satu blok `@graph` per halaman, tepat sebelum skrip
`has-js` di `<head>`:**
- `index.html` → `LocalBusiness` + `WebSite` + `WebPage`
- `produk.html` → `WebSite` + `CollectionPage` + `BreadcrumbList` + `OfferCatalog`
  (dua `Product`: Tali Plastik PP & Biji Plastik PP)
- `tentang.html` → `WebSite` + `AboutPage` + `BreadcrumbList`
- `kontak.html` → `LocalBusiness` + `WebSite` + `ContactPage` + `BreadcrumbList`
- `kebijakan-privasi.html` → `WebSite` + `WebPage` + `BreadcrumbList`

Aturan penting:
- **`LocalBusiness` lengkap HANYA ada di `index.html` & `kontak.html`** (satu sumber
  kebenaran NAP). Halaman lain **merujuk** lewat `{"@id": ".../#business"}` saja, tidak
  menduplikasi datanya → Google membaca satu entitas, bukan beberapa bisnis terpisah.
  Kalau NAP berubah, cukup dua file itu (plus alamat yang tampil di footer kelima file).
- `@id` yang dipakai sebagai jangkar: `#business` (LocalBusiness), `#website` (WebSite),
  `<url>#webpage`, `<url>#breadcrumb`, `produk.html#katalog`, `produk.html#tali`,
  `produk.html#biji`. **Jangan** merujuk `@id` yang tak pernah didefinisikan — cek dengan
  menelusuri seluruh graph kelima file dan mencocokkan `@id` referensi vs definisi.
- `Product` **sengaja tanpa `offers`/`price`** — tidak ada data harga, jangan dikarang.
  Konsekuensinya tidak muncul rich snippet harga; data entitasnya tetap terbaca.
- **`addressLocality` = `"Balaraja"`**, bukan "Kabupaten Tangerang". Di schema.org
  `addressLocality` itu kota/kota kecil (setara "Mountain View"), dan Balaraja adalah
  kata kunci lokasi terkuat; "Kab. Tangerang" ikut di `streetAddress` agar tak hilang.
  `postalCode` = `15610` (dikonfirmasi pemilik). `areaServed` distruktur: `Country`
  Indonesia + `AdministrativeArea` Banten / DKI Jakarta / Jawa Barat (didukung klaim
  "distribusi ke Jabodetabek" di `tentang.html` — jangan tambah wilayah tanpa dasar).
- **Kode pos `15610` tampil di SEMUA alamat yang terlihat**, agar NAP yang tampil
  konsisten dengan `postalCode` di JSON-LD: alamat footer kelima file (baris
  `Kab. Tangerang, Banten 15610`), kartu "Informasi kontak" (`.crow__val` di
  `kontak.html`), blok "Alamat" di `tentang.html`, dan alamat di
  `kebijakan-privasi.html`. Kalau menambah tempat alamat baru, sertakan kode posnya.
  Baris copyright `.foot-bottom` sengaja **tanpa** kode pos (bukan alamat, cuma
  penanda kota).

> Kalau mengedit header/footer/nav, ubah di **kelima** file HTML agar konsisten.
> Untuk blok identik lintas file, aman pakai skrip Python kecil (lihat riwayat commit).

### Sistem desain (`styles.css` + `responsive.css`)
- CSS variables warna: `--bg #F4F6F1`, `--ink #14271D`, `--green #1F7A4D`,
  `--green-deep #12442B`, `--blue #2B5CB8`, `--muted #5C6B60`, `--line #D9E0D6`,
  `--green-light #7BE0A6` (highlight di background gelap).
- **Warna wordmark "Ecoplast Solutions" (kiblat = LOGO ASLI):** logo memenggal warna
  **ECO = hijau** + **PLAST = navy**, dan **SOLUTIONS = navy** (sama dgn PLAST). Token:
  `--brand-green #3E9B37` (ECO) & `--brand-navy #173B5C` (PLAST + SOLUTIONS).
  - **Sumber kebenaran logo = `assets/logo-full.png`**, salinan piksel-identik dari
    file asli `ecoplast logo asli.png`. Logo asli = lingkaran/pellet **NAVY-BIRU** +
    "e" & daun hijau.
  - **JANGAN pakai file `ecoplast logo.png`** (tanpa kata "asli") — itu varian LAIN
    dengan lingkaran **panah hijau tanpa biru**, BUKAN logo autentik. Pernah dipakai
    sebagai sumber dan menghasilkan seluruh aset + token warna yang salah; sudah
    di-revert. Kalau ragu, minta konfirmasi file mana yang asli sebelum mengubah aset.
  - Kedua token diverifikasi ulang terhadap logo asli dengan **median piksel inti
    stroke** (bukan sampling naif — itu kena piksel tepi anti-alias dan menyesatkan):
    ECO median `#3D9933` → token `#3E9B37` **jarak 4.6, nyaris identik**.
    PLAST median `#0B2A51`, gradasi p5 `#0B203D` → p95 `#163C63` → token `#173B5C`
    berada di **ujung terang rentang itu**; dipertahankan karena sengaja — makin gelap
    makin sulit terbaca di footer. Emblem biru median `#0D2F58`.
  - **TIDAK ADA lagi teks wordmark HTML di situs ini.** Header **dan** footer
    memakai gambar yang sama, `assets/logo-lockup.png`, sehingga susunan
    (`ECOPLAST` di atas `SOLUTIONS`) dan **tipografinya** identik di keduanya.
    Ini permintaan pemilik: "susunan teks logo pada header samakan dengan footer
    agar konsisten". Sebelumnya header memakai emblem + `<span class="brand__name">`
    ber-font **Archivo** satu baris sementara footer memakai huruf **logo asli**
    bertumpuk — dua wordmark yang jelas beda.
    - Yang **sudah dihapus, jangan dihidupkan lagi**: `.brand__name`,
      `.foot-brand__name`, `.eco` (di styles.css & responsive.css) beserta markup
      `<span class="eco">ECO</span>PLAST SOLUTIONS`. Nama aksesibel ditanggung
      `aria-label` pada `<a class="brand">`/`<a class="foot-brand">` + `alt` gambar.
    - Header berlatar **terang** (`--bg`) → gambar langsung, **tanpa panel**.
      Footer berlatar **gelap** → gambar di dalam **panel putih** (lihat bagian
      Footer). Itu satu-satunya perbedaan perlakuan antar keduanya.
    - Tinggi: header `48px` (mobile ≤720px `40px`, ≤360px `36px`), footer `52px`.
    - Token `--brand-green`/`--brand-navy` kini **tidak lagi mewarnai apa pun yang
      tampil** — keduanya tinggal jadi acuan warna brand & dipakai memverifikasi
      aset. Kalau logo/warna brand berubah, perbarui token **dan** regenerasi
      `logo-lockup.png` + `og-image.png` + ikon.
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
  - **Ruang brand vs hamburger (terukur, iframe lebar sungguhan).** Sejak header
    memakai lockup mendatar (bukan emblem kecil + teks), brand jadi jauh lebih lebar
    — jadi ini wajib diukur ulang tiap kali tinggi `.brand__mark` diubah:
    **360px** → brand 165×36, hamburger 44×44, **sisa 111px**;
    **400px** → brand 184×40, **sisa 132px**. Tinggi header tetap **68px** di
    keduanya, jadi `top: 69px` panel drawer TIDAK perlu diubah. Kalau lockup mobile
    dibesarkan, cek ulang angka-angka ini sebelum commit.
  - **Header mobile = brand (kiri) + hamburger (kanan) SAJA.** Dulu `.nav` diberi
    `display: contents` sehingga tombol WhatsApp (`.nav__cta`, dikecilkan jadi ikon
    lewat `font-size: 0`) menjadi item header persis di sebelah hamburger — dua
    kontrol berdempet membuat **hamburger ambigu**. Sempat dicoba memindahkan WA ke
    **dalam** panel drawer, tapi ditolak karena tombol WA jadi tak terlihat sebelum
    menu dibuka. Solusi final: **WA = FAB mengambang** (lihat poin berikutnya), dan
    **tidak ada tombol WA di header maupun di dalam drawer**.
  - **`.nav` tetap `display: contents`** di mobile → kedua anaknya jadi item header,
    lalu masing-masing keluar dari alur: `.nav__links` → panel drawer
    (`position: fixed`), `.nav__cta` → FAB (`position: fixed`). Sisa item dalam alur
    hanya brand + hamburger, dan `.container` sudah `justify-content: space-between`,
    jadi **tak perlu** `order` / `margin-left: auto` (dulu perlu).
  - **FAB WhatsApp** (`.nav__cta` di ≤720px): `position: fixed`, bulat 56×56px,
    `right: 1rem`, `bottom: calc(1rem + env(safe-area-inset-bottom, 0px))` (aman dari
    home-indicator iOS), `z-index: 48`, ikon saja (`font-size: 0`; `.wa-ico` diberi
    **26px eksplisit** karena defaultnya `em` — akan kolaps kalau ikut `font-size: 0`).
    Nama aksesibelnya tetap "WhatsApp" karena teks masih ada di DOM. **Disembunyikan
    saat drawer terbuka** (`.nav-toggle:checked ~ .nav .nav__cta`) supaya tidak
    menutupi menu / salah sentuh. `position: fixed` di sini benar-benar menempel
    viewport karena tak ada ancestor ber-transform/filter/backdrop-filter — header
    mobile sengaja mematikan `backdrop-filter`. Sudah dicek: di dasar halaman FAB
    tidak menutupi teks apa pun (baris copyright center berakhir jauh di kirinya).
  - Meniru gaya **centralcats.id**: panel **geser dari kanan** = `.nav__links`,
    `position: fixed`, `transform: translateX(100%)` → `0` lewat
    `.nav-toggle:checked ~ .nav .nav__links`, transisi .35s, background
    `--green-deep`, lebar `min(74vw, 300px)`, kolom. **Isinya daftar link saja.**
  - **`top: 69px`** = tinggi header mobile sebenarnya (container `min-height: 68px`
    + border-bottom 1px). Sebelumnya 64px — 5px teratas panel tersembunyi di balik
    header. Kalau tinggi header diubah, sesuaikan angka ini.
  - `.nav-overlay` = layer gelap; klik untuk menutup. Hamburger 3 garis → X.
    Overlay diberi **`touch-action: none` + `overscroll-behavior: contain`** =
    scroll-lock ala CSS-only: menyeret di area overlay tidak menggulir halaman di
    belakangnya. Panel `.nav__links` juga `overscroll-behavior: contain` agar gulir
    di dalam panel tidak merembet keluar.
  - Header mobile mematikan `backdrop-filter` (agar tidak jadi containing block).
  - **`.nav-toggle` WAJIB `position: fixed; top:0; left:0`, JANGAN `absolute`.**
    Ini bug yang pernah terjadi: dengan `absolute`, checkbox berada di dalam
    `.site-header` yang `position: sticky` — secara visual menempel di puncak layar
    (`viewportTop=34`) tapi posisi **layout**-nya tetap jauh di bawah dokumen
    (`docTop=934` saat `scrollY=900`), karena offset sticky tidak ikut dihitung.
    Begitu label hamburger disentuh, browser memfokuskan checkbox itu lalu
    men-*scroll-into-view* "supaya terlihat" — padahal sudah terlihat — sehingga
    **halaman melompat**; terukur **388px** ke atas dan tidak kembali saat menu
    ditutup (`scroll-padding-top: 84px` ikut memengaruhi perhitungan). Elemen `fixed`
    dianggap selalu di dalam viewport, jadi scroll-into-view jadi no-op.
    Setelah diperbaiki: `dy=0` diuji dari `scrollY` 0, 900, dan 2000.
  - **Cara mengukur gejala "halaman bergeser" (jangan pakai mata):** muat halaman di
    dalam `<iframe width="375">` (viewport iframe = 375px sungguhan, tak terkena clamp
    ~504px Edge headless), suntik
    `html{scroll-behavior:auto !important}*{transition:none !important}` — **wajib**,
    karena `scroll-behavior: smooth` membuat `scrollTo` beranimasi dan di bawah
    `--virtual-time-budget` animasi itu tak pernah maju sehingga `scrollY` selalu
    terbaca 0 (ini yang dulu membuat bug-nya tampak "tak bisa direproduksi").
    Lalu `scrollTo` ke posisi tertentu → catat `scrollX/scrollY` → `.nav-burger.click()`
    → catat ulang. Selisihnya harus 0.
  - **`overflow-x` di `html` & `body` pakai `clip`, BUKAN `hidden`** (`styles.css`):
    `overflow-x: hidden` + `overflow-y: visible` membuat `overflow-y` ikut jadi
    `auto`, jadi `body` berubah menjadi scroll container → dua scroller bersarang
    (html + body). Itu sumber halaman terasa **"bergeser"** saat drawer menggeser
    masuk, dan bisa mematahkan `position: sticky` header. `clip` mengklip tanpa
    membuat scroll container. `hidden` disimpan sebagai fallback browser lama —
    urutannya `overflow-x: hidden;` lalu `overflow-x: clip;`, jangan dibalik.
  - Reduced-motion tidak perlu diubah: blok `prefers-reduced-motion` hanya menolkan
    `transition-duration` (drawer terbuka seketika, tetap berfungsi) dan memaksa
    `transform: none` pada selector hover/reveal tertentu — `.nav` tidak termasuk.
- **Footer (4 kolom, gaya B2B manufaktur):** grid `.foot-cols` (`align-items: start`)
  berisi 4 kolom yang **semua mulai di baseline atas yang sama** (sejajar tepi atas
  logo):
  1. **Perusahaan** (`.foot-info`): **logo footer ≠ logo header.** Footer memakai
     **satu gambar** lockup mendatar `assets/logo-lockup.png` (570×124, transparan)
     di dalam **PANEL PUTIH** — meniru `.footer-brand .logo-link` di centralcats.id.
     Wordmark ikut di dalam bitmap, jadi **tidak ada teks HTML** di footer:
     `.foot-brand` = `<a>` + satu `<img class="foot-brand__mark">`, titik.
     - **`.foot-brand__name`, `.eco`, `-webkit-text-stroke`, `paint-order` di footer
       sudah DIHAPUS** beserta seluruh trik keterbacaannya (kerangka putih 0.8px pada
       huruf). Panel putih menyelesaikan masalah yang dulu ditambal kerangka itu.
       `.brand__name` & `.eco` **juga sudah dihapus** dari header (lihat di bawah) —
       tidak ada teks wordmark HTML di situs ini lagi.
     - **Panel putih di sini SAH, walau chip putih pernah ditolak.** Yang ditolak dulu
       adalah chip di belakang emblem + *teks HTML* (mengganggu, karena teksnya bisa
       diwarnai langsung). Sekarang wordmark navy ada di dalam bitmap → tanpa backing
       terang ia tak terbaca di footer gelap. Jangan "perbaiki" ini balik ke lockup
       polos. Yang **tetap ditolak**: gradasi hijau di belakang lockup, outer glow
       `drop-shadow`/`text-shadow` putih berlapis, backlight hijau bernafas + light
       sweep (efek LED).
     - CSS panel: `padding: 8px 14px` (lebih lega dari centralcats `6px 10px` karena
       aset di-crop rapat — emblem menyentuh tepi atas & bawah), `background: #fff`,
       `border-radius: var(--radius-sm)`, `box-shadow: var(--shadow-sm)`,
       `width: fit-content`. Hover cuma menaikkan elevasi ke `--shadow-md`, **tanpa
       transform** → sengaja, supaya tak perlu entri baru di blok
       `prefers-reduced-motion`.
     - `.foot-brand__mark { height: 52px }` (naik dari emblem 36px yang lama, atas
       permintaan pemilik "logo agak diperbesar"). Pada rasio 570×124 itu memberi
       "ECOPLAST" 20px & "SOLUTIONS" 8.8px. **Kalau tinggi ini diubah, cek SOLUTIONS
       jangan sampai < 8px** — di bawah itu tak terbaca. Aset 2.4× ukuran tampil →
       tetap tajam di retina.
     - **Cara meregenerasi `logo-lockup.png`** (Pillow, sumber `assets/logo-full.png`):
       crop dua band lalu susun mendatar. Emblem = `(134,71,382,288)` → 248×217;
       wordmark ECOPLAST+SOLUTIONS = **satu unit** `(58,300,448,383)` → 390×83 (spasi
       antar-baris & garis samping SOLUTIONS sudah benar dari aslinya, jangan disusun
       manual). Emblem diskalakan ke `1.5 ×` tinggi wordmark (→ 124px, wordmark tetap
       **native** supaya tak di-upscale), gap **38px**, wordmark ditaruh di `y=27`.
       Band tagline (`y 399–423`) **dibuang** — cuma 3% tinggi lockup, jadi bubur.
       Angka 1.5× itu kompromi: di logo asli emblem 2,6× lebih tinggi dari wordmark,
       kalau rasio itu dipertahankan "SOLUTIONS" jatuh ke ±5px dan hilang.
       `y=27` dipilih setelah merender 3 varian (bbox-center 21, tengah 27,
       ink-centroid 34) dan **melihat**-nya: 21 menggantung ke atas, 34 terlalu turun.
       Titik berat optis emblem ada di 52,4% tingginya (ekor swoosh menarik ke bawah),
       wordmark di 37,5% (ECOPLAST berat di atas) — makanya bbox-center saja kurang pas.
     Flush-left: **panel** yang lurus kiri dengan teks di bawahnya
     (`panel.left == alamat.left`; dulu `emblem.left`, kini emblem masuk ±14px karena
     padding panel — itu benar, bukan bug). Di bawah lockup **langsung ALAMAT**
     (`.foot-address`) — **tanpa paragraf deskripsi.** Kalimat "Jasa produksi tali
     plastik & biji plastik PP untuk kebutuhan industri." dulu ada di antara keduanya,
     **sudah dihapus atas permintaan pemilik — jangan dikembalikan.**
     **Jarak lockup → alamat dikendalikan `.foot-brand { margin-bottom }` saja**,
     kini **`0.5rem` = 8px** (terukur). Riwayat angka ini, supaya tidak diputar balik:
     `1.4rem` (22.4px) itu nilai warisan dari zaman paragraf deskripsi masih ada;
     `.foot-address` sendiri punya `margin-top: 1rem`, jadi setelah paragraf dihapus
     keduanya menumpuk jadi 2.4rem → ditambal `.foot-brand + .foot-address
     { margin-top: 0 }` (**tetap perlu, jangan dihapus**) yang mengembalikannya ke
     22.4px, lalu pemilik menilai itu **masih terlalu jauh** → margin-bottom
     diturunkan ke `0.5rem`. Perhatikan `.foot-brand` = `display: inline-flex`, jadi
     margin-bottom-nya menambah tinggi *line box*; jarak **visual** ke baris "PT.
     Mencoba Bertahan Hidup" ±14px karena half-leading `line-height: 1.7` di
     `.foot-address` menambah ±6px yang tak terhitung di angka gap.
     Keempat kolom tetap mulai di baseline atas yang sama (terukur `colTops` identik).
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
  - **Susunan blok alamat footer (`.foot-address`), kelima file — urutannya penting:**
    1. `PT. Mencoba Bertahan Hidup` — `<span class="foot-address__org">`, tebal &
       sedikit lebih terang (`#C7D3CA`) supaya jadi baris utama.
    2. `Kantor Pusat` — `<strong>`, distyle jadi label mono kecil uppercase
       (`.foot-address strong`). Dulu labelnya berbunyi "Office".
    3. Alamat + kode pos, diakhiri titik.
    `<span>`/`<strong>` keduanya `display: block`, jadi **tidak perlu `<br />`** di
    antara ketiganya — menambahkannya justru memberi baris kosong ekstra.
    Nama badan hukum **PT** memang dipakai di sini (bukan nama brand). Pernah dicoba
    diganti `ECOPLAST SOLUTIONS` — **ditolak**, yang salah cuma susunannya.
    Nama brand tetap muncul di lockup logo footer & baris copyright; `legalName` di
    JSON-LD (`index.html` + `kontak.html`) tidak berubah.
  - Border-top aksen hijau; divider copyright halus (`rgba(255,255,255,.06)`); baris
    copyright **center full-width** (`.foot-bottom`, di luar grid kolom), tahun otomatis
    via `.js-year` + `getFullYear()` (fallback `2026`). Teks: `© <tahun> Ecoplast
    Solutions. Balaraja, …` — pakai **brand "Ecoplast Solutions"**, bukan nama badan
    hukum PT (badan hukum tetap tampil di kolom "Perusahaan" & JSON-LD `legalName`).
  - Responsif (`responsive.css`): desktop 4 kolom → tablet ≤900px **2×2** → mobile
    ≤560px **1 kolom** (urutan: logo, alamat, Halaman, Kontak, Media Sosial,
    copyright). Kalau mengedit footer, ubah di **kelima** file HTML.
- **Kontak**: kartu info pakai baris `.crow` (chip-ikon + label + nilai + divider),
  blok peta `.map-block` (header "Lokasi" + tombol "Buka di Google Maps"). Kartu
  "Informasi kontak" dan "Minta penawaran harga" dibuat **sama lebar & tinggi**. Kartu
  penawaran (`.quote-card`, latar hijau gelap) = **judul + deskripsi + tombol saja**
  (tombol rata-bawah via `margin-top:auto`). Pernah dicoba diisi ikon: chip kotak
  (mirip `.crow__ico`) bikin ambigu, watermark jabat tangan besar terlihat jelek —
  keduanya **ditolak**; biarkan kartu polos.
- Aksesibilitas: skip-link, `:focus-visible` jelas, `prefers-reduced-motion` dihormati,
  responsif sampai lebar 360px.

## SEO / GEO (local SEO)

- **Koordinat lokasi (sumber kebenaran): `-6.2077458, 106.4389806`** (dari embed resmi
  listing Google Business "ECOPLAST SOLUTIONS"). Dipakai di: geo meta tags (kelima
  halaman) dan JSON-LD `GeoCoordinates` (`index.html` + `kontak.html`).
- **`geo.region` / `geo.placename` / `geo.position` / `ICBM` sebenarnya DIABAIKAN
  Google & Bing** — tag warisan lama. Tidak merusak, boleh dipertahankan, tapi jangan
  dianggap fondasi GEO. Yang benar-benar dibaca: JSON-LD, NAP di HTML, Google Business
  Profile.
- **Faktor GEO terbesar ada di luar repo:** Google Business Profile (kategori utama &
  tambahan, foto, jam, area layanan, produk, dan terutama **ulasan**) menentukan sebagian
  besar peluang masuk "local 3-pack". Listing sudah ada (CID `893561851054800034`), tapi
  isinya tak bisa diaudit dari repo/CLI — halaman Maps cuma shell JavaScript. Padanan
  untuk Edge/Bing: **Bing Places for Business** (belum didaftarkan).
- **Peta = listing bisnis, bukan pin koordinat mentah.** Embed (`kontak.html`) pakai URL
  `maps/embed?pb=...` resmi listing (menampilkan kartu "ECOPLAST SOLUTIONS"). Tombol
  "Buka di Google Maps" + JSON-LD `hasMap` pakai link listing via CID:
  `https://www.google.com/maps?cid=893561851054800034`. **Jangan** kembalikan ke format
  `search/?api=1&query=LAT,LNG` — itu cuma menjatuhkan pin di titik koordinat (bukan nempel
  ke listing) dan pernah bikin peta meleset ~286 m (longitude salah `106.4415609`).
- **`robots.txt`**: allow all + `Sitemap:` pointer. **`sitemap.xml`**: 5 URL absolut
  (beranda, produk, tentang, kontak, kebijakan-privasi) + `<lastmod>` per URL.
- **Meta per halaman (kelima file):** `keywords` (mengandung Indonesia / Tangerang /
  Balaraja / Banten — permintaan pemilik; mesin pencari besar mengabaikannya, sinyal
  lokal yang benar-benar dipakai ada di `<title>`/description/JSON-LD/isi halaman),
  `robots` = `index, follow, max-snippet:-1, max-image-preview:large` (izin snippet &
  gambar preview besar), `theme-color` + `msapplication-TileColor` = `#12442B`.
  JSON-LD `LocalBusiness` juga punya properti `keywords`.
- **Favicon & gambar OG (sumber: `assets/logo-full.png`, logo resmi 500×500 transparan):**
  - **Favicon = EMBLEM saja** (lingkaran panah + "e" + daun + pellet), di-crop dari blok
    konten teratas logo (y 41–285 pada kanvas 500px), transparan. Dulu favicon memakai
    **lockup penuh** (emblem + wordmark + tagline + 4 badge) yang diperkecil ke 16px →
    tampil sebagai gumpalan hijau tak terbaca di tab Edge/Chrome. **Jangan** kembalikan
    ke lockup penuh untuk ikon kecil.
  - `favicon.ico` = **multi-size 16/32/48** (entri ber-payload PNG). Ukuran **48px wajib
    ada**: Bing & Google hanya menampilkan favicon di hasil pencarian bila tersedia
    ≥48×48. Sebelumnya ICO cuma 16×16 → favicon tak pernah muncul di hasil pencarian.
  - `<link rel="icon">` **tidak lagi memakai `sizes="any"`** pada ICO. `sizes="any"`
    menandai ikon sebagai *scalable* (semantik untuk SVG), jadi Chromium/Edge selalu
    memilih ICO itu dan mengabaikan PNG yang lebih besar. Sekarang: ICO dideklarasikan
    `sizes="16x16 32x32 48x48"` + PNG 32 & 48 terdaftar eksplisit.
  - `apple-touch-icon.png` = emblem 180×180 di atas **putih solid** (iOS/Windows tile
    tidak menangani alpha dengan baik), padding 10%.
  - `og:image` = **`assets/og-image.png` 1200×630** + `og:image:width/height/alt` +
    `twitter:card=summary_large_image`. Dulu `og:image` menunjuk `apple-touch-icon.png`
    (180×180 persegi) → di bawah minimum 1200×630 yang diminta Facebook/WhatsApp/Bing,
    preview tampil sebagai kotak kecil. Kartu OG dibuat **dari HTML + screenshot headless
    1200×630** (cara yang sama dgn verifikasi lokal), bukan digambar manual — sumber
    kartu ada di riwayat commit; komposisi: emblem kiri, wordmark `ECO` hijau + `PLAST
    SOLUTIONS` navy, tagline produk, baris mono geo, bar gradasi bawah.
  - Regenerasi ikon butuh Pillow. Pakai venv sementara (bukan install global), sumber
    `assets/logo-full.png`; skrip generator ada di riwayat commit.
    **Emblem = blok konten teratas logo, y 70–287 pada kanvas 500px** (blok di
    bawahnya = wordmark, tagline, 4 badge — semuanya tak terbaca di ukuran ikon).
    Render kartu OG dengan `--disable-lcd-text` supaya teks tak dapat fringe subpixel.
  - **`og-image.png` sudah diaudit ulang & LOLOS — tak perlu diperiksa lagi tanpa
    alasan baru.** Metodenya (kalau kelak asetnya diregenerasi): sampling **median
    piksel inti** per kelompok warna, sama seperti verifikasi token brand. Hasil:
    wordmark ECO `#3E9B37` & PLAST/SOLUTIONS `#173B5C` = **jarak 0.0** ke
    `--brand-green`/`--brand-navy` (pixel-exact); emblem hijau `#489E31` = **jarak
    0.0** ke `assets/logo-mark.png`, navy `#10325A` vs `#0F3058` = jarak 3.0 (selisih
    resampling). Arc navy + pellet navy + "e"/daun hijau semuanya ada → yang dipakai
    **logo asli**, bukan varian `ecoplast logo.png`. **Jangan** pixel-diff kartu OG
    vs `logo-mark.png` pakai crop yang ditebak — skala/offset tak presisi memberi
    mean-diff besar (pernah terbaca 77/255) yang MENYESATKAN, seolah emblemnya beda.
    Bandingkan warna inti, bukan piksel per piksel.
  - Kalau `og-image.png` diganti, ingat cache **di luar repo**: Facebook/WhatsApp
    menyimpan preview per URL, jadi kartu lama bisa tetap tampil sampai di-refresh
    manual lewat Facebook Sharing Debugger. Bukan bug yang bisa dibetulkan dari repo.
- **Bing / Microsoft Edge (terpisah dari Google!)**: Edge memakai indeks **Bing**, dan
  Bing **tidak** ikut verifikasi Google Search Console. Yang sudah disiapkan di repo:
  **kunci IndexNow** `b3f0fc85243d5aabc07151b1c4324fe5.txt` di root (isinya = nama
  kuncinya sendiri) — file ini **JANGAN dihapus/diubah**, kalau hilang seluruh
  pengiriman IndexNow gagal. Ping Bing setelah konten berubah:
  `https://api.indexnow.org/indexnow?url=<URL>&key=b3f0fc85243d5aabc07151b1c4324fe5`.
  **Bing Webmaster Tools** (bing.com/webmasters): situs ditambahkan **manual**, bukan
  lewat "Import from Google Search Console" — verifikasi kepemilikan pakai metode
  **XML file**, yaitu `BingSiteAuth.xml` di root (token `3D02D86…`). File ini
  **JANGAN dihapus/diubah** — sama seperti file verifikasi GSC, kepemilikan dicek
  ulang berkala; hilang = verifikasi dicabut. Jalur manual dipilih karena login BWT
  boleh pakai akun apa pun (Microsoft/Google/Facebook) dan tak perlu menyentuh akun
  Google pemilik GSC; buktinya kontrol atas domain, bukan akun. Setelah verified,
  submit `sitemap.xml` di menu Sitemaps.
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
- **`BingSiteAuth.xml` JANGAN dihapus/diubah** — file verifikasi Bing Webmaster
  Tools; dihapus = verifikasi dicabut.
- **`b3f0fc85243d5aabc07151b1c4324fe5.txt` JANGAN dihapus/diubah** — kunci IndexNow
  (Bing/Edge). Isinya harus tetap sama dengan nama filenya.
- Jangan mengarang klaim/angka (kapasitas ton, tahun berdiri, jumlah klien, testimoni).
  Konten hanya yang faktual/diberikan.
- Pertahankan: tanpa build step, tanpa framework, JS hanya seperlunya (kini: skrip
  tahun + reveal `IntersectionObserver`, keduanya progressive-enhancement).
  `@media` hanya di `responsive.css`.

## Deploy

1. **Kalau `styles.css`/`responsive.css` diubah: naikkan `?v=` di link stylesheet
   kelima file HTML** (lihat bagian Stack). Lupa langkah ini = perubahan CSS tak
   tampil di live sampai 4 jam.
2. `git add -A && git commit -m "..."` lalu `git push origin main`.
3. GitHub Pages rebuild otomatis; live dalam **±1 menit**.
4. Domain lewat **Cloudflare** (di depan GitHub Pages) → ada cache. Setelah push,
   **hard-refresh** (`Ctrl+Shift+R`). Banyak "keluhan tampilan" ternyata cache lama —
   selalu hard-refresh dulu. Jika perlu, purge cache di dashboard Cloudflare.
5. **Cara memastikan yang live benar-benar sudah baru (jangan pakai mata):**
   `curl -s -o /dev/null -D - <url> | grep -iE "last-modified|cf-cache-status|age"`.
   Bandingkan `last-modified` HTML vs CSS vs aset — kalau CSS lebih tua, itu edge
   cache basi, bukan bug CSS. Cek isinya langsung dengan
   `curl -s https://ecoplastsolutions.id/styles.css | grep -A6 "^\.foot-brand {"`.

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
  `/assets/logo-lockup.png`, `/assets/logo-full.png`,
  `/assets/og-image.png`,
  `/assets/product/*.webp`, `favicon.ico`, `favicon-32x32.png`, `favicon-48x48.png`,
  `apple-touch-icon.png`.
