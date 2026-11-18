# 7. Submit generation request ke Pixelforge

## Tujuan
Menghubungkan form (task 6) ke Pixelforge lewat client (task 3), menyimpan baris `generations` (task 4) dengan status awal dan estimasi biaya (task 5).

## Scope
- Controller `GenerationController@store`:
  1. Validasi request (prompt, size, quality, reference URLs) di `FormRequest` terpisah.
  2. Hitung `estimated_cost` lewat `PricingCalculator::estimate(...)`.
  3. Simpan row `generations` dengan status `pending`, `estimated_cost` terisi, `pixelforge_generation_id` masih null.
  4. Panggil `PixelforgeClient::createGeneration(...)`.
  5. Kalau sukses: update row dengan `pixelforge_generation_id` dan status `queued`/`processing` (sesuai respons awal API).
  6. Kalau gagal (exception dari task 3): tandai row sebagai `failed` dengan `failure_reason` berisi pesan error yang dipahami user (bukan stack trace), `actual_cost = 0`.
- Redirect/response Inertia balik ke halaman history atau halaman detail generation yang baru dibuat.
- Pastikan urutan "simpan row dulu, baru call API" (bukan sebaliknya) supaya kalau proses PHP mati di tengah, tidak ada panggilan API yang tidak tercatat di DB.
- Test feature: submit sukses → row tersimpan status `queued`; submit yang API-nya gagal (pakai keyword `[fail]` dari docs Pixelforge atau `Http::fake()`) → row `failed`, cost 0.

## Review checklist
- [ ] Row DB dibuat sebelum memanggil Pixelforge (urutan operasi benar).
- [ ] Response error dari Pixelforge tidak membuat user melihat stack trace mentah.
- [ ] Ada test untuk jalur sukses dan jalur gagal saat submit.
- [ ] `estimated_cost` yang tersimpan match dengan yang ditampilkan di form sebelum submit (tidak dihitung ulang dengan rumus berbeda).

## Di luar scope
- Update status dari `queued`/`processing` ke `succeeded`/`failed` final — itu tugas webhook (task 8) atau polling fallback (task 8b).
