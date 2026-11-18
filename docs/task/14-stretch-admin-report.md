# 14. (Stretch goal) Admin report

## Tujuan
Halaman khusus operator (bukan user biasa) yang menunjukkan credit spend, provider cost, dan profit, dikelompokkan per hari dan per user. Hanya dikerjakan **setelah** semua task 1-13 selesai dan benar.

## Scope
- Role/flag admin sederhana (kolom `is_admin` di tabel users, atau tabel roles kalau mau lebih rapi — untuk scope ini boolean cukup).
- Middleware yang membatasi route report hanya untuk admin.
- Query agregat dari tabel `generations` yang sudah ada (bukan tabel baru):
  - Per hari + per user: total `actual_cost` yang dibayar user (credit spend), total cost asli Pixelforge (butuh data cost asli dari Pixelforge — lihat catatan di bawah), dan profit = credit spend − provider cost.
- Provider cost (cost asli Pixelforge sebelum markup 25%) harus **sama persis** dengan yang dilaporkan `GET /v1/usage` milik Pixelforge untuk key yang dipakai — ini akan dicek langsung oleh reviewer. Artinya: saat menyimpan `actual_cost` di task 9, simpan juga provider cost mentah (sebelum markup) di kolom terpisah (tambahkan migration kecil kalau task 4 belum menyediakannya), supaya report ini tidak perlu menghitung ulang dari `token_usage` dengan rounding yang mungkin berbeda dari `GET /v1/usage`.
- Page Vue `Admin/Report.vue` menampilkan tabel per hari/per user dengan filter rentang tanggal.

## Review checklist
- [ ] Hanya admin yang bisa akses halaman ini (dites: user biasa kena 403/redirect).
- [ ] Angka provider cost di report sama dengan `GET /v1/usage` Pixelforge untuk periode yang sama (dicek manual, dicatat hasilnya).
- [ ] Profit = credit spend − provider cost, dihitung dari data yang tersimpan, bukan dihitung ulang dengan rumus pricing yang mungkin berbeda dari yang dipakai saat generation dibuat.
- [ ] Grouping per hari dan per user berfungsi dan totalnya konsisten (total semua user = total keseluruhan hari itu).

## Di luar scope
- Balance/credit purchase (task 15 — stretch goal terpisah).
