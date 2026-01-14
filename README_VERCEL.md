# Deploy ke Vercel — Petunjuk singkat

Langkah cepat untuk membuat `docs/` hidup di Vercel.

1) Persiapan lokal (opsional):

```bash
# (opsional) install Vercel CLI
npm install -g vercel

# login ke akun Vercel (ikuti prompt)
vercel login
```

2) Deploy via CLI (direkomendasikan jika sudah login):

```bash
# jalankan dari root repo
cd "$(pwd)"

# deploy sekali (ikuti prompt). vercel.json akan mengarahkan route ke folder `docs`.
vercel --prod
```

3) Deploy via Vercel dashboard (UI):

- Hubungkan repository GitHub/GitLab/Bitbucket ke Vercel.
- Pada pengaturan Project -> General, set **Root Directory** ke `/` (root repo).
- Pada Build & Output Settings, set **Output Directory** atau konfigurasi build agar menunjuk ke folder `docs` jika diminta. Jika menggunakan `vercel.json` ini, Vercel akan menggunakan builder `@vercel/static` dan melayani file dari `docs/`.

Catatan:
- Saya tidak dapat menjalankan `vercel` untuk Anda karena memerlukan otentikasi ke akun Vercel.
- Setelah deploy, Vercel memberikan URL publik (mis. `https://your-project.vercel.app`). Gunakan itu untuk verifikasi live.

Jika Anda mau, saya bisa:
- Menyiapkan variable env atau build scripts tambahan jika diperlukan.
- Membantu menjalankan `vercel --prod` di terminal Anda (beri izin atau jalankan sendiri lalu beritahu saya hasilnya).
