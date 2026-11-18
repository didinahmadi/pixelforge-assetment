# 12. Robustness: rate limit, overload, dan retry

## Tujuan
App tidak jatuh/berantakan saat Pixelforge mengembalikan rate limit, overload, atau concurrency limit — yang menurut JOBDESC memang disengaja terjadi untuk ditest.

## Scope
- Baca docs Pixelforge bagian error/rate limit: kode status apa yang dipakai untuk masing-masing kasus (429 rate limit, 503/529 overload, dsb) dan apakah ada header `Retry-After`.
- Di `PixelforgeClient` (task 3), tambahkan retry dengan backoff (misal pakai `Http::retry()` Laravel) khusus untuk error yang memang transient (rate limit, overload) — **jangan** retry untuk error permanen (validasi gagal, auth salah).
- Kalau setelah retry tetap gagal: tandai generation sebagai `failed` dengan `failure_reason` yang jelas ("Pixelforge sedang overload, coba lagi nanti" dsb), bukan error 500 polos ke user.
- Tampilkan pesan error yang dipahami user di halaman generate/history saat ini terjadi (bagian dari "product sense" di kriteria penilaian).
- Test dengan prompt keyword `[fail]`, `[slow]` dari docs Pixelforge (atau `Http::fake()` yang mensimulasikan 429/503) untuk memverifikasi retry dan fallback bekerja.

## Review checklist
- [ ] Retry hanya terjadi untuk error transient, tidak untuk error permanen (validasi, auth).
- [ ] Ada backoff (tidak retry langsung bertubi-tubi tanpa delay).
- [ ] User melihat pesan yang masuk akal saat Pixelforge gagal, bukan error generik/500.
- [ ] Dites manual dengan keyword `[fail]`/`[slow]` dari docs Pixelforge, hasilnya dicatat (misal di PR description atau catatan testing).

## Di luar scope
- Duplikat/out-of-order webhook (sudah di task 9 — itu bagian robustness yang berbeda).
