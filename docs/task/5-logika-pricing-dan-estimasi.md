# 5. Logika pricing dan estimasi biaya

## Tujuan
Menghitung estimasi biaya sebelum submit, dan biaya aktual setelah generation selesai, konsisten dengan rate resmi Pixelforge + markup 25%.

## Scope
- Buat `App\Services\Pricing\PricingCalculator` (nama bebas) yang:
  - `estimate(string $size, string $quality, int $referenceImageCount): int` — hitung estimasi biaya (dalam satuan uang dari task 4) berdasarkan rate yang didokumentasikan Pixelforge untuk kombinasi size/quality. Estimasi dipakai karena sebagian token count baru diketahui setelah generation selesai (baca docs Pixelforge bagian pricing untuk tahu bagian mana yang sudah diketahui di awal).
  - `actualCost(array $tokenUsage): int` — hitung biaya aktual dari token usage final yang dikirim Pixelforge (via webhook atau polling), dikali rate resmi, ditambah markup 25%.
- Markup 25% diterapkan di satu tempat saja (jangan diulang di beberapa file) supaya mudah diaudit.
- Generation yang berstatus `failed` → `actual_cost = 0`, terlepas dari token usage yang mungkin sudah terpakai di sisi Pixelforge.
- Unit test dengan beberapa kombinasi size/quality dan beberapa token usage, termasuk kasus pecahan sen agar tidak ada rounding error yang bias (misalnya selalu round down merugikan user atau sebaliknya — tentukan aturan pembulatan dan konsisten, dites).

## Review checklist
- [ ] Markup 25% hanya ada di satu fungsi/konstanta, tidak diulang.
- [ ] Ada aturan pembulatan yang jelas dan dites (bukan default float rounding yang implisit).
- [ ] Generation gagal selalu menghasilkan `actual_cost = 0` — ada test untuk ini.
- [ ] Rate yang dipakai match dengan yang didokumentasikan Pixelforge (dicek manual terhadap docs, tidak dikira-kira).
- [ ] Test estimate vs actualCost dengan payload token usage nyata dari Pixelforge (ambil contoh dari API docs atau hasil generation asli).

## Di luar scope
- Memanggil API Pixelforge (task 3).
- Menyimpan hasil ke database (task 7, 8).
