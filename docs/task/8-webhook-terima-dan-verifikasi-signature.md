# 8. Webhook: endpoint penerima dan verifikasi signature

## Tujuan
Endpoint publik yang menerima event webhook dari Pixelforge dan memverifikasi bahwa request benar-benar datang dari Pixelforge (bukan orang lain yang menebak URL webhook).

## Scope
- Route `POST /webhooks/pixelforge`, **di luar** middleware `auth` dan **di luar** CSRF (daftarkan di exception CSRF Laravel), karena ini dipanggil oleh Pixelforge, bukan browser user.
- Baca dari docs Pixelforge: header mana yang berisi signature, algoritma hash-nya (biasanya HMAC-SHA256 atas raw body + secret), dan format timestamp/replay protection kalau ada.
- Buat middleware atau logic verifikasi signature sendiri (`VerifyPixelforgeWebhookSignature`) yang:
  - Menolak request dengan signature salah/hilang → response 401, **tidak** diproses lebih lanjut.
  - Memakai raw request body (bukan hasil `json_decode` ulang) untuk hitung HMAC, karena re-encode JSON bisa mengubah urutan/whitespace dan membuat signature tidak match.
- Simpan setiap payload webhook mentah yang masuk (tabel `webhook_events` atau log terstruktur) sebelum diproses lebih lanjut — ini dipakai untuk audit dan debugging di dashboard Pixelforge.
- Test: signature valid → lolos; signature salah → 401; body dimodifikasi tapi signature header tetap sama → 401.

## Review checklist
- [ ] Verifikasi signature memakai raw body, bukan body yang sudah di-decode/encode ulang.
- [ ] Request tanpa signature atau signature salah selalu ditolak (401), tidak pernah diproses sebagai event valid.
- [ ] Endpoint webhook tidak memerlukan login/session (karena dipanggil server-to-server).
- [ ] Ada test unit/feature untuk signature valid dan invalid.
- [ ] Payload mentah tersimpan sebelum diproses (untuk audit), terlepas dari apakah pemrosesan selanjutnya sukses.

## Di luar scope
- Mengubah status `generations` berdasarkan isi event (task 9).
- Menangani duplikat/out-of-order (task 9).
