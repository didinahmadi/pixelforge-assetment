# 11. Halaman history generation

## Tujuan
User melihat daftar generation miliknya: status (live), estimasi biaya, biaya aktual, dan gambar hasil.

## Scope
- Page Vue `History.vue` menampilkan list `generations` milik user yang login saja (scoping per user, jangan bocor ke user lain).
- Tiap baris menampilkan: prompt (dipotong kalau panjang), size/quality, status (dengan badge warna per status), estimated cost, actual cost (kosong/placeholder kalau belum final), gambar (kalau `succeeded` dan `image_url` masih ada — tangani kasus gambar sudah expired dengan fallback gambar rusak/placeholder, bukan broken UI).
- Status "live": pakai polling ringan dari frontend (misal tiap 5-10 detik hit endpoint kecil yang mengembalikan status terbaru untuk generation yang masih non-final) atau Laravel Echo/broadcasting kalau mau lebih real-time. Polling sederhana cukup untuk scope assessment ini.
- Urutkan dari generation terbaru ke terlama, dengan pagination (Inertia pagination biasa) kalau datanya banyak.

## Review checklist
- [ ] User A tidak bisa melihat generation milik user B (dicek lewat test, bukan cuma asumsi).
- [ ] Status di halaman history berubah tanpa reload manual saat webhook masuk (dicoba manual: submit generation, tunggu webhook, lihat status update).
- [ ] Gambar yang sudah expired tidak merusak layout (ada fallback).
- [ ] Biaya ditampilkan dalam format mata uang yang mudah dibaca (dikonversi dari satuan internal integer ke tampilan dolar/sen).

## Di luar scope
- Admin melihat history semua user (task 13, stretch goal).
