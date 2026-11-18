# 3. HTTP client untuk Pixelforge API

## Tujuan
Satu class/service terpusat untuk bicara ke Pixelforge, dipakai oleh semua task lain (generate, polling, usage report). Supaya auth header, base URL, error handling tidak ditulis ulang di banyak tempat.

## Scope
- Buat `App\Services\Pixelforge\PixelforgeClient` (atau nama serupa) yang membungkus Laravel `Http` facade.
- Method minimal:
  - `createGeneration(array $payload): array` — submit generation request.
  - `getGeneration(string $id): array` — polling status (kalau docs menyediakan endpoint ini; dipakai sebagai fallback kalau webhook belum sampai).
  - `getUsage(array $params): array` — untuk `GET /v1/usage` (dipakai task admin report & task 11).
- Baca base URL dan API key dari config (`config/services.php`), bukan hardcode.
- Tangani response error (4xx/5xx) jadi exception yang jelas (`PixelforgeException` misalnya), termasuk kasus rate limit (biasanya 429) dan overload (5xx) — dibedakan supaya task 5 bisa retry secara tepat.
- Unit test dengan `Http::fake()` untuk tiap method: sukses, error 4xx, error 5xx, rate limit.

## Review checklist
- [ ] Tidak ada API key/base URL hardcoded di luar config.
- [ ] Ada exception class yang membedakan minimal: client error, rate limited, server error/overload.
- [ ] Test `Http::fake()` untuk jalur sukses dan jalur error dari masing-masing method.
- [ ] Logging: request yang gagal dicatat (ID generation kalau ada, status code, body error) tanpa membocorkan API key di log.

## Di luar scope
- Logika retry/backoff (task 5).
- Pricing/estimasi biaya (task 4).
