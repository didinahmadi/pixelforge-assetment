# 6. Halaman generate image (frontend + estimasi harga live)

## Tujuan
User bisa menulis prompt, pilih size/quality, isi reference image URL opsional, dan melihat estimasi biaya **sebelum** submit.

## Scope
- Page Vue (Inertia) `Generate.vue`: form dengan field prompt, size, quality, reference image URLs (bisa lebih dari satu, opsional).
- Endpoint kecil (misalnya `POST /generations/estimate` atau dihitung di frontend kalau rate-nya sederhana dan tidak sensitif) yang mengembalikan estimasi biaya memakai `PricingCalculator` dari task 5, dipanggil tiap kali user mengubah size/quality (debounce, tidak perlu realtime per keystroke prompt).
- Tampilkan estimasi biaya dengan jelas di dekat tombol submit, termasuk keterangan singkat "estimasi, biaya final bisa berbeda".
- Validasi input dasar: prompt tidak boleh kosong, size/quality harus salah satu opsi valid, reference image URL harus format URL valid.
- Tombol submit men-disable diri sendiri saat request sedang dikirim (hindari double submit).

## Review checklist
- [ ] Estimasi biaya muncul dan berubah sesuai pilihan size/quality sebelum submit.
- [ ] Ada disclaimer bahwa angka itu estimasi.
- [ ] Validasi form mencegah submit kosong/invalid tanpa harus roundtrip ke server dulu (client-side validation dasar), tapi server tetap validasi ulang (jangan percaya client).
- [ ] Tidak bisa submit dua kali dengan sekali klik (race double submit).

## Di luar scope
- Submit generation ke Pixelforge (task 7).
- Halaman history (task 9).
