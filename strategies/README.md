# Strategies

This folder contains `strategy()` variants of selected ICT/SMC concepts so you can backtest them in TradingView's Strategy Tester.

## Files

| File | Concept | Entry | SL | TP |
|---|---|---|---|---|
| `01_pd_strategy.pine` | New FVG with HTF bias | Bull/Bear FVG formation | FVG opposite edge − ATR buf | entry ± tpATR × ATR |
| `02_liquidity_sweep_strategy.pine` | Sweep + Reclaim | Wick beyond N-bar extreme + close back | sweep extreme ± slBuf×ATR | TP1/TP2 R:R-based, optional BE |
| `03_mss_strategy.pine` | Market Structure Shift | Close break of opposite-trend swing | recent protective swing ± ATR | TP1/TP2 R:R-based |
| `06_crt_strategy.pine` | CRT 3-candle reversal | Bullish/Bearish CRT pattern | C2 extreme − ATR buf | C2 extreme + tpMult × C2 range |
| `08_stdv_mean_reversion_strategy.pine` | Devil's Mark mean-reversion | Close beyond ±revLevel × HD from mid | ±slLevel × HD beyond entry | mid (Devil's Mark) |
| `combo_strategy.pine` | Multi-confluence (PD + LIT + MSS + KZ + P/D) | Score ≥ confluenceMin (1-7) | Recent swing ± ATR | TP1/TP2 R:R-based, optional BE |

## How to use

1. Paste a `.pine` file into TradingView's Pine Editor.
2. **Click "Save", then "Add to chart"** — it appears as a strategy.
3. Open the **Strategy Tester** tab at the bottom of the chart.
4. Adjust inputs (date range, filters, risk multipliers) and observe equity curve / drawdown / win rate.
5. Repeat with different symbols / timeframes to find regimes where the setup works.

## Defaults

- **Position sizing**: 1% of equity per trade
- **Commission**: 0.04% per trade (~4 pips on EURUSD; adjust for your broker)
- **Initial capital**: $10,000
- **Pyramiding**: 0 (one position at a time)
- **No look-ahead**: `process_orders_on_close = false` so orders fill at next bar open (realistic)

## Important caveats

- Strategy variants are **simplified** vs the indicator versions. They do NOT include:
  - Multi-confluence scoring (combo strategy partially does this)
  - Premium/Discount entry zoning (combo strategy partially does this)
- Treat these as **starting templates** — wire your own filters in.
- Backtest on the timeframe you actually trade. M5/M15 results don't transfer to D1.
- Beware survivorship bias: tune on one period, test out-of-sample on another.

## Strategy selection guide

- **Trending market** → `01_pd_strategy`, `03_mss_strategy`, `combo_strategy`
- **Range / mean-reverting** → `08_stdv_mean_reversion_strategy`
- **Stop-runs / liquidity grabs** → `02_liquidity_sweep_strategy`
- **Daily/HTF reversal** → `06_crt_strategy`
- **Curated multi-confluence (highest selectivity)** → `combo_strategy` with confluenceMin = 5+

## Tuning tips

- Start with **default risk parameters** (1 % equity, 1R/2R targets), then tune.
- Use **walk-forward**: tune on first 70 % of data, validate on last 30 %.
- Watch **profit factor (PF)**, **max drawdown**, **win rate**, and **avg trade duration** — not just net profit.
- A PF < 1.3 with WR < 50 % usually means edge is fragile.

## Out of scope (future)

- ML-based confluence weighting
- Multi-symbol portfolio backtest
- Trade journal export (CSV via labels — TV-side)
