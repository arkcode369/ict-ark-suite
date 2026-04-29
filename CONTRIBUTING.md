# Contributing to ICT/SMC Suite

Terima kasih sudah tertarik berkontribusi. Suite ini adalah kumpulan indikator Pine Script v6 yang mengimplementasikan konsep ICT/SMC dari sumber-sumber yang dirangkum di `docs/concepts/`.

## Prinsip

1. **Modular**: setiap indikator fokus pada satu domain (PD Arrays, Liquidity, Market Structure, dst). Helper bersama hidup di `library/ictlib.pine`.
2. **Pine Script v6** wajib. Tanpa exception.
3. **Tanpa repaint diam-diam**: kalau sebuah deteksi memang post-hoc (mis. swing baru valid setelah N bar), tulis itu eksplisit di komentar header.
4. **Hormati limit TradingView**: max 500 box/line/label per script. Pakai pruning FIFO.
5. **Kandidat, bukan sinyal final**: setiap label deteksi ditandai sebagai kandidat. Konsep ICT punya komponen judgment yang tidak bisa di-otomatisasi 100%.

## Coding Style

- Indent: 4 spasi.
- Header tiap script: `//@version=6` lalu komentar berisi nama, deskripsi, sumber konsep.
- Penamaan input: gunakan group + tooltip yang menjelaskan parameter.
- Konstanta MAGIC strings → `var const`.
- Hindari `request.security` untuk HTF data lebih dari 4 panggilan per indikator (limit TV = 40 lookback ditambah cap 4000 bar).

## Workflow

1. Fork / branch dari `main`: `git checkout -b devin/$(date +%s)-modul-XX-name`.
2. Tulis kode. Pastikan compile di TradingView Pine Editor.
3. Jalankan verifikasi syntax: `make lint` (jika tersedia).
4. Buat PR ke `main` dengan deskripsi mengikuti `.github/PULL_REQUEST_TEMPLATE.md`.
5. Sertakan minimal 1 screenshot hasil chart untuk modul yang divisualisasikan.

## Pertanyaan Konsep

Kalau implementasi suatu konsep ambigu, cek dulu file ringkasan di `docs/concepts/` (atau buka source PDF yang tertulis di header tiap file). Kalau masih ambigu, buka issue dan tag dengan `discussion`.
