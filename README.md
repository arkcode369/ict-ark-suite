# ICT/SMC Suite

> Modular Pine Script v6 indicators + strategies implementing ICT (Inner Circle Trader) / SMC (Smart Money Concepts) frameworks from 9 source concept files: CRT, Foundation Model, IPDA/MMXM, LIT, ICT Logic, MonsterLab STDV, Quarterly Theory, Malaysian SNR, dan Turtle Soup.

## Status — v0.4

✓ **Released.** Roadmap & changelog: [`docs/ROADMAP.md`](docs/ROADMAP.md).

### Indikator (12 modul + library)

| Modul | File | Cakupan |
|---|---|---|
| Library | `library/ictlib.pine` | helper bersama (swing, FVG, OB, opens, sessions, STDV) |
| 01 — PD Arrays | `indicators/01_pd_arrays.pine` | FVG / OB / Breaker / Mitigation |
| 02 — Liquidity & Inducement | `indicators/02_liquidity.pine` | BSL/SSL / Equal H/L / Sweep / IRL/ERL |
| 03 — Market Structure | `indicators/03_market_structure.pine` | Swings / BOS / MSS / CISD |
| 04 — Time & Sessions | `indicators/04_time_sessions.pine` | Killzones / Macros / SB / NWOG / CBDR / 1-5-9 |
| 05 — Quarterly Theory | `indicators/05_quarterly_theory.pine` | Q1-Q4 / True Opens / DFR / Magneto / Pi Ranges |
| 06 — CRT & PO3 | `indicators/06_crt_po3.pine` | CRT 3-candle / OHLC / Wednesday Model |
| 07 — Malaysian SNR | `indicators/07_msnr.pine` | Body S/R / Engulfing / QML / HNS / Trendline / Storyline |
| 08 — STDV Projections | `indicators/08_stdv_projections.pine` | Devil's Mark / MMBM / MMSM |
| 09 — Top-Down Dashboard | `indicators/09_dashboard.pine` | Real-time checklist tabel + input.source bind |
| 10 — sSMT | `indicators/10_ssmt.pine` | Sequential SMT multi-asset divergence |
| 11 — OTE | `indicators/11_ote.pine` | Optimal Trade Entry zone (62-79 % fib + 70.5 % sweet) |
| 12 — Liquidity Void | `indicators/12_liquidity_void.pine` | Vacuum Block + 3-bar Liquidity Void detector |

### Strategies (6 backtest variants)

| File | Setup |
|---|---|
| `strategies/01_pd_strategy.pine` | New FVG with HTF bias |
| `strategies/02_liquidity_sweep_strategy.pine` | Sweep + Reclaim (TP1/TP2 + BE) |
| `strategies/03_mss_strategy.pine` | Market Structure Shift continuation |
| `strategies/06_crt_strategy.pine` | CRT 3-candle reversal |
| `strategies/06_crt_model1_strategy.pine` | CRT Model #1 (HTF bias + manip + OTE) |
| `strategies/06_po3_strategy.pine` | Daily Power-of-3 AMD |
| `strategies/08_stdv_mean_reversion_strategy.pine` | Devil's Mark mean-reversion |
| `strategies/combo_strategy.pine` | Multi-confluence (7-factor) |

## Kenapa Modular, Bukan Satu Indikator Monolitik?

Pine Script di TradingView punya batas keras: maks 500 `box`, 500 `line`, 500 `label`, 64 plot, dan 40 `request.security`. Menggabungkan semua konsep dalam satu indikator akan langsung kena cap. Suite ini dipecah menjadi 1 library + 12 indikator + 1 dashboard sehingga pengguna bisa pilih kombinasi modul yang relevan untuk style trading mereka.

## Cara Memasang di TradingView

1. Buka chart di TradingView.
2. Klik **Pine Editor** di bawah.
3. Salin isi salah satu file `.pine` dari folder `indicators/` atau `strategies/` (atau `library/` untuk library).
4. Tempel ke editor → **Save** → **Add to chart**.
5. Konfigurasi inputs lewat ⚙ (gear icon) di chart.
6. Set up alerts via **Alert** menu, pilih kondisi yang sudah di-`alertcondition()` — webhook templates di [`docs/alerts/`](docs/alerts/README.md).

> Catatan: untuk library, save dulu sebagai library di TradingView (Pine Editor → Save → pilih "Save as Library"), lalu indikator bisa `import arkcode369/ictlib/<version>`.

## Inter-Module Communication (v0.2+)

Modul 09 Dashboard punya 4 slot `input.source` yang bisa di-bind ke hidden plots dari modul lain (01/02/03/11/12). Buka Modul 09 settings → tab "External Sources" → pilih `ICT-MS:trendDir`, `ICT-LIT:sweepFlag`, `ICT-PD:bullFvgFlag`, `ICT-PD:bearFvgFlag` dari dropdown. Sumber tersedia kalau modul terkait sudah di-add ke chart.

## Konsep Sumber

File ringkasan konsep ada di [`docs/concepts/`](docs/concepts/). Setiap file mencantumkan sumber asli (PDF / mentorship / webinar).

## Dokumentasi

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — design rationale + Pine v6 limits
- [`docs/ROADMAP.md`](docs/ROADMAP.md) — roadmap & changelog
- [`docs/alerts/README.md`](docs/alerts/README.md) — alert webhook templates (Discord / Telegram / generic JSON)
- [`docs/WALK_FORWARD.md`](docs/WALK_FORWARD.md) — walk-forward & out-of-sample validation guide
- [`strategies/README.md`](strategies/README.md) — strategy selection guide & tuning tips

## Lisensi

[MIT](LICENSE).

## Disclaimer

Suite ini adalah **alat bantu visualisasi & analisis**, bukan rekomendasi trading. Setiap deteksi berlabel "kandidat" — keputusan akhir tetap pada trader. Tidak ada jaminan profitabilitas. Trading mengandung risiko.
