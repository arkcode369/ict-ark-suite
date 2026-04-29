# Roadmap — ICT/SMC Suite

> Pemetaan terperinci konsep → modul → fitur. Status awal April 2026.

## Pemetaan Konsep ke Modul

| Modul | Konsep yang dicover (file di `docs/concepts/`) |
|---|---|
| `library/ictlib.pine` | helper bersama (swing, FVG calc, OB calc, true open, STDV, session, ATR) |
| `01_pd_arrays.pine` | FVG, IFVG, Implied FVG, OB, Breaker, Mitigation, Propulsion, Volume Imbalance, BPR, Vacuum Block, Rejection Block (`concept-logic.md`, `concept-turtle-soup.md`) |
| `02_liquidity.pine` | BSL/SSL, Equal H/L, Liquidity Pools, Sweep/Run, IRL/ERL, HRL/LRL, Inducement (Major/Medium/Minor), False BOS, LIT Cycle, IOF (`concept-logic.md`, `concept-lit.md`, `concept-monsterlab.md`) |
| `03_market_structure.pine` | Swing H/L, BOS, MSS, CISD, Advanced Market Structure, Protected Swings (TTrades), Storyline (`concept-logic.md`, `concept-foundation-model.md`, `concept-snr.md`, `concept-turtle-soup.md`) |
| `04_time_sessions.pine` | Killzones (Asia/London/NY AM/PM), ICT Macros, Silver Bullet, 1-5-9 AM Cycle, NWOG/NDOG, CBDR, Hours of Operation, Economic Calendar markers (`concept-logic.md`, `concept-crt.md`, `concept-foundation-model.md`) |
| `05_quarterly_theory.pine` | Q1-Q4 AMD/XAMD/AMDX, True Opens (Year/Month/Week/Day/Session/Micro), DFR, sSMT, NWOG, PSP, PC, Magneto/Doubling, Cycle Limits, Pi Ranges (`concept-quarterly-theory.md`, `concept-foundation-model.md`, `concept-ipda-mmxm.md`) |
| `06_crt_po3.pine` | CRT 3-candle (Range/Manipulation/Distribution), OHLC/OLHC, PO3 (Monthly/Weekly/Daily/4H), Wednesday Model, KOD, Model #1, OB/KOD entries, Time Divisions (`concept-crt.md`, `concept-monsterlab.md`, `concept-turtle-soup.md`, `concept-ipda-mmxm.md`) |
| `07_msnr.pine` | Malaysian SNR (body-based: Classical/Breakout/GAP), Fresh/Unfresh, Touch & Miss, Engulfing, QML/HNS, Trendline, Storyline, Roadblocks (`concept-snr.md`) |
| `08_stdv_projections.pine` | Standard Deviations dari range (CBDR/Asia/London/Daily/Q1), Devil's Mark, MMBM/MMSM curve, ABC/123 (`concept-monsterlab.md`, `concept-ipda-mmxm.md`, `concept-crt.md`, `concept-foundation-model.md`, `concept-quarterly-theory.md`) |
| `09_dashboard.pine` | Top-down narrative & checklist real-time (HTF bias, DOL, Phase IPDA, Setup score) — orkestrator semua modul |

## Tier Rilis

### MVP v0.1 — Tier 1 (Core)
Fokus: building blocks + structure + waktu. Cukup untuk standalone analysis.
- [x] Repo scaffold + library skeleton + concept docs
- [ ] `library/ictlib.pine` — full helper set
- [ ] `01_pd_arrays.pine` — FVG, OB, Breaker, Mitigation
- [ ] `02_liquidity.pine` — Equal H/L, BSL/SSL, Liquidity Sweep, Inducement (LIT) Major/Medium/Minor
- [ ] `03_market_structure.pine` — Swing, BOS, MSS, CISD
- [ ] `04_time_sessions.pine` — Killzones, Macros, Silver Bullet, NWOG/NDOG, CBDR

### v0.2 — Tier 2 (HTF & Quarterly)
- [ ] `05_quarterly_theory.pine` — True Opens stack, Q1-Q4 markers, DFR, STDV ±1/±2/±2.5
- [ ] `06_crt_po3.pine` — CRT 3-candle highlighter, PO3 AMD overlay, OHLC/OLHC labels
- [ ] `08_stdv_projections.pine` — STDV dari range terpilih, -4 sampai +4

### v0.3 — Tier 3 (Style & Dashboard)
- [ ] `07_msnr.pine` — body-based S/R + Engulfing
- [ ] `09_dashboard.pine` — top-down narrative dashboard

### v0.4+ — Iterations
- [ ] sSMT detector multi-asset (request.security ke 2 instrument lain dari Triad)
- [ ] Doubling Theory / Magneto detection
- [ ] Pi Range projection (Q1 → Q3 prediction)
- [ ] Backtester wrapper (PineScript strategy version)
- [ ] Alert templates per setup model

## Catatan Implementasi

- **Pine v6 wajib**, semua file mulai dengan `//@version=6`.
- **Limit boxes/lines/labels**: setiap modul punya input `Max items to display` (default 50, hard cap 100). FIFO trim.
- **HTF security**: max 4 panggilan `request.security` per modul (dari cap 40, sisakan margin untuk modul lain).
- **Repaint disclosure**: setiap fitur post-hoc (mis. swing, MSS) ditandai dengan komentar header.
- **Timezone**: semua waktu sesi dan True Opens menggunakan `America/New_York` (EST/EDT) — sesuai standar ICT.
