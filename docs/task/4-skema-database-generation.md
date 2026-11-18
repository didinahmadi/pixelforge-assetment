# 4. Skema database untuk generation

## Tujuan
Satu tabel (atau lebih) yang merekam setiap generation image: input user, status, estimasi biaya, biaya aktual, dan metadata dari Pixelforge. Ini fondasi untuk "correct money" — kriteria penilaian paling berat.

## Scope
- Migration tabel `generations` dengan kolom kira-kira:
  - `id`, `user_id` (FK).
  - `pixelforge_generation_id` (string, unique, nullable sampai request terkirim) — kunci untuk mencocokkan webhook.
  - `prompt`, `size`, `quality`, `reference_image_urls` (json/nullable).
  - `status` enum: `pending`, `queued`, `processing`, `succeeded`, `failed`.
  - `estimated_cost` (integer, simpan dalam micro-cents atau sejenisnya — **jangan pakai float** untuk uang).
  - `actual_cost` (integer, nullable, satuan sama dengan estimated_cost; diisi setelah webhook `succeeded`/`failed`).
  - `provider_token_usage` (json, nullable) — simpan mentah token usage dari Pixelforge supaya bisa dihitung ulang/diaudit.
  - `failure_reason` (string, nullable).
  - `image_url` (string, nullable).
  - timestamps.
- Tentukan satuan uang yang konsisten di seluruh app (misal integer micro-dollar, 1 USD = 1_000_000 unit) supaya pecahan sen dari Pixelforge tidak hilang karena rounding. Dokumentasikan keputusan ini di komentar migration atau `docs/pixelforge-notes.md`.
- Buat Eloquent model `Generation` dengan cast yang sesuai (enum/cast untuk status, cast integer untuk cost, cast array untuk json).
- Buat index pada `pixelforge_generation_id` dan `user_id`.

## Review checklist
- [ ] Tidak ada kolom uang bertipe float/double.
- [ ] Ada dokumentasi satuan uang yang dipakai (micro-dollar/micro-cent/dst) dan dipakai konsisten.
- [ ] `pixelforge_generation_id` unique dan di-index.
- [ ] Status di-cast ke enum PHP (`App\Enums\GenerationStatus`) bukan string bebas.
- [ ] Migration bisa jalan dari nol di SQLite tanpa error.

## Di luar scope
- Logika mengisi kolom-kolom ini (task 5, 7, 8).
- Tabel balance/credit user (task stretch goal "Balances").
