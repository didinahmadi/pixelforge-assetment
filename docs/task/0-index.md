# Index task — breakdown dari JOBDESC.md

Urutan disusun berdasarkan dependency, bukan prioritas. Kerjakan 1-13 dulu (bagian wajib) sampai semua checklist-nya hijau sebelum menyentuh 14/15 (stretch goals) — sesuai instruksi JOBDESC: "Only once everything above works."

| # | Task | Kategori |
|---|------|----------|
| [1](1-auth-dan-skeleton-user.md) | Auth dan skeleton user | Wajib |
| [2](2-setup-akun-dan-api-key-pixelforge.md) | Setup akun dan API key Pixelforge | Wajib |
| [3](3-http-client-pixelforge.md) | HTTP client untuk Pixelforge | Wajib |
| [4](4-skema-database-generation.md) | Skema database generation | Wajib |
| [5](5-logika-pricing-dan-estimasi.md) | Logika pricing dan estimasi biaya | Wajib |
| [6](6-halaman-generate-image.md) | Halaman generate image | Wajib |
| [7](7-submit-generation-ke-pixelforge.md) | Submit generation ke Pixelforge | Wajib |
| [8](8-webhook-terima-dan-verifikasi-signature.md) | Webhook: terima dan verifikasi signature | Wajib |
| [9](9-webhook-idempoten-dan-urutan-event.md) | Webhook: idempoten dan tahan urutan acak | Wajib |
| [10](10-polling-fallback-status.md) | Polling fallback status | Disarankan |
| [11](11-halaman-history.md) | Halaman history | Wajib |
| [12](12-robustness-rate-limit-dan-retry.md) | Robustness: rate limit dan retry | Wajib |
| [13](13-deploy-dan-key-environment-deployed.md) | Deploy + key environment deployed | Wajib |
| [14](14-stretch-admin-report.md) | Stretch: Admin report | Bonus |
| [15](15-stretch-balances-dan-buy-credits.md) | Stretch: Balances & Buy credits | Bonus |

## Catatan umum
- Satuan uang: pakai integer (bukan float) di seluruh app, dan tentukan satuannya sekali di task 4, lalu pakai konsisten di semua task lain.
- Task 2 wajib dikerjakan lebih dulu karena isi API docs Pixelforge menentukan detail implementasi banyak task lain (format webhook, cara hitung pricing, dll) yang belum bisa dipastikan dari JOBDESC.md saja.
- Task 8 dan 9 sengaja dipisah dari task 7 karena itu bagian paling riskan untuk "correct money" dan "robustness" — layak direview sebagai unit kecil tersendiri.
