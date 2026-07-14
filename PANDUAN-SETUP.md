# Panduan Setup — Kalkulator HPP & Harga Jual GMG

Aplikasi ini punya 2 fitur tambahan yang butuh sedikit setup di luar file:

1. **Instal sebagai aplikasi** (PWA) — butuh hosting HTTPS.
2. **Sinkronisasi ke Google Drive** — butuh Google OAuth Client ID (gratis).

Kabar baik: aplikasi **tetap berfungsi penuh secara offline** (data tersimpan
di perangkat/browser) walau kamu belum melakukan setup ini. Kedua fitur di
bawah sifatnya opsional/tambahan.

---

## Bagian A — Hosting agar bisa "Diinstal" sebagai Aplikasi

Fitur "Instal Aplikasi" (PWA) hanya aktif penuh jika file dibuka lewat
**HTTPS**, bukan dibuka langsung dari file di komputer (`file://...`).
Paling gampang pakai **GitHub Pages** (gratis, tanpa kartu kredit).

### Langkah — GitHub Pages

1. Buat akun di [github.com](https://github.com) kalau belum punya.
2. Klik **New repository** → beri nama misalnya `kalkulator-hpp-gmg` →
   pilih **Public** → **Create repository**.
3. Klik **Add file → Upload files**, lalu unggah **semua isi folder**
   yang saya berikan (`index.html`, `manifest.json`, `sw.js`, folder
   `icons/`, dsb) — jaga strukturnya tetap sama persis.
4. Klik **Commit changes**.
5. Buka tab **Settings → Pages** (menu kiri).
6. Di **Branch**, pilih `main` dan folder `/ (root)` → **Save**.
7. Tunggu 1–2 menit, GitHub akan memberi alamat seperti:
   `https://namakamu.github.io/kalkulator-hpp-gmg/`
8. Buka alamat itu di HP atau laptop. Di Chrome/Edge akan muncul ikon
   "Install" di address bar; di HP Android akan muncul banner
   "Tambahkan ke layar utama". Di iPhone: buka di Safari → tombol
   **Share** → **Add to Home Screen**.

> Alternatif lain yang sama mudahnya: **Netlify** (drag & drop folder ke
> app.netlify.com) atau **Vercel**. Prinsipnya sama: unggah folder ini apa
> adanya ke hosting statis apa pun yang punya HTTPS.

---

## Bagian B — Membuat Google OAuth Client ID (untuk Sinkron Drive)

Client ID ini seperti "kartu identitas" aplikasi kamu ke Google, supaya
Google tahu aplikasi mana yang boleh menyimpan file ke Drive milik
pengguna. **Gratis**, tidak perlu kartu kredit, dan hanya kamu yang pegang.

### Langkah 1 — Buat Project di Google Cloud Console

1. Buka [console.cloud.google.com](https://console.cloud.google.com/) →
   login dengan akun Google (bisa akun pribadi/perusahaan).
2. Di bagian atas, klik dropdown nama project → **New Project**.
3. Isi nama, misalnya `Kalkulator HPP GMG` → **Create**.
4. Tunggu beberapa detik sampai project aktif (pilih project itu di
   dropdown atas jika belum otomatis terpilih).

### Langkah 2 — Aktifkan Google Drive API

1. Di kotak pencarian atas, ketik **Google Drive API** → klik hasilnya.
2. Klik tombol **Enable**.

### Langkah 3 — Setup OAuth Consent Screen

1. Di menu kiri: **APIs & Services → OAuth consent screen**.
2. Pilih **User Type: External** → **Create**.
3. Isi:
   - **App name**: `Kalkulator HPP GMG`
   - **User support email**: email kamu
   - **Developer contact information**: email kamu
   - Lainnya boleh dikosongkan → **Save and Continue**.
4. Di halaman **Scopes**, klik **Add or Remove Scopes**, cari dan centang:
   `.../auth/drive.appdata` (Google Drive API — See, create, and delete
   its own configuration data in your Google Drive) → **Update** →
   **Save and Continue**.
5. Di halaman **Test users**, klik **Add Users** → masukkan alamat email
   Google yang akan dipakai untuk login di aplikasi ini (misalnya email
   admin toko/bengkel) → **Save and Continue**.
   > Selama app masih status "Testing", **hanya email yang didaftarkan di
   > sini** yang bisa login. Kalau butuh banyak orang pakai, ulangi
   > menambahkan email di sini, atau ajukan **Publish App** (lihat catatan
   > di bawah).
6. Selesai → **Back to Dashboard**.

### Langkah 4 — Buat Client ID

1. Menu kiri: **APIs & Services → Credentials**.
2. Klik **+ Create Credentials → OAuth client ID**.
3. **Application type**: pilih **Web application**.
4. **Name**: `Kalkulator HPP GMG - Web`.
5. Di **Authorized JavaScript origins**, klik **+ Add URI** dan masukkan
   alamat tempat aplikasi ini akan diakses, contoh:
   - `https://namakamu.github.io` (kalau pakai GitHub Pages — origin
     saja, **tanpa** path `/kalkulator-hpp-gmg` di belakangnya)
   - Tambahkan juga `http://localhost:5500` atau port lain jika kamu mau
     mencoba dari komputer sendiri sebelum di-hosting (opsional).
6. Klik **Create**.
7. Muncul popup berisi **Client ID** — bentuknya seperti:
   `123456789012-abcxyz1234567890.apps.googleusercontent.com`
   Salin nilai ini.

### Langkah 5 — Pasang Client ID ke Aplikasi

1. Buka aplikasi Kalkulator HPP di browser.
2. Masuk ke tab **⚙ Admin** (login dulu, default `admin` / `admin123`
   kecuali sudah pernah diganti).
3. Scroll ke kartu **Sinkronisasi Google Drive**.
4. Tempel Client ID ke kolom **Google OAuth Client ID**.
5. Klik **🔗 Sambungkan ke Google Drive** → pilih akun Google (harus akun
   yang didaftarkan sebagai *Test user* di Langkah 3.5) → izinkan akses.
6. Selesai — status berubah jadi **Tersambung**, dan sejak itu data akan
   otomatis tersinkron ke Drive setiap ada perubahan.

> Client ID ini **tidak rahasia** (aman ditempel di halaman web publik),
> tapi tetap disarankan hanya membagikannya ke tim internal GMG.

### Catatan: Batas "Testing" vs "Published"

Selama OAuth consent screen berstatus **Testing**, Google akan menampilkan
peringatan "Google hasn't verified this app" saat login, dan **hanya**
email yang terdaftar sebagai Test user yang bisa masuk. Ini **aman** untuk
dipakai — cukup klik **Advanced → Go to Kalkulator HPP GMG (unsafe)** saat
peringatan muncul (peringatan ini normal untuk semua app baru yang belum
diajukan verifikasi resmi ke Google, bukan berarti aplikasinya berbahaya).

Kalau nanti ingin semua staf GMG bisa connect tanpa didaftarkan satu-satu,
kamu bisa klik **Publish App** di OAuth consent screen. Karena scope yang
dipakai (`drive.appdata`) tergolong low-risk, biasanya tidak memerlukan
proses verifikasi tambahan dari Google.

---

## Struktur File

```
kalkulator-hpp-gmg/
├── index.html          ← aplikasi utama
├── manifest.json        ← metadata PWA (nama, ikon, warna)
├── sw.js                 ← service worker (cache offline)
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   ├── icon-maskable-192.png
│   ├── icon-maskable-512.png
│   ├── apple-touch-icon.png
│   ├── favicon-32.png
│   └── favicon-16.png
└── PANDUAN-SETUP.md     ← file ini
```

Jaga struktur ini tetap sama saat mengunggah ke hosting, karena
`index.html` merujuk ke file-file lain dengan path relatif
(`./manifest.json`, `./icons/...`, `./sw.js`).

---

## Pertanyaan Umum

**Kalau saya ganti/update `index.html` di masa depan, apa yang perlu
diingat?**
Naikkan nomor versi di baris `CACHE_VERSION` pada `sw.js` (contoh:
`gmg-hpp-v1.0.0` → `gmg-hpp-v1.0.1`), supaya perangkat yang sudah instal
aplikasi mengambil versi terbaru, bukan versi lama dari cache.

**Apakah data di Drive bisa dilihat orang lain?**
Tidak. File disimpan di *App Data folder* — folder tersembunyi khusus
milik aplikasi ini di akun Google masing-masing pengguna, tidak muncul di
Google Drive biasa dan tidak bisa diakses aplikasi lain.

**Bagaimana kalau dua orang mengubah data di waktu yang hampir sama?**
Sinkronisasi ini bersifat *last-write-wins* (data yang terakhir disimpan
akan menimpa). Untuk penggunaan tim, disarankan satu orang sebagai
penanggung jawab input data, atau selalu klik **⇩ Tarik dari Drive**
sebelum mulai mengedit di perangkat lain.
