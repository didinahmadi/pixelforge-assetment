# 10. Polling fallback untuk status generation (opsional tapi disarankan)

## Tujuan
Kalau webhook gagal terkirim (misalnya tunnel local putus, atau webhook hilang), generation jangan nyangkut selamanya di status `queued`/`processing`.

## Scope
- Scheduled job (Laravel schedule, jalan tiap beberapa menit) yang mengambil semua `generations` dengan status `queued`/`processing` yang sudah lebih tua dari N menit, lalu panggil `PixelforgeClient::getGeneration($id)` untuk cek status terbaru.
- Proses hasil polling lewat **fungsi yang sama** dengan pemroses webhook (task 9) — jangan duplikasi logic state machine/idempotensi di tempat lain. Idealnya polling cukup membentuk "event palsu" dari hasil polling dan memanggil service yang sama.
- Batasi job ini agar tidak memanggil API Pixelforge tanpa henti untuk generation yang sama (misal maksimal cek tiap 1 menit per generation, stop setelah beberapa kali gagal terus dan tandai sebagai `failed` dengan alasan "timeout menunggu provider").

## Review checklist
- [ ] Polling tidak menduplikasi logic update status — memanggil fungsi yang sama dengan webhook handler.
- [ ] Ada batas waktu/jumlah percobaan sehingga generation tidak bisa nyangkut selamanya di status non-final.
- [ ] Job tidak membebani rate limit Pixelforge (ada jeda antar polling yang wajar).

## Di luar scope
- Perubahan pada webhook handler itu sendiri (task 8, 9).
