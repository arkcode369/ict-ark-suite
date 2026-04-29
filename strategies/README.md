# Strategies

This folder contains `strategy()` variants of selected ICT/SMC concepts so you can backtest them in TradingView's Strategy Tester.

## Files

| File | Concept | Entry | SL | TP |
|---|---|---|---|---|
| `01_pd_strategy.pine` | New FVG with HTF bias | Bull/Bear FVG formation | FVG opposite edge − ATR buf | entry ± tpATR × ATR |
| `06_crt_strategy.pine` | CRT 3-candle reversal | Bullish/Bearish CRT pattern | C2 extreme − ATR buf | C2 extreme + tpMult × C2 range |

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
  - Multi-confluence scoring (e.g. FVG + OB + Killzone)
  - Liquidity sweep filter
  - Premium/Discount entry zoning
  - Breakeven moves / partial profit
- Treat these as **starting templates** — wire your own filters in.
- Backtest on the timeframe you actually trade. M5/M15 results don't transfer to D1.
- Beware survivorship bias: tune on one period, test out-of-sample on another.

## Future strategies (v0.3+)

- `02_liquidity_sweep_strategy.pine` — sweep + reclaim entries
- `03_mss_strategy.pine` — MSS continuation/reversal entries
- `08_stdv_mean_reversion_strategy.pine` — Devil's Mark mean-reversion
- `combo_strategy.pine` — multi-confluence (PD + LIT + MSS)
