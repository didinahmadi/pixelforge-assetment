# 15. (Stretch goal) Balances dan "Buy credits"

## Tujuan
User punya saldo credit (dalam USD). Generate image memotong saldo. Ada tombol beli credit palsu (tanpa pembayaran asli). User yang saldonya tidak cukup tidak bisa generate. Hanya dikerjakan **setelah** task 1-13 selesai dan benar.

## Scope
- Kolom `balance` di tabel `users` (satuan integer sama dengan satuan uang di task 4 — konsisten, jangan pakai float).
- Tabel `credit_transactions` (ledger) yang mencatat setiap perubahan saldo: top up (`type = purchase`), pemotongan saat generation sukses/gagal (`type = generation_charge`), dengan `generation_id` nullable untuk audit trail. Saldo user sebaiknya **dihitung** dari ledger ini (atau minimal disinkronkan dengan ledger), bukan cuma kolom `balance` yang diubah lepas tanpa jejak — supaya auditable.
- Tombol "Buy credits" di UI: form pilih nominal → langsung tambah saldo + catat row ledger `purchase`, tanpa payment gateway asli (sesuai instruksi "fake purchase").
- Saat submit generation (task 7): cek dulu apakah `estimated_cost` ≤ saldo user. Kalau tidak cukup, tolak submit dengan pesan jelas, jangan sampai memanggil Pixelforge dulu baru ketahuan saldo kurang.
- Saat generation selesai dan `actual_cost` berbeda dari `estimated_cost` (karena estimasi vs aktual): sesuaikan saldo dengan selisihnya (atau pendekatan lain yang didokumentasikan — misalnya hold saldo sebesar estimasi saat submit, lalu refund selisih saat tahu biaya aktual). Dokumentasikan pendekatan yang dipilih di PR description karena ini "detail yang sengaja dibuat terbuka" menurut JOBDESC.
- Test: submit dengan saldo cukup → sukses dan saldo berkurang; submit dengan saldo kurang → ditolak sebelum memanggil Pixelforge; generation gagal → saldo yang sempat di-hold dikembalikan penuh.

## Review checklist
- [ ] Saldo tidak pernah bisa negatif akibat race condition (dua submit bersamaan) — pertimbangkan row lock atau DB constraint `CHECK (balance >= 0)`.
- [ ] Ada ledger (`credit_transactions`) yang bisa dipakai untuk audit, saldo tidak hanya "angka yang diubah diam-diam".
- [ ] User dengan saldo tidak cukup tidak pernah berhasil memanggil Pixelforge (dicek urutan operasi: cek saldo dulu, baru call API).
- [ ] Generation yang gagal mengembalikan penuh saldo yang di-hold (kalau pendekatannya hold-then-settle).
- [ ] Pendekatan hold/refund vs potong-setelah-selesai didokumentasikan secara eksplisit (keputusan desain, bukan default diam-diam).

## Di luar scope
- Payment gateway asli (JOBDESC eksplisit minta fake purchase).
