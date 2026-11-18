# 2. Setup akun dan API key Pixelforge

## Tujuan
Punya akses ke Pixelforge API untuk environment local, sebelum task lain yang butuh panggil API-nya bisa dikerjakan.

## Scope
- Daftar akun di Pixelforge.
- Baca seluruh API docs Pixelforge (ini wajib — banyak detail penilaian ada di sana: format request/response, rate limit, cara hitung harga, format webhook, signature verification, keyword `[fail]`/`[slow]`/`[duplicate]`).
- Buat satu API key untuk environment **local** (nanti task 11 bikin key terpisah untuk **deployed**).
- Simpan API key dan webhook signing secret di `.env` (jangan commit).
- Catat ringkasan hal-hal penting dari docs yang dipakai berulang (endpoint, auth header, format signature, rate/size pricing) di `docs/pixelforge-notes.md` supaya tidak perlu buka docs API terus-menerus.

## Review checklist
- [ ] Akun Pixelforge aktif, emailnya dicatat (dibutuhkan saat submit assessment).
- [ ] API key local tersimpan di `.env`, bukan di kode.
- [ ] Ada catatan ringkas (`docs/pixelforge-notes.md`) berisi: endpoint generate, endpoint usage, format webhook, cara verifikasi signature, struktur pricing/rate.

## Di luar scope
- Implementasi client PHP-nya (task 3).
- Deploy key (task 11).
