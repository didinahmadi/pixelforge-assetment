# 13. Deploy ke public host + API key environment "deployed"

## Tujuan
App bisa diakses lewat URL publik, dengan webhook yang benar-benar diterima dari internet (bukan tunnel local), dan key Pixelforge terpisah dari local supaya `GET /v1/usage` bisa dicocokkan per environment.

## Scope
- Pilih host (Laravel Cloud disebut di JOBDESC sebagai yang paling mudah; boleh host lain).
- Buat API key Pixelforge **kedua**, khusus untuk environment deployed (terpisah dari key local di task 2), dengan webhook URL yang diarahkan ke domain publik hasil deploy.
- Set environment variables di host: `APP_URL`, `APP_KEY`, kredensial Pixelforge (key + signing secret environment deployed), DB (SQLite file path yang persistent — pastikan platform deploy tidak menghapus file SQLite tiap deploy; kalau platform tidak mendukung disk persistent, pertimbangkan migrasi ke DB terkelola seperti Postgres/MySQL untuk deployed saja).
- Jalankan migration di environment deploy.
- Verifikasi webhook benar-benar sampai: submit generation di app yang sudah deploy, cek dashboard Pixelforge bahwa webhook attempt tercatat dan endpoint menjawab 200.
- Tinggalkan beberapa generation nyata di app yang sudah deploy (diminta JOBDESC) supaya reviewer bisa bandingkan angka dengan `GET /v1/usage`.

## Review checklist
- [ ] URL publik bisa diakses dan berfungsi (bisa login, generate, lihat history).
- [ ] Webhook dari Pixelforge sukses sampai ke app deployed (dicek di dashboard Pixelforge, bukan asumsi).
- [ ] Key Pixelforge untuk deployed berbeda dari key local.
- [ ] Ada generation yang sudah selesai (`succeeded`/`failed`) tersisa di app deployed sebagai contoh untuk reviewer.
- [ ] SQLite (atau DB pengganti) persistent antar restart/deploy — dicek dengan redeploy kecil dan pastikan data generation sebelumnya tidak hilang.

## Di luar scope
- Setup Docker (sudah ada dari task sebelumnya, dipakai sebagai dasar kalau host butuh container; kalau Laravel Cloud, biasanya tidak perlu Dockerfile sendiri — cek dokumentasi host yang dipilih).
