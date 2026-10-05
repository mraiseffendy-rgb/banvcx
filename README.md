# Pendeteksi WhatsApp — SinTax Edition

Web tool ringan untuk **scan screenshot verifikasi WhatsApp** dan mendeteksi apakah nomor target dalam kondisi **aman** atau **banned 12 jam**. Semua proses jalan di browser (client-side), nggak butuh backend, nggak ada tracking.

![Version](https://img.shields.io/badge/version-3.2-e91e3c)
![License](https://img.shields.io/badge/license-MIT-00d2ff)
![No Backend](https://img.shields.io/badge/backend-none-00e676)

---

## Fitur

- **Scan gambar verifikasi WA** — upload screenshot, engine langsung analisis
- **10 metode deteksi** berjalan berurutan dengan output terminal real-time
- **OCR beneran** (Tesseract.js) — baca nomor telepon langsung dari pixel gambar
- **Heuristik warna & layout** — deteksi popup "terlalu banyak menebak kode" via analisis piksel
- **Popup terminal comic-style** — animasi shake, skull, progress bar merah bergaris
- **Auto-detect nomor** — 5 pola regex bertingkat (fallback ke nama file kalau OCR gagal)
- **Result card dinamis** — merah kalau banned, hijau kalau aman
- **100% client-side** — nggak ada data yang dikirim ke server manapun
- **Single file HTML** — tinggal buka, nggak butuh npm, nggak butuh build step

---

## Demo

| Kondisi | Output |
|---------|--------|
| Nomor aman | Header hijau + nomor tampil + snark santai |
| Nomor banned | Header merah + nomor tampil + snark toxic + estimasi 12 jam |

---

## Cara Pakai

1. **Save file** HTML-nya sebagai `index.html`
2. **Buka** di browser (Chrome, Safari, Firefox, Edge — mobile atau desktop)
3. **Ketuk drop zone** → pilih screenshot verifikasi WhatsApp
4. **Tekan tombol ENTER** → popup terminal muncul
5. **Tunggu** sampai 10 metode selesai (biasanya 5-15 detik, tergantung gambar & koneksi)
6. **Lihat hasil** di dalam popup — tekan **KEMBALI** untuk reset

---

## 10 Metode Deteksi

| # | Nama | Fungsi |
|---|------|--------|
| 1 | PREPROCESS | Load, resize, normalize gambar |
| 2 | OCR INIT | Inisialisasi Tesseract.js worker |
| 3 | TEXT EXTRACT | Ekstrak teks dari area crop 10%-70% |
| 4 | NUMBER PARSE | Match pola nomor telepon |
| 5 | E.164 VALIDATE | Validasi format internasional |
| 6 | COLOR ANALYSIS | Hitung rasio merah/hijau/gelap |
| 7 | POPUP DETECT | Deteksi overlay dialog/popup |
| 8 | UI PATTERN MATCH | Match layout WhatsApp |
| 9 | HEURISTIC SCORE | Skor heuristik + klasifikasi |
| 10 | FINAL VERDICT | Agregasi semua signal → verdict |

---

## Bagaimana Deteksi Bekerja

### 1. Heuristik Gambar
Gambar di-load ke `<canvas>`, di-resize ke max 320px, lalu dianalisis:

```js
redPixels   // pixel dominan merah (popup warning)
greenPixels // pixel dominan hijau (tombol/CTA)
darkPixels  // pixel gelap (background popup)
midBright   // kecerahan area tengah (10-15% dari tinggi)
