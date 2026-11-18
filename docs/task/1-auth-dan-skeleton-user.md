# 1. Auth dan skeleton user

## Tujuan
User bisa daftar/login sebelum bisa generate image. Semua data generation harus terikat ke user yang login.

## Scope
- Pasang Laravel Breeze (varian Vue + Inertia) atau bikin auth manual minimal: register, login, logout.
- Pastikan route generate/history nanti dilindungi middleware `auth`.
- Tidak perlu email verification, password reset, dsb kecuali mau — cukup yang minimum supaya tiap request punya `user_id`.

## Review checklist
- [ ] Bisa register user baru.
- [ ] Bisa login/logout.
- [ ] Route yang butuh login redirect ke login kalau belum auth.
- [ ] Tabel `users` default Laravel dipakai apa adanya (tidak perlu kolom tambahan di task ini).

## Di luar scope
- Balance/credit user (lihat task stretch goal "Balances").
- Role admin (lihat task admin report).
