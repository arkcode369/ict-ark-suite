# Roadmap — ICT/SMC Suite

> Pemetaan terperinci konsep → modul → fitur. Status terkini April 2026.

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
| `10_ssmt.pine` | Sequential SMT — multi-asset divergence (chart vs ES/NQ/YM atau forex triad) |

## Tier Rilis

### v0.1 — Core 9 modul (RILIS)
- [x] Repo scaffold + library skeleton + concept docs
- [x] `library/ictlib.pine` — full helper set
- [x] `01_pd_arrays.pine` — FVG, OB, Breaker, Mitigation
- [x] `02_liquidity.pine` — Equal H/L, BSL/SSL, Liquidity Sweep, Inducement (LIT)
- [x] `03_market_structure.pine` — Swing, BOS, MSS, CISD
- [x] `04_time_sessions.pine` — Killzones, Macros, Silver Bullet, NWOG/NDOG, CBDR, CRT 1/5/9
- [x] `05_quarterly_theory.pine` — True Opens stack, Q1-Q4 markers
- [x] `06_crt_po3.pine` — CRT 3-candle, OHLC/OLHC, Wednesday Model
- [x] `07_msnr.pine` — body-based S/R + Engulfing + QML
- [x] `08_stdv_projections.pine` — STDV dari range terpilih, Devil's Mark, MMBM/MMSM
- [x] `09_dashboard.pine` — top-down narrative dashboard

### v0.2 — Advanced features (RILIS)
- [x] DFR (Defining Range) projection levels — di Modul 05
- [x] Magneto / Doubling Theory — di Modul 05
- [x] Pi Range projection (HTF Q1) — di Modul 05
- [x] sSMT (Sequential SMT) multi-asset — Modul 10 baru
- [x] HNS (Head & Shoulders) full pattern matcher — di Modul 07
- [x] Multi-trendline auto-fit (body-based) — di Modul 07
- [x] Storyline + Roadblocks visual — di Modul 07
- [x] Inter-module communication via hidden plots + `input.source` — Modul 01/02/03/09
- [x] Backtest hooks (`strategy()` variants) — `strategies/01_pd_strategy.pine`, `strategies/06_crt_strategy.pine`

### v0.3 — Strategy library expansion (RILIS)
- [x] `strategies/02_liquidity_sweep_strategy.pine` — sweep + reclaim entries
- [x] `strategies/03_mss_strategy.pine` — MSS continuation/reversal entries
- [x] `strategies/08_stdv_mean_reversion_strategy.pine` — Devil's Mark mean-reversion
- [x] `strategies/combo_strategy.pine` — multi-confluence (PD + LIT + MSS + KZ + P/D)

### v0.4 — Models, OTE, Voids & Docs (RILIS)
- [x] CRT Model #1 entry detector + dedicated strategy — `strategies/06_crt_model1_strategy.pine`
- [x] PO3 AMD model strategy variant — `strategies/06_po3_strategy.pine`
- [x] OTE (Optimal Trade Entry) zone confluence overlay — `indicators/11_ote.pine`
- [x] Liquidity-vacuum / void detector — `indicators/12_liquidity_void.pine`
- [x] Alert webhook JSON templates — `docs/alerts/README.md`
- [x] Walk-forward / Monte Carlo guide — `docs/WALK_FORWARD.md`

### v0.5+ — Future iterations
- [ ] Multi-symbol portfolio backtest harness (Python wrapper)
- [ ] Trade journal CSV export (script-based, via labels)
- [ ] Auto-trade integration helper (PineConnector / TradingConnector recipes)
- [ ] Order Flow / volume-profile overlay (if TradingView Premium volume data available)
- [ ] Composite "ICT Score" oscillator (sub-pane)
- [ ] Telegram bot scaffolding (Python service that consumes webhooks → human-readable digest)

## Catatan Implementasi

- **Pine v6 wajib**, semua file mulai dengan `//@version=6`.
- **Limit boxes/lines/labels**: setiap modul punya input `Max items to display` (default 50, hard cap 100). FIFO trim.
- **HTF security**: max 4 panggilan `request.security` per modul (dari cap 40, sisakan margin untuk modul lain).
- **Repaint disclosure**: setiap fitur post-hoc (mis. swing, MSS) ditandai dengan komentar header.
- **Timezone**: semua waktu sesi dan True Opens menggunakan `America/New_York` (EST/EDT) — sesuai standar ICT.
