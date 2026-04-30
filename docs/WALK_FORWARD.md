# Walk-Forward & Out-of-Sample Validation Guide

> Strategy yang lulus backtest belum tentu profitable real-time. Pakai walk-forward + Monte Carlo untuk validasi.

## 1. Kenapa walk-forward?

In-sample backtest bisa **overfit**: kamu tune parameter sampai equity curve cantik, tapi market regime berubah dan strategy gagal di forward live.

**Walk-forward** = bagi data ke segmen, tune di awal, validasi di segmen berikutnya. Menghasilkan estimasi performa lebih realistis.

## 2. Walk-forward sederhana di TradingView

TradingView **tidak punya** built-in walk-forward optimizer. Tapi kamu bisa lakukan manual:

### A. Anchored walk-forward

1. Pilih strategy (mis. `combo_strategy.pine`).
2. Set **Backtest start = 2020-01-01**, **end = 2022-12-31** (3-year in-sample).
3. Tune parameters untuk maksimal **Profit Factor** (PF), bukan Net Profit.
4. Lock parameters.
5. Set **Backtest start = 2023-01-01**, **end = 2024-12-31** (2-year out-of-sample).
6. Compare: kalau PF in-sample 1.8 dan out-of-sample 1.5 → strategy robust. Kalau OOS PF < 1.0 → overfit.

### B. Rolling walk-forward (lebih ketat)

| Window | Train (in-sample) | Test (out-of-sample) |
|---|---|---|
| 1 | 2020-01 → 2020-12 | 2021-01 → 2021-06 |
| 2 | 2020-07 → 2021-06 | 2021-07 → 2021-12 |
| 3 | 2021-01 → 2021-12 | 2022-01 → 2022-06 |
| 4 | 2021-07 → 2022-06 | 2022-07 → 2022-12 |
| ... | ... | ... |

Catat OOS net profit + PF + DD per window. **Konsisten?** Lulus. **Random / negatif?** Reject.

## 3. Metrik wajib

- **Profit Factor (PF)**: gross profit / gross loss. Target ≥ 1.5 (≥ 2.0 untuk strategi pendek HTF).
- **Win Rate (WR)**: % trades yang profit. Tidak penting kalau average win >> average loss.
- **Max Drawdown (DD)**: peak-to-trough equity decline. Target < 20 %.
- **Sharpe / Sortino**: risk-adjusted return. TradingView tidak menampilkan langsung; export trades ke spreadsheet.
- **Avg trade duration**: harus konsisten dengan TF kamu. Strategy M5 dengan avg duration 8 jam = setup salah.
- **Trades count**: minimal 100 trades untuk hasil signifikan secara statistik. < 30 trades = noise.

## 4. Monte Carlo simulation

Setelah backtest, ekspor list trades (TradingView Strategy Tester → Performance Summary → klik tab **List of Trades** → copy ke Excel/Sheets/Python).

Kemudian:

1. Acak urutan trades 1000 kali.
2. Untuk tiap simulasi, hitung equity curve + max DD.
3. Distribusi DD: kalau real-DD ada di percentile 5 % terendah → kemungkinan strategy lucky di urutan tertentu. Kalau di tengah → robust.

Python sketch:

```python
import pandas as pd
import numpy as np

trades = pd.read_csv("trades.csv")["NetProfit"].values  # in $
n_sim = 1000
max_dds = []
for _ in range(n_sim):
    np.random.shuffle(trades)
    equity = np.cumsum(trades)
    peak = np.maximum.accumulate(equity)
    dd = peak - equity
    max_dds.append(dd.max())
print("real DD:", trades_real_dd)
print("MC 5%-95% DD range:", np.percentile(max_dds, [5, 95]))
```

## 5. Kapan reject strategy?

- ✗ OOS PF < 1.0
- ✗ OOS DD > 2× in-sample DD
- ✗ Avg win / avg loss < 1 dengan WR < 50 %
- ✗ Top 5 trades > 50 % total profit (luck-driven)
- ✗ DD > total profit (1:1 risk:reward terlalu lemah)
- ✗ Performa berbeda drastis di volatil vs tenang regimes

## 6. Kapan approve strategy?

- ✓ OOS PF ≥ 1.3 (consistent with in-sample)
- ✓ Robust di multiple TF / multiple instruments (forex triad: EU/GU/UJ)
- ✓ Trades count ≥ 100 untuk statistik
- ✓ DD < 20 % maximum
- ✓ Performa konsisten across regimes (trending + ranging)
- ✓ Tidak sensitif terhadap perubahan parameter ±20 %

## 7. Spesifik untuk ICT/SMC strategies

- **PD-FVG** dan **MSS**: tune di pair likuid (EURUSD, GBPUSD). Hindari pair eksotis dengan slippage tinggi.
- **PO3 / CRT Model #1**: butuh data dengan volatil session (NY/London open). Test di indices futures (ES/NQ) atau forex major.
- **STDV mean-reversion**: skip kalau market trending kuat (ATR rolling > 1.5× rata-rata) — strategy designed untuk range.
- **Combo**: tune `confluenceMin` per regime. Tinggi (5-6) di range, rendah (3-4) di trending.

## 8. Tools tambahan

- **TradingView Deep Backtesting** (Premium): up to 20 ribu bars history.
- **Pine Screener / Pine Profiler** (TradingView built-in): identify slow scripts before live.
- **External**: PineConnector, TradingConnector — auto-trade dari TV alerts ke MT4/MT5/broker.
- **Python**: `vectorbt`, `backtrader`, `backtesting.py` untuk validasi cross-platform.

## Lihat juga

- `strategies/README.md` — strategy selection guide
- `docs/alerts/README.md` — webhook templates
- `docs/ARCHITECTURE.md` — limitasi TradingView Pine
