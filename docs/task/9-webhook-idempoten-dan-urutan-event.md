# 9. Webhook: proses event idempoten dan tahan urutan acak

## Tujuan
Event webhook yang terkirim dua kali tidak boleh tercatat dua kali. Event yang datang tidak berurutan (misalnya `failed` sempat terkirim lebih dulu lalu `succeeded` menyusul, atau sebaliknya) tidak boleh membuat status generation jadi salah secara permanen.

## Scope
- Setiap event webhook punya ID unik dari Pixelforge (cek nama field di docs, misalnya `event_id`). Simpan di tabel `webhook_events` dengan constraint unique pada ID itu.
- Sebelum memproses isi event: cek dulu apakah `event_id` ini sudah pernah diproses (sudah ada row dengan ID itu dan status `processed`). Kalau sudah, langsung return 200 tanpa mengubah apa pun lagi (idempotent no-op).
- Gunakan DB transaction + lock row (`lockForUpdate` pada row `generations` terkait) saat update status, supaya dua webhook untuk generation yang sama yang datang hampir bersamaan tidak saling menimpa secara acak (race condition).
- Tentukan aturan urutan status yang valid (state machine), misal:
  - `pending/queued/processing` → `succeeded` atau `failed` : boleh.
  - `succeeded` atau `failed` → status lain: **tolak**, log sebagai anomali, jangan diproses (generation yang sudah final tidak boleh berubah lagi).
  - Kalau terima `processing` setelah sudah `succeeded` (event lama yang datang terlambat): no-op, karena status final sudah benar.
- Begitu event membawa status `succeeded`: ambil token usage dari payload, hitung `actual_cost` lewat `PricingCalculator::actualCost(...)` (task 5), simpan ke row generation. Begitu `failed`: set `actual_cost = 0`.
- Test yang wajib ada:
  - Event sama dikirim 2x → `actual_cost` tetap tercatat sekali, tidak dobel, row `webhook_events` tidak dobel.
  - Event `succeeded` lalu event `failed` lama yang baru sampai belakangan untuk generation yang sama → status akhir tetap `succeeded` (tidak ditimpa event basi).
  - Dua event untuk generation berbeda diproses konkuren tidak saling mengganggu.

## Review checklist
- [ ] Ada unique constraint di DB (bukan cuma cek aplikasi) untuk `event_id`, karena race condition bisa membuat cek-lalu-insert di level aplikasi tidak aman sendirian.
- [ ] Update status generation dibungkus transaction + row lock.
- [ ] Ada state machine eksplisit (bukan sekadar overwrite kolom status apa adanya) yang menolak transisi dari status final ke status lain.
- [ ] Test duplikat event dan test out-of-order event keduanya ada dan hijau.
- [ ] `actual_cost` hanya dihitung sekali per generation, tidak bertambah kalau event sukses terkirim berkali-kali.

## Di luar scope
- UI yang menampilkan status real-time ke user (task 10).
- Polling fallback kalau webhook tidak pernah datang (task 9b, opsional).
