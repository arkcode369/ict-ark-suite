# Architecture — ICT/SMC Suite

## Overview

Suite ini terdiri dari:

```
ict-ark-suite/
├── library/
│   └── ictlib.pine              # Pine Library v6 — exported helpers
├── indicators/
│   ├── 01_pd_arrays.pine        # PD Arrays (FVG/OB/Breaker/Mitigation/...)
│   ├── 02_liquidity.pine        # Liquidity + Inducement (LIT)
│   ├── 03_market_structure.pine # Structure (BOS/MSS/CISD)
│   ├── 04_time_sessions.pine    # Time & Sessions
│   ├── 05_quarterly_theory.pine # Quarterly Theory
│   ├── 06_crt_po3.pine          # CRT & PO3
│   ├── 07_msnr.pine             # Malaysian SNR
│   ├── 08_stdv_projections.pine # Standard Deviations
│   └── 09_dashboard.pine        # Top-down dashboard (panel terpisah)
├── docs/
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   └── concepts/                # 9 file konsep sumber
├── examples/                    # contoh setup chart per modul
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

## Pine Library (`ictlib.pine`)

Pine Library adalah unit reusable code yang bisa di-`import` oleh indikator lain. Karena library tidak bisa menggambar (`box`/`line`/`label` butuh konteks indikator), library kita fokus pada **kalkulasi**.

### Exported API (rencana)

```pinescript
//@version=6
library("ictlib", overlay=true)

// === Swing detection ===
export isSwingHigh(int lookback=3) =>
    // returns true jika bar saat ini = swing high N-bar fractal
    ...

export isSwingLow(int lookback=3) =>
    ...

// === FVG ===
// Returns: [bool detected, float top, float bottom]
export bullFVG() =>
    [low > high[2], low, high[2]]

export bearFVG() =>
    [high < low[2], low[2], high]

// === Order Block ===
// Returns: [bool detected, float top, float bottom, int barIdx]
export bullOB(int displacementLookback=5) =>
    ...

export bearOB(int displacementLookback=5) =>
    ...

// === True Opens ===
// Returns float price
export trueDayOpen() =>          // 00:00 NY
    ...

export trueWeekOpen() =>          // Monday 18:00 NY (~ Q2 weekly)
    ...

export trueMonthOpen() =>         // 2nd Monday of month
    ...

export trueYearOpen() =>          // First Monday of April
    ...

export trueSessionOpen(int sessionId) =>
    // 0=Asia 19:30, 1=London 01:30, 2=NY AM 07:30, 3=NY PM 13:30
    ...

// === Sessions ===
// Returns true if current bar is within session window (NY time)
export inAsia() => ...
export inLondon() => ...
export inNYAM() => ...
export inNYPM() => ...
export inSilverBullet(int sessionId) => ...
export inMacro(int macroId) => ...

// === STDV ===
// Returns array<float> of -4, -2.5, -2, -1, 0, +1, +2, +2.5, +4 levels from a range
export stdvLevels(float rangeHigh, float rangeLow, array<float> deviations) =>
    ...

// === Premium / Discount ===
export equilibrium(float h, float l) => math.avg(h, l)
export inPremium(float price, float h, float l) => price > equilibrium(h, l)
export inDiscount(float price, float h, float l) => price < equilibrium(h, l)

// === Misc ===
export atr(int len=14) => ta.atr(len)
```

## Konvensi Indikator

Setiap indikator punya struktur:

```pinescript
//@version=6
//
// ICT/SMC Suite | <Modul Name>
// Source: <konsep yang diimplementasikan>
// Repaint disclosure: <statement>
//
indicator("ICT/SMC Suite | <Modul Name>", "<short>", overlay=true, max_boxes_count=500, max_lines_count=500, max_labels_count=500)

import arkcode369/ictlib/1 as ict

// === GROUPS for inputs ===
G_DISP   = "Display"
G_DET    = "Detection"
G_FILT   = "Filter"
G_ALERT  = "Alerts"

// === INPUTS ===
showXxx     = input.bool(true,  "Show Xxx", group=G_DISP)
colXxxBull  = input.color(color.new(color.green, 80), "Xxx Bull color", group=G_DISP)
...

// === STATE ===
var array<box> boxes = array.new<box>()
...

// === DETECTION ===
[detected, top, bot] = ict.bullFVG()
if detected and showXxx
    b = box.new(bar_index-2, top, bar_index, bot, bgcolor=colXxxBull, border_color=na)
    array.push(boxes, b)
    if array.size(boxes) > maxItems
        b0 = array.shift(boxes)
        box.delete(b0)

// === ALERTS ===
alertcondition(detected, "Bull FVG formed", "Bull FVG @ {{ticker}} {{close}}")
```

## Konvensi Inputs UI

Setiap modul mengikuti groups:

- **Display** — toggle show/hide, warna, transparansi, style box/line/label.
- **Detection** — parameter algoritma (lookback, ATR threshold, mitigation rule).
- **Filter** — by session, by HTF bias, by Premium/Discount.
- **Alerts** — toggle per event.

## Konvensi Time

- Default timezone: `"America/New_York"` (EST/EDT).
- True Opens & Killzones dihitung pada timezone ini.
- User bisa override via `input.string("America/New_York", "Timezone", options=...)`.

## Repaint Policy

| Fitur | Repaints? | Catatan |
|---|---|---|
| FVG | No | Detected on bar close (current FVG = `[2]`-back). |
| OB | Yes (in lookback window) | Last opposing candle redefined when displacement extends. |
| Breaker | No after MSS confirmed | MSS event freezes the breaker. |
| Mitigation | No | Detected real-time when price re-enters zone. |
| Swing H/L | Yes (until N+1 bars after) | Inherent to fractal detection. |
| MSS | No after confirm bar closes | |
| CISD | No | Detected on bar close. |
| Liquidity Sweep | No | Detected on wick close. |

Setiap modul akan menyatakan kebijakan repaint ini di header.

## Performance

- `var` untuk semua array/box/line state (jangan rebuild tiap bar).
- Pruning FIFO untuk mencegah hit limit 500 objek.
- `request.security()` minimum: hanya HTF data yang strict perlu (mis. Daily candle untuk True Day Open).
- Hindari loop dalam `for` di area logic per-bar; precompute saat data baru.
