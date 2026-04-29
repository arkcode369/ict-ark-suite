# ICT/SMC Suite

> Modular Pine Script v6 indicators implementing ICT (Inner Circle Trader) / SMC (Smart Money Concepts) frameworks from 9 source concept files: CRT, Foundation Model, IPDA/MMXM, LIT, ICT Logic, MonsterLab STDV, Quarterly Theory, Malaysian SNR, and Turtle Soup.

## Status

🚧 **Work in progress.** Roadmap ada di [`docs/ROADMAP.md`](docs/ROADMAP.md).

| Modul | File | Status |
|---|---|---|
| Library | `library/ictlib.pine` | ⏳ in progress |
| 01 — PD Arrays | `indicators/01_pd_arrays.pine` | ⏳ in progress |
| 02 — Liquidity & Inducement (LIT) | `indicators/02_liquidity.pine` | ⏳ planned |
| 03 — Market Structure (BOS / MSS / CISD) | `indicators/03_market_structure.pine` | ⏳ planned |
| 04 — Time & Sessions (Killzones / Macros / NWOG / CBDR) | `indicators/04_time_sessions.pine` | ⏳ planned |
| 05 — Quarterly Theory (True Opens / DFR / sSMT / Doubling) | `indicators/05_quarterly_theory.pine` | ⏳ planned |
| 06 — CRT & PO3 (3-candle / OHLC-OLHC / Wednesday Model) | `indicators/06_crt_po3.pine` | ⏳ planned |
| 07 — Malaysian SNR (body-based S/R / Engulfing / QML) | `indicators/07_msnr.pine` | ⏳ planned |
| 08 — STDV Projections (CRT / MMXM / MonsterLab / Foundation) | `indicators/08_stdv_projections.pine` | ⏳ planned |
| 09 — Top-Down Dashboard | `indicators/09_dashboard.pine` | ⏳ planned |

## Kenapa Modular, Bukan Satu Indikator Monolitik?

Pine Script di TradingView punya batas keras: maks 500 `box`, 500 `line`, 500 `label`, 64 plot, dan 40 `request.security`. Menggabungkan semua konsep dalam satu indikator akan langsung kena cap. Suite ini dipecah menjadi 1 library + 8 indikator + 1 dashboard sehingga pengguna bisa pilih kombinasi modul yang relevan untuk style trading mereka.

## Cara Memasang di TradingView

1. Buka chart di TradingView.
2. Klik **Pine Editor** di bawah.
3. Salin isi salah satu file `.pine` dari folder `indicators/` (atau `library/` untuk library).
4. Tempel ke editor → **Save** → **Add to chart**.
5. Konfigurasi inputs lewat ⚙ (gear icon) di chart.
6. Set up alerts via **Alert** menu, pilih kondisi yang sudah di-`alertcondition()`.

> Catatan: untuk library, save dulu sebagai library di TradingView (Pine Editor → Save → pilih "Save as Library"), lalu indikator bisa `import arkcode369/ictlib/<version>`.

## Konsep Sumber

File ringkasan konsep ada di [`docs/concepts/`](docs/concepts/). Setiap file mencantumkan sumber asli (PDF / mentorship / webinar).

## Roadmap

Lihat [`docs/ROADMAP.md`](docs/ROADMAP.md).

## Lisensi

[MIT](LICENSE).

## Disclaimer

Suite ini adalah **alat bantu visualisasi & analisis**, bukan rekomendasi trading. Setiap deteksi berlabel "kandidat" — keputusan akhir tetap pada trader. Tidak ada jaminan profitabilitas. Trading mengandung risiko.
