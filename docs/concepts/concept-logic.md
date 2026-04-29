i# Fair Value Gap (FVG) Logic

## Definisi

FVG adalah gap harga yang terbentuk di antara 3 candle berurutan, di mana candle tengah tidak overlap dengan candle pertama dan ketiga.

---

## Formasi

```
Index:   [2]       [1]       [0]
          |    gap candle     |
         high               low   ← Bullish FVG: low[0] > high[2]

         low               high   ← Bearish FVG: high[0] < low[2]
```

---

## Kondisi Terbentuk

### Bullish FVG
```
low[0] > high[2]
```
- Gap terbentuk di atas: antara `high[2]` dan `low[0]`
- Candle[1] adalah gap candle (body tidak menutup gap)

### Bearish FVG
```
high[0] < low[2]
```
- Gap terbentuk di bawah: antara `low[2]` dan `high[0]`
- Candle[1] adalah gap candle

---

## Zona FVG

| FVG Type    | Top      | Bottom   |
|-------------|----------|----------|
| **Bullish** | `low[0]` | `high[2]` |
| **Bearish** | `low[2]` | `high[0]` |

---

## Implementasi Pine Script

```pinescript
// Bullish FVG
bool bull_fvg = low[0] > high[2]
float bull_top = low[0]
float bull_bot = high[2]
float bull_mid = math.avg(bull_top, bull_bot)

// Bearish FVG
bool bear_fvg = high[0] < low[2]
float bear_top = low[2]
float bear_bot = high[0]
float bear_mid = math.avg(bear_top, bear_bot)
```

---

## Status Mitigasi

FVG dikatakan **mitigated** ketika harga kembali masuk ke dalam zona.

| FVG Type    | Partial Fill (entry)       | Full Fill (invalidasi)     |
|-------------|----------------------------|----------------------------|
| **Bullish** | harga turun ke `bull_top`  | harga turun ke `bull_bot`  |
| **Bearish** | harga naik ke `bear_bot`   | harga naik ke `bear_top`   |

```pinescript
// Contoh cek mitigasi Bullish FVG
bool bull_mitigated = low <= bull_top

// Contoh cek mitigasi Bearish FVG
bool bear_mitigated = high >= bear_bot
```

---

## Variasi Umum

- **IFVG (Inverse FVG)**: FVG yang sudah terisi penuh dan berbalik fungsi sebagai resistance/support.
- **Consequent Encroachment (CE)**: Titik tengah zona FVG (`mid`), sering digunakan sebagai target entry atau target TP.
- **HTF FVG**: FVG yang dihitung dari timeframe lebih tinggi (misalnya D1, W1) lalu diplot di timeframe rendah.
- **Equilibrium FVG**: FVG yang berada dekat dengan 50% range dari swing high-low.

---

## Catatan Tambahan

- FVG hanya valid jika terbentuk **sesuai arah bias** (bullish FVG di uptrend, bearish FVG di downtrend).
- Semakin besar gap, semakin signifikan FVG tersebut.
- Pada ICT, FVG sering disebut juga sebagai **Imbalance** atau **Price Inefficiency**.

---

## Definisi Lanjutan (dari ICT 2022 Mentorship)

**Fair Value Gap** = range dalam Price Delivery di mana satu sisi Market Liquidity ditawarkan, dan biasanya dikonfirmasi oleh **Liquidity Void** pada Lower Time Frame chart di range harga yang sama.

> FVGs adalah entry point, target, dan sarana mengukur pergeseran sentimen ketika Price berpindah dari buy program ke sell program (atau sebaliknya).

**Liquidity Void** = range harga di mana satu sisi market liquidity ditunjukkan oleh candle besar satu arah (one-sided range). Terjadi ketika market bergerak agresif menjauhi konsolidasi, menciptakan void buy-side liquidity. Dengan probabilitas tinggi, harga akan kembali untuk menutup void tersebut.

**Displacement** = lokasi dalam harga di mana seseorang dengan banyak modal masuk ke market dengan conviction kuat untuk menggerakkan harga naik atau turun sangat cepat. Displacement ditandai oleh pergerakan harga yang kuat dan cepat yang meninggalkan Fair Value Gaps.

```
Displacement = candle besar energetik yang:
  - Menembus previous high atau low
  - Ditandai oleh close jauh dari open (body dominan)
  - Meninggalkan FVG di belakangnya

Cara identifikasi:
  - Bullish displacement: aggressive candles close above a low → LOD dibentuk, potensi long
  - Bearish displacement: aggressive candles close below a high → HOD dibentuk, potensi short
  - Lack of displacement = market tidak "committed" → sinyal long di bullish trend
```

**FVG tidak valid (Balanced Price Action)** = ketika high dari candle pertama menyentuh/melewati low dari candle ketiga, atau sebaliknya → harga dianggap sudah seimbang (balanced), bukan FVG.

```
Invalid (Bearish FVG):
  high[2] ≥ low[0]  → price balanced, bukan FVG
```

**Bearish FVG Optimal**:
- Ditemukan setelah run ke BSL (Buyside Liquidity)
- Biasanya berada tepat di atas single price high atau multiple price highs (double top / relative equal highs)
- FVG terbentuk di dalam **Displacement Range** (antara Displacement High dan Displacement Low)

---

---

# Order Block (OB) Logic

## Definisi

Order Block adalah **candle terakhir yang berlawanan arah** sebelum terjadi impulsive move yang menembus struktur pasar (BOS/CHoCH).

---

## Formasi

```
Bullish OB:
  [Bearish candle] → [impulsive move UP] → BOS
        ↑ OB

Bearish OB:
  [Bullish candle] → [impulsive move DOWN] → BOS
        ↑ OB
```

---

## Kondisi Terbentuk

### Bullish OB
```
close[1] < open[1]   // candle sebelumnya bearish
close > open          // candle saat ini bullish (impulsif)
impulsive move menembus swing high sebelumnya
```

### Bearish OB
```
close[1] > open[1]   // candle sebelumnya bullish
close < open          // candle saat ini bearish (impulsif)
impulsive move menembus swing low sebelumnya
```

---

## Zona OB

| OB Type | Top | Bottom |
|---|---|---|
| **Bullish OB** (full body) | `high[1]` | `low[1]` |
| **Bullish OB** (wick-trim) | `open[1]` | `low[1]` |
| **Bearish OB** (full body) | `high[1]` | `low[1]` |
| **Bearish OB** (wick-trim) | `high[1]` | `open[1]` |

> Versi wick-trim lebih konservatif — hanya menggunakan body candle sebagai zona.

---

## Implementasi Pine Script

```pinescript
bool is_impulsive = (high - low) > ta.atr(14) * 1.5

// Bullish OB
bool bull_ob = close[1] < open[1] and close > open and is_impulsive
float bull_ob_top = open[1]    // wick-trim; gunakan high[1] untuk full body
float bull_ob_bot = low[1]
float bull_ob_mid = math.avg(bull_ob_top, bull_ob_bot)

// Bearish OB
bool bear_ob = close[1] > open[1] and close < open and is_impulsive
float bear_ob_top = high[1]
float bear_ob_bot = open[1]    // wick-trim; gunakan low[1] untuk full body
float bear_ob_mid = math.avg(bear_ob_top, bear_ob_bot)
```

---

## Mitigasi OB

```pinescript
// Harga masuk zona Bullish OB
bool bull_ob_mitigated = low <= bull_ob_top

// Harga masuk zona Bearish OB
bool bear_ob_mitigated = high >= bear_ob_bot
```

---

---

# Breaker Block Logic

## Definisi

Breaker Block adalah **OB yang sudah ditembus dan berbalik fungsi**. OB yang gagal menahan harga dan kemudian digunakan sebagai zona baru dengan polaritas terbalik.

---

## Formasi

```
Bullish Breaker (dari Bearish OB yang ditembus ke atas):
  [Bearish OB terbentuk] → harga turun → harga berbalik naik → menembus OB ke atas
  → Bearish OB berubah menjadi Bullish Breaker

Bearish Breaker (dari Bullish OB yang ditembus ke bawah):
  [Bullish OB terbentuk] → harga naik → harga berbalik turun → menembus OB ke bawah
  → Bullish OB berubah menjadi Bearish Breaker
```

---

## Kondisi Terbentuk

### Bullish Breaker
```
1. Bearish OB terbentuk
2. Harga bergerak di bawah OB (mitigasi)
3. Harga berbalik dan menutup DI ATAS zona Bearish OB
→ OB berubah menjadi support (Bullish Breaker)
```

### Bearish Breaker
```
1. Bullish OB terbentuk
2. Harga bergerak di atas OB (mitigasi)
3. Harga berbalik dan menutup DI BAWAH zona Bullish OB
→ OB berubah menjadi resistance (Bearish Breaker)
```

---

## Implementasi Pine Script

```pinescript
// Bullish Breaker: Bearish OB yang ditembus ke atas
bool bull_breaker = bear_ob_exists and close > bear_ob_top

// Bearish Breaker: Bullish OB yang ditembus ke bawah
bool bear_breaker = bull_ob_exists and close < bull_ob_bot

// Zona Breaker mewarisi zona OB asalnya
// Bullish Breaker zone = bear_ob_top / bear_ob_bot
// Bearish Breaker zone = bull_ob_top / bull_ob_bot
```

---

---

# Mitigation Block Logic

## Definisi

Mitigation Block adalah **candle yang menyebabkan harga gagal melanjutkan arah sebelumnya**, terbentuk ketika harga berbalik setelah menciptakan swing yang tidak efisien.

---

## Formasi

```
Bullish Mitigation Block:
  Harga turun → terbentuk swing low → harga naik → gagal break swing high
  → harga turun kembali melewati swing low sebelumnya
  → Candle swing low tersebut = Bullish Mitigation Block

Bearish Mitigation Block:
  Harga naik → terbentuk swing high → harga turun → gagal break swing low
  → harga naik kembali melewati swing high sebelumnya
  → Candle swing high tersebut = Bearish Mitigation Block
```

---

## Kondisi Terbentuk

### Bullish Mitigation Block
```
1. Terbentuk swing low (LL)
2. Harga retracement ke atas tapi gagal membuat HH
3. Harga turun menembus swing low (LL) sebelumnya
→ Candle di swing low = Bullish Mitigation Block
```

### Bearish Mitigation Block
```
1. Terbentuk swing high (HH)
2. Harga retracement ke bawah tapi gagal membuat LL
3. Harga naik menembus swing high (HH) sebelumnya
→ Candle di swing high = Bearish Mitigation Block
```

---

## Implementasi Pine Script

```pinescript
// Swing detection sederhana
bool is_swing_low  = low[2] < low[3] and low[2] < low[1]
bool is_swing_high = high[2] > high[3] and high[2] > high[1]

// Bullish Mitigation Block: swing low yang ditembus ke bawah ulang
bool bull_mit_block = is_swing_low[n] and close < low[n]   // n = bar swing low

// Bearish Mitigation Block: swing high yang ditembus ke atas ulang
bool bear_mit_block = is_swing_high[n] and close > high[n] // n = bar swing high

// Zona sama seperti OB: high dan low dari candle swing tersebut
```

---

---

# Propulsion Block Logic

## Definisi

Propulsion Block adalah **kombinasi OB + FVG dalam satu area harga yang berdekatan**, menciptakan zona konfluens tinggi yang sering digunakan sebagai area entry.

---

## Formasi

```
Bullish Propulsion Block:
  [Bullish OB] langsung diikuti atau overlap dengan [Bullish FVG]
  → Zona OB dan FVG membentuk satu area konfluens

Bearish Propulsion Block:
  [Bearish OB] langsung diikuti atau overlap dengan [Bearish FVG]
  → Zona OB dan FVG membentuk satu area konfluens
```

---

## Kondisi Terbentuk

```
1. OB terbentuk pada candle tertentu
2. FVG terbentuk pada candle yang sama atau candle berikutnya
3. Zona OB dan FVG saling overlap atau berdekatan
→ Area overlap = Propulsion Block
```

---

## Implementasi Pine Script

```pinescript
// Bullish Propulsion Block: OB dan FVG overlap
bool bull_propulsion = bull_ob and bull_fvg
float prop_top = math.min(bull_ob_top, bull_fvg_top)   // irisan zona teratas
float prop_bot = math.max(bull_ob_bot, bull_fvg_bot)   // irisan zona terbawah
bool valid_propulsion = prop_top > prop_bot             // ada overlap nyata

// Bearish Propulsion Block
bool bear_propulsion = bear_ob and bear_fvg
float bear_prop_top = math.min(bear_ob_top, bear_fvg_top)
float bear_prop_bot = math.max(bear_ob_bot, bear_fvg_bot)
bool valid_bear_propulsion = bear_prop_top > bear_prop_bot
```

---

---

# Volume Imbalance Logic

## Definisi

Volume Imbalance adalah **gap antara `close` candle sebelumnya dan `open` candle saat ini**, berbeda dengan FVG yang membutuhkan 3 candle. Terjadi ketika pasar membuka dengan gap dari penutupan sebelumnya.

---

## Formasi

```
Index:   [1]           [0]
       close  →  gap  →  open
                  ↑
           Volume Imbalance
```

---

## Kondisi Terbentuk

### Bullish Volume Imbalance
```
open[0] > close[1]
```
→ Candle saat ini membuka **di atas** penutupan candle sebelumnya.

### Bearish Volume Imbalance
```
open[0] < close[1]
```
→ Candle saat ini membuka **di bawah** penutupan candle sebelumnya.

---

## Zona Volume Imbalance

| Type | Top | Bottom |
|---|---|---|
| **Bullish** | `open[0]` | `close[1]` |
| **Bearish** | `close[1]` | `open[0]` |

---

## Implementasi Pine Script

```pinescript
// Bullish Volume Imbalance
bool bull_vi = open > close[1]
float bull_vi_top = open
float bull_vi_bot = close[1]
float bull_vi_mid = math.avg(bull_vi_top, bull_vi_bot)

// Bearish Volume Imbalance
bool bear_vi = open < close[1]
float bear_vi_top = close[1]
float bear_vi_bot = open
float bear_vi_mid = math.avg(bear_vi_top, bear_vi_bot)
```

---

## Mitigasi Volume Imbalance

```pinescript
// Bullish VI terisi ketika harga turun ke dalam gap
bool bull_vi_mitigated = low <= bull_vi_top

// Bearish VI terisi ketika harga naik ke dalam gap
bool bear_vi_mitigated = high >= bear_vi_bot
```

---

## Perbedaan FVG vs Volume Imbalance

| Aspek | FVG | Volume Imbalance |
|---|---|---|
| **Candle dibutuhkan** | 3 candle | 2 candle |
| **Gap antara** | `high[2]` dan `low[0]` | `close[1]` dan `open[0]` |
| **Terjadi di** | Tengah pergerakan impulsif | Open candle baru (gap open) |
| **Signifikansi** | Lebih tinggi | Lebih rendah |

---

---

# Inverse Fair Value Gap (IFVG) Logic

## Definisi

IFVG adalah **FVG yang sudah terisi penuh** (harga melewati seluruh zona) dan kemudian **berbalik fungsi** — zona yang semula support menjadi resistance, atau sebaliknya.

---

## Formasi

```
Bullish FVG → harga turun menembus bull_bot → menjadi Bearish IFVG (resistance)

  [bull_top]  ← dulu resistance, kini support gagal
  [bull_bot]  ← ditembus ke bawah → zona kini jadi resistance

Bearish FVG → harga naik menembus bear_top → menjadi Bullish IFVG (support)

  [bear_top]  ← ditembus ke atas → zona kini jadi support
  [bear_bot]  ← dulu support, kini resistance gagal
```

---

## Kondisi Terbentuk

### Bearish IFVG (dari Bullish FVG yang terisi penuh)
```
1. Bullish FVG terbentuk: low[0] > high[2]
2. Harga kembali masuk ke zona (mitigasi)
3. Harga menutup DI BAWAH bull_bot (full fill)
→ Bullish FVG berubah menjadi Bearish IFVG
```

### Bullish IFVG (dari Bearish FVG yang terisi penuh)
```
1. Bearish FVG terbentuk: high[0] < low[2]
2. Harga kembali masuk ke zona (mitigasi)
3. Harga menutup DI ATAS bear_top (full fill)
→ Bearish FVG berubah menjadi Bullish IFVG
```

---

## Zona IFVG

Zona IFVG **mewarisi zona FVG asalnya** — top dan bottom tidak berubah, hanya fungsinya yang berbalik.

| IFVG Type | Top | Bottom | Fungsi Baru |
|---|---|---|---|
| **Bullish IFVG** (dari Bearish FVG) | `bear_top` | `bear_bot` | Support |
| **Bearish IFVG** (dari Bullish FVG) | `bull_top` | `bull_bot` | Resistance |

---

## Implementasi Pine Script

```pinescript
// Cek full fill Bullish FVG → jadi Bearish IFVG
bool bull_fvg_full_filled = bull_fvg_exists and close < bull_fvg_bot
// Setelah full fill, zona bull_fvg_top / bull_fvg_bot menjadi Bearish IFVG

// Cek full fill Bearish FVG → jadi Bullish IFVG
bool bear_fvg_full_filled = bear_fvg_exists and close > bear_fvg_top
// Setelah full fill, zona bear_fvg_top / bear_fvg_bot menjadi Bullish IFVG
```

---

---

# Implied Fair Value Gap Logic

## Definisi

Implied Fair Value Gap adalah gap yang **terimplikasi oleh struktur wick candle** — terbentuk dari selisih antara **ujung wick** candle dengan **body** candle berikutnya, bukan dari 3 candle konvensional.

---

## Formasi

```
Bullish Implied FVG:
  Candle[1]: body bullish dengan upper wick
  Candle[0]: open lebih rendah dari high[1] tapi masih di atas close[1]

  high[1]  ──┐
             │  ← gap antara high[1] wick dan open[0]  = Implied FVG
  open[0]  ──┘
  close[1]

Bearish Implied FVG:
  Candle[1]: body bearish dengan lower wick
  Candle[0]: open lebih tinggi dari low[1] tapi masih di bawah close[1]

  close[1]
  open[0]  ──┐
             │  ← gap antara open[0] dan low[1] wick = Implied FVG
  low[1]   ──┘
```

---

## Kondisi Terbentuk

### Bullish Implied FVG
```
open[0] > close[1] and open[0] < high[1]
→ Ada gap antara close[1] dan open[0], tapi masih dalam range wick candle[1]
```

### Bearish Implied FVG
```
open[0] < close[1] and open[0] > low[1]
→ Ada gap antara open[0] dan close[1], tapi masih dalam range wick candle[1]
```

---

## Zona Implied FVG

| Type | Top | Bottom |
|---|---|---|
| **Bullish** | `open[0]` | `close[1]` |
| **Bearish** | `close[1]` | `open[0]` |

---

## Implementasi Pine Script

```pinescript
// Bullish Implied FVG
bool bull_ifvg = open > close[1] and open < high[1]
float bull_ifvg_top = open
float bull_ifvg_bot = close[1]
float bull_ifvg_mid = math.avg(bull_ifvg_top, bull_ifvg_bot)

// Bearish Implied FVG
bool bear_ifvg = open < close[1] and open > low[1]
float bear_ifvg_top = close[1]
float bear_ifvg_bot = open
float bear_ifvg_mid = math.avg(bear_ifvg_top, bear_ifvg_bot)
```

---

## Perbedaan FVG vs Implied FVG

| Aspek | FVG | Implied FVG |
|---|---|---|
| **Candle dibutuhkan** | 3 candle | 2 candle |
| **Gap antara** | `high[2]` dan `low[0]` | `close[1]` dan `open[0]` (dalam wick) |
| **Validasi** | `low[0] > high[2]` | `open > close[1] and open < high[1]` |
| **Ukuran gap** | Bisa besar | Biasanya kecil |

---

---

# Balance Price Range (BPR) Logic

## Definisi

Balance Price Range adalah **area overlap antara Bullish FVG dan Bearish FVG** yang saling bertumpuk. Zona ini merepresentasikan keseimbangan antara buyer dan seller — area di mana institusi menempatkan order dari kedua sisi.

---

## Formasi

```
Bearish FVG:  [bear_bot ........... bear_top]
Bullish FVG:          [bull_bot ... bull_top]
                              ↑
                       BPR = irisan keduanya
```

---

## Kondisi Terbentuk

```
1. Bullish FVG terbentuk: zona [bull_bot, bull_top]
2. Bearish FVG terbentuk: zona [bear_bot, bear_top]
3. Kedua zona saling overlap

BPR valid jika:
  math.min(bull_top, bear_top) > math.max(bull_bot, bear_bot)
```

---

## Zona BPR

```pinescript
float bpr_top = math.min(bull_fvg_top, bear_fvg_top)
float bpr_bot = math.max(bull_fvg_bot, bear_fvg_bot)
bool  bpr_valid = bpr_top > bpr_bot
float bpr_mid = math.avg(bpr_top, bpr_bot)  // equilibrium / CE
```

---

## Implementasi Pine Script

```pinescript
// Asumsikan ada array/tracker untuk menyimpan zona FVG aktif
// BPR terbentuk ketika ada overlap antara bull FVG dan bear FVG

bool has_bull_fvg = bull_fvg_top > bull_fvg_bot  // zona bullish FVG aktif
bool has_bear_fvg = bear_fvg_top > bear_fvg_bot  // zona bearish FVG aktif

float bpr_top   = math.min(bull_fvg_top, bear_fvg_top)
float bpr_bot   = math.max(bull_fvg_bot, bear_fvg_bot)
bool  bpr_valid = has_bull_fvg and has_bear_fvg and bpr_top > bpr_bot
float bpr_mid   = math.avg(bpr_top, bpr_bot)
```

---

## Mitigasi BPR

```pinescript
// BPR dianggap mitigated ketika harga masuk ke zona
bool bpr_mitigated = high >= bpr_bot and low <= bpr_top

// BPR equilibrium (CE) tercapai
bool bpr_ce_hit = (high >= bpr_mid and low <= bpr_mid)
```

---

## Catatan Tambahan

- BPR sering digunakan sebagai **area reversal** kuat karena merupakan konfluens dua imbalance.
- Titik tengah BPR (`bpr_mid`) setara dengan **Consequent Encroachment (CE)** dan sering menjadi target harga.
- BPR yang terbentuk di area **50% range** (equilibrium market) lebih signifikan.
- Dalam konteks ICT, BPR sering terbentuk sebelum **displacement** besar ke satu arah.

---

---

# Premium / Discount Array Logic

## Definisi

Premium/Discount Array adalah framework yang **membagi range harga antara swing high dan swing low** menjadi zona premium (mahal) dan discount (murah) menggunakan **50% equilibrium** sebagai garis pembagi.

---

## Struktur Zona

```
Swing High  ─────────────  100%  (Premium Extreme)
            ─────────────   79%  (OTE Premium — optimal sell)
  PREMIUM   ─────────────   62%  (Fib 0.618 — sell area)
    ZONE
            ─────────────   50%  ← Equilibrium (CE / fair value)

    ZONE
  DISCOUNT  ─────────────   38%  (Fib 0.382 — buy area)
            ─────────────   21%  (OTE Discount — optimal buy)
Swing Low   ─────────────    0%  (Discount Extreme)
```

---

## Aturan Dasar

| Zona | Posisi vs Equilibrium | Bias |
|---|---|---|
| **Premium** | Di atas 50% | SELL / Short |
| **Equilibrium** | Tepat di 50% | Netral / transisi |
| **Discount** | Di bawah 50% | BUY / Long |

---

## Level Kunci (Fibonacci)

| Level | Fibonacci | Keterangan |
|---|---|---|
| 100% | — | Swing High (Premium Extreme) |
| 79% | 0.786 | OTE Premium — zona sell terbaik |
| 62% | 0.618 | Golden ratio — sell area |
| 50% | 0.500 | Equilibrium / CE |
| 38% | 0.382 | Buy area |
| 21% | 0.214 | OTE Discount — zona buy terbaik |
| 0% | — | Swing Low (Discount Extreme) |

> **OTE (Optimal Trade Entry)** adalah zona antara 62%–79% untuk sell, dan 21%–38% untuk buy.

---

## Implementasi Pine Script

```pinescript
// Tentukan range berdasarkan swing high dan swing low
int   length      = 50
float swing_high  = ta.highest(high, length)
float swing_low   = ta.lowest(low, length)
float range       = swing_high - swing_low

// Level utama
float equilibrium = swing_low + range * 0.50
float fib_62      = swing_low + range * 0.62   // sell area
float fib_79      = swing_low + range * 0.79   // OTE premium
float fib_38      = swing_low + range * 0.38   // buy area
float fib_21      = swing_low + range * 0.21   // OTE discount

// Klasifikasi posisi harga saat ini
bool in_premium   = close > equilibrium
bool in_discount  = close < equilibrium
bool in_ote_sell  = close >= fib_62 and close <= fib_79
bool in_ote_buy   = close >= fib_21 and close <= fib_38
```

---

## Cara Pakai dalam Trading

```
BUY Setup:
  1. Harga berada di zona DISCOUNT (< 50%)
  2. Ada Bullish FVG / Bullish OB di zona discount
  3. Lebih optimal jika FVG/OB berada di OTE Discount (21%–38%)

SELL Setup:
  1. Harga berada di zona PREMIUM (> 50%)
  2. Ada Bearish FVG / Bearish OB di zona premium
  3. Lebih optimal jika FVG/OB berada di OTE Premium (62%–79%)
```

---

## Konfluens dengan Array Lain

| Konfluens | Signifikansi |
|---|---|
| FVG di OTE Discount | Sangat tinggi — buy setup |
| OB di OTE Premium | Sangat tinggi — sell setup |
| BPR di Equilibrium | Tinggi — reversal potensial |
| IFVG di OTE | Tinggi — re-entry setelah full fill |

---

## Catatan Tambahan

- Range dapat diambil dari **swing HTF** (misalnya daily high/low) untuk bias makro, atau **swing LTF** untuk entry mikro.
- ICT sering menyebut OTE sebagai area di mana **smart money** menempatkan order sebelum move besar.
- Equilibrium (50%) adalah target harga yang paling sering dicapai sebelum harga berbalik ke arah premium/discount.

---

---

# Vacuum Block Logic

## Definisi

Vacuum Block adalah **gap harga yang terbentuk akibat event volatilitas tinggi** — seperti FOMC, NFP, data ekonomi, atau event geopolitik (perang, sanksi, dll). Disebut "vacuum" karena **tidak ada aktivitas trading di dalam gap tersebut** — tidak ada trader yang bisa mengeksekusi order di zona itu karena pergerakan harga terlalu cepat.

---

## Penyebab Terbentuk

```
1. High-impact news event  : FOMC, NFP, CPI, GDP
2. Geopolitical event      : perang, sanksi, bencana
3. Session/day/week open   : harga gap open dari sesi sebelumnya
4. Circuit breaker / halt  : trading dihentikan sementara lalu gap open
```

---

## Formasi

```
Bullish Vacuum Block:
  Harga close di level X → event terjadi → harga open jauh DI ATAS X
  meninggalkan gap yang tidak pernah diperdagangkan

  open[0]   ─────  ← harga buka di sini (jauh di atas)
     ↑ GAP (vacuum — tidak ada trading activity)
  close[1]  ─────  ← harga tutup sebelum event

Bearish Vacuum Block:
  Harga close di level X → event terjadi → harga open jauh DI BAWAH X

  close[1]  ─────  ← harga tutup sebelum event
     ↓ GAP (vacuum — tidak ada trading activity)
  open[0]   ─────  ← harga buka di sini (jauh di bawah)
```

---

## Kondisi Terbentuk

### Bullish Vacuum Block
```
open[0] > close[1]
gap_size > threshold (misal: ATR * 1.0 atau nilai pip tertentu)
```
→ Harga membuka **jauh di atas** penutupan sebelumnya karena event volatilitas.

### Bearish Vacuum Block
```
open[0] < close[1]
gap_size > threshold
```
→ Harga membuka **jauh di bawah** penutupan sebelumnya karena event volatilitas.

---

## Zona Vacuum Block

| Type | Top | Bottom |
|---|---|---|
| **Bullish** | `open[0]` | `close[1]` |
| **Bearish** | `close[1]` | `open[0]` |

---

## Implementasi Pine Script

```pinescript
float gap_size    = math.abs(open - close[1])
float atr         = ta.atr(14)
bool  is_vacuum   = gap_size > atr * 1.0   // threshold minimal 1x ATR

// Bullish Vacuum Block
bool bull_vacuum     = open > close[1] and is_vacuum
float bull_vac_top   = open
float bull_vac_bot   = close[1]
float bull_vac_mid   = math.avg(bull_vac_top, bull_vac_bot)

// Bearish Vacuum Block
bool bear_vacuum     = open < close[1] and is_vacuum
float bear_vac_top   = close[1]
float bear_vac_bot   = open
float bear_vac_mid   = math.avg(bear_vac_top, bear_vac_bot)
```

---

## Perilaku Harga

```
Bullish Vacuum Block:
  Harga naik karena event → kemudian FILL GAP ke bawah (vakum terisi)
  → setelah fill, harga cenderung lanjut naik kembali

Bearish Vacuum Block:
  Harga turun karena event → kemudian FILL GAP ke atas (vakum terisi)
  → setelah fill, harga cenderung lanjut turun kembali
```

---

## Mitigasi Vacuum Block

```pinescript
// Bullish Vacuum Block terisi ketika harga turun ke dalam gap
bool bull_vac_filling  = low <= bull_vac_top
bool bull_vac_full_fill = low <= bull_vac_bot

// Bearish Vacuum Block terisi ketika harga naik ke dalam gap
bool bear_vac_filling  = high >= bear_vac_bot
bool bear_vac_full_fill = high >= bear_vac_top
```

---

## Perbedaan Vacuum Block vs Volume Imbalance vs FVG

| Aspek | FVG | Volume Imbalance | Vacuum Block |
|---|---|---|---|
| **Candle** | 3 candle | 2 candle | 2 candle (gap open) |
| **Gap antara** | `high[2]` & `low[0]` | `close[1]` & `open[0]` | `close[1]` & `open[0]` |
| **Penyebab** | Impulsive move organik | Gap open kecil | News/event eksternal |
| **Ukuran gap** | Kecil–sedang | Kecil | **Besar** (> 1x ATR) |
| **Likuiditas dalam gap** | Sangat sedikit | Sedikit | **Nol** |
| **Tendency fill** | Sebagian | Sebagian | **Hampir selalu fill** |

---

## Catatan Tambahan

- Vacuum Block yang terbentuk di **session open** (Asia, London, NY) lebih sering diisi di sesi yang sama.
- Vacuum Block dari **FOMC/NFP** biasanya diisi dalam beberapa jam hingga beberapa hari.
- Semakin besar gap, semakin kuat **daya tarik** harga untuk kembali mengisi zona tersebut.
- Setelah fill, arah lanjutan mengikuti **bias HTF** — bukan selalu berbalik.

---

---

# Rejection Block Logic

## Definisi

Rejection Block adalah **zona yang terbentuk dari wick/shadow signifikan** pada sebuah candle, menunjukkan penolakan kuat harga dari suatu level. Area antara **ujung wick dan body candle** menjadi zona support/resistance ketika harga kembali ke sana.

---

## Formasi

```
Bearish Rejection Block (upper wick besar):
  high (wick tip)   ──┐
                      │  ← zona rejection block (wick area)
  open atau close   ──┘  (body candle — batas bawah zona)

Bullish Rejection Block (lower wick besar):
  open atau close   ──┐  (body candle — batas atas zona)
                      │  ← zona rejection block (wick area)
  low (wick tip)    ──┘
```

---

## Kondisi Terbentuk

### Bullish Rejection Block
```
1. Candle memiliki lower wick yang signifikan
2. Wick size > body size (wick minimal 2x body)
3. Candle menunjukkan penolakan dari level low

wick_size = open > close ? (close - low) : (open - low)
body_size = math.abs(close - open)
bull_rejection = wick_size > body_size * 2.0
```

### Bearish Rejection Block
```
1. Candle memiliki upper wick yang signifikan
2. Wick size > body size (wick minimal 2x body)
3. Candle menunjukkan penolakan dari level high

wick_size = open > close ? (high - open) : (high - close)
body_size = math.abs(close - open)
bear_rejection = wick_size > body_size * 2.0
```

---

## Zona Rejection Block

| Type | Top (batas atas) | Bottom (batas bawah) |
|---|---|---|
| **Bullish** | `math.max(open, close)` — body top | `low` — ujung wick |
| **Bearish** | `high` — ujung wick | `math.min(open, close)` — body bottom |

---

## Implementasi Pine Script

```pinescript
float body_size = math.abs(close - open)
float body_top  = math.max(open, close)
float body_bot  = math.min(open, close)

// Bullish Rejection Block: lower wick dominan
float lower_wick     = body_bot - low
bool  bull_rejection = lower_wick > body_size * 2.0
float bull_rej_top   = body_bot   // batas atas = body bottom
float bull_rej_bot   = low        // batas bawah = wick tip

// Bearish Rejection Block: upper wick dominan
float upper_wick     = high - body_top
bool  bear_rejection = upper_wick > body_size * 2.0
float bear_rej_top   = high       // batas atas = wick tip
float bear_rej_bot   = body_top   // batas bawah = body top
```

---

## Mitigasi Rejection Block

```pinescript
// Harga masuk ke zona Bullish Rejection Block (beli di wick area)
bool bull_rej_hit = low <= bull_rej_top and low >= bull_rej_bot

// Harga masuk ke zona Bearish Rejection Block (jual di wick area)
bool bear_rej_hit = high >= bear_rej_bot and high <= bear_rej_top
```

---

## Perbedaan OB vs Rejection Block

| Aspek | Order Block | Rejection Block |
|---|---|---|
| **Dasar zona** | Seluruh body candle | Wick/shadow candle |
| **Sinyal** | Candle berlawanan arah sebelum impuls | Wick besar menunjukkan penolakan |
| **Konfirmasi** | BOS/impulsive move setelahnya | Ukuran wick relatif terhadap body |
| **Fungsi** | Demand/supply zone | Support/resistance dari penolakan |

---

## Catatan Tambahan

- Rejection Block paling valid ketika terbentuk di **level kunci** (HTF OB, FVG, struktur pasar).
- Candle **doji** atau **pin bar** adalah bentuk paling ekstrem dari Rejection Block.
- Semakin panjang wick relatif terhadap body, semakin kuat sinyal penolakan.
- Dalam ICT, Rejection Block sering muncul bersamaan dengan **liquidity sweep** — harga menyapu likuiditas lalu berbalik kuat.

---

---

# Institutional Liquidity Zones Logic

## Definisi

Institutional Liquidity Zones adalah **area harga di mana institusi (bank, hedge fund, smart money) menempatkan order besar**. Zona ini terbentuk karena institusi membutuhkan likuiditas yang cukup untuk mengisi posisi besar mereka tanpa menggerakkan pasar terlalu jauh.

---

## Karakteristik Zona

```
1. Terletak di level yang mudah diprediksi oleh retail trader
   → Di sinilah stop loss retail trader menumpuk
   → Institusi memanfaatkan likuiditas ini untuk entry/exit

2. Biasanya berada di:
   - Swing High / Swing Low yang obvious
   - Equal Highs / Equal Lows
   - Round numbers (00, 50 levels)
   - Previous Day/Week/Month High & Low
   - Session High & Low (Asia, London, NY)
   - Trendline / channel boundaries
```

---

## Tipe Institutional Liquidity Zones

| Zona | Lokasi | Isi Likuiditas |
|---|---|---|
| **Above swing high** | Di atas high yang jelas | Buy stop dari short seller |
| **Below swing low** | Di bawah low yang jelas | Sell stop dari long buyer |
| **Round numbers** | 1.2000, 1.2050, dll | Pending order retail |
| **Previous high/low** | PDH, PWH, PMH | Cluster stop & pending order |
| **Session open** | Asia/London/NY open | Gap fill & stop hunt |

---

## Implementasi Pine Script

```pinescript
int swing_len = 10

// Identifikasi swing high/low sebagai potential liquidity zones
bool is_swing_high = ta.pivothigh(high, swing_len, swing_len)
bool is_swing_low  = ta.pivotlow(low, swing_len, swing_len)

float inst_liq_high = ta.valuewhen(is_swing_high, high[swing_len], 0)
float inst_liq_low  = ta.valuewhen(is_swing_low, low[swing_len], 0)

// Round number detection (misal per 50 pip / 500 point)
float round_level = math.round(close / 500) * 500
bool  near_round  = math.abs(close - round_level) < ta.atr(14) * 0.5
```

---

## Catatan Tambahan

- Semakin banyak swing yang terbentuk di satu level, semakin besar likuiditas yang menumpuk.
- Institusi **tidak membeli di resistance atau menjual di support** — mereka membeli *setelah* likuiditas di bawah support diambil, dan menjual *setelah* likuiditas di atas resistance diambil.
- Zona ini adalah **target** pergerakan harga, bukan area untuk entry langsung.

---

---

# Buy Side & Sell Side Liquidity Logic

## Definisi

- **Buy Side Liquidity (BSL)**: Kumpulan **buy stop order** yang berada *di atas* swing high. Terdiri dari stop loss milik short seller dan pending buy order retail yang menunggu breakout.
- **Sell Side Liquidity (SSL)**: Kumpulan **sell stop order** yang berada *di bawah* swing low. Terdiri dari stop loss milik long buyer dan pending sell order retail yang menunggu breakdown.

---

## Formasi

```
BSL (Buy Side Liquidity):
  ─────────────  Swing High  ← BSL menumpuk DI ATAS sini
                               (stop loss short seller + buy stop retail)
  ─────────────  Harga saat ini

SSL (Sell Side Liquidity):
  ─────────────  Harga saat ini
                               (stop loss long buyer + sell stop retail)
  ─────────────  Swing Low   ← SSL menumpuk DI BAWAH sini
```

---

## Mekanisme

```
Institusi ingin SELL besar:
  1. Harga dinaikkan ke atas BSL (sweep)
  2. Buy stop retail tereksekusi → memberi likuiditas untuk institusi SELL
  3. Harga berbalik turun setelah sweep

Institusi ingin BUY besar:
  1. Harga diturunkan ke bawah SSL (sweep)
  2. Sell stop retail tereksekusi → memberi likuiditas untuk institusi BUY
  3. Harga berbalik naik setelah sweep
```

---

## Identifikasi Level BSL / SSL

```pinescript
int  swing_len = 10

// BSL: swing high yang belum ditembus
float bsl_level = ta.valuewhen(ta.pivothigh(high, swing_len, swing_len), high[swing_len], 0)

// SSL: swing low yang belum ditembus
float ssl_level = ta.valuewhen(ta.pivotlow(low, swing_len, swing_len), low[swing_len], 0)

// Cek apakah BSL sudah diambil (swept)
bool bsl_taken = high >= bsl_level
bool ssl_taken = low  <= ssl_level
```

---

## Equal Highs / Equal Lows sebagai BSL/SSL Kuat

```pinescript
float tolerance = ta.atr(14) * 0.1

// Equal Highs = BSL yang lebih kuat (double/triple top)
bool eq_highs = math.abs(high - high[1]) <= tolerance or
                math.abs(high - high[2]) <= tolerance

// Equal Lows = SSL yang lebih kuat (double/triple bottom)
bool eq_lows  = math.abs(low - low[1]) <= tolerance or
                math.abs(low - low[2]) <= tolerance
```

---

## Catatan Tambahan

- **BSL** selalu di atas harga, **SSL** selalu di bawah harga — relatif terhadap posisi harga saat ini.
- Equal highs/lows = **magnet likuiditas** yang lebih kuat dari single swing high/low.
- Setelah BSL diambil → bias bearish. Setelah SSL diambil → bias bullish.
- Dalam ICT, proses pengambilan likuiditas ini disebut **"running liquidity"**.

---

## Mekanisme Likuiditas: Dua Kelompok Pelaku Pasar

**Kelompok 1** = Central banks, algoritma, dan institusi → memegang pengaruh besar atas arus dana pasar. Mereka memanipulasi persepsi retail untuk menciptakan kondisi yang menguntungkan posisi mereka.

**Kelompok 2** = Retail traders → cenderung menempatkan stop order di area yang mudah diprediksi.

```
Mekanisme BSL/SSL (dari perspektif institusi):

Retail LONG → pasang Stop Loss di bawah swing low → ketika terkena = SSL
  Stop order retail long yang terkena → membuka short trade otomatis
  → Menciptakan Sell Side Liquidity yang digunakan institusi untuk BUY besar

Retail SHORT → pasang Stop Loss di atas swing high → ketika terkena = BSL
  Stop order retail short yang terkena → membuka long trade otomatis
  → Menciptakan Buy Side Liquidity yang digunakan institusi untuk SELL besar
```

> "Liquidity is the lifeblood of the markets. It allows anyone to buy or sell for a profit or a loss. It creates opportunity in the markets." — ICT

- Dalam illiquid market: sedikit pembeli/penjual, trade lebih lama selesai, harga lebih volatile.
- Institusi tidak bisa langsung membeli dalam jumlah besar — mereka **butuh retail** sebagai counterparty.
- Inilah mengapa mereka "hunt" stop loss retail sebelum bergerak ke arah sesungguhnya.

---

---

# High & Low Resistance Liquidity (HRL / LRL) Logic

## Definisi

- **Low Resistance Liquidity (LRL)**: Area di mana harga bergerak **mudah dan cepat** karena sedikit order berlawanan — imbalance, FVG, satu sisi order flow.
- **High Resistance Liquidity (HRL)**: Area di mana harga bergerak **lambat dan choppy** karena banyak order dari dua arah — konsolidasi, area yang sudah banyak diperdagangkan.

---

## Perbedaan Kunci

```
LRL (Low Resistance):
  Harga  ──────────────────────────►  (cepat, sedikit hambatan)
  Contoh: FVG, displacement, imbalance zone

HRL (High Resistance):
  Harga  ─┐┌─┐┌─┐┌──────────────►  (lambat, banyak hambatan)
  Contoh : konsolidasi, area yang sudah diperdagangkan berulang
```

---

## Identifikasi LRL

```
Area LRL (mudah dilalui harga):
  1. FVG — gap belum terisi
  2. Vacuum Block / Liquidity Void
  3. Area displacement (candle besar satu arah)
  4. Above BSL atau below SSL yang belum diambil
```

## Identifikasi HRL

```
Area HRL (sulit dilalui harga):
  1. Area konsolidasi (ranging market)
  2. Zona yang sudah sering disentuh harga (well-traded)
  3. Area dengan banyak OB/FVG yang sudah termitigasi
  4. Equilibrium / 50% range
```

---

## Implementasi Pine Script

```pinescript
// LRL proxy: area yang belum diperdagangkan (FVG masih aktif)
bool lrl_zone = bull_fvg_active or bear_fvg_active

// HRL proxy: area konsolidasi (range kecil dalam N bar)
int   consol_len   = 20
float highest_n    = ta.highest(high, consol_len)
float lowest_n     = ta.lowest(low, consol_len)
float range_n      = highest_n - lowest_n
bool  hrl_zone     = range_n < ta.atr(14) * consol_len * 0.5
```

---

## Penggunaan dalam Trading

```
Bias BUY:
  - Entry di LRL discount (FVG/OB di bawah 50%)
  - Target: LRL premium di atas (FVG di atas 50%)
  - Hindari masuk di HRL (konsolidasi) — terlalu banyak noise

Bias SELL:
  - Entry di LRL premium (FVG/OB di atas 50%)
  - Target: LRL discount di bawah (FVG di bawah 50%)
```

---

---

# Internal & External Range Liquidity Logic

## Definisi

- **Internal Range Liquidity (IRL)**: Likuiditas yang berada **di dalam** range harga saat ini — FVG, OB, CE/equilibrium, imbalance dalam swing.
- **External Range Liquidity (ERL)**: Likuiditas yang berada **di luar** range harga saat ini — swing high/low, equal highs/lows, BSL/SSL di luar range.

---

## Formasi

```
ERL atas  ─────────────────  Swing High (BSL)
           ↑ luar range
           ─────────────────  Range High

           ↕ dalam range      IRL: FVG, OB, CE, imbalance
           ─────────────────  Range Low
           ↓ luar range
ERL bawah ─────────────────  Swing Low (SSL)
```

---

## IRL — Contoh Level

```
Di dalam range (antara swing high dan swing low saat ini):
  - Bullish/Bearish FVG
  - Order Block
  - Equilibrium (50% range)
  - BPR (Balance Price Range)
  - Consequent Encroachment (CE)
```

## ERL — Contoh Level

```
Di luar range (melampaui swing high/low saat ini):
  - Previous swing high / swing low
  - Equal highs / equal lows
  - PDH/PDL (Previous Day High/Low)
  - PWH/PWL (Previous Week High/Low)
  - BSL / SSL di luar range
```

---

## Implementasi Pine Script

```pinescript
int  swing_len  = 20
float range_high = ta.highest(high, swing_len)
float range_low  = ta.lowest(low, swing_len)
float range_eq   = math.avg(range_high, range_low)

// IRL: level di dalam range
bool price_in_irl = close > range_low and close < range_high
bool at_irl_eq    = math.abs(close - range_eq) < ta.atr(14) * 0.3

// ERL: level di luar range (target sweep)
float erl_high = ta.highest(high, swing_len * 2)  // swing lebih lebar
float erl_low  = ta.lowest(low, swing_len * 2)

bool erl_high_taken = high >= erl_high
bool erl_low_taken  = low  <= erl_low
```

---

## Pola Pergerakan IRL → ERL

```
Skenario Bullish:
  1. Harga berada di bawah range (discount)
  2. Harga sweep SSL (ERL bawah) → ambil likuiditas
  3. Harga naik melalui IRL (FVG, OB dalam range)
  4. Target akhir: BSL / ERL atas

Skenario Bearish:
  1. Harga berada di atas range (premium)
  2. Harga sweep BSL (ERL atas) → ambil likuiditas
  3. Harga turun melalui IRL (FVG, OB dalam range)
  4. Target akhir: SSL / ERL bawah
```

---

## Catatan Tambahan

- ICT menyebut pola ini sebagai **"draw on liquidity"** — harga selalu bergerak dari satu likuiditas ke likuiditas berikutnya.
- IRL adalah **area reaksi** (entry/retracement), ERL adalah **target** pergerakan.
- Setelah ERL tercapai, harga sering berbalik kembali menuju IRL sebelum melanjutkan ke ERL berikutnya.

---

---

# Liquidity Pool Logic

## Definisi

Liquidity Pool adalah **kluster/kumpulan stop loss dan pending order** yang menumpuk di satu area harga tertentu. Semakin banyak stop order di satu level, semakin besar "pool" likuiditas di sana, dan semakin menarik bagi institusi untuk mengambilnya.

---

## Terbentuk Di

```
1. Equal Highs / Equal Lows     → stop loss menumpuk di satu level
2. Swing High / Swing Low       → stop loss obvious retail
3. Round Numbers                → 1.2000, 1.2500, 2000.00
4. Trendline touch points       → semakin banyak touch = semakin besar pool
5. Previous Day/Week/Month H&L  → level yang diperhatikan banyak trader
6. Opening prices               → daily/weekly open
```

---

## Identifikasi Liquidity Pool

```pinescript
int    swing_len  = 10
float  tolerance  = ta.atr(14) * 0.15
int    pool_count = 0

// Hitung berapa kali harga menyentuh level yang sama (equal highs)
for i = 1 to 10
    if math.abs(high - high[i]) <= tolerance
        pool_count += 1

// Semakin besar pool_count, semakin besar liquidity pool
bool strong_pool = pool_count >= 2   // minimal 2 equal highs/lows
```

---

## Kekuatan Liquidity Pool

| Tipe Pool | Kekuatan | Keterangan |
|---|---|---|
| Single swing high/low | Lemah | Hanya satu titik |
| Equal Highs (2x) | Sedang | Double top/bottom |
| Equal Highs (3x+) | Kuat | Triple top/bottom |
| Round number + swing | Sangat kuat | Konfluens dua faktor |
| PDH/PDL + equal | Ekstrem | Level institusional |

---

## Catatan Tambahan

- Liquidity Pool **bukan zona entry** — ini adalah **target** harga.
- Setelah pool diambil, cari entry di arah berlawanan.
- Pool yang lebih tua (lebih lama belum diambil) cenderung lebih kuat.

---

---

# Liquidity Void Logic

## Definisi

Liquidity Void adalah **area harga di mana tidak ada atau sangat sedikit trading activity** karena harga bergerak terlalu cepat melewatinya. Mirip dengan FVG dan Vacuum Block, tapi dalam skala yang lebih besar dan merujuk pada kekosongan likuiditas secara umum.

---

## Formasi

```
Harga sebelumnya  ─────  Level A
                  ↕ Liquidity Void (tidak ada trading di sini)
Harga setelahnya  ─────  Level B

Contoh pada chart:
  Candle besar satu arah yang "loncat" dari A ke B tanpa konsolidasi
  → Area antara open dan close candle tersebut = Liquidity Void
```

---

## Perbedaan dengan FVG dan Vacuum Block

| Aspek | FVG | Vacuum Block | Liquidity Void |
|---|---|---|---|
| **Skala** | Micro (3 candle) | Macro (gap event) | Meso (displacement) |
| **Penyebab** | Imbalance organik | News/event eksternal | Displacement / impuls |
| **Ukuran** | Kecil | Besar | Sedang–besar |
| **Definisi gap** | Antara high[2] & low[0] | Antara close[1] & open[0] | Area candle besar tanpa retracement |

---

## Implementasi Pine Script

```pinescript
// Liquidity Void: candle besar yang bergerak cepat tanpa overlap signifikan
float candle_range = high - low
bool  is_void      = candle_range > ta.atr(14) * 2.0 and
                     math.abs(close - open) > candle_range * 0.7  // body dominan

float void_top = close > open ? close : open
float void_bot = close > open ? open  : close
float void_mid = math.avg(void_top, void_bot)
```

---

## Perilaku Harga

```
Harga cenderung kembali mengisi Liquidity Void:
  - Untuk menyeimbangkan order flow
  - Setelah mencapai target ERL / Liquidity Pool
  - Dalam fase retracement sebelum lanjut arah

Void terisi sebagian (50% / CE):  entry opportunity
Void terisi penuh:                lanjut arah asli
```

---

---

# Liquidity Sweep & Liquidity Run Logic

## Definisi

- **Liquidity Sweep**: Harga **sebentar menembus** level likuiditas (BSL/SSL/swing) lalu **langsung berbalik** — stop hunt cepat.
- **Liquidity Run**: Harga **menembus dan terus bergerak** melewati **beberapa level likuiditas berturut-turut** tanpa reversal signifikan.

---

## Formasi

### Liquidity Sweep
```
SSL  ────────────  Swing Low
       ↓ spike sebentar (wick / 1-2 candle)
       ↑ langsung berbalik naik
→ Stop loss long buyer tereksekusi, institusi BUY, harga naik
```

### Liquidity Run
```
SSL 1  ──────────  Swing Low 1
       ↓ tembus
SSL 2  ──────────  Swing Low 2
       ↓ tembus
SSL 3  ──────────  Swing Low 3
       ↓ terus bergerak turun
→ Tidak ada reversal — ini tren kuat ke bawah
```

---

## Kondisi Terbentuk

### Liquidity Sweep (Stop Hunt)
```
1. Harga mendekati level BSL/SSL yang jelas
2. Harga menembus level tersebut (mengambil stop loss)
3. Harga menutup KEMBALI di dalam range sebelumnya
4. Konfirmasi: candle berikutnya berlawanan arah (reversal candle)
```

### Liquidity Run (Trending)
```
1. Harga menembus level BSL/SSL
2. Harga TIDAK kembali ke dalam range
3. Harga terus bergerak ke level BSL/SSL berikutnya
4. Tidak ada retracement signifikan di antara level-level tersebut
```

---

## Implementasi Pine Script

```pinescript
int  swing_len = 10
float swing_high_level = ta.valuewhen(ta.pivothigh(high, swing_len, swing_len), high[swing_len], 0)
float swing_low_level  = ta.valuewhen(ta.pivotlow(low, swing_len, swing_len), low[swing_len], 0)

// Liquidity Sweep (BSL): spike ke atas lalu close di bawah swing high
bool bsl_sweep = high > swing_high_level and close < swing_high_level

// Liquidity Sweep (SSL): spike ke bawah lalu close di atas swing low
bool ssl_sweep = low < swing_low_level and close > swing_low_level

// Liquidity Run (BSL): close DI ATAS swing high (tidak kembali)
bool bsl_run = close > swing_high_level

// Liquidity Run (SSL): close DI BAWAH swing low (tidak kembali)
bool ssl_run = close < swing_low_level
```

---

## Perbedaan Sweep vs Run

| Aspek | Liquidity Sweep | Liquidity Run |
|---|---|---|
| **Penembusan** | Sementara (wick/spike) | Permanen (close melewati level) |
| **Candle close** | Kembali ke dalam range | Tetap di luar range |
| **Implikasi** | **Reversal** signal | **Continuation** signal |
| **Arah setelahnya** | Berbalik berlawanan | Lanjut ke likuiditas berikutnya |
| **Contoh** | Stop hunt, false breakout | Breakout valid, trending move |

---

## Konfirmasi Entry Setelah Sweep

```
Setelah SSL Sweep (bullish setup):
  1. Low menembus swing low (sweep SSL)
  2. Candle close kembali di atas swing low
  3. Candle konfirmasi bullish muncul
  4. Entry buy dengan SL di bawah wick sweep
  5. Target: BSL (ERL) di atas

Setelah BSL Sweep (bearish setup):
  1. High menembus swing high (sweep BSL)
  2. Candle close kembali di bawah swing high
  3. Candle konfirmasi bearish muncul
  4. Entry sell dengan SL di atas wick sweep
  5. Target: SSL (ERL) di bawah
```

---

## Catatan Tambahan

- Sweep yang terjadi di **session open** (London, NY) lebih signifikan.
- Sweep yang diikuti oleh **displacement candle** (candle besar berlawanan) adalah konfirmasi terkuat.
- **Liquidity Run** sering terjadi setelah CHoCH (Change of Character) — perubahan struktur pasar.
- Dalam ICT, sweep sering disebut **"stop hunt"** atau **"turtle soup"**.

---

---

# ICT Market Profiles Logic

## Definisi

ICT Market Profiles adalah **framework untuk membaca pola pergerakan harga berdasarkan waktu dan sesi trading**. Berbeda dari Market Profile/TPO konvensional, ICT Market Profiles berfokus pada **perilaku berulang harga** di setiap sesi (Asia, London, New York) dan bagaimana sesi-sesi tersebut berinteraksi.

---

## Tiga Sesi Utama

| Sesi | Waktu (UTC) | Karakteristik |
|---|---|---|
| **Asia** | 00:00 – 08:00 | Konsolidasi, range kecil, likuiditas rendah |
| **London** | 08:00 – 16:00 | Ekspansi, volatilitas tinggi, sering membuat high/low harian |
| **New York** | 13:00 – 21:00 | Overlap dengan London, reversal atau continuation |

---

## Struktur Sesi

```
Asia Session:
  → Membentuk range (consolidation)
  → High dan Low Asia menjadi level likuiditas
  → SSL berada di bawah Asia Low, BSL di atas Asia High

London Session:
  → Sering sweep salah satu sisi Asia range (Judas Swing)
  → Membentuk high atau low harian
  → Ekspansi ke arah bias

New York Session:
  → Bisa lanjutkan arah London (continuation)
  → Atau reversal dari high/low London
  → NY Open (13:00–14:00 UTC) sering ada manipulasi kedua
```

---

## Implementasi Pine Script

```pinescript
// Session detection (UTC-based)
bool asia_session   = (hour >= 0  and hour < 8)
bool london_session = (hour >= 8  and hour < 16)
bool ny_session     = (hour >= 13 and hour < 21)

// Asia range
var float asia_high = na
var float asia_low  = na

if asia_session
    asia_high := ta.highest(high, 1)
    asia_low  := ta.lowest(low, 1)

// Level likuiditas dari Asia range
float bsl_asia = asia_high  // BSL di atas Asia high
float ssl_asia = asia_low   // SSL di bawah Asia low

// Cek sweep Asia range di London
bool london_bsl_sweep = london_session and high > bsl_asia and close < bsl_asia
bool london_ssl_sweep = london_session and low  < ssl_asia and close > ssl_asia
```

---

## Catatan Tambahan

- **Asia High/Low** adalah level penting — sering menjadi target sweep di London/NY open.
- **London Kill Zone** (08:00–10:00 UTC) adalah waktu paling aktif untuk setup ICT.
- **NY Kill Zone** (13:00–14:00 UTC) adalah waktu setup NY open.
- Profile dapat berubah tergantung **hari dalam seminggu** — lihat Weekly Profiles.

---

---

# Weekly Profiles Logic

## Definisi

Weekly Profiles adalah **pola pergerakan mingguan yang berulang**, berdasarkan pada hari mana high dan low minggu terbentuk. ICT mengidentifikasi bahwa pasar cenderung mengikuti pola tertentu sepanjang minggu.

---

## Tipe Weekly Profile Utama

### 1. Classic Bullish Weekly
```
Senin    : Turun → sweep SSL (sell side)
Selasa   : Reversal naik mulai
Rabu     : Ekspansi naik
Kamis    : High minggu terbentuk
Jumat    : Retracement / konsolidasi

Weekly Low  = Senin/Selasa
Weekly High = Kamis/Jumat
```

### 2. Classic Bearish Weekly
```
Senin    : Naik → sweep BSL (buy side)
Selasa   : Reversal turun mulai
Rabu     : Ekspansi turun
Kamis    : Low minggu terbentuk
Jumat    : Retracement / konsolidasi

Weekly High = Senin/Selasa
Weekly Low  = Kamis/Jumat
```

### 3. Consolidation Weekly
```
Senin–Rabu  : Range / konsolidasi sempit
Kamis–Jumat : Ekspansi ke satu arah
→ Sering terjadi sebelum high-impact news (FOMC, NFP)
```

### 4. Reversal Weekly
```
Senin    : Lanjutkan arah minggu lalu
Selasa   : Puncak / bottom terbentuk
Rabu–Jumat: Reversal penuh ke arah berlawanan
```

---

## Hari Kritis dalam Minggu

| Hari | Peran ICT |
|---|---|
| **Senin** | Sering manipulasi / setup arah minggu |
| **Selasa** | Sering terbentuk high/low mingguan |
| **Rabu** | Mid-week — consolidation atau pivot |
| **Kamis** | Ekspansi / continuation hari terkuat |
| **Jumat** | Profit taking, retracement, penutupan posisi |

---

## Implementasi Pine Script

```pinescript
// Hari dalam minggu (1=Senin, 2=Selasa, ..., 5=Jumat)
int day_of_week = dayofweek == dayofweek.monday    ? 1 :
                  dayofweek == dayofweek.tuesday   ? 2 :
                  dayofweek == dayofweek.wednesday ? 3 :
                  dayofweek == dayofweek.thursday  ? 4 :
                  dayofweek == dayofweek.friday    ? 5 : na

// Previous week high & low
float pwh = request.security(syminfo.tickerid, "W", high[1])
float pwl = request.security(syminfo.tickerid, "W", low[1])

// Current week high & low (running)
float cwh = request.security(syminfo.tickerid, "W", high)
float cwl = request.security(syminfo.tickerid, "W", low)
```

---

## Catatan Tambahan

- **Selasa** adalah hari paling sering terbentuknya weekly high/low dalam kondisi trending.
- Jika **Senin membuat high** → bias minggu bearish. Jika **Senin membuat low** → bias minggu bullish.
- **Rabu** sering menjadi hari reversal atau konsolidasi sebelum ekspansi Kamis.
- NFP/FOMC yang jatuh di tengah minggu dapat mengubah profil secara drastis.

---

---

# Daily Bias Logic

## Definisi

Daily Bias adalah **penentuan arah harga dominan untuk hari trading tersebut** — apakah bias bullish (cari buy) atau bearish (cari sell). Ditentukan dengan menganalisis HTF context, previous day structure, dan session behavior.

---

## Faktor Penentu Daily Bias

```
1. HTF Trend (W1, D1)
   → Apakah harga dalam uptrend atau downtrend secara HTF?

2. Previous Day High & Low (PDH/PDL)
   → Di mana harga menutup kemarin relatif terhadap range?
   → Apakah PDH/PDL sudah diambil?

3. Previous Day Close
   → Close di atas midpoint kemarin → bias bullish hari ini
   → Close di bawah midpoint kemarin → bias bearish hari ini

4. Asia Session Range
   → Apakah Asia membuat higher high dari kemarin → bullish
   → Apakah Asia membuat lower low dari kemarin → bearish

5. Premium / Discount
   → Harga di discount → bias bullish
   → Harga di premium → bias bearish
```

---

## Framework Penentuan Bias

```
Bullish Daily Bias:
  ✓ HTF trend bullish (D1/W1 uptrend)
  ✓ Harga di discount zone (< 50% range HTF)
  ✓ SSL kemarin sudah diambil (sweep SSL)
  ✓ Previous day close di atas midpoint
  ✓ Ada Bullish FVG/OB di discount yang belum termitigasi
  → Cari BUY setup di sesi London/NY

Bearish Daily Bias:
  ✓ HTF trend bearish (D1/W1 downtrend)
  ✓ Harga di premium zone (> 50% range HTF)
  ✓ BSL kemarin sudah diambil (sweep BSL)
  ✓ Previous day close di bawah midpoint
  ✓ Ada Bearish FVG/OB di premium yang belum termitigasi
  → Cari SELL setup di sesi London/NY
```

---

## Implementasi Pine Script

```pinescript
// Previous Day levels
float pdh = request.security(syminfo.tickerid, "D", high[1])
float pdl = request.security(syminfo.tickerid, "D", low[1])
float pdc = request.security(syminfo.tickerid, "D", close[1])
float pdm = math.avg(pdh, pdl)  // previous day midpoint

// Daily bias berdasarkan close vs midpoint
bool bull_daily_bias = pdc > pdm
bool bear_daily_bias = pdc < pdm

// Konfirmasi dengan premium/discount
float daily_eq = math.avg(pdh, pdl)
bool in_discount = close < daily_eq
bool in_premium  = close > daily_eq

// Bias kuat jika keduanya selaras
bool strong_bull_bias = bull_daily_bias and in_discount
bool strong_bear_bias = bear_daily_bias and in_premium
```

---

## Catatan Tambahan

- Daily bias bisa **berubah** jika ada news event besar.
- Selalu konfirmasi bias dengan **HTF (W1/D1)** sebelum entry di LTF.
- Jika bias tidak jelas → **no trade day** (hindari trading di hari konsolidasi).
- **Rabu** sering menjadi hari pivot — bias bisa berbalik dari Senin/Selasa.

---

---

# Intraday Profiles Logic

## Definisi

Intraday Profiles adalah **pola pergerakan harga dalam satu hari trading** yang berulang berdasarkan interaksi antar sesi. ICT mengidentifikasi beberapa pola tipikal yang sering terjadi.

---

## Tipe Intraday Profile

### 1. London Expansion → NY Continuation
```
Asia     : Konsolidasi (range sempit)
London   : Sweep Asia low/high → ekspansi naik/turun
NY Open  : Retracement ke FVG/OB → lanjut arah London

Ini adalah profil paling umum di trending market.
```

### 2. London Reversal (NY berlawanan dengan London)
```
Asia     : Konsolidasi
London   : Membuat high/low hari → ekspansi
NY Open  : Reversal dari high/low London → bergerak berlawanan

Terjadi ketika London membuat false move (Judas Swing panjang)
```

### 3. Asia Expansion
```
Asia     : Ekspansi (tidak konsolidasi) — jarang terjadi
London   : Continuation atau reversal dari Asia move
NY       : Mengikuti arah yang sudah terbentuk
```

### 4. NY-Only Profile
```
Asia     : Flat
London   : Minimal movement
NY Open  : Seluruh daily range terbentuk di sesi NY
→ Sering terjadi hari NFP atau FOMC
```

---

## Kill Zones (Waktu Setup Terbaik)

| Kill Zone | Waktu UTC | Deskripsi |
|---|---|---|
| **London Open KZ** | 08:00–10:00 | Setup utama London, high volatilitas |
| **London Close KZ** | 11:00–12:00 | Reversal / profit taking sebelum NY |
| **NY Open KZ** | 13:00–14:00 | Setup utama NY, sering ada manipulasi |
| **NY PM Session** | 15:00–16:00 | Continuation setelah NY lunch |
| **Asia KZ** | 20:00–00:00 | Level dan range Asia terbentuk |

---

## Implementasi Pine Script

```pinescript
// Kill Zone detection (UTC)
bool london_open_kz = (hour == 8  or hour == 9)
bool london_close_kz= (hour == 11 or hour == 12)
bool ny_open_kz     = (hour == 13 or hour == 14)
bool ny_pm_kz       = (hour == 15 or hour == 16)
bool asia_kz        = (hour >= 20 or hour < 1)

// Intraday levels
float london_open_price = request.security(syminfo.tickerid, "60",
                          open, lookahead=barmerge.lookahead_off)
```

---

## Catatan Tambahan

- **London Open Kill Zone** adalah waktu terbaik untuk setup ICT harian.
- **NY Open Kill Zone** sering menghasilkan **Judas Swing** yang kemudian berbalik.
- Hindari trading di luar Kill Zone — noise lebih banyak, setup kurang valid.
- Profil bisa **overlap** — misalnya London Expansion bisa sekaligus Judas Swing.

---

---

# Advanced Market Structure Logic

## Definisi

Advanced Market Structure adalah pemahaman mendalam tentang **bagaimana harga membentuk dan mengubah struktur pasar** — melampaui HH/HL/LH/LL dasar, mencakup CHoCH, BOS, SMS, dan internal vs external structure.

---

## Konsep Dasar Structure

```
Uptrend (Bullish Structure):
  HH (Higher High) → HL (Higher Low) → HH → HL ...
  Setiap retracement membuat HL yang lebih tinggi dari HL sebelumnya

Downtrend (Bearish Structure):
  LH (Lower High) → LL (Lower Low) → LH → LL ...
  Setiap retracement membuat LH yang lebih rendah dari LH sebelumnya
```

---

## Break of Structure (BOS)

```
BOS Bullish: harga menembus swing HIGH sebelumnya (konfirmasi uptrend lanjut)
  HL ──────────────── HH ← BOS (close di atas swing high)
        ↑ HL baru

BOS Bearish: harga menembus swing LOW sebelumnya (konfirmasi downtrend lanjut)
  LH ──────────────── LL ← BOS (close di bawah swing low)
        ↓ LH baru
```

---

## Change of Character (CHoCH)

```
CHoCH Bullish (struktur bearish → mulai bullish):
  LH → LL → LH → tiba-tiba close DI ATAS LH sebelumnya
  → Pertanda awal pembalikan uptrend

CHoCH Bearish (struktur bullish → mulai bearish):
  HH → HL → HH → tiba-tiba close DI BAWAH HL sebelumnya
  → Pertanda awal pembalikan downtrend
```

---

## Internal vs External Structure

```
External Structure (macro):
  Swing high/low yang terbentuk di TF lebih tinggi
  → Digunakan untuk menentukan bias dan target

Internal Structure (micro):
  Swing high/low yang terbentuk di dalam leg dari external structure
  → Digunakan untuk timing entry
```

---

## Implementasi Pine Script

```pinescript
int swing_len = 5

// Swing detection
bool is_swing_high = ta.pivothigh(high, swing_len, swing_len)
bool is_swing_low  = ta.pivotlow(low, swing_len, swing_len)

float last_sh = ta.valuewhen(is_swing_high, high[swing_len], 0)
float last_sl = ta.valuewhen(is_swing_low, low[swing_len], 0)
float prev_sh = ta.valuewhen(is_swing_high, high[swing_len], 1)
float prev_sl = ta.valuewhen(is_swing_low, low[swing_len], 1)

// BOS Bullish: close di atas swing high sebelumnya
bool bos_bull = close > last_sh

// BOS Bearish: close di bawah swing low sebelumnya
bool bos_bear = close < last_sl

// CHoCH Bullish: dalam downtrend, close di atas LH
bool choch_bull = close > last_sh and last_sh < prev_sh

// CHoCH Bearish: dalam uptrend, close di bawah HL
bool choch_bear = close < last_sl and last_sl > prev_sl
```

---

## Hirarki Structure

```
W1 Structure  →  Bias jangka panjang (minggu/bulan)
D1 Structure  →  Bias harian
H4 Structure  →  Bias intraday
H1 Structure  →  Entry timeframe
M15 Structure →  Timing presisi entry
M5/M1         →  Eksekusi
```

---

## Catatan Tambahan

- **BOS** = konfirmasi tren lanjut. **CHoCH** = sinyal awal pembalikan.
- CHoCH **tidak selalu** berarti reversal besar — bisa hanya koreksi dalam tren.
- Selalu analisis structure dari **TF tinggi ke TF rendah** (top-down analysis).
- Internal CHoCH di dalam retracement = peluang entry searah tren HTF.

---

---

# Market Maker Buy & Sell Model Logic

## Definisi

Market Maker Model adalah **siklus lengkap pergerakan harga** yang menggambarkan bagaimana institusi/market maker mengakumulasi posisi, memanipulasi harga untuk mengambil likuiditas, lalu mendistribusikan posisi ke arah yang sebenarnya.

---

## Market Maker Buy Model (Bullish)

```
Fase 1 — Original Consolidation:
  Harga konsolidasi → institusi akumulasi posisi sell dari retail long

Fase 2 — Expansion Down (Bearish leg):
  Harga turun → retail panic sell → institusi akumulasi BUY di bawah

Fase 3 — Retracement:
  Harga naik sedikit → retail short → stop loss retail long tereksekusi

Fase 4 — Reversal / Manipulation:
  Harga sweep SSL (di bawah swing low) → ambil sell stop retail
  → Institusi SELL stop retail = likuiditas untuk BUY institusi

Fase 5 — Expansion Up (Bullish leg):
  Harga naik kuat → menembus BSL → retail yang short terkena stop
  → Institusi profit taking / distribusi

Fase 6 — Target: BSL / ERL di atas
```

```
Visual:
  BSL ─────────────────────────────  ← Target akhir (fase 6)
                               ↗ ekspansi naik (fase 5)
  ───────────────────────────
                          ↙ sweep SSL (fase 4)
  SSL ──────────────────
        ↗ retracement (fase 3)
  ─────────────────────────
  ↘ ekspansi turun (fase 2)
  Konsolidasi (fase 1)
```

---

## Market Maker Sell Model (Bearish)

```
Fase 1 — Original Consolidation:
  Harga konsolidasi → institusi akumulasi posisi buy dari retail short

Fase 2 — Expansion Up (Bullish leg):
  Harga naik → retail FOMO buy → institusi distribusi SELL di atas

Fase 3 — Retracement:
  Harga turun sedikit → retail long → stop loss retail short tereksekusi

Fase 4 — Reversal / Manipulation:
  Harga sweep BSL (di atas swing high) → ambil buy stop retail
  → Institusi BUY stop retail = likuiditas untuk SELL institusi

Fase 5 — Expansion Down (Bearish leg):
  Harga turun kuat → menembus SSL → retail yang long terkena stop
  → Institusi profit taking / distribusi

Fase 6 — Target: SSL / ERL di bawah
```

---

## Implementasi Pine Script

```pinescript
// Identifikasi fase model (simplified)

// Fase konsolidasi: range sempit
int   consol_len = 20
float h_range    = ta.highest(high, consol_len) - ta.lowest(low, consol_len)
bool  in_consol  = h_range < ta.atr(14) * consol_len * 0.4

// Fase expansion: candle besar searah
bool expansion_up   = (high - low) > ta.atr(14) * 1.5 and close > open
bool expansion_down = (high - low) > ta.atr(14) * 1.5 and close < open

// Fase manipulation (sweep): spike tembus level lalu kembali
float swing_high_ref = ta.valuewhen(ta.pivothigh(high, 10, 10), high[10], 0)
float swing_low_ref  = ta.valuewhen(ta.pivotlow(low, 10, 10), low[10], 0)

bool manipulation_up   = low  < swing_low_ref  and close > swing_low_ref  // sweep SSL
bool manipulation_down = high > swing_high_ref and close < swing_high_ref // sweep BSL
```

---

## Catatan Tambahan

- Fase **manipulation/sweep** adalah sinyal entry terkuat dalam model ini.
- Entry terbaik: **setelah sweep konfirmasi** + ada FVG/OB di area sweep.
- Stop loss: **di balik wick sweep** (di bawah low jika buy, di atas high jika sell).
- Target: **BSL/ERL terdekat** di atas (buy) atau **SSL/ERL** di bawah (sell).
- Model ini bisa terjadi di **semua timeframe** — dari M1 hingga W1.

---

---

# Judas Swing Logic

## Definisi

Judas Swing adalah **pergerakan palsu/manipulatif di awal sesi** yang menipu retail trader ke arah yang salah, lalu berbalik ke arah sebenarnya. Nama "Judas" merujuk pada sifatnya yang mengkhianati — harga seolah bergerak ke satu arah lalu berkhianat.

---

## Kapan Terjadi

```
Paling sering di:
  1. London Open Kill Zone (08:00–10:00 UTC)
  2. NY Open Kill Zone (13:00–14:00 UTC)
  3. Setelah periode konsolidasi Asia session
```

---

## Formasi

### Judas Swing Bullish (palsu naik → turun sebenarnya)
```
Asia range terbentuk
─────────────────────  Asia High (BSL)
                      ↑ Judas Swing: harga spike ke atas (sweep BSL)
─────────────────────  Asia High
─────────────────────  Asia Low
                      ↓ Kemudian harga turun (arah sebenarnya)
─────────────────────  SSL (target)
```

### Judas Swing Bearish (palsu turun → naik sebenarnya)
```
Asia range terbentuk
─────────────────────  BSL (target)
                      ↑ Kemudian harga naik (arah sebenarnya)
─────────────────────  Asia High
─────────────────────  Asia Low
                      ↓ Judas Swing: harga spike ke bawah (sweep SSL)
─────────────────────  Asia Low (SSL)
```

---

## Kondisi Terbentuk

```
1. Asia session membentuk range yang jelas
2. Di London/NY Open, harga bergerak ke SALAH SATU sisi Asia range
3. Harga sweep level Asia High atau Asia Low
4. Harga close kembali ke dalam Asia range (atau di atas/bawahnya)
5. Harga kemudian bergerak ke ARAH BERLAWANAN dari Judas Swing
```

---

## Implementasi Pine Script

```pinescript
// Asia range
var float asia_high = na
var float asia_low  = na

if (hour >= 0 and hour < 8) and timeframe.isintraday
    asia_high := ta.highest(high, 1)
    asia_low  := ta.lowest(low, 1)

// Judas Swing Bearish (false move up, then down)
bool judas_bear = london_session and
                  high > asia_high and          // spike ke atas Asia high
                  close < asia_high             // close kembali di bawah

// Judas Swing Bullish (false move down, then up)
bool judas_bull = london_session and
                  low  < asia_low  and          // spike ke bawah Asia low
                  close > asia_low              // close kembali di atas

// Entry setelah Judas Swing terkonfirmasi
bool sell_after_judas = judas_bear and close < open  // konfirmasi bearish
bool buy_after_judas  = judas_bull and close > open  // konfirmasi bullish
```

---

## Entry Setup Setelah Judas Swing

```
Setelah Judas Swing Bearish (palsu naik):
  1. Judas Swing terkonfirmasi (spike atas + close balik)
  2. Cari Bearish FVG / OB di area sweep
  3. Entry SELL saat harga retracement ke FVG/OB
  4. SL: di atas high Judas Swing
  5. Target: SSL / Asia Low / ERL bawah

Setelah Judas Swing Bullish (palsu turun):
  1. Judas Swing terkonfirmasi (spike bawah + close balik)
  2. Cari Bullish FVG / OB di area sweep
  3. Entry BUY saat harga retracement ke FVG/OB
  4. SL: di bawah low Judas Swing
  5. Target: BSL / Asia High / ERL atas
```

---

## Catatan Tambahan

- Judas Swing paling kuat ketika terjadi di **London Open** dan didukung **Daily Bias** yang berlawanan dengan arah Judas.
- Semakin cepat harga berbalik setelah sweep, semakin kuat sinyalnya.
- Judas Swing yang diikuti **displacement candle** berlawanan = konfirmasi terkuat.
- Dalam Market Maker Model, Judas Swing adalah **Fase Manipulation**.

---

---

# IRL to ERL Price Move Logic

## Definisi

IRL to ERL adalah **pola draw on liquidity** — harga bergerak dari area Internal Range Liquidity (FVG, OB, CE dalam range) menuju External Range Liquidity (swing high/low di luar range). Ini adalah "jalan" yang diambil harga dari zona reaksi ke target likuiditas.

---

## Pola Pergerakan

```
Skenario Bullish (IRL → ERL atas):

ERL ──────────────────────────────  Swing High (BSL) ← Target
                           ↗ ekspansi
IRL ─────────  FVG/OB ← Entry (reaksi dari IRL)
                  ↗ dari SSL sweep
SSL ──────────────────────────────  Swing Low (SSL) ← Start

Skenario Bearish (IRL → ERL bawah):

BSL ──────────────────────────────  Swing High (BSL) ← Start
                  ↘ dari BSL sweep
IRL ─────────  FVG/OB ← Entry (reaksi dari IRL)
                           ↘ ekspansi
ERL ──────────────────────────────  Swing Low (SSL) ← Target
```

---

## Urutan Pergerakan

```
Bullish IRL → ERL:
  1. ERL bawah (SSL) diambil / sweep
  2. Harga berbalik naik (CHoCH atau BOS)
  3. Retracement ke IRL (FVG/OB di discount)
  4. Entry BUY dari IRL
  5. Target: ERL atas (BSL / swing high)

Bearish IRL → ERL:
  1. ERL atas (BSL) diambil / sweep
  2. Harga berbalik turun (CHoCH atau BOS)
  3. Retracement ke IRL (FVG/OB di premium)
  4. Entry SELL dari IRL
  5. Target: ERL bawah (SSL / swing low)
```

---

## Implementasi Pine Script

```pinescript
int swing_len = 20

// ERL levels
float erl_high = ta.highest(high, swing_len)  // BSL / ERL atas
float erl_low  = ta.lowest(low, swing_len)    // SSL / ERL bawah

// IRL level (midpoint / equilibrium)
float irl_eq = math.avg(erl_high, erl_low)

// Draw on liquidity direction
bool draw_to_bsl = close > irl_eq   // harga di discount, draw ke BSL
bool draw_to_ssl = close < irl_eq   // harga di premium, draw ke SSL

// Jarak ke target
float dist_to_erl_high = erl_high - close
float dist_to_erl_low  = close - erl_low

// RR estimasi
float potential_rr = draw_to_bsl ?
                     dist_to_erl_high / ta.atr(14) :
                     dist_to_erl_low  / ta.atr(14)
```

---

## Catatan Tambahan

- Selalu periksa apakah ada **IRL yang belum termitigasi** di antara posisi saat ini dan ERL target.
- Setiap IRL yang dilalui = **area retracement potensial** sebelum lanjut ke ERL.
- Semakin sedikit IRL di antara posisi dan target, semakin **smooth** pergerakan ke ERL.
- Konsep ini adalah inti dari ICT: **"Price always seeks liquidity"**.

---

---

# HRLR to LRLR Logic

## Definisi

**High Resistance Liquidity Run (HRLR)** to **Low Resistance Liquidity Run (LRLR)** adalah konsep yang menggambarkan **transisi harga dari area choppy/konsolidasi (HRLR) ke area trending/impulsif (LRLR)**.

---

## Definisi HRLR dan LRLR

```
HRLR (High Resistance Liquidity Run):
  Harga bergerak melalui area yang PENUH dengan order dua arah
  → Pergerakan lambat, choppy, sering reversal kecil
  → Contoh: konsolidasi, area yang sudah banyak diperdagangkan,
             equilibrium zone, area dengan banyak OB/FVG termitigasi

LRLR (Low Resistance Liquidity Run):
  Harga bergerak melalui area yang SEDIKIT order berlawanan
  → Pergerakan cepat, impulsif, sedikit retracement
  → Contoh: FVG aktif, Liquidity Void, Vacuum Block,
             area di atas BSL atau di bawah SSL yang baru diambil
```

---

## Pola Transisi

```
HRLR → LRLR (Breakout dari konsolidasi):

  [HRLR zone]                [LRLR zone]
  ┌─────────┐                    ↗ cepat, impulsif
  │choppy   │ ──── breakout ────►
  │ranging  │
  └─────────┘
  Banyak order      Sedikit order berlawanan
  dua arah          → harga meluncur cepat

LRLR → HRLR (Masuk ke area well-traded):
  Setelah LRLR, harga bertemu area konsolidasi lama
  → Kembali menjadi choppy (HRLR)
  → Butuh breakout baru untuk masuk LRLR berikutnya
```

---

## Identifikasi Zona

### HRLR (High Resistance)
```pinescript
// Konsolidasi: range sempit dalam N bar
int   n          = 20
float h          = ta.highest(high, n)
float l          = ta.lowest(low, n)
float range_size = h - l
bool  is_hrlr    = range_size < ta.atr(14) * n * 0.35
```

### LRLR (Low Resistance)
```pinescript
// Area impulsif: FVG aktif atau candle besar tanpa retracement
bool  is_lrlr    = bull_fvg_active or bear_fvg_active or
                   (math.abs(close - open) > ta.atr(14) * 1.5)
```

---

## Cara Pakai dalam Trading

```
HRLR → LRLR Breakout (entry saat transisi):
  1. Identifikasi HRLR (konsolidasi / range)
  2. Tunggu harga keluar dari HRLR (breakout + close di luar range)
  3. Breakout ke atas + BSL terambil → LRLR bullish
  4. Breakout ke bawah + SSL terambil → LRLR bearish
  5. Entry saat retracement ke FVG yang terbentuk saat breakout
  6. Target: ERL berikutnya (HRLR atau liquidity pool baru)

LRLR sebagai target:
  - Jangan entry DI DALAM LRLR — harga sudah bergerak
  - Gunakan LRLR sebagai konfirmasi bahwa harga sedang dalam momentum
  - Cari entry di retracement ke IRL setelah LRLR terbentuk
```

---

## Perbedaan HRL/LRL vs HRLR/LRLR

| Aspek | HRL / LRL | HRLR / LRLR |
|---|---|---|
| **Fokus** | Karakteristik *zona* | Karakteristik *pergerakan* |
| **Konteks** | Area statis di chart | Dinamika pergerakan harga |
| **Penggunaan** | Entry vs hindari | Momentum dan breakout |
| **Suffix "R"** | Tidak ada | Run = pergerakan aktif |

---

## Catatan Tambahan

- HRLR sering terjadi di area **equilibrium (50%)** — banyak dua arah order di sana.
- LRLR sering dimulai setelah **liquidity sweep** yang sukses → momentum bersih.
- Dalam trending market: **LRLR → HRLR → LRLR** berulang (ekspansi → konsolidasi → ekspansi).
- HRLR yang panjang = **akumulasi besar** → potensi LRLR yang lebih kuat setelahnya.

---

---

# AMD Pattern (Accumulation-Manipulation-Distribution) Logic

## Definisi

AMD adalah **tiga fase utama pergerakan harga** yang menggambarkan siklus lengkap dari konsolidasi hingga distribusi. Digunakan untuk memahami konteks pasar secara intraday.

---

## Tiga Fase

### 1. Accumulation (Asia Session)
```
Pasar bergerak sideways di dekat harga pembukaan.
Smart money mengakumulasi posisi.
Retail menempatkan order di support (buy) dan resistance (sell).
Stop-loss retail: di atas high dan di bawah low range.
```

### 2. Manipulation (London Open / Judas Swing)
```
Harga bergerak berlawanan arah dari distribusi sebenarnya:
- Bullish day → harga dulu turun (sweep SSL) → baru naik
- Bearish day → harga dulu naik (sweep BSL) → baru turun

= False breakout dari Accumulation range
= Likuiditas retail terharvest
```

### 3. Distribution (NY Session)
```
Arah sebenarnya: harga bergerak mengikuti bias HTF.
Smart money mendistribusikan posisi ke arah target.
```

---

## Formasi

```
Bullish AMD:
  [Asia: range sempit] → [London: sweep SSL ke bawah] → [NY: naik ke BSL]

Bearish AMD:
  [Asia: range sempit] → [London: sweep BSL ke atas] → [NY: turun ke SSL]
```

---

## Implementasi Pine Script

```pinescript
// Identifikasi Asia range (07:00 PM - 12:00 AM NY time)
// Accumulation = range sempit
float asia_high = ta.highest(high, asia_bars)
float asia_low  = ta.lowest(low, asia_bars)
float asia_mid  = math.avg(asia_high, asia_low)

// Manipulation = breakout palsu dari Asia range
bool manip_up   = high > asia_high and close < asia_high  // spike atas → balik
bool manip_down = low  < asia_low  and close > asia_low   // spike bawah → balik

// Distribution = harga berlanjut pasca-manipulation
bool dist_bull  = manip_down and close > asia_high        // MSS ke atas
bool dist_bear  = manip_up   and close < asia_low         // MSS ke bawah
```

---

## Catatan Tambahan

- AMD bukan pola candle — ini adalah **framework sesi harian**.
- Manipulation phase = **Judas Swing** (lihat section terpisah).
- Distribution phase dimulai setelah MSS di LTF (1M/5M).

---

---

# Market Structure Shift (MSS) Logic

## Definisi

MSS adalah **sinyal awal pembalikan tren** — break of swing high atau swing low **disertai displacement move**. Berbeda dari BOS (Break of Structure) yang terjadi di arah tren; MSS terjadi **melawan tren** yang sedang berjalan.

---

## Formasi

```
Bullish MSS (sinyal dari bearish ke bullish):
  Downtrend → terbentuk LL → harga naik → close DI ATAS swing high sebelumnya
  → displacement candle menembus HH dengan body besar
  → MSS Bullish confirmed

Bearish MSS (sinyal dari bullish ke bearish):
  Uptrend → terbentuk HH → harga turun → close DI BAWAH swing low sebelumnya
  → displacement candle menembus LL dengan body besar
  → MSS Bearish confirmed
```

---

## Kondisi Terbentuk

### Bullish MSS
```
1. Harga dalam downtrend (HL/LL)
2. Terbentuk swing low baru
3. Harga rebound → close di atas swing high terdekat
4. Candle displacement: body besar, arah bullish
→ MSS Bullish = potensi reversal ke atas
```

### Bearish MSS
```
1. Harga dalam uptrend (HH/HL)
2. Terbentuk swing high baru
3. Harga retrace → close di bawah swing low terdekat
4. Candle displacement: body besar, arah bearish
→ MSS Bearish = potensi reversal ke bawah
```

---

## Perbedaan MSS vs BOS vs CHoCH

| Sinyal | Arah | Konteks |
|---|---|---|
| **BOS** | Searah tren | Konfirmasi kelanjutan tren |
| **CHoCH** | Melawan tren | Perubahan struktur minor |
| **MSS** | Melawan tren + displacement | Sinyal reversal kuat |

> MSS = CHoCH dengan **displacement candle** yang konfirmasi perubahan delivery.

---

## Implementasi Pine Script

```pinescript
// Swing high/low sederhana
bool is_swing_high = high[2] > high[3] and high[2] > high[1]
bool is_swing_low  = low[2]  < low[3]  and low[2]  < low[1]

// Bullish MSS: close di atas swing high sebelumnya dengan displacement
bool bull_mss = is_swing_low[n] and
                close > swing_high_prev and
                (close - open) > ta.atr(14) * 1.0  // displacement

// Bearish MSS: close di bawah swing low sebelumnya dengan displacement
bool bear_mss = is_swing_high[n] and
                close < swing_low_prev and
                (open - close) > ta.atr(14) * 1.0
```

---

## Catatan Tambahan

- MSS sering terjadi **setelah liquidity sweep** (Judas Swing / Turtle Soup).
- MSS di LTF (1M/5M) digunakan sebagai **entry trigger** setelah HTF setup terbentuk.
- MSS tanpa displacement → lemah, bisa jadi hanya CHoCH biasa.

---

## Prinsip Kritis MSS (dari ICT 2022 Mentorship)

> **"Structure means nothing without narrative. Lose sight of the draw on price, HTF structure, and resting liquidity and you're vulnerable to being faked out."** — ICT

**Bearish MSS** secara detail:
- Market akan melihat Price deliver rally **di atas Old High atau beberapa Old Highs**
- Kemudian **DENGAN CEPAT** shift lower
- Kata kunci: "quick" — harus dengan Displacement Lower
- Bukan small candle move lower atau wick saja — harus ada **candle close** yang menyertainya

**Bullish MSS** secara detail:
- Market akan melihat Price deliver penurunan **di bawah Old Low atau beberapa Old Lows**
- Kemudian **DENGAN CEPAT** shift higher
- Harus ada Displacement Higher — bukan wick atau small candle saja

**POI di mana MSS terjadi:**
- Area Support & Resistance: session highs/lows (Asian 8pm-12am, London 2am-5am, NY 7am-10am)
- Significant MSS **selalu bertepatan dengan price mengambil likuiditas** di market
- Ketika price menembus S/R kunci → menarik lebih banyak partisipasi trader dan algoritma

**Timeframe untuk Liquidity Highs/Lows:**
| Session | EST | Signifikansi |
|---|---|---|
| Asian Session | 08:00 PM – 12:00 AM | Identifikasi liquidity pools + S/R Asian |
| London Session | 02:00 AM – 05:00 AM | Liquidity pools + key S/R London |
| NY Session | 07:00 AM – 10:00 AM | Liquidity pools + S/R NY kritis |

**HFT Algorithms & MSS:**
- High-frequency trading algorithms beroperasi di timeframe pendek: **5M, 4M, 3M, 2M, 1M**
- Algoritma ini memanfaatkan informasi Market Structure untuk kapital pada pergerakan jangka pendek
- MSS yang potensial dapat meningkatkan volatilitas karena partisipasi algoritma HFT

> **Jangan force atau antisipasi MSS tanpa sinyal valid.** Tunggu price action memvalidasi analisis sebelum menganggap terjadi MSS.

---

---

# CISD (Change in State of Delivery) Logic

## Definisi

CISD adalah **perubahan arah price delivery** — dari bullish delivery ke bearish delivery atau sebaliknya. Ditandai oleh candle yang menutup **di atas open candle bearish** (untuk bullish CISD) atau **di bawah open candle bullish** (untuk bearish CISD).

---

## Konsep Dasar

ICT membagi pergerakan harga menjadi dua mode delivery:
- **Bullish Delivery**: harga disampaikan ke atas (target BSL / buy side)
- **Bearish Delivery**: harga disampaikan ke bawah (target SSL / sell side)

CISD = titik di mana algoritma **berpindah mode delivery**.

---

## Formasi

```
Bullish CISD:
  Harga sedang dalam bearish delivery (turun)
  → Candle close DI ATAS open candle bearish sebelumnya
  → Delivery berubah ke bullish

Bearish CISD:
  Harga sedang dalam bullish delivery (naik)
  → Candle close DI BAWAH open candle bullish sebelumnya
  → Delivery berubah ke bearish
```

---

## Kondisi Terbentuk

### Bullish CISD
```
close[0] > open[n]   // di mana candle[n] adalah bearish candle dalam delivery sebelumnya
```
- Candle saat ini menutup di atas open dari candle bearish sebelumnya
- Sinyal: algoritma berpindah ke bullish delivery

### Bearish CISD
```
close[0] < open[n]   // di mana candle[n] adalah bullish candle dalam delivery sebelumnya
```
- Candle saat ini menutup di bawah open dari candle bullish sebelumnya
- Sinyal: algoritma berpindah ke bearish delivery

---

## Implementasi Pine Script

```pinescript
// Bullish CISD: close saat ini di atas open candle bearish sebelumnya
bool prev_bearish = close[1] < open[1]
bool bull_cisd    = prev_bearish and close > open[1]

// Bearish CISD: close saat ini di bawah open candle bullish sebelumnya
bool prev_bullish = close[1] > open[1]
bool bear_cisd    = prev_bullish and close < open[1]
```

---

## Perbedaan CISD vs MSS

| Aspek | CISD | MSS |
|---|---|---|
| **Fokus** | Perubahan mode delivery | Perubahan struktur tren |
| **Konfirmasi** | Close melampaui open candle sebelumnya | Close melampaui swing high/low |
| **Timeframe** | Berlaku di semua TF | Umumnya LTF untuk entry trigger |
| **Penggunaan** | Entry trigger setelah Asian Range sweep | Entry trigger setelah liquidity hunt |

---

## Cara Pakai

```
1. Identifikasi directional bias (HTF)
2. Tunggu liquidity sweep (sweep Asia high/low atau swing H/L)
3. Cari CISD di LTF (1M atau 5M) setelah sweep
4. Entry saat CISD konfirmasi + ada FVG/OB sebagai area entry
5. Target: Draw on Liquidity berikutnya (ERL)
```

---

---

# Turtle Soup Logic

## Definisi

Turtle Soup adalah **pola false breakout dari key level** — harga seolah menembus support/resistance, kemudian langsung berbalik. Bukan breakout sejati, melainkan **liquidity hunt terhadap stop order retail**.

---

## Konsep

Dua alasan utama harga bergerak (ICT):
1. Menyeimbangkan imbalance (FVG)
2. Mengambil likuiditas

Turtle Soup memanfaatkan poin 2: setelah likuiditas diambil, harga berbalik untuk menyeimbangkan imbalance atau mengambil likuiditas sisi lain.

---

## Formasi

```
Bullish Turtle Soup (dari sweep SSL):
  Key support level terbentuk (equal lows / swing low)
  → Harga turun menembus support (SSL terambil)
  → Harga langsung berbalik naik (close kembali di atas level)
  → Entry: long setelah reversal confirmed

Bearish Turtle Soup (dari sweep BSL):
  Key resistance level terbentuk (equal highs / swing high)
  → Harga naik menembus resistance (BSL terambil)
  → Harga langsung berbalik turun (close kembali di bawah level)
  → Entry: short setelah reversal confirmed
```

---

## Kondisi Terbentuk

```
1. Identifikasi key level: equal highs, equal lows, PDH, PDL, round number
2. Harga menembus level tersebut (spike/wick melewati level)
3. Candle close KEMBALI ke dalam range (tidak konfirmasi breakout)
4. Terbentuk MSS atau CISD di LTF → entry
5. Target: likuiditas sisi berlawanan atau FVG terdekat
```

---

## Implementasi Pine Script

```pinescript
// Key level: equal lows (2 swing low berdekatan)
float swing_low_1 = ...
float swing_low_2 = ...
bool  equal_lows  = math.abs(swing_low_1 - swing_low_2) < ta.atr(14) * 0.1

// Bullish Turtle Soup: spike bawah, close kembali di atas level
bool bull_turtle = low < swing_low_1 and close > swing_low_1

// Bearish Turtle Soup: spike atas, close kembali di bawah level
bool bear_turtle = high > swing_high_1 and close < swing_high_1
```

---

## Catatan Tambahan

- Turtle Soup efektif di **ranging market** (konsolidasi) karena banyak equal highs/lows terbentuk.
- Konfirmasi: gunakan MSS atau CISD di 1M/5M setelah sweep.
- Paling kuat saat terjadi di **premium/discount zone** (OTE 62%-79%).
- Identik dengan konsep **Liquidity Sweep** yang diikuti reversal.

---

---

# Asian Range Logic

## Definisi

Asian Range adalah **range harga sesi Asia** (07:00 PM – 12:00 AM NY Time / 00:00 – 05:00 GMT). Range ini sering menjadi **setup zone** untuk pergerakan London dan NY session.

---

## Karakteristik

- Volatilitas rendah → range sempit
- USD biasanya konsolidasi
- Pasangan aktif: AUD, NZD, JPY (cross pairs)
- Range Asia sering di-**sweep** di London/NY session

---

## Cara Pakai

```
1. Tandai high dan low sesi Asia
2. Tentukan directional bias dari HTF
3. Tunggu liquidity sweep di London/NY session:
   - Jika Asian Low terambil → cari CISD/MSS di LTF → BUY
   - Jika Asian High terambil → cari CISD/MSS di LTF → SELL
4. Entry saat konfirmasi reversal
5. Target: sisi berlawanan dari Asian Range atau ERL berikutnya
```

---

## Formasi

```
Bullish Setup:
  [Asia: konsolidasi] → [London: spike bawah ambil Asian Low (SSL)]
  → [CISD/MSS di 1M] → [Entry Long] → [Target: Asian High / BSL]

Bearish Setup:
  [Asia: konsolidasi] → [London: spike atas ambil Asian High (BSL)]
  → [CISD/MSS di 1M] → [Entry Short] → [Target: Asian Low / SSL]
```

---

## Implementasi Pine Script

```pinescript
// Waktu sesi Asia: 19:00 - 00:00 NY time (UTC-5)
// Dalam UTC: 00:00 - 05:00
int asia_start_h = 0   // UTC
int asia_end_h   = 5   // UTC

bool in_asia = (hour >= asia_start_h and hour < asia_end_h)

var float asia_high = na
var float asia_low  = na

if in_asia
    asia_high := na(asia_high) ? high : math.max(asia_high, high)
    asia_low  := na(asia_low)  ? low  : math.min(asia_low,  low)

// Sweep detection (setelah Asia session)
bool sweep_asia_low  = low  < asia_low  and close > asia_low   // bullish reversal
bool sweep_asia_high = high > asia_high and close < asia_high  // bearish reversal
```

---

---

# ICT Macros Logic

## Definisi

ICT Macro adalah **interval waktu pendek** di mana algoritma secara aktif mencari likuiditas atau mengisi FVG. Terjadi pada setiap jam: **10 menit terakhir jam sebelumnya + 10 menit pertama jam berikutnya**.

---

## Konsep

> "ICT macro happens in every single hour containing last 10 minutes of closing hour and first 10 minutes of opening hour." — Michael Huddleston, 2024 Mentorship

- Selama macro: algoritma hunt likuiditas (BSL/SSL) atau reprice FVG
- Setup bisa terbentuk **sebelum** macro → macro memberikan volatilitas untuk eksekusi
- Setup bisa terbentuk dan hit target **selama** macro

---

## ICT Macro Times (EST / NY Time)

| Session | Macro Window (EST) |
|---|---|
| **London** | 02:33 – 03:00, 04:03 – 04:30 |
| **NY AM** | 08:50 – 09:10, 09:50 – 10:10, 10:50 – 11:10 |
| **NY Lunch** | 11:50 – 12:10 |
| **NY PM** | Setiap jam: xx:50 – (xx+1):10 |
| **Last Hour** | 4 macros @ setiap 15 menit |

> Prinsip umum: **:50 – :10** di setiap jam (±10 menit dari pergantian jam)

---

## Implementasi Pine Script

```pinescript
// Macro window: 10 menit terakhir + 10 menit pertama setiap jam
bool in_macro = (minute >= 50) or (minute <= 10)

// Lebih presisi: tandai macro windows spesifik
bool london_macro1 = (hour == 2  and minute >= 33) or (hour == 3  and minute <= 0)
bool london_macro2 = (hour == 4  and minute >= 3)  or (hour == 4  and minute <= 30)
bool ny_macro1     = (hour == 8  and minute >= 50) or (hour == 9  and minute <= 10)
bool ny_macro2     = (hour == 9  and minute >= 50) or (hour == 10 and minute <= 10)
bool ny_macro3     = (hour == 10 and minute >= 50) or (hour == 11 and minute <= 10)

bool in_ict_macro  = london_macro1 or london_macro2 or ny_macro1 or ny_macro2 or ny_macro3
```

---

## Cara Pakai

```
1. Tandai BSL/SSL terdekat di 15M sebelum macro dimulai
2. Saat macro aktif: perhatikan apakah harga sweep salah satu sisi
3. Setelah sweep + MSS/CISD di 1M → entry
4. Target: FVG terdekat atau sisi likuiditas berlawanan
```

---

---

# Silver Bullet Strategy Logic

## Definisi

Silver Bullet adalah **strategi scalp berbasis waktu** yang terjadi **3 kali sehari dalam jendela 1 jam**. Berdasarkan liquidity sweep + FVG entry. Berulang setiap hari dan dapat menghasilkan 20-30 pips.

---

## Silver Bullet Times (EST / NY Time)

| Session | Window |
|---|---|
| **London Silver Bullet** | 03:00 AM – 04:00 AM EST |
| **NY AM Silver Bullet** | 10:00 AM – 11:00 AM EST |
| **NY PM Silver Bullet** | 02:00 PM – 03:00 PM EST |

---

## Cara Kerja

```
1. Sebelum silver bullet window:
   → Tandai BSL dan SSL terdekat di 15M

2. Saat window aktif:
   → Jika harga sweep satu sisi → kemungkinan ke sisi berlawanan
   → Jika harga run (close melampaui level) → kemungkinan lanjut ke level berikutnya

3. Setelah sweep/run:
   → Cari MSS di 1M atau 3M ke arah Draw on Liquidity

4. Setelah MSS:
   → Cari FVG yang terbentuk sebelum/saat MSS
   → Entry saat harga retest FVG (gunakan Premium/Discount untuk pilih FVG terbaik)

5. Target: Draw on Liquidity berikutnya (BSL/SSL/ERL)
```

---

## Implementasi Pine Script

```pinescript
// Silver Bullet windows (EST = UTC-5)
// London: 03:00-04:00 EST = 08:00-09:00 UTC
bool sb_london = (hour == 8  and minute >= 0 and minute < 60)  // UTC

// NY AM:  10:00-11:00 EST = 15:00-16:00 UTC
bool sb_ny_am  = (hour == 15 and minute >= 0 and minute < 60)  // UTC

// NY PM:  14:00-15:00 EST = 19:00-20:00 UTC
bool sb_ny_pm  = (hour == 19 and minute >= 0 and minute < 60)  // UTC

bool in_silver_bullet = sb_london or sb_ny_am or sb_ny_pm
```

---

## Catatan Tambahan

- Silver Bullet bukan hanya "trading di jam tertentu" — butuh **konfirmasi MSS + FVG**.
- Paling efektif saat ada **clear Draw on Liquidity** di HTF sebelum window.
- SIBI (bearish FVG) digunakan untuk sell entry; BISI (bullish FVG) untuk buy entry.
- **SIBI** = FVG yang terbentuk dari candle yang close turun (down-close FVG).
- **BISI** = FVG yang terbentuk dari candle yang close naik (up-close FVG).

---

---

# ICT Kill Zones Logic

## Definisi

Kill Zones adalah **periode waktu dengan volume tinggi dan volatilitas tinggi** — ketika institutional trader paling aktif. Kill zones terbentuk dari overlap antar session (London, New York, Asia). Memberikan probabilitas setup terbaik dalam sehari.

> "Algorithmic theory is based on time and price. Price levels are useless until Time is considered. Time is of no use unless Price is at a key PD array. Blending the two yield astonishing results and precision." — ICT

---

## Tabel Sesi & Kill Zone Lengkap (EST / NY Time)

| Session / Event | EST | Keterangan |
|---|---|---|
| **IPDA Reset** | 00:00 | Algorithmic reset — New Day Open |
| **London Open Kill Zone** | 02:00 – 05:00 | Volume tertinggi, EUR/GBP |
| **NY AM Session** | 07:00 – 10:00 | High Impact data, US market open |
| **US High Impact Data** | 08:30 | NFP, CPI, dll — paling volatile |
| **London Close** | 10:00 – 12:00 | Retracement/close posisi London |
| **NY Lunch** | 12:00 – 13:00 | Low volume, hindari trading |
| **NY PM Session** | 14:00 – 16:00 | Continuation atau reversal minor |
| **CBDR** | 14:00 – 20:00 | Central Bank Dealers Range |
| **Asian Kill Zone** | 20:00 – 00:00 | Konsolidasi, AUD/NZD/JPY |

---

## Karakteristik Tiap Kill Zone

### Asian Kill Zone (08:00 PM – 00:00 EST)
- **Pairs terbaik**: AUD, NZD, JPY — USD pairs kurang aktif saat ini
- Asian Range **menentukan directional bias** untuk sesi London & NY hari itu
- Di mana uang diposisikan di Asian → menentukan profit release pada trading day
- Asian session trades biasanya **counter NY session direction** (corrective bounce)
- Biasanya: konsolidasi sempit → accumulate liquidity di atas/bawah swing terdekat
- **AUDJPY Model**:
  - Bullish bias: cari **15m swing low dijalankan antara 8pm–12am EST** → Turtle Soup Long
  - Bearish bias: cari **15m swing high dijalankan antara 8pm–12am EST** → Turtle Soup Short
- Narrow Asian range = **setup Big Trending Model** (1-2 std dev play) untuk sesi berikutnya

### London Open Kill Zone (02:00 AM – 05:00 AM EST)
- **Volume tertinggi dalam 24 jam** — institusi paling aktif
- Asian session telah mengakumulasi likuiditas → London datang dengan volume besar
- **Goal London**: set High of Day (jika bullish) atau Low of Day (jika bearish)
- **Model tipikal (Bearish London)**:
  - Objective: dial in ke High of Day untuk go short
  - First objective after London Open: **raid Asian Low stops**
  - Setelah stops diambil → harga naik testing key resistance + OTE
  - Sell **above opening price** dan **above Asian Session Swing High**
- **Turtle Soup sebelum Judas Swing**:
  - Turtle Soup = fake-out awal di luar Asian range sebelum Judas Swing sebenarnya
  - Setelah Turtle Soup → Judas Swing mulai dan break Asian Session High/Low ke key S/R
  - Pada move ke key S/R → antisipasi harga retest Asian range (terkadang pullback minor/tidak ada)
- **Key S/R yang relevan**: HTF OB, Previous Day H/L, Previous Week H/L, Week Open, New Day Open, Previous Day NY H/L

### Kapan HINDARI London Open
- Interest rate announcement
- Key Speeches (gubernur bank sentral)
- Holiday trading
- Global and Economic events
- Weekly Range objective sudah tercapai
- Jika Asian Session range **lebih dari 15-20 points (ES/SP500)** → tunggu NY trade lebih aman

### NY AM Kill Zone (07:00 AM – 10:00 AM EST)
- High of Day atau Low of Day **bisa juga terbentuk di NY Open**
- NY session adalah yang **paling mudah** ditrade — London session high/low sudah terbentuk
- Pertimbangkan **HTF Premise** untuk arah
- Monitor **Asian dan London session highs/lows** — harga sering bounce dari level ini
- Mayoritas waktu price akan **sinkron dengan arah HTF**
- Price akan retest high/low yang terbentuk di London (sebagai liquidity pool)
- **Dua skenario NY**:
  1. **Continuation** of London's Move
  2. **Complete Reversal** of daily direction
- Jika Asian+London range sempit dan akumulasi likuiditas → NY bisa membuat **withdrawal dari range** ini → HOD (bearish) atau LOD (bullish)

### London Close Kill Zone (10:00 AM – 12:00 PM EST)
- Trader menutup posisi London → retracement ke daily range
- Bullish day: setelah HOD terbentuk → harga retrace ke range
- Bearish day: setelah LOD terbentuk → harga retrace ke range
- Setup scalp 15-20 pips di 5M

### NY Lunch (12:00 PM – 01:00 PM EST)
- Volume sangat rendah → **hindari trading**
- Harga choppy, false signals tinggi
- Tunggu NY PM Session untuk lanjutkan

### CBDR — Central Bank Dealers Range (02:00 PM – 08:00 PM EST)
- Range yang dibentuk oleh Central Bank Dealers
- Sama seperti Asian Range — bisa digunakan untuk proyeksi 1-2 std dev
- Accumulation sebelum sesi Asia berikutnya

---

## Inter-Session Relationship

```
Skenario Bullish Day:
  Asian KZ: Konsolidasi → akumulasi SSL di bawah Asian Low
  London KZ: Raid Asian Low stops (LOD terbentuk) → harga naik
  NY KZ: Uptrend berlanjut → HOD terbentuk atau retest London Low

Skenario Bearish Day:
  Asian KZ: Konsolidasi → akumulasi BSL di atas Asian High
  London KZ: Raid Asian High stops (HOD terbentuk) → harga turun
  NY KZ: Downtrend berlanjut → LOD terbentuk atau retest London High

Jika London set LOD:
  → NY uptrend berlanjut, exit ke next bearish POI atau upper liquidity zone
Jika London set HOD:
  → NY downtrend berlanjut, exit ke next bullish POI atau lower liquidity zone
```

---

## Implementasi Pine Script

```pinescript
// Kill Zone detection (UTC offset = EST + 5)
bool ipda_reset   = (hour == 5)                           // 00:00 EST = 05:00 UTC
bool asian_kz     = (hour >= 1  and hour < 5)             // 20:00-00:00 EST = 01:00-05:00 UTC
bool london_kz    = (hour >= 7  and hour < 10)            // 02:00-05:00 EST = 07:00-10:00 UTC
bool ny_am_kz     = (hour >= 12 and hour < 15)            // 07:00-10:00 EST = 12:00-15:00 UTC
bool ldn_close_kz = (hour >= 15 and hour < 17)            // 10:00-12:00 EST = 15:00-17:00 UTC
bool ny_lunch     = (hour >= 17 and hour < 18)            // 12:00-13:00 EST = 17:00-18:00 UTC
bool ny_pm_kz     = (hour >= 19 and hour < 21)            // 14:00-16:00 EST = 19:00-21:00 UTC
bool cbdr_kz      = (hour >= 19 and hour < 25)            // 14:00-20:00 EST (overlap NY PM)

bool in_kill_zone = asian_kz or london_kz or ny_am_kz or ldn_close_kz
bool avoid_zone   = ny_lunch
```

---

## Catatan Tambahan

- Jangan trading **di luar kill zone** — probabilitas setup rendah.
- Kill zone **bukan** berarti langsung entry — tetap butuh setup (liquidity sweep + MSS/CISD + FVG).
- London KZ = Kill Zone dengan **probabilitas directional move terbesar** dalam sehari.
- NY AM KZ = paling mudah karena London sudah set HOD/LOD — tinggal konfirmasi.
- **IPDA Reset (00:00 EST)** = algorithmic new day — biasanya ada shift kecil di harga yang menandai day open.
- Asian range yang **sempit** = sinyal potensi big trending move di London/NY.

---

---

# ICT 2022 Mentorship Model Logic

## Definisi

ICT 2022 Mentorship Model adalah **framework entry lengkap 6 langkah** yang menggabungkan liquidity grab, displacement, MSS, Fibonacci, dan FVG menjadi satu sistem terpadu. Ini adalah model inti dari ICT 2022 Mentorship.

---

## 6 Langkah Model

```
1. LIQUIDITY GRAB
   Harga sweep major BSL atau SSL:
   - Asian High/Low
   - London High/Low
   - Previous Day(s) High/Low
   - Obvious swing highs/lows di HTF

2. DISPLACEMENT + FVG
   Harga berbalik arah dan menciptakan FVG dengan displacement.
   - Displacement HARUS ada (bukan reversal biasa)
   - Displacement = candle besar energetik = bukti institutional sponsorship
   - FVG terbentuk dari displacement ini

3. MARKET STRUCTURE SHIFT (MSS)
   Swing high atau swing low harus HORIZONTAL ke salah satu
   dari 3 candle yang membentuk FVG.
   - MSS bisa terbentuk SEBELUM atau SESUDAH liquidity grab
   - Kedua-duanya valid

4. FIBONACCI 50% (dua opsi)
   Opsi A: Tarik dari swing yang mengambil likuiditas ke swing di sisi lain FVG
   Opsi B: Tarik dari swing prominent setelah likuiditas diambil ke swing di sisi lain FVG

5. VALIDASI FVG
   Gap dalam FVG harus berada DI atau di sisi yang lebih baik dari garis 50% Fibonacci.
   - Bullish setup: FVG harus di bawah atau pada 50%
   - Bearish setup: FVG harus di atas atau pada 50%

6. ENTRY
   Place limit order di dalam FVG:
   - Di level Equilibrium (50% FVG) atau
   - Di sisi yang lebih baik dari Equilibrium (deeper into FVG)
```

---

## Formasi Visual

```
Bearish Setup (dari sweep BSL):
                    [BSL terambil → spike atas]
                           ↓ displacement + FVG
  Old High ────────────────[MSS: swing ini horizontal ke candle FVG]
                    [FVG di bawah 50% Fibonacci]
                           ↓ harga retrace ke FVG
                    → Entry SHORT di FVG / 50% Fibonacci

Bullish Setup (dari sweep SSL):
  [SSL terambil → spike bawah]
         ↓ displacement + FVG
  Old Low ─────────────────[MSS: swing ini horizontal ke candle FVG]
                    [FVG di atas 50% Fibonacci]
                         ↓ harga retrace ke FVG
                    → Entry LONG di FVG / 50% Fibonacci
```

---

## Displacement Range

**Displacement Range** = range antara displacement high dan displacement low:
- **Displacement Low** = short-term low yang ditembus saat bearish move
- **Displacement High** = old high yang menjadi batas atas
- Range di antara keduanya = **Displacement Range**

FVG yang valid harus berada di dalam atau pada batas displacement range ini.

---

## Implementasi Pine Script

```pinescript
// Step 1-2: Liquidity grab + FVG detection
bool bsl_swept    = high > prev_swing_high and close < prev_swing_high  // sweep BSL, close balik
bool ssl_swept    = low  < prev_swing_low  and close > prev_swing_low   // sweep SSL, close balik

// Displacement: candle besar setelah sweep
bool is_displacement = (math.abs(close - open) > ta.atr(14) * 1.5) and
                       (math.abs(close - open) / (high - low) > 0.6)

// FVG setelah displacement (bearish case)
bool bear_fvg_disp = bear_fvg and is_displacement[1]

// Step 4-5: Fibonacci 50% dari swing ke swing
float fib_50  = math.avg(swing_high, swing_low)  // 50% level

// FVG valid jika gap berada di atas 50% (bearish: resistance sisi atas)
bool fvg_valid_bear = bear_fvg_bot >= fib_50   // gap di atas 50% = di sisi yang lebih baik

// Step 6: Entry
float entry_bear = math.avg(bear_fvg_top, bear_fvg_bot)  // CE dari FVG
```

---

## Catatan Tambahan (dari Lumitraders)

- Jika ada **old FVG di atas pasar** dan harga bergerak naik lalu masuk FVG → tidak perlu close sempurna. Gunakan **low dari FVG lama sebagai trigger point** (sama seperti relative equal highs).
- Setelah harga masuk FVG, kembali ke leg tersebut dan identifikasi trigger + displacement leg untuk menemukan FVG entry yang lebih presisi.
- Missing a move ≠ losing trade. Fokus pada learning, bukan FOMO.

---

---

# ICT ATM Method Logic

## Definisi

ICT ATM (Automatic Trading Method) adalah **setup standalone price action** yang memanfaatkan stop hunt (liquidity grab) untuk entry. Cocok di **60 menit chart**, bisa ditemukan di timeframe apapun. Bukan strategi lengkap — butuh konteks HTF.

---

## Karakteristik

- Pattern independen (tidak memerlukan bias HTF khusus, meskipun tetap membantu)
- Beroperasi dari **stop hunt** (liquidity grab)
- Sangat visual dan mudah diidentifikasi
- Paling efektif di timeframe **1 jam**

---

## Bearish ATM Setup

```
Setup:
  1. Scan 1H chart untuk KEY HIGH:
     - Terbentuk setelah pair swing highs ditembus ke atas
     - Short-term swing low ditembus ke bawah setelah key high
  2. Tunggu harga pullback ke BREAKER BLOCK
     (Breaker = semua up-close candle sebelum drop yang menembus 2 short-term lows)
  3. Entry SHORT saat harga menyentuh Breaker Block

  Stop Loss  : Di atas Breaker Block (atau di atas key high)
  Take Profit: Di bawah Short-Term Low ke-2 (yang ditembus)
```

---

## Bullish ATM Setup

```
Setup:
  1. Scan 1H chart untuk KEY LOW:
     - Terbentuk setelah pair swing lows ditembus ke bawah
     - Short-term swing high ditembus ke atas setelah key low
  2. Tunggu harga pullback ke BREAKER BLOCK
     (Breaker = semua down-close candle sebelum naik yang menembus 2 short-term highs)
  3. Entry LONG saat harga menyentuh Breaker Block

  Stop Loss  : Di bawah Breaker Block (atau di bawah key low)
  Take Profit: Di atas Short-Term High ke-2 (yang ditembus)
```

---

## Formasi Visual

```
Bearish ATM:
       [2] ← Key High (stop loss di sini)
      /   \
   [1]    [Breaker Block] ← entry short
           \
           [STL 1]
            \
            [STL 2] ← take profit di bawah sini

Bullish ATM:
           [STH 2] ← take profit di atas sini
            /
           [STH 1]
           /
   [Breaker Block] ← entry long
      \   /
       [1]
      [2] ← Key Low (stop loss di sini)
```

---

## Implementasi Pine Script

```pinescript
// ATM Setup detection (bearish)
// Key high: swing high setelah 2 swing highs sebelumnya ditembus
bool key_high = is_swing_high and high > swing_high_prev1 and high > swing_high_prev2

// Short-term low ditembus setelah key high
bool stl_broken = key_high[n] and low < swing_low_prev

// Breaker Block: last down-close candle sebelum drop (setelah key high)
bool breaker_bear = close < open  // down-close candle
// Zona: high dan low dari candle tersebut

// Entry: harga retrace ke breaker block zone
bool atm_bear_entry = stl_broken and high >= bear_breaker_bot and high <= bear_breaker_top

// SL / TP
float sl_bear = bear_breaker_top + ta.atr(14) * 0.1   // di atas breaker
float tp_bear = stl2_low - ta.atr(14) * 0.1           // di bawah STL ke-2
```

---

## Perbedaan ATM vs ICT 2022 Model

| Aspek | ICT 2022 Model | ICT ATM Method |
|---|---|---|
| **Kompleksitas** | 6 langkah, butuh validasi FVG + Fib | 3 langkah, lebih sederhana |
| **Timeframe** | Fleksibel (dengan HTF bias) | Terutama 1H chart |
| **Entry zona** | FVG di 50% Fibonacci | Breaker Block |
| **Konteks** | Butuh HTF bias + kill zone | Standalone (tapi konteks membantu) |
| **Target** | Draw on Liquidity (ERL) | Short-Term Low/High ke-2 |

---

## Catatan Tambahan

- ATM paling kuat saat terjadi di **kill zone** (London atau NY AM).
- Gunakan **ATM sebagai filter**: jika tidak ada ATM setup valid di kill zone → skip trading hari itu.
- Jika ada **confluence dengan ICT 2022 Model** (misal ATM entry bertepatan dengan FVG + 50% Fib) → probabilitas lebih tinggi.
- **Scan dulu di 1H**, baru zoom ke 15M atau 5M untuk entry yang lebih presisi.

---

---

# Economic Calendar & Weekly News Pattern Logic

## Definisi

Economic Calendar adalah **panduan jadwal event ekonomi** (interest rate, GDP, CPI, employment) yang berdampak signifikan pada pergerakan harga. Trader ICT menggunakannya untuk memahami kapan volatilitas akan terjadi dan bagaimana market akan bereaksi.

---

## Impact Color Coding

| Warna | Impact | Prioritas |
|---|---|---|
| **Red** | High Impact | Fokus utama |
| **Orange** | Medium Impact | Perhatikan |
| **Yellow** | Low Impact | Kurang signifikan |

> Fokus pada **High dan Medium Impact** news events saja.

---

## Weekly News Pattern (AMD Mingguan)

```
SENIN — Accumulation
  - Tidak ideal untuk day trade
  - Weekly Open → market ranging
  - Small trading range
  - Judas Swing dari Weekly Open (manipulasi awal)
  - Bearish Bias: cari raid of PWH / HTF POI
  - Bullish Bias: cari raid of PWL / HTF POI

SELASA — Manipulation + High/Low of Week
  - Ideal untuk day trade
  - Typically Low/High of the Week terbentuk
  - Judas Swing / Raid of Monday HL / FVG Fill → Manipulation
  - London Open/Close/Fix = Pivotal level
  - Monitor SMR/MMxM
  - High probability: harga return ke Monday London Close/Fix

RABU–KAMIS — Expansion (Distribution)
  - Rabu: Good to day trade, Expansion
    → Jika di atas Weekly Open & PW EQ → cari Longs
    → Jika di bawah WO & PW EQ → cari Shorts
    → Monitor move ke Premium/Discount PDR
    → Internal BSL/SSL raid
    → Continuation of Trend (Distribution)
  - Kamis: Continuation → Take Profit by 10:00 EST atau saat DOL tercapai
    → Trend Reversal di London Close/Fix 11:00 EST
    → Typically Low/High of Week terbentuk

JUMAT — Return to Range (TGIF)
  - Tidak ideal untuk day trade
  - Return into the range (20-30% dari weekly range)
  - Reversal untuk melengkapi Weekly AMD Profile
  - Profit Taking Distribution
  - Weekly Close
```

---

## Catatan Tambahan

- **High-impact news = volatility** → gunakan bersamaan dengan HTF PD Array untuk establish bias.
- Gunakan Economic Calendar sebagai **guiding map**: track manipulasi dan ekspansi dari news high-impact, selaraskan dengan HTF PD Array.
- Jika MON–WED high-impact news tidak bisa manipulate external range → antisipasi **consolidation reversal profile**.
- **Negative Condition** (low-probability): Monday accumulation, Tuesday expansion tanpa manipulasi terlebih dahulu.

---

---

# Daily Bias Framework Logic

## Definisi

Daily Bias Framework adalah **sistem 3 pertanyaan** untuk menentukan bias harian berdasarkan apa yang sudah dilakukan market, lalu mengidentifikasi Draw on Liquidity berikutnya.

---

## Framework 3 Pertanyaan

```
STEP 1 — Tentukan Current Framework (berdasarkan apa yang sudah terjadi):
  1. Did the Market take out any Significant Liquidity Pools?
  2. Did the Market hit a Significant FVG?
  3. Did the Market create a Market Structure Break with a Displacement?

Price is Fractal: apa yang terlihat di Monthly Chart, bisa dilihat di Daily Chart.
Apa yang terlihat di Daily Chart, bisa dilihat di 5 Minute Chart.

STEP 2 — Tentukan Draw on Liquidity berikutnya:
  1. Do we have clean lows or highs? (External range liquidity)
  2. Do we have any FVGs that need to be rebalanced? (Internal range liquidity)

→ Hindari picking tops and bottoms tanpa konfirmasi framework.
→ Jika salah mengikuti trend → market akan beri kesempatan masuk di sisi sebaliknya.
```

---

## Dealing Range

**Dealing Range** = range trading baru yang terbentuk **setelah BSL dan SSL kedua-duanya sudah diambil**.

```
Dealing Range dibentuk oleh:
1. Price run yang mengambil likuiditas di kedua sisi dari previous range
2. Framework yang membentuk ICT PD Array matrix:
   → Top/Bottom of dealing range = Old High/Low
   → Kemudian RB (Rejection Block), OB, FVG, dll.
```

**PD Array di dalam Dealing Range:**

| Zone | Bearish Premium Array | Bullish Discount Array |
|---|---|---|
| Atas | Old High/Low, Rejection Block, Bearish OB, FVG, Liquidity Void, Bearish Breaker, Mitigation Block | — |
| Tengah | — Equilibrium — | — Equilibrium — |
| Bawah | — | Mitigation Block, Bullish Breaker, Liquidity Void, FVG, Bullish OB, Rejection Block, Old Low/High |

> Dealing Range = Opening Range ketika new day/session opens. Saat KEDUA sisi opening range sudah diambil → menjadi Dealing Range.

---

## Bearish Daily Mindset

```
Daily Chart Mindset (Bearish):
  Setiap UP candle (atau beberapa candle) = SELLING OPPORTUNITY

  Kamu menunggu harga mencapai:
  - Key POI di atas (Premium PD Array)
  - Di atas Opening Price (atau CPE zone)
  - Sweep BSL di LTF

  Target: Old Lows di Daily Chart atau Previous Day's Lows

Swing Points untuk Daily Bias & PO3:
  Stage 1: Butuh Swing High dengan Bearish Context
    → Lihat apa yang terjadi di 3rd Candle Intra-day
    → Jika 3rd Candle Intra-day menunjukkan REVERSAL
    → Ekspektasi 4th Candle = Expansion lower

  Stage 2: Setelah Swing High dikonfirmasi dengan close candle ke-4
    → Price akan build short-term premium sebelum expansion lower
    → Tunggu price trade back ke Premium relative terhadap range-nya
    → Expect price pause/konsolidasi atau retrace ke Premium
```

---

## Bullish Daily Mindset

```
Daily Chart Mindset (Bullish):
  Setiap DOWN candle (atau beberapa candle) = BUYING OPPORTUNITY

  Kamu menunggu harga mencapai:
  - Key POI di bawah (Discount PD Array)
  - Di bawah Opening Price (atau CPE zone)
  - Sweep SSL di LTF

  Target: Old Highs di Daily Chart atau Previous Day's Highs

  Goal: Buy below the Opening Price (12:00 AM Eastern / Midnight Open)
  → Clear move below 12 AM Eastern price ke key level dari prior day(s)
  → Bisa retracement ke reference point di 4H atau 1H dari prior day(s)
```

---

---

# Close Proximity Entries (CPE) Logic

## Definisi

Close Proximity Entries (CPE) adalah **zona entry di sekitar Opening Price** — area di mana Smart Money melakukan akumulasi posisi sebelum distribusi dimulai. Dalam Power of Three, ini adalah zona Accumulation + awal Manipulation.

---

## Power of Three dalam CPE

```
1. ACCUMULATION: Price trading di atas/bawah Opening Price → Smart Money akumulasi posisi
2. MANIPULATION: False Price Move (Judas Swing) di market opening atau sebelum news → menyesatkan retail
3. DISTRIBUTION: Sisi berlawanan dari candle terbentuk menuju closing → expansion terjadi
   → Distribution cycle: sekitar 3:30–3:45 PM NY Time
```

---

## CPE Bearish Setup

```
Accumulation: Harga di atas atau di sekitar Opening Price
  → Smart Money akumulasi net short book

"Close Proximity Entries": di atas atau dekat Opening Price
  → Area untuk entry SHORT

Manipulation: False Price Move ke atas sebelum news / market open = Judas Swing
  → Menipu retail untuk LONG

Distribution: Opposite side candle terbentuk ke bawah (sisi short)
```

---

## Kalkulasi Opening Range & CPE

```
Opening Range Calculation:
  Step 1: Open + HOD = Opening Range Projection
  Step 2: Opening Range Projection − Opening Price = Opening Range

CPE Zone (untuk Bearish):
  Above Opening Price = CPE Entry area
  1:1 Deviation below Opening Range = Target

Formula visual:
  [Open + High of Day] ← batas atas Opening Range Projection
       ↓
  [CPE Zone] ← area entry short (di atas Opening Price)
       ↓
  [1:1 Deviation] ← target short (1x Opening Range di bawah Open)
```

---

## Implementasi

```
CPE Bearish:
  Entry: Harga retrace ke atas/dekat Opening Price (CPE zone)
         → Cari FVG, Imbalance, atau Raid di CPE zone ini
  Target: 1:1 Deviation = Open − Opening Range

CPE Bullish (mirror):
  Entry: Harga retrace ke bawah/dekat Opening Price (CPE zone)
  Target: 1:1 Deviation = Open + Opening Range

Catatan:
  - CPE ideal saat kamu MISS entry utama di atas Opening Price
  - Atau untuk PYRAMID posisi yang sudah ada
  - Opening Range mendefinisikan seberapa besar movement di bawah Open yang diizinkan
    untuk High Probability Short scenario

Pyramiding:
  - Mulai dengan posisi terbesar di entry pertama
  - Kurangi ukuran untuk setiap entry berikutnya
  - Pendekatan ini meminimalkan compound loss jika market bergerak melawan
```

---

## Catatan Tambahan

- Kamu **tidak perlu memprediksi** di mana Low akan terbentuk atau candle akan close untuk profitable.
- CPE adalah **zona**, bukan satu level spesifik — FVG, imbalance, dan raids di zona ini = high probability entries.
- Pagi Session (Morning Session 08:30–12:00): gunakan 5M chart untuk identifikasi CPE entries.

---

---

# Blending News with the Open Logic

## Definisi

Blending News Events with the Opening Price adalah **cara membaca apakah news release akan menjadi displacement atau manipulation**, tergantung kondisi price action sebelum news dirilis.

---

## Dua Skenario

```
SKENARIO A — False Move SUDAH terbentuk sebelum news release:
  → News akan menyebabkan DISPLACEMENT (move sesungguhnya)
  
  Diagram:
  [False Move ke atas (Judas)] → [News Release] → [Displacement ke bawah]
                                        ↑ MSS terjadi di sini
  
  → Ini adalah setup ideal: bias sudah clear, news hanya konfirmasi/trigger

SKENARIO B — False Move BELUM terbentuk sebelum news release:
  → News akan MENYEBABKAN False Move (manipulasi)
  
  Diagram:
  [News Release] → [Judas Swing (false move ke atas/bawah)] → [Real move sesudahnya]
  
  → Tunggu setelah Judas Swing selesai, baru entry mengikuti bias HTF
```

---

## Catatan Tambahan

- Jangan blindly trade news spike — pahami dulu **konteks price action sebelumnya**.
- Jika false move sudah ada → news = timing entry.
- Jika false move belum ada → news = trigger manipulasi → tunggu reversal setelah Judas.
- Selalu pair dengan HTF PD Array dan bias harian.

---

---

# Top Down Analysis Entry Process Logic

## Definisi

Top Down Analysis Entry Process adalah **proses drill-down timeframe** dari HTF ke LTF untuk menemukan FVG entry yang paling presisi. Proses ini dimulai dari identifikasi impulse di HTF hingga eksekusi di chart 1M.

---

## Proses 6 Langkah

```
STEP 1 — Identify Major Trend (4H Chart):
  - Lihat 4H untuk arah major trend
  - Identifikasi SSL dan BSL level signifikan di 4H
  - Ini menentukan overall direction trading

STEP 2 — Locate SSL/BSL levels (4H Chart):
  - Tandai level SSL/BSL yang significant
  - Level ini akan memandu keputusan trading
  - Contoh: SSL NQ di 15,000 dan SSL ES di 4,400 di 4H chart

STEP 3 — Move to 1H Chart:
  - Zoom ke 1H untuk melihat order flow lebih detail
  - Cari: konsolidasi, buystops, sellstops
  - Contoh: Run on sellstops di 1H → internal liquidity taken → potensi reversal ke external BSL (4H)

STEP 4 — Analyze 15M Chart:
  - Refine entry dan exit points
  - Cari confluence: SSL/BSL, SMT Divergence, FVG
  - Contoh: test of discount FVG di 15M mengkonfirmasi reaksi ke 4H external BSL

STEP 5 — Confirm Entry on 5M Chart:
  - Timing entries di 5M
  - Cari price action signal yang align dengan HTF analysis
  - Contoh: Bullish OB setelah run on sellstops di 5M → potential long entry

STEP 6 — Execute on 1M Chart:
  - Fine-tune entry, set SL, dan set TP di 1M
```

---

## LTF FVG Drill-Down (untuk Precision Entry)

```
Setelah HTF displacement diidentifikasi:
  → Mulai Top Down Analysis untuk entries dari dalam displacement tersebut

Urutan kronologis:
  5M → 4M → 3M → 2M → 1M

Aturan:
  1. Jika ada FVG di 5M → STOP, jangan turun lebih rendah → gunakan 5M FVG
  2. Jika tidak ada di 5M, turun ke 4M
  3. Jika ada FVG di 4M → STOP → gunakan 4M FVG
  4. Jika tidak ada di 4M, turun ke 3M → dst.
  5. Jika tidak ada FVG sama sekali di 1M → TIDAK ADA TRADE

Catatan: Begitu menemukan FVG → berhenti, jangan cari lebih rendah lagi.
```

---

## Formasi Visual

```
HTF (Weekly) — Identifikasi Impulse + HTF Reference Point (FVG):
  [Impulse naik besar]
       ↓
  [HTF FVG terbentuk di dalam impulse leg]

Daily — Konfirmasi displacement:
  [Harga masuk HTF FVG] → [Displacement leg terkonfirmasi di Daily]
       ↓ → Setup untuk entry di LTF

LTF (5M→1M) — Drill-down untuk FVG precision entry:
  [Dalam displacement leg] → scan 5M dulu
  → Jika ada FVG → entry di situ
  → Jika tidak → scan 4M → 3M → 2M → 1M
  → Jika 1M tidak ada FVG → skip trade
```

---

## Catatan Tambahan

- Model entry paling mudah: **clear move below 12AM Eastern price** ke key level dari prior day(s).
- Retracement ke reference point di **4H atau 1H dari prior day** = ideal setup.
- Top Down Analysis selalu dimulai dari **HTF bias** — jangan reverse engineered dari LTF.

---

---

# Mindset & Trading Rules (Lumitraders Ch.14)

## Stick with Your Bias Only

```
→ Jika Bearish: hanya short. Jangan flip ke long.
→ Jika Bullish: hanya long. Jangan flip ke short.
→ Kamu tidak perlu capture semua move besar — cukup kerja dengan biased expectation
→ Jangan flip-flop antara bias dan membingungkan diri sendiri
```

---

## Day Trading ≠ Everyday Trading

```
Kesalahan umum:
  → Kamu pikir bisa trading setiap hari → SALAH
  → Kamu jatuh cinta dengan WINNING bukan dengan PROCESS
  → "Lebih baik dari yang lain" dan "selalu benar" adalah jebakan

Kebenaran:
  → Tidak perlu trading setiap hari
  → Temukan kenyamanan dalam TIDAK mengambil trade
  → Pahami risiko pasar terbuka setiap hari
  → Kamu terlalu fokus pada uang
  → Tidak setiap hari memberikan high probability opportunity
  → Be PROCESS-ORIENTATED
```

---

## Risk Management Essentials (dari perspektif Lumitraders)

- **Setting a risk tolerance**: tentukan maksimum kapital yang berani di-risk per trade.
- **Position sizing**: hitung ukuran posisi berdasarkan risk tolerance dan ukuran akun.
- **Stop-loss orders**: gunakan SL otomatis untuk membatasi kerugian.
- **Regular review**: monitor dan sesuaikan strategi risk management secara berkala.
- **Avoid overleveraging**: leverage tinggi memperbesar loss, gunakan secara bertanggung jawab.
- **Avoid overtrading**: trading berlebihan = biaya transaksi meningkat + pengambilan keputusan buruk.

---

## Catatan Tambahan

- Patience: tunggu setup probabilitas tinggi, jangan FOMO.
- Discipline: ikuti trading plan dan jangan deviate karena emosi.
- Proses > Hasil: fokus pada eksekusi yang benar, bukan pada P&L per trade.

---

---

# Three Drives Pattern Logic

## Definisi

Three Drives Pattern adalah **pola 3 impulse drive yang bergerak menuju HTF PD Array**, di mana setiap drive menghasilkan higher high + higher low (uptrend) atau lower low + lower high (downtrend). Pola ini menunjukkan bahwa market **sedang kehabisan likuiditas** (running out of liquidity) dan Smart Money sedang membangun posisi baru.

> "The Three Drives Pattern will Help you Understand Stop Hunts." — LumiTraders

---

## Formasi

```
Bearish Three Drives (menuju BSL/HTF PD Array di atas):

  Drive 1: HH1 (higher high pertama)
       \
        HL1
       /
  Drive 2: HH2 > HH1 (higher high kedua, lebih tinggi)
       \
        HL2
       /
  Drive 3: HH3 ≥ atau ≤ HH2 (tidak harus menembus HH2)
       ↓ DISPLACEMENT (sangat jelas, "elephant jumping into pool")
       ↓ FVG- terbentuk
       ↓ MSS terkonfirmasi
  → Entry SHORT di FVG-

Bullish Three Drives (menuju SSL/HTF PD Array di bawah):

  Drive 1: LL1 (lower low pertama)
       /
        LH1
       \
  Drive 2: LL2 < LL1 (lower low kedua, lebih rendah)
       /
        LH2
       \
  Drive 3: LL3 ≤ atau ≥ LL2 (tidak harus menembus LL2)
       ↑ DISPLACEMENT (sangat jelas)
       ↑ FVG+ terbentuk
       ↑ MSS terkonfirmasi
  → Entry LONG di FVG+
```

---

## 3 Komponen Drives

| Drive | Uptrend (Bearish setup) | Downtrend (Bullish setup) |
|---|---|---|
| **Drive 1** | Initial move ke atas → HH + HL | Initial move ke bawah → LL + LH |
| **Drive 2** | Second push ke atas → HH2 > HH1 + HL2 | Second push ke bawah → LL2 < LL1 + LH2 |
| **Drive 3** | Final push → HH3 ≥ HH1, harus exceed HH1 | Final push → LL3 ≤ LL1, harus exceed LL1 |

---

## Aturan Kritis

```
1. Tidak perlu Drive ke-3 mencapai/menembus Liquidity Pool.
   → Market sudah running out of Short-term Liquidity setiap kali
     menciptakan Short-term High baru.
   → Smart Money sudah membangun posisi baru.

2. Jika harga TIDAK mencapai liquidity zone/POI dan berbalik KUAT:
   → Sinyal yang bahkan LEBIH KUAT (tidak ada order di sisi lain,
     institutional sudah positioned duluan)

3. Setelah 3 drives → cari Displacement:
   → Displacement HARUS sangat obvious ("elephant jumping into children's pool")
   → Displacement mencetak FVG
   → Entry di FVG tersebut

4. Jika tidak ada 3 Drives Pattern → antisipasi Stop Hunt biasa.
   → Pelajari bagaimana harga bereaksi saat sweep short-term high.
   → Jika harga tidak "pressing higher" seperti 3 Drives → kemungkinan Stop Hunt.
```

---

## Kondisi Entry (Bearish Setup)

```
1. Identifikasi HTF PD Array / BSL di atas
2. Amati 3 drives berurutan menuju area tersebut (HH1 → HH2 → HH3)
3. Tunggu Displacement ke bawah yang sangat jelas setelah Drive 3
4. Konfirmasi MSS terbentuk (swing low horizontal ke candle FVG)
5. Identifikasi FVG- yang terbentuk dari displacement
6. Entry SHORT di FVG- (gunakan limit order di CE atau sisi yang lebih baik)
7. SL: di atas swing high Drive 3
8. TP: liquidity di bawah (SSL, old lows)
```

---

## Formasi Visual (dari chart Lumitraders)

```
Bearish Setup dengan Inversion FVG:
  [BSL di atas]
       ↑
  [Drive 3 sweep BSL]
  [Drive 2]        → MSS
  [Drive 1]        → FVG-
  [Short-term SSL] → Inversion FVG (IFVG-)
  [SSL di bawah]   ← Target

Full Model (dengan HTF FVG):
  [Consolidation/HTF FVG di bawah]
       ↑ 3 Drives naik
  [HTF FVG di atas (premium)]
  [Drive 3] → MSS → Breaker
                   → FVG/OB-
  [Drive lanjutan] → FVG- → MSS → Entry Short
                                 → Target: jauh ke bawah
```

---

## Implementasi Pine Script

```pinescript
// Three Drives detection (bearish)
// Deteksi 3 swing highs yang berurutan naik
bool swing_high = high[2] > high[3] and high[2] > high[1]  // pivot high

// Simpan 3 swing highs terakhir
var float sh1 = na, sh2 = na, sh3 = na
if swing_high
    sh3 := sh2
    sh2 := sh1
    sh1 := high[2]

// Three Drives: setiap SH lebih tinggi dari sebelumnya
bool three_drives_bear = not na(sh1) and not na(sh2) and not na(sh3) and
                         sh1 > sh2 and sh2 > sh3

// Setelah 3 drives → tunggu displacement
bool displacement_after_3d = three_drives_bear and
                              (math.abs(close - open) > ta.atr(14) * 1.5) and
                              close < open  // bearish displacement

// FVG setelah displacement
bool bear_fvg_3d = displacement_after_3d[1] and high[0] < low[2]
```

---

## Catatan Tambahan

- Three Drives Pattern terlihat di **semua timeframe** — semakin besar TF, semakin signifikan.
- Pattern ini sangat berguna untuk **memahami Stop Hunt** — retail stop-loss di atas setiap SH terkena satu per satu.
- Konfluens terkuat: Three Drives yang berakhir **tepat di HTF PD Array** (FVG, OB, BSL).
- Jika ada **Inverted FVG** di area reversal → tambah probabilitas setup.
- Setelah entry, **next reference point** adalah internal liquidity (FVG/OB di bawah) atau external liquidity (SSL/old low).

---

---

# Elements of Trade Setup (Checklist ICT)

## Definisi

Checklist elemen yang harus ada untuk mengkonfirmasi sebuah setup yang valid dalam framework ICT. Digunakan sebagai panduan cepat untuk menilai apakah kondisi pasar memenuhi syarat untuk entry.

---

## Setup Platform

```
1. Kalibrasi TradingView ke NY Timezone
   → Semua referensi waktu ICT menggunakan NY Time (EST)

2. Waktu terbaik untuk eksekusi: 8:30 AM – 11:00 AM NY Session
   → Overlap high impact data + kill zone aktif

3. HFT Algorithms mengeksekusi di timeframe 1M–5M
   → Berdasarkan market structure pada saat itu
```

---

## 4 Elemen Wajib dalam Trade Setup

```
1. RUN ON LIQUIDITY
   → Lihat harga menyapu BSL (Buyside) atau SSL (Sellside)
   → Tanpa ini, tidak ada "fuel" untuk move berikutnya

2. MARKET STRUCTURE SHIFT (MSS)
   → Break of short-term high (bullish) atau low (bearish)
   → Harus disertai displacement — bukan small candle atau wick saja

3. ENTRY DI DALAM FVG
   → Fair Value Gap yang terbentuk dari displacement
   → Masuk di CE (Consequent Encroachment = 50% FVG) atau lebih dalam

4. TARGET: LIQUIDITY ATAU IMBALANCE
   → External: Old Highs/Lows, BSL/SSL yang belum diambil
   → Internal: FVG, OB yang belum termitigasi
```

---

## Premium vs Discount (Fibonacci)

```
Gunakan Fibonacci Extension Tool di TradingView:

Tarik dari Swing Low ke Swing High (atau sebaliknya):
  - Di atas 50% level  → PREMIUM market (mahal)
  - Di bawah 50% level → DISCOUNT market (murah)

Aturan:
  - Bullish bias: beli di DISCOUNT (di bawah 50%)
  - Bearish bias: jual di PREMIUM (di atas 50%)
  - Jangan beli di premium atau jual di discount
```

---

## Weekly Candle Analysis

Weekly candle movements adalah fondasi dari trading model ICT:

1. **Foundation of Trading Model** — Weekly candle menentukan arah bias untuk semua timeframe di bawahnya
2. **Independent Thinking** — Hindari bergantung pada signal service; pahami rules sendiri
3. **Clean and Precise Charts** — Weekly analysis menghasilkan chart bersih, bebas noise

Swing highs/lows pada daily chart:
- **Imbalance & Efficient Pricing**: Swing high/low = area imbalance yang perlu "dibooked" oleh algoritma
- **Precision and Delivery**: Setiap range high-to-low = inefficiently delivered price move
- 90% retail traders kalah karena tidak memahami siapa yang berada di sisi lain trade mereka

---

## Quick Reference Checklist

| # | Element | Valid? |
|---|---|---|
| 1 | HTF Bias ditentukan (Bullish/Bearish)? | ☐ |
| 2 | Kill Zone aktif (Asian/London/NY)? | ☐ |
| 3 | BSL atau SSL sudah diambil (Run on Liquidity)? | ☐ |
| 4 | MSS terjadi dengan displacement? | ☐ |
| 5 | FVG terbentuk dari displacement? | ☐ |
| 6 | FVG di sisi Discount (long) atau Premium (short)? | ☐ |
| 7 | Target jelas (liquidity atau imbalance)? | ☐ |
| 8 | Risk management sesuai (SL di bawah/atas FVG)? | ☐ |

> Semua ☐ harus tercentang untuk setup probabilitas tinggi.

---

## Catatan Tambahan

- **Micro contracts** = starting point untuk build experience dengan capital kecil (futures index trading)
- Mentorship ini **bukan** high-frequency trading — fokus pada comprehensive approach dengan precision
- Backtest setiap setup sebelum live trading — consistency adalah kunci
- Goal: bukan profit setiap hari, tapi **menemukan profitable moves** yang tidak terbatas pada small price movements

---

---

# London Session Highs & Lows Logic

## Definisi

London Session Highs dan Lows adalah **level harga tertinggi dan terendah yang terbentuk selama London Kill Zone (02:00–05:00 AM EST)**. Level ini berfungsi sebagai referensi likuiditas utama yang digunakan algoritma NY session untuk menentukan arah pergerakan hari itu.

---

## Tiga Insight Utama

### 1. Market Sweeping
- London H/L bertindak sebagai level yang **diambil (swept) sebelum harga reversal**
- Algoritma NY sering menargetkan London High (BSL) atau London Low (SSL) sebagai stop hunt sebelum bergerak ke arah sebenarnya
- Jika NY sweep London High → kemungkinan bearish continuation atau reversal ke bawah
- Jika NY sweep London Low → kemungkinan bullish continuation atau reversal ke atas

### 2. Key Support & Resistance
- London H/L menciptakan **zona S/R intraday** dengan institutional significance
- Harga yang mencapai London High/Low akan mengalami peningkatan buying/selling pressure
- Level ini lebih signifikan daripada S/R biasa karena merupakan institutional reference

### 3. Intraday Trading Opportunities
- London H/L terbentuk **sebelum NY Open (08:30 AM)** → dapat diidentifikasi sebelum entry
- Memberikan level target dan referensi setup untuk NY session
- NY session reaction terhadap London H/L = sinyal konfirmasi bias

---

## Cara Identifikasi

```
1. Set chart ke NY Timezone (EST)
2. London Session = 02:00 AM – 05:00 AM EST
3. Identify highest high = London Session High (BSL target)
4. Identify lowest low  = London Session Low (SSL target)
5. Plot horizontal lines di kedua level
6. Monitor reaksi harga saat NY session mendekati level ini
```

---

## Formasi

```
Skenario Bullish NY (London Low sebagai SSL yang diambil):
  London KZ: harga turun → bentuk London Low
  NY Open 08:30: price sweep di bawah London Low → raid SSL
  → MSS ke atas → FVG entry → target London High atau lebih

Skenario Bearish NY (London High sebagai BSL yang diambil):
  London KZ: harga naik → bentuk London High
  NY Open 08:30: price sweep di atas London High → raid BSL
  → MSS ke bawah → FVG entry → target London Low atau lebih
```

---

## Entry & Exit Guidelines

| Parameter | Detail |
|---|---|
| **Trading hours** | 08:30 AM – 12:00 PM EST (prime window) |
| **Extension** | Dapat dilanjutkan ke NY Lunch / Afternoon (1:30–4:00 PM) |
| **Avoid** | Setelah 12:00 PM (NY Lunch) — volume rendah, false signals |
| **Afternoon resume** | 1:30 PM – 4:00 PM jika key liquidity belum diambil |
| **Timeframe** | 15M = key timeframe; 5M untuk entry precision |
| **Morning window** | 08:30 AM – 12:00 PM |
| **Positioning** | Sebelum 11:00 AM untuk ride NY Lunch / Afternoon |

---

## Implementasi Pine Script

```pinescript
// London Session High/Low tracker (UTC offset: EST + 5)
var float london_high = na
var float london_low  = na
bool is_london = (hour >= 7 and hour < 10)   // 02:00-05:00 EST = 07:00-10:00 UTC

if is_london
    london_high := na(london_high) ? high : math.max(london_high, high)
    london_low  := na(london_low)  ? low  : math.min(london_low, low)

// Reset setiap hari baru
if (hour == 5 and minute == 0)   // IPDA reset 00:00 EST = 05:00 UTC
    london_high := na
    london_low  := na

// Plot London H/L sebagai reference levels
plot(london_high, "London High", color=color.red,   style=plot.style_stepline)
plot(london_low,  "London Low",  color=color.green, style=plot.style_stepline)
```

---

## Catatan Tambahan

- London H/L sering berfungsi sebagai **HOD/LOD** dari hari itu — pantau apakah NY hanya extension atau reversal
- Jika London range **sudah sangat lebar** (15–20+ poin ES) → NY lebih likely bersifat retracement
- Konfluens terbaik: London H/L + HTF PD Array + Kill Zone aktif
- **Gunakan 15M timeframe** sebagai primary chart untuk read London H/L context sebelum 08:30

---

---

# Intraday Order Flow & Daily Range Logic

## Definisi

**Intraday Order Flow** adalah analisis interaksi buyer dan seller dalam satu trading day, yang mencerminkan bagaimana institusi mendistribusikan posisi. **Daily Range** adalah selisih antara highest dan lowest price dalam satu hari — semakin lebar = semakin volatil.

---

## Futures Contract Reference

| Instrument | Tick Value | Per Point |
|---|---|---|
| **ES / MES** | $12.50 (per 0.25) | $50 / point |
| **NQ / MNQ** | $5.00 (per 0.25) | $20 / point |
| **YM / MYM** | $5.00 (per 1.00) | $5 / point |

**Contract Delivery Month Codes:**
- **H** = March | **M** = June | **U** = September | **Z** = December
- Contoh: `ESU2023` = ES September 2023

---

## Timeframe 15 Menit — Primary Analysis Frame

15M timeframe adalah **primary timeframe** untuk intraday analysis:

1. Identify **key highs dan lows** sebagai stop hunt candidates
2. Identify **imbalances** (FVG, OB) dalam sesi London dan NY
3. Monitor **kill zone aktif** — algoritma paling agresif di sini
4. Algoritma seek liquidity dengan cara **buy di bawah SSL** dan **sell di atas BSL**

```
Before 08:30 AM: identify potential highs/lows sebagai stop hunt candidates
08:30 AM: beralih dari 15M → 5M untuk entry precision
12:00 PM: stop trading (no trade hour mulai)
```

---

## Jadwal Harian

| Waktu (EST) | Aktivitas |
|---|---|
| **Pre 08:30** | Identifikasi London H/L, imbalances, stop hunt candidates |
| **08:30 – 12:00** | Prime trading window — high volume, high volatility |
| **Sebelum 11:00** | Position sebelum ini untuk ride NY Lunch/Afternoon |
| **12:00 – 13:00** | **No Trade Hour** — volume rendah, manipulasi tinggi |
| **13:30 – 16:00** | Afternoon session — continuation atau reversal |

---

## Intraday Order Flow Principles

```
1. Order flow mencerminkan supply/demand aktual dari institutional participant
2. "Afternoon acceleration" terjadi 20–10 menit sebelum 4:00 PM
   → Disebabkan market-on-close orders dari fund dan institusi besar
3. Jika bullish order flow: down-close candles = support, up-close candles = fuel
4. Jika bearish order flow: up-close candles = resistance, down-close candles = fuel
```

---

## Daily Range Concept

```
Daily Range = High of Day - Low of Day

Narrow Daily Range:
  → Volatilitas rendah
  → Konsolidasi, akumulasi likuiditas
  → Setup untuk big move di sesi berikutnya

Wide Daily Range:
  → Volatilitas tinggi
  → Trending day
  → Gunakan sebagai reference untuk profit target dan stop-loss calibration
```

**Penggunaan Daily Range untuk Trading:**
- **Target**: proyeksikan berapa poin yang realistis berdasarkan ATR harian
- **Stop-loss**: kalibrasi SL tidak melebihi daily range normal
- **Momentum**: width of range menentukan agresivitas position size

---

## Implementasi Pine Script

```pinescript
// Daily Range calculation
float daily_high = ta.highest(high, 1440 / timeframe.multiplier)
float daily_low  = ta.lowest(low,  1440 / timeframe.multiplier)
float daily_range = daily_high - daily_low

// Intraday Order Flow: read candle close bias
bool bullish_candle = close > open
bool bearish_candle = close < open

// Afternoon acceleration zone (UTC): 20:40 - 21:00 EST = 16:40-21:00 UTC
bool accel_zone = (hour == 20 and minute >= 40) or (hour == 20 and minute <= 50)
```

---

## Catatan Tambahan

- **15M → 5M workflow**: analisis di 15M, eksekusi di 5M
- "No trade hour" 12:00–13:00 bukan berarti tidak ada pergerakan — justru pergerakan choppy/false
- Afternoon acceleration (menjelang close) bisa membentuk HOD/LOD final
- Gunakan Daily Range untuk ekspektasi realistis — jangan memaksakan target yang jauh di luar range normal

---

---

# Morning Trade and Positioning Logic

## Definisi

Morning Trade adalah **framework untuk session 08:30 AM – 12:00 PM EST**, menggunakan dua level referensi kritis: **8:30 Open** dan **Midnight Open (MNO/NMO)** untuk mengidentifikasi Judas Swing direction dan menentukan posisi optimal.

---

## Dua Level Referensi Kritis

### 1. Midnight Open (MNO / NMO)
- **NMO = harga open pada 00:00 AM NY Time (IPDA Reset)**
- Level ini berfungsi sebagai "anchor" untuk Power of 3 pada daily chart
- Antara 08:30–09:45 AM: harga bergerak menuju MNO sebagai Manipulation phase
- MNO bertindak sebagai Judas Swing (gerakan counter terhadap arah sesungguhnya)

### 2. 8:30 AM Open
- Level open pada saat US data rilis / NY session start
- Konfirmasi premium/discount bersamaan dengan MNO

---

## Buy Day vs Sell Day Framework

```
BUY DAY (Bullish):
  Price BELOW 8:30 Open AND BELOW Midnight Open
  → Market berada di DISCOUNT zona
  → PD arrays di bawah 8:30/MNO = konfirmasi long opportunity
  → 8:30/MNO = target (point of interest) untuk profit-taking
  
  Flow: LOD terbentuk → sweep SSL → MSS ke atas → FVG entry → target BSL

SELL DAY (Bearish):
  Price ABOVE 8:30 Open AND ABOVE Midnight Open
  → Market berada di PREMIUM zona
  → PD arrays di atas 8:30/MNO = konfirmasi short opportunity
  → 8:30/MNO = target (point of interest) untuk profit-taking

  Flow: HOD terbentuk → sweep BSL → MSS ke bawah → FVG entry → target SSL
```

---

## Incorporating Power of 3 (PO3)

```
NMO (00:00 AM) → serves as the anchor point for daily PO3:
  A (Accumulation) : Asian session konsolidasi di sekitar NMO
  M (Manipulation) : 08:30–09:45 AM, price trade toward/past NMO (Judas Swing)
  D (Distribution)  : NY session bergerak ke arah sesungguhnya setelah Judas Swing

PO3 untuk 1:30 PM Session:
  → Gunakan 1:30 PM opening price seperti Midnight/8:30 Open
  → Treat 1:30 PM session sebagai PO3 mini untuk daily chart afternoon
```

---

## Step-by-Step Morning Trade Guide

```
Step 1: Pre 08:30 AM
  - Identify liquidity pool (BSL di atas swing high atau SSL di bawah swing low)
  - Plot London H/L sebagai reference
  - Siapkan 15M chart

Step 2: 08:30 AM – Entry Window
  - Switch dari 15M ke 5M untuk entry precision
  - Tunggu Judas Swing (gerakan counter-trend) terhadap MNO

Step 3: Konfirmasi Displacement
  - Cari displacement BELOW liquidity pool (untuk buy day)
  - atau ABOVE liquidity pool (untuk sell day)
  - Displacement harus kuat — candle besar dengan close di sisi berlawanan

Step 4: FVG Entry
  - Identifikasi FVG yang terbentuk dari displacement candle
  - Entry di dalam FVG — idealnya di CE (50% FVG) atau lebih dalam
  - SL: di bawah FVG bottom (long) atau di atas FVG top (short)

Step 5: Target
  - Internal: imbalance / OB yang belum termitigasi
  - External: opposite session high/low (London H, Previous Day H/L)
```

---

## Afternoon Session (1:30 PM – 4:00 PM)

### Tiga Strategi Afternoon

| Strategi | Kondisi | Deskripsi |
|---|---|---|
| **Continuation Trade** | Trend kuat pagi | Ride momentum yang sama arah dengan morning move |
| **Reversal Trade** | Sentiment shift | Trading berlawanan arah jika ada MSS + displacement baru |
| **Measured Move** | Standard model | Morning move ≈ Afternoon move (1:1 measured move) |

```
Measured Move Example (NQ):
  Morning: turun 70 poin (HOD → LOD)
  → Afternoon: naik 70 poin dari LOD
  = LOD + 70 poin = target afternoon

Acceleration:
  20 menit sebelum 4:00 PM: market-on-close orders aktif
  10 menit sebelum 4:00 PM: maximum acceleration
  → Bisa membentuk HOD/LOD final
```

---

## 3 Market Profile Templates

```
Template 1: Morning Trend → Afternoon REVERSAL
  Pagi: trending (bullish/bearish)
  12:00: NY Lunch — konsolidasi
  1:30 PM: reversal dari arah pagi
  4:00 PM: close berlawanan dengan open pagi
  Typical pada: NFP day, CPI day, major news event

Template 2: Morning Trend → Afternoon CONTINUATION (1:1 Measured Move)
  Pagi: trending
  12:00: NY Lunch — pullback minor
  1:30 PM: resume trend yang sama
  Afternoon: measured move = morning move size
  Typical pada: strong trending days dengan macro tailwind

Template 3: Morning CONSOLIDATION → Afternoon TREND
  Pagi: range-bound, choppy, tidak ada directional move
  12:00: NY Lunch
  1:30 PM: breakout dengan displacement dari konsolidasi
  4:00 PM: trending hingga close
  Typical pada: awaiting news di pagi hari
```

---

## Implementasi Pine Script

```pinescript
// Midnight Open (NMO) — 00:00 EST = 05:00 UTC
var float midnight_open = na
if (hour == 5 and minute == 0)
    midnight_open := open

// 8:30 AM Open — 08:30 EST = 13:30 UTC
var float eight30_open = na
if (hour == 13 and minute == 30)
    eight30_open := open

// 1:30 PM Open — 13:30 EST = 18:30 UTC
var float afternoon_open = na
if (hour == 18 and minute == 30)
    afternoon_open := open

// Plot reference levels
plot(midnight_open,  "NMO",          color=color.blue,   linewidth=1)
plot(eight30_open,   "8:30 Open",    color=color.orange, linewidth=1)
plot(afternoon_open, "1:30 PM Open", color=color.purple, linewidth=1)

// Buy Day / Sell Day detection
bool buy_day_setup  = close < midnight_open and close < eight30_open
bool sell_day_setup = close > midnight_open and close > eight30_open
```

---

## Catatan Tambahan

- MNO dan 8:30 Open harus **berdua konfirmasi** premium/discount — satu saja tidak cukup
- Judas Swing bisa terjadi 08:30–09:45 AM → setelah itu setup entry mulai terbentuk
- **Jangan entry sebelum Judas Swing selesai** — tunggu MSS + displacement konfirmasi
- Afternoon 1:30 PM setup memiliki konteks PO3 sendiri — perlakukan sebagai session baru
- Template 1 (Reversal) paling sering di high-impact news days

---

---

# Market Efficiency Paradigm (MEP) & BISI/SIBI Logic

## Definisi

Market Efficiency Paradigm adalah **framework untuk memahami bagaimana algoritma interbank (IPDA) mengantarkan harga secara efisien** untuk kepentingan Smart Money. Paradigm ini menjelaskan mengapa retail traders kalah: mereka trading berlawanan dengan price delivery algorithm.

> "The market is not designed to be predicted by retail traders. It is designed to seek liquidity wherever it rests." — ICT

---

## Tiga Komponen Utama

```
1. SMART MONEY (Institutional Order Flow)
   → Banks, Central Banks, Sovereign Funds
   → Trading dengan IPDA algorithm
   → Mereka TIDAK menggunakan indikator retail

2. MARKET EFFICIENCY PARADIGM (Interbank Price Delivery Algorithm / IPDA)
   → Engine yang mengantarkan harga
   → Selalu mencari likuiditas untuk Smart Money
   → Beroperasi berdasarkan Time dan Price (bukan indikator)

3. SPECULATIVE UNINFORMED MONEY (Retail + New Hedge Funds + Large Fund Traders)
   → Retail Traders: high failure rate, emotional, over-leveraged
   → New Hedge Funds: lifespan 5-7 tahun rata-rata; LTCM = contoh kegagalan
   → Large Fund Traders: pakai metode retail (indikator), beda hanya di risk management
```

---

## 6 Aturan Internalisasi Price Delivery

```
Rule 1: Do NOT trade patterns for pattern's sake
  → Pola hanya valid jika aligned dengan liquidity narrative

Rule 2: Do NOT trade indicator readings or momentum
  → Indikator lagging; IPDA tidak berbasis indikator

Rule 3: Enter LONGS when Retail SELLS
  → Retail sell = stop loss hit di bawah swing low = SSL = Smart Money entry Long

Rule 4: Enter SHORTS when Retail BUYS
  → Retail buy = stop loss hit di atas swing high = BSL = Smart Money entry Short

Rule 5: Anticipate Price seeking OPPOSITE Liquidity
  → Setelah SSL diambil → harga cari BSL; setelah BSL diambil → harga cari SSL
  → IOF selalu bergerak dari satu pool liquidity ke pool berikutnya

Rule 6: TIME OF DAY is vital when engaging with Price
  → Kill Zone memberi context temporal yang membuat setup valid
  → Setup yang sama DI LUAR kill zone = probabilitas rendah
```

---

## BISI — Buyside Imbalance Sellside Inefficiency

**Konteks**: Market bullish oriented setelah news embargo. Algoritma menyerap sellside liquidity lalu deliver ke buyside.

### 9-Step Protocol BISI

```
Step 1: Tunggu big BEARISH displacement setelah news embargo (30+ poin ES)
        → News embargo = saat news rilis; jangan masuk sebelum ini

Step 2: Jangan trading 10 menit PERTAMA setelah news rilis
        → Jika news 10:00 AM → setup window: 10:10 AM – 12:00 PM

Step 3: Tunggu dan observe order flow:
        a) Bearish PD arrays MULAI GAGAL (harga tidak mau turun lebih lanjut)
        b) Ada bullish displacement candle
        c) Change in order flow terdeteksi

Step 4: Down-close candles sekarang bertindak sebagai SUPPORT
        → Harga bounce dari candle bearish sebelumnya = bullish IOF

Step 5: Up-close candles DIINVALIDASI
        → Jika harga break di bawah up-close candle = bukan bullish lagi

Step 6: PREMIUM arrays DIINVALIDASI
        → OB di atas tidak relevan dalam BISI context

Step 7: DISCOUNT arrays DIHORMATI
        → FVG di bawah, Bullish OB, Breaker di discount = valid entry zone

Step 8: Antisipasi BUY di Discount Arrays:
        a) FVG (Bullish) atau Inversion FVG
        b) Order Block (Bullish OB)
        c) Breaker Block (Bullish Breaker)
        → Entry: setelah harga retrace ke salah satu array ini

Step 9: Risk Management BISI
        Max Stop Loss: 5 poin (ES/SP500)
        Target       : 10 poin minimum (2:1 RR)
        Final objective: 50% retracement dari entire BISI range
```

---

## SIBI — Sellside Imbalance Buyside Inefficiency

**Konteks**: Market bearish oriented setelah news embargo. Algoritma menyerap buyside liquidity lalu deliver ke sellside.

### 9-Step Protocol SIBI

```
Step 1: Tunggu big BULLISH displacement setelah news embargo (30+ poin ES)
        → Displacement besar = institutional sell program aktif

Step 2: Jangan trading 10 menit PERTAMA setelah news rilis

Step 3: Tunggu dan observe order flow:
        a) Bullish PD arrays MULAI GAGAL
        b) Ada bearish displacement candle
        c) Change in order flow ke bearish terdeteksi

Step 4: Up-close candles sekarang bertindak sebagai RESISTANCE
        → Harga bounce dari candle bullish sebelumnya = bearish IOF

Step 5: Down-close candles DIINVALIDASI
        → Jika harga break di atas down-close candle = bukan bearish lagi

Step 6: DISCOUNT arrays DIINVALIDASI
        → Bullish OB / FVG di bawah tidak relevan dalam SIBI context

Step 7: PREMIUM arrays DIHORMATI
        → FVG di atas, Bearish OB, Breaker di premium = valid entry zone

Step 8: Antisipasi SELL di Premium Arrays:
        a) FVG (Bearish) atau Inversion FVG
        b) Order Block (Bearish OB)
        c) Breaker Block (Bearish Breaker)

Step 9: Risk Management SIBI
        Max Stop Loss: 5 poin (ES/SP500)
        Target       : 10 poin minimum (2:1 RR)
        Final objective: 50% retracement dari entire SIBI range
```

---

## Kapan Menerapkan MEP / BISI / SIBI

```
Kondisi Ideal:
  ✓ High-impact news event (NFP, CPI, FOMC, FED talks, Unemployment)
  ✓ Ada "news embargo" yang jelas (sebelum rilis = no trade)
  ✓ Trending day (bukan ranging/choppy day)
  ✓ Displacement besar (30+ poin ES, 120+ poin NQ)
  ✓ Kill Zone aktif (NY AM 08:30–12:00 adalah ideal)

Kondisi Hindari:
  ✗ Ranging/sideways day tanpa news catalyst
  ✗ Trading dalam 10 menit pertama setelah news
  ✗ Tidak ada displacement yang jelas
  ✗ Di luar kill zone
```

---

## Institutional Order Flow (IOF) — Efisiensi Harga

**IOF** = analisis bagaimana institusi besar trade pada basis daily/weekly/monthly:

```
IOF Bullish Signal:
  → Down-close candles = "footprint" institusi beli
  → Bodies candle bearish = support yang direspect
  → Harga tidak mau break di bawah level ini

IOF Bearish Signal:
  → Up-close candles = "footprint" institusi jual
  → Bodies candle bullish = resistance yang direspect
  → Harga tidak mau break di atas level ini

Smart Money → Liquidity (diagram):
  Smart Money sebagai Liquidity Provider
  → Harga didelivered untuk efisiensi institusi
  → "What the macro does: run Buyside then attack Sellside (atau sebaliknya)"
```

---

## Dealing Range (Interbank Dealing Range / IDR)

```
Definisi:
  Dealing Range = range yang terbentuk SETELAH liquidity swept pada KEDUA sisi
  → Ranging → sweep top of range → sweep bottom → DEALING RANGE TERBENTUK

Karakteristik:
  - Formed after BOTH BSL dan SSL telah diambil
  - Provide excellent bias pada higher timeframes
  - Identify premium (atas 50%) dan discount (bawah 50%)

Cara Identifikasi:
  1. Lihat ke mana "most recent dealing range" telah diperdagangkan
  2. Apakah sudah take out BSL? Atau SSL?
  3. Gunakan 15M TF sebagai referensi utama
  4. IDR = price area antara swept high dan swept low

Bias dari IDR:
  → Draw on Liquidity BEARISH: price draw below IDR Low
  → Draw on Liquidity BULLISH: price draw toward IDR High
  → PO3: Open → HOW (manipulation) → LOD (distribution) dalam IDR context

IDR Power:
  "Dealing ranges are so powerful because they are ONLY formed after price
   sweeps both sides of liquidity. They can provide amazing bias on HTF."
```

---

## Trade Journal Framework (dari Ch9)

Journaling adalah alat untuk membangun pemahaman IPDA:

**Daily Journal Elements:**
```
a) Displacement of Daily Range from a specific level
b) Clean Highs or Lows neutralized for the Daily Range
c) Price reaction at Swing Lows and Highs between A and B
```

**Weekly Journal Elements:**
```
a) Displacement of Weekly Range from a specific level
b) Clean Highs and Lows neutralized for the Weekly Range
c) Price reaction at Swing Highs and Lows in between A and B

Questions to answer:
  - What day did the Weekly High or Low form?
  - What Kill Zone did the Weekly High or Low form?
```

**Multi-Timeframe Perspective:**
- **Long-term**: Weekly Chart atau Daily Chart
- **Intermediate-term**: 4H atau 1H Chart
- **Short-term**: 15M atau 5M Chart

---

## Implementasi Pine Script

```pinescript
// BISI/SIBI displacement detection
float displacement_size = math.abs(close - open)
float atr_val = ta.atr(14)
bool big_displacement = displacement_size > atr_val * 2.0

// BISI context: bearish displacement followed by bullish reversal
bool bisi_displacement = close < open and big_displacement    // initial bearish dump
bool sibi_displacement = close > open and big_displacement    // initial bullish pump

// IOF: down-close as support (bullish IOF)
bool down_close_support = close[1] < open[1] and low >= low[1]

// IOF: up-close as resistance (bearish IOF)
bool up_close_resistance = close[1] > open[1] and high <= high[1]

// News window: avoid first 10 min after news (define news_time manually)
// var int news_hour = 10, news_min = 0
// bool post_news_window = (hour * 60 + minute) > (news_hour * 60 + news_min + 10)
```

---

## Catatan Tambahan

- BISI/SIBI bukan pattern — ini adalah **context** yang menentukan mana PD array yang valid
- Setelah displacement besar, market **tidak langsung reversal** — tunggu 10 menit + observasi IOF
- "50% BISI/SIBI retracement" = Consequent Encroachment dari seluruh displacement range
- Speculative Uninformed Money (retail) = **sumber likuiditas** untuk Smart Money — mereka tidak musuh, mereka adalah bahan bakar
- Interbank Price Delivery Algorithm (IPDA) beroperasi di semua timeframe simultaneously

---

---

# Daily Bias & Consolidation Logic

## Definisi

**Daily Bias** adalah arah pergerakan yang diharapkan untuk satu hari trading. Bukan ditetapkan sebelum market buka — melainkan ditentukan secara **reaktif berdasarkan rules spesifik** yang terkonfirmasi saat market bergerak. Daily Bias menentukan mana PD array yang relevan dan sisi mana yang trading valid.

> "Determining the daily bias is not about establishing a preconceived bias before the market commences trading. Instead, it relies on experience and adherence to specific rules."

---

## Level Referensi Utama

| Level | Deskripsi |
|---|---|
| **PDH** (Previous Day High) | Draw on Liquidity untuk move ke atas; jika gagal close di atas = reversal |
| **PDL** (Previous Day Low) | Draw on Liquidity untuk move ke bawah; jika gagal close di bawah = reversal |
| **PWH** (Previous Week High) | Higher timeframe DOL / reversal level |
| **PWL** (Previous Week Low) | Higher timeframe DOL / reversal level |

---

## Cara Menentukan Daily Bias

### Prinsip Dasar

```
Bullish Bias (Buy Day):
  - Price di bawah Midnight Open DAN di bawah 8:30 Open = Discount
  - Cari setup long di discount PD arrays (FVG, OB, Breaker)
  - PDH = target profit (Draw on Liquidity ke atas)
  - Jika bullish dan PDH belum tercapai → masih bullish bias

Bearish Bias (Sell Day):
  - Price di atas Midnight Open DAN di atas 8:30 Open = Premium
  - Cari setup short di premium PD arrays (FVG, OB, Breaker)
  - PDL = target profit (Draw on Liquidity ke bawah)
  - Jika bearish dan PDL belum tercapai → masih bearish bias
```

### PDH/PDL sebagai Liquidity Pool untuk Reversal

```
Bullish Reversal Signal (dari uptrend):
  1. Uptrend aktif
  2. PDH menjadi Draw on Liquidity → harga naik menuju PDH
  3. Harga GAGAL CLOSE DI ATAS PDH (wick menembus, body tidak)
  4. Ini sinyal potential reversal
  5. Expect harga turun menuju PDL sebagai Draw on Liquidity berikutnya

Bearish Reversal Signal (dari downtrend):
  1. Downtrend aktif
  2. PDL menjadi Draw on Liquidity → harga turun menuju PDL
  3. Harga GAGAL CLOSE DI BAWAH PDL
  4. Ini sinyal potential reversal
  5. Expect harga naik menuju PDH sebagai Draw on Liquidity berikutnya
```

---

## Previous Day H/L vs Previous Week H/L

```
PDH/PDL:
  - Reference utama untuk intraday DOL
  - Reversal framed off PDH/PDL pada 4H timeframe
  - Blue line (dengan BOS sebelumnya) = HIGH probability entry
  - Red line (tanpa BOS atau melawan struktur) = LOWER probability

PWH/PWL:
  - Higher timeframe reference
  - Lebih powerful sebagai DOL karena lebih banyak stop orders
  - Digunakan untuk weekly bias dan swing trading
  - Ketika tercapai: tunggu reaction — bisa reversal atau continuation
```

---

## Liquidity Level Trading Rules

Dari analisis historical ICT (2:1 RR dengan entry di liquidity level):

```
HIGH PROBABILITY entry:
  ✓ Ada BOS (Break of Structure) sebelum liquidity level
  ✓ Price melanjutkan arah BOS setelah menyentuh level
  ✓ Blue line setup

LOWER PROBABILITY entry:
  ✗ Tidak ada BOS sebelumnya
  ✗ Entry melawan dominant market structure
  ✗ Red line setup

Risk:Reward Target = 2:1 minimal
Stop: half size of previous swing low/high
Break-even: move to 1:1
```

---

## Daily Bias dan Economic Calendar

```
Prinsip:
  - Economic calendar digunakan untuk mengantisipasi potential manipulation
  - News high-impact = potential catalyst untuk Judas Swing
  - Bias TIDAK ditentukan dari kalender — kalender hanya memberikan KONTEKS

Flow:
  1. Cek economic calendar (NFP, CPI, FOMC, PMI)
  2. Identifikasi potential manipulation waktu news
  3. Tentukan daily bias dari price action + PDH/PDL/MNO
  4. Gunakan kalender untuk TIMING — bukan direction
```

---

## Konsolidasi sebagai Foundation

```
"Everything starts with Consolidation."
"Nothing can happen without consolidation."

Karakteristik Konsolidasi:
  - Harga terkonstriksi dalam range
  - Tidak ada willingness untuk breakout
  - Market Makers membangun likuiditas DI ATAS dan DI BAWAH range
  - Equilibrium (50%) dari range = reference point IOF

Kapan Terjadi:
  - Asian session (20:00-00:00 EST): konsolidasi intraday
  - 5:00-8:00 AM EST: return to consolidation setelah London expansion
  - Midweek (weekly context): konsolidasi setelah Mon-Tue expansion
  - Setelah 16:00 EST: daily consolidation
```

---

## Implementasi Pine Script

```pinescript
// Previous Day H/L
var float pdh = na
var float pdl = na

// Update pada awal hari baru
if (dayofweek != dayofweek[1])
    pdh := high[1]
    pdl := low[1]

// Previous Week H/L
var float pwh = na
var float pwl = na

if (weekofyear != weekofyear[1])
    pwh := ta.highest(high, 5 * 1440 / timeframe.multiplier)[1]
    pwl := ta.lowest(low,  5 * 1440 / timeframe.multiplier)[1]

// Daily Bias detection
var float midnight_open = na
var float eight30_open  = na

if (hour == 5 and minute == 0)   // 00:00 EST = 05:00 UTC
    midnight_open := open
if (hour == 13 and minute == 30) // 08:30 EST = 13:30 UTC
    eight30_open  := open

bool bullish_bias = close < midnight_open and close < eight30_open
bool bearish_bias = close > midnight_open and close > eight30_open

// Failure to displace (reversal signal)
bool fail_above_pdh = high > pdh and close < pdh   // wick above, close below → bearish reversal
bool fail_below_pdl = low < pdl  and close > pdl   // wick below, close above → bullish reversal

plot(pdh, "PDH", color=color.red,   linewidth=1, style=plot.style_stepline)
plot(pdl, "PDL", color=color.green, linewidth=1, style=plot.style_stepline)
plot(pwh, "PWH", color=color.maroon, linewidth=2, style=plot.style_stepline)
plot(pwl, "PWL", color=color.teal,   linewidth=2, style=plot.style_stepline)
```

---

## Catatan Tambahan

- **Bullish bias ≠ beli setiap saat** — butuh discount array dan kill zone
- **Bearish bias ≠ jual setiap saat** — butuh premium array dan kill zone
- PDH/PDL yang "clean" (tanpa BOS sebelumnya) = lebih kuat sebagai reversal level
- Kombinasi terbaik: PDH/PDL yang align dengan HTF OB atau HTF FVG
- Economic calendar = timing tool, bukan direction tool

---

---

# Power of 3 (PO3) & Daily Range Structure Logic

## Definisi

**Power of 3 (PO3)** adalah framework yang menjelaskan bahwa setiap candlestick/bar — di semua timeframe — terbentuk melalui **3 fase**: Accumulation (A), Manipulation (M), dan Distribution (D). Framework ini merupakan representasi visual dari bagaimana IPDA algorithm mengantarkan harga.

> "Power of 3 are candle/bar formation stages that are relevant for ALL timeframes, especially used with daily and weekly trading ranges, where the opening price is considered to be the beginning of the period."

---

## Tiga Fase PO3

```
A — ACCUMULATION (Opening Level):
  → Konsolidasi di sekitar opening price
  → Smart money membangun posisi secara diam-diam
  → Terlihat sebagai sideways / ranging movement
  → Bisa juga berbentuk descending atau ascending channel
  → Tujuan: absorb selling/buying pressure dari retail

M — MANIPULATION (Judas Swing):
  → Gerakan palsu berlawanan arah sebenarnya
  → Menciptakan false breakout untuk menjebak retail
  → Stop hunts: ambil SSL (untuk buy day) atau BSL (untuk sell day)
  → Teknik: false breakout, stop run, fakeout
  → Terjadi melalui news event atau right before it

D — DISTRIBUTION:
  → Gerakan sesungguhnya setelah manipulation selesai
  → Smart money unwind posisi → harga bergerak cepat
  → Retail masuk terlambat saat distribution sudah jauh berjalan
  → Price action menunjukkan exhaustion atau reversal di ujung
```

---

## Multi-Timeframe PO3

```
PO3 berlaku untuk semua timeframe:

Monthly PO3:
  A: minggu pertama bulan
  M: fake move awal bulan (biasanya minggu ke-1/2)
  D: trend utama bulan itu

Weekly PO3:
  A: Sunday-Monday open (konsolidasi)
  M: Judas Swing biasanya Senin-Selasa (paling sering Selasa/Rabu)
  D: distribusi harga menuju weekly high/low

Daily PO3:
  A: Asian session (konsolidasi, 20:00-00:00 EST)
  M: London session (Judas Swing, 02:00-05:00 EST via midnight open)
  D: NY session (distribusi ke HOD/LOD)

Intraday PO3 (NY session):
  A: 07:00-08:30 EST (pre-market konsolidasi)
  M: 08:30-09:45 EST (Judas Swing dari 8:30/MNO reference)
  D: 09:45-12:00 EST (distribusi ke target)

Afternoon PO3 (1:30 PM session):
  A: 13:00-13:30 EST (NY Lunch konsolidasi)
  M: 13:30-14:00 EST (manipulation dari 1:30 open)
  D: 14:00-16:00 EST (distribusi hingga market close)
```

---

## Weekly PO3 Detail

### Bullish Weekly Bias (Bias ↑)

```
Week Open (Sunday):
  → Expect harga bergerak DI BAWAH opening minggu (Judas Swing ke bawah)
  → Weekly Low biasanya terbentuk antara Senin-Rabu
  → Paling sering: Selasa atau Rabu
  → Jika harga kembali melewati level opening ke atas → reversal scenario mungkin
  → Bullish Candle: Close > Open, Low = Manipulation, High = Distribution
```

### Bearish Weekly Bias (Bias ↓)

```
Week Open (Sunday):
  → Expect harga bergerak DI ATAS opening minggu (Judas Swing ke atas)
  → Weekly High biasanya terbentuk antara Senin-Rabu
  → Paling sering: Rabu
  → Jika harga kembali melewati level opening ke bawah → reversal scenario mungkin
  → Bearish Candle: Close < Open, High = Manipulation, Low = Distribution
```

---

## Daily PO3 (Daily Power of Three)

```
Opening Level = Midnight Open (00:00 EST = IPDA Reset):
  → Menentukan zona profit untuk long dan short posisi

Bullish Daily PO3:
  Asian: konsolidasi di bawah/sekitar MNO
  Manipulation (London): harga DROP DI BAWAH midnight opening → Judas Swing
  → Smart money accumulate long saat manipulation ke bawah
  Konfirmasi: MSS terjadi → price KEMBALI DAN STAY DI ATAS midnight opening
  Distribution (NY): expansion ke atas → HOD terbentuk
  
Bearish Daily PO3:
  Asian: konsolidasi di atas/sekitar MNO
  Manipulation (London): harga NAIK DI ATAS midnight opening → Judas Swing
  → Smart money accumulate short saat manipulation ke atas
  Konfirmasi: MSS terjadi → price KEMBALI DAN STAY DI BAWAH midnight opening
  Distribution (NY): expansion ke bawah → LOD terbentuk
```

### Diagram Bullish Daily PO3
```
      HOD (Distribution/High)
       ↑
       |  Distribution
   ────┼──────── Open
       |  Manipulation ← Judas Swing (drop below MNO)
       ↓
      LOD (Manipulation/Low) ← Asian Low / SSL diambil
```

### Diagram Bearish Daily PO3
```
      HOD (Manipulation/High) ← Asian High / BSL diambil
       ↑  Manipulation → Judas Swing (spike above MNO)
   ────┼──────── Open
       |  Distribution
       ↓
      LOD (Distribution/Low)
```

---

## Daily Range Structure (6 Fase)

IPDA algorithm mengantarkan harga melalui urutan fase yang deterministik:

```
Daily Range Structure:
  1. Price Equilibrium   (= Asian range = Consolidation)
  2. Manipulation        (= Judas Swing melalui news / sebelum/setelah midnight)
  3. Expansion           (= HOD/LOD terbentuk sebelum 5:00 AM NY)
  4. Reversal            (= reversal di London session)
  5. Retracement         (= pullback 8:00-8:30 AM → FVG/Imbalance)
  6. Consolidation       (= 16:00 EST hingga akhir hari)
```

### Detail Timing Harian

| Fase | Waktu (EST) | Deskripsi |
|---|---|---|
| **Price Equilibrium** | 20:00 – 00:00 | Asian range = konsolidasi |
| **Manipulation** | 00:00 – 02:00 | Judas Swing via news driver, melalui midnight open |
| **Expansion** | 02:00 – 05:00 | London session → HOD atau LOD terbentuk |
| **Reversal** | 05:00 – 08:00 | Market returns to consolidation (pre-NY) |
| **Retracement** | 08:00 – 08:30 | Pullback → FVG/Imbalance entry zone |
| **Expansion (NY)** | 08:30 – 10:00 | NY continuation atau reversal |
| **Reversal (LDN Close)** | 10:00 – 12:00 | London close = potential reversal |
| **Consolidation** | 16:00 – 20:00 | Daily closing consolidation |

### Weekly Range Structure

| Fase | Waktu | Deskripsi |
|---|---|---|
| **Consolidation** | Sunday open | Weekly open consolidation |
| **Expansion** | Monday | Directional move mulai |
| **Reversal** | Monday/Tuesday | Judas Swing (weekly) |
| **Expansion 2** | Midweek | True direction expansion |
| **Consolidation** | Wednesday | Midweek pause |
| **Reversal/Retracement** | Thu/Fri | Akhir minggu reversal/pullback |

---

## Aturan Price Delivery (Fundamental Rules)

```
RULE 1: Consolidation SELALU diikuti Expansion
  Consolidation → Expansion (ALWAYS)
  Tidak ada: Consolidation → Retracement (TIDAK PERNAH langsung)
  Tidak ada: Consolidation → Reversal (TIDAK PERNAH langsung)

RULE 2: Setelah Expansion, ada DUA pilihan
  Expansion → Retracement (pullback ke OB/FVG, lalu Expansion lagi)
  Expansion → Reversal (perubahan trend total)

RULE 3: Tidak ada Consolidation langsung setelah Expansion
  Market does NOT: Consolidation → Expansion → Consolidation
  Market DOES:     Consolidation → Expansion → Retracement → Expansion → Consolidation
  OR:              Consolidation → Expansion → Reversal → Expansion (opposite) → Consolidation

FLOW DIAGRAM:
  START → CONSOLIDATION → EXPANSION → [RETRACEMENT or REVERSAL]
                                            ↓              ↓
                                        EXPANSION    EXPANSION (opposite)
                                            ↓              ↓
                                      CONSOLIDATION  CONSOLIDATION
```

---

## Consolidation Detail

```
Reference Point IOF: Equilibrium (50% dari range)

Karakteristik:
  - Price constricted by trading range, no willingness to breakout yet
  - Market Makers BUILDING liquidity above AND below the range
  - Be patient: look for Displacement AWAY from Equilibrium
  - OB terbentuk saat price BREAK OUT dari konsolidasi

Identifikasi:
  - Sideways movement dengan equal highs dan equal lows
  - Narrow ATR relatif terhadap recent expansion
  - Volume rendah

Timing:
  - Asian (20:00-00:00 EST): daily consolidation
  - 5:00-8:00 AM EST: return to consolidation post-London
  - 12:00-13:00 EST: NY Lunch consolidation
  - 16:00+ EST: end-of-day consolidation
```

---

## Expansion Detail

```
Reference Point IOF: Willingness to move to next objective

Karakteristik:
  - Price moves QUICKLY from Equilibrium
  - Displacement candle breaks out of consolidation
  - Price prints OB whilst breaking out
  - OB found near or at Equilibrium of Consolidation

Expansion pada Buy Day:
  → Harga DROP sebelum 5:00 AM NY (Judas Swing)
  → Setelah manipulation selesai → NAIK ke HOD

Expansion pada Sell Day:
  → Harga NAIK sebelum 5:00 AM NY (Judas Swing)
  → Setelah manipulation selesai → TURUN ke LOD
```

---

## Retracement Detail

```
Reference Point IOF: FVG / Imbalance

Karakteristik:
  - Price retraces BACK WITHIN price range formed by Displacement
  - Areas not efficiently traded during expansion
  - FVG dan Imbalance = area yang tidak efisien saat expansion
  - "When thinking retracements → think FVG/Imbalance"

Timing Intraday:
  - Antara 8:00-8:30 AM EST (pre-NY open)
  - Setelah expansion London → pullback sebelum NY continuation

Entry Opportunity:
  - Entry di FVG/Imbalance saat retracement
  - Konfirmasi: harga hold di FVG → lanjutkan expansion
```

---

## Reversal Detail

```
Reference Point IOF: Liquidity Pool

Karakteristik:
  - Price COMPLETELY reverses current trend
  - Terjadi DI Liquidity Pools (bukan sembarang level)
  - Bullish LP: found UNDER old lows (SSL)
  - Bearish LP: found ABOVE old highs (BSL)

Kapan Terjadi:
  - London session: setelah Judas Swing → reversal ke true direction
  - NY session (10:00-12:00): London close reversal
  - Weekly: setelah weekly high/low terbentuk di Tue/Wed

Konfirmasi Reversal PO3:
  Bullish: harga turun bawah MNO (manipulation) →
           MSS terjadi → harga naik kembali DAN STAY di atas MNO
  Bearish: harga naik atas MNO (manipulation) →
           MSS terjadi → harga turun kembali DAN STAY di bawah MNO
```

---

## NY PM Session Opportunities

```
NY PM Session (1:30 PM – 4:00 PM EST):

Pendekatan:
  1. PO3 mini dari 1:30 PM open sebagai anchor
  2. Identifikasi konsolidasi NY Lunch (12:00-13:00)
  3. Cari POI DAN target di atas dan di bawah konsolidasi
  4. Setelah manipulation terjadi → entry model

Tiga Skenario:
  A. Continuation: lanjutkan arah morning trend (1:1 measured move)
  B. Reversal: berlawanan dengan morning jika ada MSS + displacement
  C. Consolidation→Afternoon Trend: pagi ranging → sore trending

Acceleration (closing):
  → 20 menit sebelum 16:00: market-on-close orders mulai aktif
  → 10 menit sebelum 16:00: maximum acceleration
  → Final HOD/LOD sering terbentuk di window ini
```

---

## Implementasi Pine Script

```pinescript
// PO3 Phase Detection (based on time, Daily context)
// UTC offsets: EST + 5

// Asian (Accumulation) = 20:00-00:00 EST = 01:00-05:00 UTC (next day)
bool po3_asian = (hour >= 1 and hour < 5)

// Manipulation/London = 00:00-05:00 EST = 05:00-10:00 UTC
bool po3_manipulation = (hour >= 5 and hour < 10)

// Expansion / NY = 08:30-12:00 EST = 13:30-17:00 UTC
bool po3_expansion = (hour >= 13 and minute >= 30) or (hour >= 14 and hour < 17)

// Retracement window = 8:00-8:30 EST = 13:00-13:30 UTC
bool po3_retracement = (hour == 13 and minute < 30)

// Consolidation (end of day) = 16:00+ EST = 21:00+ UTC
bool po3_consolidation = (hour >= 21)

// Weekly PO3: Judas Swing window (Mon-Wed)
bool weekly_judas_window = (dayofweek == dayofweek.monday or
                            dayofweek == dayofweek.tuesday or
                            dayofweek == dayofweek.wednesday)

// PO3 Confirmation: Midnight Open reference
var float midnight_open = na
if (hour == 5 and minute == 0)
    midnight_open := open

bool bullish_po3_confirm = close > midnight_open and low[1] < midnight_open
bool bearish_po3_confirm = close < midnight_open and high[1] > midnight_open
```

---

## Catatan Tambahan

- PO3 bukan "pattern" yang bisa ditrading langsung — ini adalah **framework kontekstual**
- **Setiap timeframe memiliki PO3-nya sendiri** — weekly PO3 berisi daily PO3 yang berisi intraday PO3
- Weekly Low biasanya Selasa atau Rabu (bullish week); Weekly High biasanya Rabu (bearish week)
- **Manipulation tanpa MSS = belum selesai** — tunggu MSS sebelum entry
- Gunakan Weekly PO3 untuk bias → Daily PO3 untuk entry → Intraday PO3 untuk execution
- "The Interbank Price Delivery Algorithm moves from consolidation to expansion — and between these two periods, Manipulation will happen to take out retail buy or sell stops before the real expansion move."

---

---

# Market Structure & Bias Framework Logic (Ch15)

## Definisi

**Market Structure** adalah pola susunan swing high dan swing low yang mencerminkan kondisi pasar saat ini. Dikombinasikan dengan **Bias** (arah pergerakan yang paling mungkin), market structure menentukan sisi trading yang benar dan PD array yang relevan.

> "Market structure and bias are crucial concepts in making informed trading decisions."
> "This is because Price is Fractal. What can be seen on a Monthly Chart, can be seen on a Daily Chart. What can be seen on a Daily Chart, can be seen on a 5 Minute Chart."

---

## Swing High / Swing Low

```
Swing High: candle dengan high lebih tinggi dari candle sebelum DAN sesudahnya
  → Terbentuk jika: high[n] > high[n+1] AND high[n] > high[n-1]

Swing Low: candle dengan low lebih rendah dari candle sebelum DAN sesudahnya
  → Terbentuk jika: low[n] < low[n+1] AND low[n] < low[n-1]
```

---

## Tiga Pertanyaan Framework (Pra-Trading)

Sebelum entry, jawab tiga pertanyaan kunci:

```
1. Did the Market take out any Significant Liquidity Pools?
   → BSL atau SSL sudah diambil?
   → Jika ya → expect rebalancing atau reversal

2. Did the Market hit a Significant FVG?
   → HTF FVG sudah tersentuh / diisi?
   → Jika ya → expect reaction (support / resistance)

3. Did the Market create a Market Structure Break with Displacement?
   → MSS terjadi dengan displacement candle?
   → Jika ya → trend baru sedang terbentuk
```

---

## Menentukan Draw on Liquidity (DOL)

```
Setelah framework ditentukan, identifikasi DOL berikutnya:

Pertanyaan DOL:
  - Do we have clean lows or highs? (= external liquidity pool)
  - Do we have any FVGs that need to be rebalanced? (= internal liquidity)

Prioritas:
  BSL/SSL (External Liquidity) > FVG/Imbalance (Internal Liquidity)
  → Market akan selalu bergerak dari IRL ke ERL atau sebaliknya
```

---

## ITH / ITL (Intermediate Term High / Low)

```
Intermediate Term High (ITH):
  → Terbentuk ketika imbalance (FVG) diisi, kemudian market membuat high baru
  → ITH = high tersebut
  → Jika ITH tidak lebih tinggi dari dua Short-Term Highs (STH) sebelumnya → market SANGAT LEMAH

Intermediate Term Low (ITL):
  → Terbentuk ketika imbalance diisi, kemudian market membuat low baru
  → ITL = low tersebut

Hierarki Swing:
  LTH (Long-Term High) > ITH (Intermediate-Term High) > STH (Short-Term High)
  LTL (Long-Term Low)  > ITL (Intermediate-Term Low)  > STL (Short-Term Low)
```

---

## Market Phases (Fase Pasar dalam Struktur)

```
ACCUMULATION PHASE:
  → Proses membuka posisi besar oleh Market Maker
     tanpa menyebabkan pergerakan harga yang signifikan berlawanan arah
  → Terlihat sebagai: konsolidasi / ranging / sideways
  → Smart money diam-diam membangun posisi

MANIPULATION PHASE:
  → Rilis berita negatif membuat weak players panik → jual massal
  → Rilis berita positif membuat weak players FOMO → beli massal
  → Market Weakness manifests in moments of UPTREND
  → Market Strength manifests in moments of DOWNTREND
  → Retail trader terjebak di sisi yang salah

DISTRIBUTION PHASE:
  → Reverse dari akumulasi
  → Market Maker melepas posisi saat retail masuk terlambat
  → Terlihat sebagai: expansion candle ke arah sebenarnya
  → "Distribution is where the opposite side of the candle forms"
```

---

## Bearish Market Structure — Bias Stages

```
Bearish Market Structure (Bullish → Bearish Transition):

Stage 1 (Bearish Swing High terbentuk):
  1. Butuh Swing High dengan Bearish Context (HTF bearish)
  2. Pelajari apa yang terjadi pada 3rd Candle intraday
  3. Jika 3rd Candle intraday = Reversal → expect 4th Candle = Expansion LOWER

Stage 2 (Premium Retracement):
  → Setelah Swing High dikonfirmasi dengan 4th candle close
  → Harga kemungkinan besar membangun short-term premium
     sebelum expansion lebih rendah lagi
  → Inilah momen: tunggu harga trade BACK INTO Premium
     (counter-trend level) → harga pause/konsolidasi di sana
  → POI: FVG Weekly, Bullish OB, dll.
```

---

## Close Proximity Entries (CPE)

CPE adalah **area di mana Smart Money mencari entry short atau long** setelah akumulasi terjadi dekat opening price.

```
BEARISH CPE:
  → Di atas atau dekat Opening Price (Premium area)
  → Smart Money mengakumulasi net short di sana
  → Manipulation: Judas Swing ke atas saat news / market open
  → Distribution: 3:30-3:45 PM NY (closing candle expansion)

  Rumus Opening Range:
    Step 1: Open + HOD = Opening Range Projection
    Step 2: Opening Range Projection - Opening Price = Opening Range
    → Proyeksikan 1:1 deviation KE BAWAH dari Open = CPE zone short

BULLISH CPE:
  → Di bawah atau dekat Opening Price (Discount area)
  → Smart Money mengakumulasi net long di sana
  → Manipulation: Judas Swing ke bawah → ambil SSL
  → Distribution: expansion ke atas setelah MSS
```

---

## Top-Down Analysis (6-Step Execution)

```
Step 1: Daily Chart
  → Tentukan current framework (Liquidity? FVG? MSS?)
  → Identifikasi DOL: BSL/SSL atau FVG yang perlu direbalanced
  → Bias: bullish atau bearish?

Step 2: 4-Hour Chart
  → Identifikasi HTF Levels yang memberi Trade Setup Opportunity
  → "Trade Ideas will be built upon levels derived from the Higher Time Frame"
  → Cari Key Resistance/Support Level (ITH/ITL, OB+FVG)

Step 3: 1-Hour Chart
  → Intermediate-Term Perspective: lebih banyak definisi struktur
  → Konfirmasi area konsolidasi, buy stops, sell stops
  → Managing trades dilakukan via mid-level timeframe

Step 4: 15-Minute Chart
  → Short-term Perspective: refine entry dan exit
  → Cari confluence: SSL/BSL, SMT Divergence, FVG
  → Entry timing semakin presisi

Step 5: 5-Minute Chart
  → Konfirmasi entry via price action signals
  → Align dengan analisis dari HTF
  → Cari bullish OB setelah run on sellstops

Step 6: 1-Minute Chart
  → Fine-tune entry
  → Set stop-loss dan take-profit levels
  → Execution level
```

---

## Implementasi Pine Script

```pinescript
// Swing High/Low Detection
bool is_swing_high = high[2] > high[1] and high[2] > high[3]
bool is_swing_low  = low[2] < low[1] and low[2] < low[3]

// ITH/ITL Tracking (simplified)
var float ith = na
var float itl = na

if is_swing_high
    ith := high[2]
if is_swing_low
    itl := low[2]

// Market Phase Detection (simplified)
bool accumulation = ta.atr(14) < ta.atr(14)[20] * 0.7   // ATR compression
bool manipulation = math.abs(close - open) > ta.atr(14) * 0.3 and volume > ta.sma(volume, 20) * 1.5
bool distribution = math.abs(close - open) > ta.atr(14) * 1.5 and close > open[1]

// CPE zone: 1:1 deviation from Open
var float day_open = na
if (hour == 13 and minute == 30)
    day_open := open

var float hod = ta.highest(high, 390 / timeframe.multiplier)
float or_proj = day_open + (hod - day_open)
float cpe_bear = or_proj - (hod - day_open)   // 1:1 below open = bearish CPE

// Three Framework Questions
var float htf_fvg_top = na
var float htf_fvg_bot = na
bool fvg_hit        = low <= htf_fvg_top and low >= htf_fvg_bot
bool liquidity_taken = high > ith or low < itl
bool mss_detected   = is_swing_low and close > ith   // simplified bullish MSS
```

---

## Catatan Tambahan

- **Avoid picking tops and bottoms** — retail methods yang mencoba ini akan gagal lebih sering
- Jika market salah arah (invalid bias): "If you are wrong by following trend, the market will give you a chance to get in on the opposite side"
- **ITH lebih tinggi dari 2 STH** = konfirmasi bullish; **ITH tidak lebih tinggi** = market sangat lemah
- **Daily chart adalah prioritas utama** — "That's where the money is. Most of your time should be focused on determining where the daily chart will be for the next day, two, or week."
- Market weakness muncul di momen uptrend; market strength muncul di momen downtrend

---

---

# Multiple Setups Inside Trading Session Logic (Ch16)

## Definisi

Chapter 16 membahas cara mengidentifikasi **beberapa setup valid dalam satu sesi trading** berdasarkan Daily BIAS, Institutional Order Flow (IOF), dan OTE (Optimal Trade Entry) zones. Semua setup berasal dari satu logika: **ikuti arah smart money, bukan retail.**

---

## Daily BIAS — 3-Step Process

```
Step 1: STRUCTURE
  → Analisis external markets (premium/discount)
  → Identifikasi impulse structure: strong trending moves, rejections, breakouts
  → Map structure levels: support/resistance, liquidity zones

Step 2: LIQUIDITY
  → Identifikasi liquidity pools di atas (BSL) dan di bawah (SSL)
  → Clean highs/lows = un-raided liquidity pools
  → FVG yang belum direbalanced = internal liquidity yang perlu diisi

Step 3: MANIPULATION
  → Tunggu manipulation (Judas Swing) untuk konfirmasi arah sebenarnya
  → Manipulation melalui: news release, session open, killzone

Tiga Pertanyaan BIAS:
  - "What was taken?" → liquidity atau imbalance yang sudah diambil
  - "Where can this lead?" → DOL berikutnya
  - "What hasn't been taken yet?" → sisa liquidity yang belum diambil = next target
```

---

## Institutional Order Flow (IOF)

```
Definisi:
  IOF/OF = aliran order besar dari institusi untuk membeli/menjual
  → menentukan arah harga ke POI (area of interest) big capital
  → Large orders dari institutions = signifikan price movements
  → Pada bearish OF: lower lows dan lower highs; price takes sell stops
  → Pada bullish OF: higher highs dan higher lows; price takes buy stops

Logika Order Flow:
  → Seluruh impulse candle TANPA pullback = Order Flow
  → Saat OF terbentuk: large capital punya 2 posisi terbuka (manipulatif + utama)
  → OF AKTIF setelah price keluar dari zona OF dan hold di atasnya
  → "The Order Flow on a higher timeframe looks like an Order Block"
  → Price akan TEST (mitigate) zona OF untuk menutup posisi manipulatif
    → kemudian bounce ke arah berlawanan
```

### Order Flow Formation (Bearish)

```
Bearish Order Flow:
  1. Terbentuk Swing High (manipulatif — take BSL)
  2. Market Shift ke bearish (MSS) → harga break below support
  3. Retracement ke zona OF (area di antara swing high dan breakdown)
  4. Zona OF = OB+FVG area
  5. Konfirmasi: harga masuk zona OF → bearish reaction → expansion ke bawah

Bearish OF Logic:
  → Large capital membuat manipulative buy untuk mengambil sell stops (BSL)
  → Kemudian membuka posisi short utama
  → Price return ke OF zone untuk menutup long yang tidak profitable
  → Bounce ke bawah (distribusi)
```

### Order Flow Formation (Bullish)

```
Bullish Order Flow:
  1. Terbentuk Swing Low (manipulatif — take SSL)
  2. Market Shift ke bullish (MSS) → harga break above resistance
  3. Retracement ke zona OF
  4. Zona OF = OB+FVG area (bullish)
  5. Konfirmasi: harga masuk zona OF → bullish reaction → expansion ke atas

Bullish OF Logic:
  → Large capital membuat manipulative sell untuk mengambil buy stops (SSL)
  → Kemudian membuka posisi long utama
  → Price return ke OF zone → close unprofitable short positions
  → Bounce ke atas (distribusi)
```

---

## Dealing Range & OTE Zone

```
Dealing Range (Fibonacci-based):
  → Run Fibonacci retracement dari Highest High ke Lowest Low (atau sebaliknya)
  → 50%  = Equilibrium (fair value)
  → 62%  = OTE zone mulai (Optimal Trade Entry)
  → 70.5% = OTE midpoint
  → 79%  = OTE zone berakhir

  *** PENTING:
  Fibonacci levels TIDAK bertindak sebagai support/resistance secara langsung.
  Gunakan Fib untuk MENCARI "Liquidity Blocks" di dalam OTE zone:
  → FVG di antara 62-79% = high-probability entry
  → OB di antara 62-79% = high-probability entry

Cara Menentukan Dealing Range:
  1. Run Fibonacci dari highest high ke lowest low dalam dealing range
  2. Perhatikan area konsolidasi → ini menunjukkan fair value
  3. Gunakan area konsolidasi untuk menentukan dealing range yang benar
  4. Price tidak selalu masuk OTE zone → kadang cukup 50% (equilibrium) sudah bereaksi
```

---

## Finding Entry Point (Multi-TF)

```
Step 1: 4H Chart → tentukan struktur + premium/discount zone
Step 2: 1H Chart → tentukan struktur + cari Fibonacci OTE zone
Step 3: Cari OB atau FVG di dalam OTE zone (62-79% retracement)
Step 4: Entry ketika harga menyentuh OB/FVG di dalam OTE
Step 5: Stop-loss di bawah OTE zone (bearish) atau di atas OTE zone (bullish)

Contoh Bullish Entry:
  → 4H: bullish struktur + price di discount zone
  → 1H: price retraces ke 62-79% dari swing low ke swing high
  → Di zona 62-79%: ada 1H OB+ atau FVG+ → entry long
  → SL: di bawah swing low
  → TP: ke swing high atau BSL berikutnya
```

---

## Implementasi Pine Script

```pinescript
// OTE Zone (Optimal Trade Entry — 62% to 79% retracement)
float swing_high = ta.highest(high, 50)
float swing_low  = ta.lowest(low, 50)
float range      = swing_high - swing_low

// Bullish OTE (retracement in uptrend)
float bull_ote_top = swing_high - range * 0.618
float bull_ote_bot = swing_high - range * 0.786

// Bearish OTE (retracement in downtrend)
float bear_ote_top = swing_low + range * 0.786
float bear_ote_bot = swing_low + range * 0.618

// Equilibrium (50%)
float eq = math.avg(swing_high, swing_low)

// IOF Detection (simplified): impulse without pullback
bool bullish_iof = close > open and close[1] > open[1] and close[2] > open[2] and
                   low > low[1] and low[1] > low[2]   // 3 bars trending up, no pullback

bool bearish_iof = close < open and close[1] < open[1] and close[2] < open[2] and
                   high < high[1] and high[1] < high[2]   // 3 bars trending down

// BIAS Questions
bool liquidity_taken_high = ta.crossover(high, ta.highest(high[1], 20))  // BSL taken
bool liquidity_taken_low  = ta.crossunder(low, ta.lowest(low[1], 20))    // SSL taken

plot(bull_ote_top, "Bull OTE Top", color=color.green, style=plot.style_stepline)
plot(bull_ote_bot, "Bull OTE Bot", color=color.lime,  style=plot.style_stepline)
plot(eq,           "EQ (50%)",     color=color.gray,  style=plot.style_stepline)
```

---

## Catatan Tambahan

- **"The main idea of BIAS is to determine where the daily candle will move, based on the liquidity grab and the one that has not yet been grabbed."**
- Setup high-probability: Killzone + HTF POI + IOF activation + OTE zone = 4 confluence factors
- Hindari masuk sebelum manipulation selesai — tunggu MSS di LTF
- Jika bearish dan intermediate high tembus → keluar trade, tunggu setup baru
- Multiple setups dalam satu sesi mungkin terjadi jika bias tetap konsisten: entry di setiap retest OTE yang baru

---

---

# Macro & Seasonal Analysis Logic (Ch17)

## Definisi

**Macro Analysis** adalah analisis faktor-faktor besar yang mempengaruhi pasar secara struktural, termasuk US Dollar Index (DXY), korelasi antar aset, dan **Seasonal Tendencies** (pola musiman berulang). Pemahaman ini membantu menentukan **bias jangka menengah-panjang** sebelum melakukan analisis intraday.

---

## US Dollar Index (DXY)

```
DXY:
  → Index nilai US Dollar terhadap basket mata uang utama
  → Dibuat oleh JP Morgan pada 1973
  → Komponen: EUR (57.6%), JPY (13.6%), GBP (11.9%), CAD (9.1%), SEK (4.2%), CHF (3.6%)

Korelasi DXY vs Indeks Ekuitas:
  → Umumnya INVERSE correlation: DXY naik = ES/NQ/YM turun
  → Tiga skenario utama:
```

### Tiga Skenario DXY vs Ekuitas

| Skenario | DXY | ES / NQ / YM |
|---|---|---|
| **#1** | Turun (Bearish) | Naik (Bullish) |
| **#2** | Naik (Bullish) | Turun (Bearish) |
| **#3** | Konsolidasi | Naik (Bullish) |

```
Catatan Skenario #3:
  → Ketika DXY konsolidasi (tidak trending), equities cenderung naik
  → Smart capital masuk pasar ekuitas ketika ketidakpastian DXY rendah

Cara Menggunakan Korelasi:
  → DXY trending higher + ES trending lower → investors risk-averse, pertimbangkan short ES
  → DXY trending lower + ES trending higher → investors risk-on, pertimbangkan long ES
  → Gunakan korelasi untuk KONFIRMASI, bukan sinyal utama
```

---

## Seasonal Tendencies (Pola Musiman Tahunan)

```
Pasar mengikuti pola musiman yang berulang akibat:
  → Smart capital masuk/keluar di waktu tertentu
  → Tax year-end dan fund rebalancing
  → Likuiditas rendah saat liburan
```

### Pola per Bulan/Kuartal

| Periode | Karakteristik | Implikasi Trading |
|---|---|---|
| **Jan** | New Year momentum, fund inflow | Bullish continuation atau reversal dari Dec |
| **Feb – Apr** | Smart capital masuk masif → Rally | Trending pasar paling optimal dan probabilistik tinggi |
| **May – Aug** | "Sell in May and go away" — summer depression | Downtrend → konsolidasi, volatilitas rendah |
| **Sep – Oct** | Historically bearish (September effect) | Potensi correction atau bear move |
| **Nov – Dec** | Year-end rally, Santa Claus rally | Bullish momentum, rebalancing |

### Detail Periode Utama

```
FEBRUARI – APRIL:
  → Pasar keluar dari konsolidasi (Jan), masuk trending
  → Smart capital masuk dalam volume besar
  → "Most HIGHLY probabilistic trading models" selama periode ini
  → Strategi: ikuti trend, gunakan retracements sebagai entry

MEI – AGUSTUS:
  → "Sell in May and go away" — dikonfirmasi oleh perilaku smart capital
  → Smart capital take profits sebelum summer low volatility
  → Pasar membentuk downtrend → konsolidasi (summer depression)
  → Agustus = likuiditas terendah (liburan profesional)
  → Strategi: reduce exposure, hindari counter-trend

SEPTEMBER – OKTOBER:
  → "September Effect" — historis bulan paling bearish
  → Fund managers dump positions sebelum Q4 rebalancing
  → Strategi: defensive, wait for Q4 confirmation

NOVEMBER – DESEMBER:
  → Year-end rally (Santa Claus Rally)
  → Rebalancing fund menuju akhir tahun fiskal
  → Strategi: long bias, take profits sebelum Dec 31
```

---

## Implementasi Pine Script

```pinescript
// DXY Correlation Tracking (requires DXY data)
float dxy_close  = request.security("DXY", timeframe.period, close)
float dxy_change = (dxy_close - dxy_close[20]) / dxy_close[20] * 100   // 20-bar % change

float es_close   = close
float es_change  = (es_close - es_close[20]) / es_close[20] * 100

// Correlation scenarios
bool scenario_1 = dxy_change < -0.5 and es_change > 0.5   // DXY down, ES up
bool scenario_2 = dxy_change > 0.5  and es_change < -0.5  // DXY up, ES down
bool scenario_3 = math.abs(dxy_change) < 0.5 and es_change > 0   // DXY flat, ES up

// Seasonal Bias
int m = month
bool feb_apr_bullish = m >= 2 and m <= 4
bool may_aug_bearish = m >= 5 and m <= 8
bool sep_oct_bearish = m == 9 or m == 10
bool nov_dec_bullish = m >= 11

// Background color for seasonal context
bgcolor(feb_apr_bullish ? color.new(color.green,  92) : na, title="Feb-Apr Bull Season")
bgcolor(may_aug_bearish ? color.new(color.red,    92) : na, title="May-Aug Bear Season")
bgcolor(sep_oct_bearish ? color.new(color.orange, 92) : na, title="Sep-Oct Risk Season")
bgcolor(nov_dec_bullish ? color.new(color.blue,   92) : na, title="Nov-Dec Bull Season")
```

---

## Catatan Tambahan

- **Seasonal tendencies bukan rule absolut** — mereka adalah probabilistic tendencies, bukan kepastian
- DXY korelasi paling kuat untuk: ES (S&P500), NQ (Nasdaq), YM (Dow Jones), Gold
- Untuk Forex: DXY naik = USD pairs bullish (USD/JPY naik, EUR/USD turun)
- **Kombinasikan dengan weekly/daily bias** — seasonal hanya memberikan medium-term context
- "Sell in May" lebih reliable di tahun-tahun dengan DXY yang juga menguat bersamaan

---

---

# FOMC & NFP Study Logic (Ch18)

## Definisi

Chapter 18 membahas **"Taboo Trading Days"** — hari-hari di mana market sangat berbahaya untuk diperdagangkan karena didominasi oleh manipulasi institusional. Dua hari paling berbahaya adalah **FOMC day** dan **NFP day**.

---

## FOMC Days (Federal Open Market Committee)

```
FOMC:
  → Komite yang menentukan suku bunga di Amerika Serikat
  → Meeting dilakukan 8x per tahun
  → Hari meeting = TABOO trading day

Kenapa FOMC berbahaya:
  → Keputusan FOMC berdampak signifikan terhadap pasar
  → Trader cenderung membuat keputusan impulsif
  → Increased volatility + potential for false moves
  → Manual intervention by institutions

FOMC Two-Stage Event (jam 2:00 PM EST):
  Stage 1 (Announcement):
    → Initial run setelah announcement = FAKE MOVE
    → Dirancang untuk menjebak trader yang tidak sabar
    → Ini adalah Judas Swing — arah pertama BERLAWANAN dengan arah sebenarnya

  Stage 2 (Press Conference):
    → Gerak pasar saat conference = REAL DIRECTION
    → Analogu "Tsunami": air surut ke belakang (Stage 1) sebelum ombak besar menerjang (Stage 2)
    → "The market may move in one direction initially before reversing during the conference portion"
```

---

## NFP Days (Non-Farm Payroll)

```
NFP:
  → Laporan bulanan jumlah pekerjaan baru di ekonomi AS
  → Dirilis setiap Jumat pertama setiap bulan
  → Dapat berdampak signifikan terhadap pasar

Kenapa HINDARI Kamis & Jumat NFP Week:
  → "Heavy manipulation that week, market tends to be really unfavorable"
  → Market cenderung konsolidasi dalam range kecil yang tidak bermakna
  → "A lot of wait and see type conditions"
  → Bisa menyebabkan kerugian bagi trader baru yang belum berpengalaman

Selama NFP Week:
  → Market: consolidate, gyrate around in a small, meaningless, aimless range
  → Menunggu news untuk drop
  → Tidak ideal untuk trading setup ICT

Risks di hari ini:
  - Increased volatility → sulit profit
  - Liquidity issues → banyak trader ragu
  - Potential for false moves → stop-out trader
```

---

## Taboo Days Lainnya

```
1. Holiday pada Mon/Tue/Wed di NFP Week:
   → Kamis dan Jumat SETELAH NFP release = fokus untuk trading
   → Pergerakan pasar bisa signifikan pada hari-hari tersebut

2. Senin di awal minggu (atau sebelum Rabu):
   → Particularly volatile karena increased movement

3. Setelah FOMC yang menciptakan whipsaw:
   → Hindari London open hari berikutnya
   → Manual intervention by institutions masih berlangsung
   → Seek and Destroy profile expected pada Kamis (Jumat)
   → Setelah large expansion move
   → High resistance liquidity conditions expected
```

---

## Seek and Destroy Market Profile

```
Profil pasar yang umum di taboo days:
  Buy Stops Blown Out (atas)
        ↓
  BSL triggered → short taken out → new longs on breakout active
        ↓
  Buy Stops Orders zone
        ↓
  Sell Stops Orders zone
        ↓
  SSL triggered → long taken out → new shorts on breakout active
        ↓
  Sell Stops Blown Out (bawah)

→ Profil ini: TIDAK mengikuti narasi bias yang jelas
→ Harga terus berputar membersihkan BSL lalu SSL lalu BSL lagi
→ DXY ranging + Futures saling bertentangan arah = "High Resistance Liquidity Conditions"
→ Sangat sulit trading secara profitable di kondisi ini
```

---

## Analyzing Market Manipulation During Significant Economic Reports

```
→ Market manipulation adalah kejadian umum saat economic reports besar
→ FOMC = contoh utama (two-stage event di 2 PM)
→ First run setelah event = FAKE MOVE untuk menjebak trader
→ Saat press conference = market sering bergerak berlawanan arah dari initial run

"This can be compared to a tsunami, where the water moves away from the beach
before the big wave hits."

Prinsip utama:
  → Jangan terjebak di initial run
  → Tunggu stage 2 (press conference) untuk konfirmasi arah sebenarnya
  → ICT 2022 Mentorship Model tetap berlaku: liquidity sweep → MSS → FVG → entry
```

---

## Implementasi Pine Script

```pinescript
// Taboo Day Detection
int dayofweek_val = dayofweek
int day_month     = dayofmonth

// NFP = first Friday of month
bool is_friday    = dayofweek == dayofweek.friday
bool is_first_fri = is_friday and day_month <= 7

// Simple FOMC schedule check (approximate - 8x per year)
// Bulan FOMC: Jan, Mar, May, Jun, Jul, Sep, Nov, Des
bool is_fomc_month = month == 1 or month == 3 or month == 5 or month == 6 or
                     month == 7 or month == 9 or month == 11 or month == 12

// High Resistance Liquidity (DXY ranging proxy)
float dxy_range = ta.highest(high, 20) - ta.lowest(low, 20)
bool hrl_condition = dxy_range < ta.atr(14) * 5   // DXY dalam range sempit

// Taboo day flag
bool taboo_day = is_first_fri  // NFP day
bgcolor(taboo_day ? color.new(color.red, 85) : na, title="NFP Taboo Day")

// FOMC 2-stage: after 2PM EST = Stage 2 (real direction)
bool fomc_stage2 = hour >= 14 and hour <= 17 and is_fomc_month
```

---

## Catatan Tambahan

- **"Don't trade during taboo days"** — ICT sangat menekankan ini untuk trader baru
- FOMC stage 1 (fake move) biasanya terjadi antara 2:00-2:30 PM EST; stage 2 mulai saat press conference (~2:30 PM)
- **NFP week Thursday**: biasanya seek and destroy profile — stop hunt ke dua sisi
- **NFP Friday**: rilis pukul 08:30 EST — biasanya expansion besar tapi tidak predictable arahnya
- Jika terjebak di FOMC fake move: tunggu stage 2 dan lihat apakah setup ICT terbentuk
- "ICT advises avoiding trading London open after FOMC events that create whipsaw"
- High resistance liquidity (HRL) vs Low resistance liquidity (LRL): pilih trade di LRL environment

---

---

# Daily Rebalance Theory Logic (Ch20)

## Definisi

**Daily Rebalance Theory** adalah konsep bahwa **pasar bergerak secara siklus dan dapat diprediksi** berdasarkan aliran order institusional. Harga tidak random — Market Makers secara aktif menggerakkan harga ke level-level yang menguntungkan mereka, dan pola ini berulang setiap hari.

> "Price action is not random. Market Makers specifically move price to where they want it to go."

---

## Seasonal Tendencies (Pola Musiman)

```
Seasonal trends = periode dalam setahun di mana market/asset class paling mungkin bergerak.
Data historis:
  → S&P 500 E-Mini (CME) — 25 Year Seasonal (1997-2021)
  → NASDAQ 100 E-Mini (CME) — 26 Year Seasonal (1996-2021)
  → E-mini DJIA Index (CBOT) — 25 Year Seasonal (1997-2021)
  → FT-SE 100 (EURONEXT) — 39 Year Seasonal (1983-2021)

Temuan umum:
  → Mei = bulan ketika market biasanya turun untuk index futures dan stocks
  → Ini selaras dengan "Sell in May" seasonal tendency dari Ch17
  → Gunakan seasonal data untuk KONFIRMASI bias medium-term
```

---

## Market Cycles (Siklus Pergerakan)

```
Siklus Buy Stops ↔ Sell Stops:
  Market moves:
    Buy Stops → Sell Stops → Buy Stops → ...

Siklus Fair Value ↔ Premium ↔ Discount:
  Market moves:
    Fair Value → Discount → Premium → Fair Value → ...

"The market moves between three breakpoints: Discount, Premium, or Fair Value."
```

---

## Immediate Rebalance (Konsep Kunci)

Immediate Rebalance adalah fenomena di mana **harga langsung kembali ke level high/low candle sebelumnya** setelah candle baru terbentuk, menciptakan "rocket fuel" untuk pergerakan selanjutnya.

```
Bullish Immediate Rebalance:
  [Candle X membuat High] → candle berikutnya open DI ATAS High[X] tapi langsung turun ke High[X]
  → Level High[X] langsung direbalance
  → Ini seperti "rocket fuel" → harga akan naik tajam setelah ini

Bearish Immediate Rebalance:
  [Candle X membuat Low] → candle berikutnya open DI BAWAH Low[X] tapi langsung naik ke Low[X]
  → Level Low[X] langsung direbalance
  → Harga akan turun tajam setelah ini

Diagram:
  Bullish:              Bearish:
  [Green][Red][Green]   [Red][Green][Red]
         ←Imm.Reb.↓           ↑Imm.Reb.→
```

---

## Smart Money Perspective

```
Prinsip utama yang harus dipahami:
  1. Price is NOT random
  2. Market Makers SPECIFICALLY move price to where they want it to go
  3. Market Makers control the REASON why price goes in a direction

Implikasi praktis:
  → "It doesn't matter how many Retail traders buy or sell"
  → "The price is going to be set by Market Makers — they do things to line
     their own pockets and not Retail traders"
  → "It takes a perspective shift (Market Efficiency Paradigm) to view things
     from Smart Money's perspective and not what Retail logic teaches"

Konsekuensi:
  → Jika kamu melakukan kesalahan atau melewatkan sebuah move: JANGAN panik
  → "The market will give you another chance almost every day"
  → "Because everything repeats! Because everything is controlled by Smart Money"
  → Market Makers net long = price delivered at higher prices
  → Market Makers net short = price delivered at lower prices
```

---

## Algorithm Timing & Market Open Structure

```
Algoritma bekerja berdasarkan New York time:

  00:00 (Midnight NY):
    → Algorithm starts (reference point untuk daily order flow)
    → NY Midnight Open Price = reference level utama

  08:30 EST:
    → Algorithm starts looking for liquidity
    → Jika bearish: antara 08:30-09:30 = expect price trade HIGHER untuk find short setup
      (looks like breakout → sebenarnya Judas Swing)

  09:30 EST:
    → Stock market opens
    → Manipulation terbentuk: tampak seperti breakout ke retail
    → Sebenarnya: arah berlawanan dengan yang terlihat = real direction

Bearish Example (08:30-09:30):
  1. Take old high/BSL above: Smart money menjual buy stops
  2. Simultaneously: Smart money membeli SSL untuk close short positions
  → Price naik ke premium → Smart money enter short
  → Price turun ke discount → expansion
```

---

## SMT Divergence sebagai Konfirmasi Rebalance

```
SMT (Smart Money Technique) Divergence di ITH/ITL:

Step 3 dari SMT reversal model:
  "If market is going to have these internal turning points at major ITH/ITL,
   there should be some kind of signature or fingerprint there,
   a reoccurring phenomenon. It's always there in a rangebound market."

Contoh Bearish SMT:
  → ES membuat Higher High (tampak bullish)
  → NQ membuat Lower High (tidak konfirmasi)
  → Kesimpulan: HH pada ES = fake-out (Turtle Soup, run on stops)
  → Distribution sedang terjadi

Implikasi:
  → Cross pairs hanya membuat major moves saat DXY dalam consolidation
  → "If FED is holding dollar in narrow range → crosses will have big moves"
  → ES, NQ, YM, DXY = 4 instrumen utama untuk SMT analysis
```

---

## Implementasi Pine Script

```pinescript
// Immediate Rebalance Detection
bool bullish_imm_reb = low[0] <= high[1] and low[0] >= high[2]
// Candle saat ini turun ke level high candle sebelumnya = bullish immediate rebalance

bool bearish_imm_reb = high[0] >= low[1] and high[0] <= low[2]
// Candle saat ini naik ke level low candle sebelumnya = bearish immediate rebalance

// Market Cycle Tracking
float swing_high_20 = ta.highest(high, 20)
float swing_low_20  = ta.lowest(low, 20)
float eq_level      = math.avg(swing_high_20, swing_low_20)

bool price_at_buy_stops   = high >= swing_high_20 * 0.999
bool price_at_sell_stops  = low <= swing_low_20 * 1.001
bool price_at_fair_value  = math.abs(close - eq_level) < ta.atr(14) * 0.5

// NY Midnight Open Reference
var float ny_midnight_open = na
if (hour == 0 and minute == 0 and session.ismarket)
    ny_midnight_open := open

bool bullish_of = close > ny_midnight_open   // price above midnight = bullish OF
bool bearish_of = close < ny_midnight_open   // price below midnight = bearish OF

// Immediate Rebalance plot
plotshape(bullish_imm_reb, title="Bull Imm. Reb", style=shape.triangleup,
          location=location.belowbar, color=color.lime, size=size.small)
plotshape(bearish_imm_reb, title="Bear Imm. Reb", style=shape.triangledown,
          location=location.abovebar, color=color.red,  size=size.small)
```

---

## Catatan Tambahan

- **Immediate Rebalance = sinyal kuat**: ketika terbentuk setelah liquidity grab, kemungkinan besar akan terjadi ekspansi tajam
- Smart Money harus mengambil keuntungan dari posisi mereka → inilah yang menciptakan predictable cycles
- "It takes a perspective shift" — trader harus berpikir seperti Market Maker, bukan retail
- Seasonal tendencies dari data 25-39 tahun adalah bias probabilistik, bukan kepastian
- **Institutional Order Flow**: the easiest way to understand it is that markets are moving from buy stops to sell stops, from sell stops to buy stops; from Fair Value to Discount to Premium to Fair Value

---

---

# View Price with Institutional Perception Logic (Ch21)

## Definisi

**View Price with Institutional Perception** adalah pendekatan melihat harga **dari sudut pandang Market Maker/institusi**, bukan dari sudut pandang retail. Ini melibatkan penggunaan level-level referensi yang digunakan algoritma institusional: NWOG/NDOG, NY Midnight Opening Price, dan Daily Range Templates.

> "Don't predict low or high, you will only lose money! The algorithms work on New York time."

---

## NWOG & NDOG (New Week/Day Opening Gap)

### Definisi NWOG
```
NWOG (New Week Opening Gap):
  → Gap antara harga CLOSE Jumat dan harga OPEN Minggu
  → Bukan gap biasa — ini adalah "real dynamic fair value level"
  → Merupakan range antara dua price point spesifik
  → Di antara dua titik ini = ketidakpastian → ketidakpastian = peluang

Kenapa NWOG bekerja:
  → Perbedaan opini → ada trade di sana
  → "That's the reason why NWOG works, that's a real liquidity void"
  → Algorithm mengirimkan harga ke level ini untuk menawarkan fair value
    dan mendorong new business (peserta pasar baru)
```

### Cara Membaca NWOG
```
Cara menemukan NWOG di chart:
  → Gunakan LOWER timeframe (jangan D chart) — misalnya 5 menit
  → Mark range antara Sunday opening price & Friday closing price
  → NWOG hanya terlihat di "Regular Trading Hours" (bukan Electronic/Extended hours)
  → Midpoint NWOG = Consequent Encroachment (CE):
    Gunakan Fibonacci dari Friday close ke Sunday open → 50% = midpoint (CE)

Cara menggunakannya:
  → Perlakukan seperti FVG
  → Harga kemungkinan besar akan revisit dan bereaksi terhadap NWOG
  → Price juga menghormati CE (Consequent Encroachment) dari NWOG
```

### NDOG (New Day Opening Gap)
```
NDOG = sama seperti NWOG, tetapi untuk basis harian:
  → Gap antara penutupan hari sebelumnya dan pembukaan hari baru
  → Level S/R harian yang lebih granular
  → Institusi akan fill gap sebelum big move
```

### ICT Event Horizon PD Array
```
ICT Event Horizon PD Array:
  → Halfway between a NWOG and the closest NWOG = Event Horizon
  → "It will not allow price to escape the draw to the NWOG"
  → "It will create a surge towards the NWOG it got too close to but yet reached"

Cara menggunakannya:
  → Measure High-Low of NWOG dari 2 closest NWOGs
  → 50% antara 2 NWOG = Event Horizon level
  → Jika harga mendekati Event Horizon tapi belum mencapai NWOG →
    expect surge menuju NWOG tersebut

Penerapan:
  → Seperti "gravitational pull" — harga tertarik ke NWOG terdekat
  → Event Horizon adalah titik di mana pull ini menjadi inevitable
```

---

## NY Midnight Opening Price

```
Definisi:
  → Harga di mana market opens pada midnight (12 AM) Eastern Standard Time
  → Serves as reference point untuk daily order flow

Cara menggunakannya:
  → Outline NY Midnight Opening Price di chart untuk memisahkan setiap trading day
  → Membantu mengidentifikasi key price levels dan memahami market behavior

Pembacaan:
  BULLISH ORDER FLOW:
    → Market CONSISTENTLY trades dan stays ABOVE NY Midnight Opening Price
    → Cari peluang beli, terutama saat market break above Previous Day's High (PDH)

  BEARISH ORDER FLOW:
    → Market trades ABOVE midnight price, kemudian starts to DECLINE
    → Indikasi potential bearish order flow

Hubungan dengan PDH/PDL:
  → PDH = level resistance harian (Previous Day High)
  → PDL = level support harian (Previous Day Low)
  → Jika market di atas NY Midnight Open → bullish OF → target PDH
  → Jika market di bawah NY Midnight Open → bearish OF → target PDL
```

---

## Classic Buy / Sell Day Template

```
Classic Buy Day Template:
  Asia Session:   → Konsolidasi (range formation)
  London Open:    → Judas Swing ke BAWAH (take sell stops)
  London Session: → Reversal ke ATAS setelah SSL diambil
  NY Killzone:    → Ekspansi ke ATAS (daily uptrend)
  London Close:   → Retracement kecil atau kelanjutan

Classic Sell Day Template:
  Asia Session:   → Konsolidasi (range formation)
  London Open:    → Judas Swing ke ATAS (take buy stops)
  London Session: → Reversal ke BAWAH setelah BSL diambil
  NY Killzone:    → Ekspansi ke BAWAH (daily downtrend)
  London Close:   → Retracement kecil atau kelanjutan

Best day untuk Classic Buy/Sell:
  → "Unfolds mostly on Monday, Tuesday, and latest on Wednesday during London session"
  → Daily range akan berlangsung 7-8 jam setelah profile terbentuk
  → NY session memberikan retracement untuk melanjutkan trend London
```

### Bullish Classic Day (Detail)
```
Bullish version:
  → Selalu dimulai seperti Classic Buy: decline below opening price dulu
  → Kemudian price rallies ke HTF POI (bearish OB atau FVG)
  → Jika ini terjadi di NY session → classic market reversal
  → Digunakan untuk: reach bearish OB pada HTF, atau turtle soup raid, atau close range

  Sequence lengkap:
  1. London session → initial LOW of the day terbentuk
  2. NY session → run UP, create HIGH of the day (di sekitar London Close)
  3. Late NY → run back down clearing initial London low
  (Ideal: market dalam exhaustion berdasarkan HTF dominant trend)
```

### London Close Trend Trade (Classic Reversal Profile)
```
LONDON CLOSE TREND TRADE:
  → Trend trade entry untuk sync dengan daily trend / long-term price swing
  → Akan terjadi jika price trading ke HTF optimal trade entry

  For Bearish London Close Trend:
    → Day starts as Classic Buy day
    → Reversal di London Close/Late New York
    → Price trades BELOW London Low

  For Bullish London Close Trend:
    → Day starts as Classic Sell day
    → Reversal di London Close/Late New York
    → Price trades ABOVE London High

London Close Killzone:
  → Price action di London Close = retracement from HOD (bullish days)
  → atau retracement from LOD (bearish days)
  → Typical: 5-minute OTE setup — VERY SHORT TERM moves
  → Karakteristik: bisa menjadi continuation point ke NY afternoon
  → Atau bisa menjadi Reversal Point (mengubah arah dari sisi hari atau minggu)
```

---

## Session Reference Prices untuk OTE (NYKZ)

```
Di New York Kill Zone (NYKZ), tiga harga opening penting sebagai referensi OTE:

  02:00 AM EST (Asian Session Open):
    → Seringkali menjadi referensi level untuk intraday range
    → "In NY most of the time price will trade back to 02:00am Price"
    → Memberikan optimal trade entry

  05:00 AM EST (London Session Open):
    → "Mark the opening price of 05:00am EST"
    → Price setup New York Optimal Trade Entry in sync dengan H/L yang terbentuk di London
    → "When you don't see a Judas swing in London, expect the Judas to form in 05:00am EST"

  07:00 AM EST:
    → London Pre-market / early NY pre-market
    → Konfirmasi arah sebelum 09:30 official open

Key Insight:
  → Jika Judas Swing TIDAK terbentuk di London (02:00-05:00):
    → Tunggu Judas di 05:00 AM level
  → NYKZ = 07:00-11:00 AM EST untuk setup OTE terbaik
```

---

## Daily Range Templates

```
Two Narratives untuk Daily Range:
  1. Asia Session KONSOLIDASI
  2. Asia Session TRENDING (arah jelas)

                        │ Asia Konsolidasi          │ Asia Trending
─────────────────────────┼───────────────────────────┼──────────────────────────
London Session          │ Take BSL atau SSL         │ Follow Asia's directional
                        │ → kemudian reversal       │ trend
─────────────────────────┼───────────────────────────┼──────────────────────────
NY Session              │ Reverse clearing BSL/SSL  │ Continue atau reverse
                        │ dari London session       │ at London Close
```

---

## Implementasi Pine Script

```pinescript
// NY Midnight Opening Price
var float ny_midnight = na
if (hour == 0 and minute == 0)
    ny_midnight := open

// Session Reference Levels
var float open_0200 = na
var float open_0500 = na
if (hour == 2 and minute == 0)
    open_0200 := open
if (hour == 5 and minute == 0)
    open_0500 := open

// NWOG (New Week Opening Gap) — detect gap on Sunday open
bool is_sunday_open = dayofweek == dayofweek.sunday and hour == 0 and minute == 0
var float nwog_high = na
var float nwog_low  = na
var float nwog_ce   = na

if is_sunday_open
    nwog_high := open              // Sunday open
    nwog_low  := close[1]          // Friday close (simplified)
    nwog_ce   := math.avg(nwog_high, nwog_low)   // Consequent Encroachment

// Order Flow relative to NY Midnight
bool bullish_daily_of = close > ny_midnight
bool bearish_daily_of = close < ny_midnight

// Plot levels
plot(ny_midnight, "NY Midnight Open", color=color.purple, linewidth=1)
plot(open_0200,   "02:00 AM Open",    color=color.blue,   linewidth=1)
plot(open_0500,   "05:00 AM Open",    color=color.orange, linewidth=1)
plot(nwog_ce,     "NWOG CE",          color=color.yellow, linewidth=2)

// NWOG Box
var box nwog_box = na
if is_sunday_open
    nwog_box := box.new(bar_index, nwog_high, bar_index + 500, nwog_low,
                        border_color=color.new(color.yellow, 30),
                        bgcolor=color.new(color.yellow, 88))
```

---

## Catatan Tambahan

- **NWOG adalah "real liquidity void"** — sangat kuat sebagai S/R karena tidak ada trading di sana → ketidakpastian → institusi tertarik ke level ini
- **Event Horizon**: jika price sudah lewat 50% antara dua NWOG → hampir pasti akan mencapai NWOG terdekat
- **Classic Buy/Sell Day**: best setup di Senin-Rabu London session; hindari setup ini di hari FOMC/NFP
- NY Midnight Open = cara paling sederhana untuk mengidentifikasi bullish vs bearish daily order flow
- **"Don't predict high or low"** — gunakan templates untuk ANTICIPATE behavior, bukan predict exact level
- Session references (02:00, 05:00, 07:00) bekerja BERSAMA dengan Kill Zones dari Ch11
- ICT: "If you're wrong by following trend, the market will give you a chance to get in on the opposite side"

---

---

# General Range Trading Rules Logic (Ch22)

## Definisi

**General Range Trading Rules** adalah **4 cara untuk mendefinisikan batas-batas trading range** yang valid, yang kemudian digunakan dengan Fibonacci untuk menemukan midrange (50%), premium zone, dan discount zone sebagai area entry/target.

> "There is more than one way to draw a range. The key is: does the midrange make sense?"

---

## Prinsip Dasar Range Trading

```
Komponen Range:
  Range High (atas) = batas premium / potential sell zone
  Range Low (bawah)  = batas discount / potential buy zone
  Midrange (50%)     = Equilibrium / Fair Value

Fibonacci Range:
  0%   = Range Low
  50%  = Midrange (Equilibrium)
  100% = Range High

  → Upper half (50-100%): PREMIUM zone (jual di sini jika bearish)
  → Lower half (0-50%):   DISCOUNT zone (beli di sini jika bullish)

  Penting: Fibonacci levels TIDAK sebagai S/R langsung
  → Gunakan Fib untuk MENCARI Liquidity Blocks di dalam zona:
    FVG atau OB di 50-79% = high-probability entry
```

---

## Rule 1: The High/Low That Led to a Sweep of the Opposite Side

```
Deskripsi:
  → Temukan HIGH yang langsung menyebabkan terjadinya SWEEP ke range LOW
  → HIGH tersebut = Range High (batas atas)
  → LOW yang tersweep = Range Low (batas bawah)
  → Jalankan Fibonacci dari Range High ke Range Low

Diagram:
         Range High (High yang led to sweep)
              ↓
  ┌─────────────────────────────────── 100%
  │           /\      /\
  │          /  \    /  \
  │─────────── 50% ─────────────────── 50% (Midrange)
  │        /        \    /
  │       /          \  /
  └─────────────────────────────────── 0%
              Low of impulse (swept)

Catatan:
  → Trickier to spot: bukan high pertama yang terlihat, tapi high yang LANGSUNG
    menyebabkan sweep ke sisi yang berlawanan
  → "The midrange is not quite lining up" jika kamu gunakan high yang salah
```

---

## Rule 2: The High/Lows Before the Sweep of the Same Side

```
Deskripsi:
  → Bukan high yang "menyebabkan" sweep, tapi high/low SEBELUM liquidity grab
  → "Range high" = High yang terlihat sebelum liquidity sweep terjadi
  → "Low of impulse" = titik terbawah (yang menjadi impulse)

Diagram (Bearish contoh):
         Range High (high before liquidity grab)
              ↓
  ┌─────────────────────────────────── 100%
  │    /\  ← High yang grabbed liquidity
  │   /  \              /\
  │──────── 50% ──────────────────── 50% (Midrange)
  │       \            /
  │        \          /
  └─────────────────────────────────── 0%
            Range Low (low before grab)

Perbedaan dengan Rule 1:
  → Rule 1: gunakan high yang LED ke sweep
  → Rule 2: gunakan high SEBELUM sweep (range high yang lebih rendah)
  → Memberikan range boundary yang berbeda → test mana yang midrange-nya lebih akurat
```

---

## Rule 3: Equal Highs & Lows

```
Deskripsi:
  → Equal Highs = level resistance yang obvious dengan multiple tests
  → Equal Lows  = level support yang obvious dengan multiple tests
  → Mudah dikenali → sering jadi target sweep

Penggunaan:
  → Equal High as Range High → Fibonacci → temukan midrange
  → Equal Low  as Range Low  → Fibonacci → temukan midrange
  → "This one should be obvious as equal lows and highs are usually easy
     to spot and give clear targets that get swept most of the time"

Visual:
    ─────────── Equal High (Range High, 100%)
        /\  /\  /\   ← multiple tests
   ────────────────── 50% (Midrange)
        \/  \/  \/   ← multiple tests
    ─────────── Equal Low (Range Low, 0%)
```

---

## Rule 4: Previous Daily/Weekly/Monthly Range

```
Deskripsi:
  → Zoom out ke D1, W1, atau M1 timeframe
  → Ambil HIGH dan LOW dari candle tersebut → itulah range
  → "Simply zoom out to the desired time frame and take the high and low of the candle"

Variasi:
  → Beberapa trader gunakan WICK (full range including tails)
  → Lainnya hanya BODY candle
  → "Just have a look at what works best with the midrange and go from there"

Aplikasi:
  → Paling simple dan efektif untuk daily range trading
  → PDH (Previous Day High) = Range High
  → PDL (Previous Day Low)  = Range Low
  → Midrange = (PDH + PDL) / 2 = equilibrium level

  Weekly/Monthly:
  → PWH/PWL (Previous Week High/Low) = range untuk swing trading
  → PMH/PML (Previous Month High/Low) = range untuk position trading
```

---

## Monday Range Trading Approach

```
Pendekatan khusus untuk range berbasis Monday:
  (Tidak bisa trade pada Senin karena range belum terbentuk)

  SENIN:
    → Observasi saja
    → Tunggu Monday range terbentuk

  SETELAH SENIN CLOSE:
    → Mark Monday High dan Monday Low
    → Belum trading — masih tunggu:
      Salah satu sisi diambil DAN harga kembali ke DALAM range

  SELASA SAMPAI KAMIS:
    → Cari liquidity grab of either side (BSL = Monday High atau SSL = Monday Low)
    → Setelah run on BSL/SSL → cari Market Structure Shift
    → Entry di OB / BB / FVG yang terbentuk setelah MSS
    → Target: sisi BERLAWANAN dari Monday range (untested liquidity)

  INVALIDASI:
    → Jika price bergerak terlalu jauh dan tidak kembali ke dalam range
    → Lebih sulit menemukan setup yang jelas
    → Skip — tunggu setup berikutnya

Diagram Monday Range:
        Monday High ──────────────────────
                           OB/BB/FVG+
                        MSS ↑
        ────────── 50% ─────────────────
                        MSS ↓
                           OB/BB/FVG-
        Monday Low ──────────────────────
        Run on Buystops        Run on Sellstops
```

---

## Breaker Block — Stop Hunt Deep Dive

```
STOP HUNT:
  → Smart money practice yang sangat umum
  → Market bergerak langsung ke stop loss, menghantamnya, lalu BERBALIK arah
  → Tujuan: mengambil posisi di harga terbaik dengan biaya terendah

  "Smart money uses this method to take their positions at the price they want,
   in the best possible way for themselves. By manipulating the price, they will
   force you to go in a certain direction, and then swallow your position abruptly."

Breaker Block vs Order Block:
  → BB beroperasi pada PRINSIP YANG SAMA dengan OB
  → PERBEDAAN: reaksi dari BB terjadi SETELAH strong impulse penetration
  → Setelah Stop Hunt movement memanipulasi liquidity dan menyebabkan trend change,
    large capital sering mengembalikan harga ke zona BB untuk menutup
    unprofitable positions (yang diambil selama manipulasi)

  → "A breaker in trading is NOT necessarily a reversal pattern —
     it can signal the CONTINUATION of a trend"

Formasi Breaker Block:
  Bullish Breaker:
    1. Bearish OB terbentuk (candle bullish sebelum impulsive DOWN)
    2. Price bergerak ke bawah (OB valid)
    3. Impulsive move UP menembus ATAS zona OB
    4. OB berubah menjadi Bullish Breaker (zona support baru)
    5. Price kembali ke zona Breaker → entry long

  Bearish Breaker:
    1. Bullish OB terbentuk (candle bearish sebelum impulsive UP)
    2. Price bergerak ke atas (OB valid)
    3. Impulsive move DOWN menembus BAWAH zona OB
    4. OB berubah menjadi Bearish Breaker (zona resistance baru)
    5. Price kembali ke zona Breaker → entry short
```

---

## A-to-B Fibonacci (Breaker Entry Approach)

```
A-to-B Price Range:
  A = titik awal leg (High untuk bearish, Low untuk bullish)
  B = titik akhir leg (Low untuk bearish, High untuk bullish)

  Cara menggunakannya:
  1. Identifikasi leg A ke B yang mengandung Breaker Block
  2. Jalankan Fibonacci dari A ke B
  3. Entry zone: 1 standard deviation LOWER dari range ini (untuk bearish)
     → "Taking one standard deviation lower from this range is the easiest
        and most straightforward approach to using the breaker"

Best Breakers (Kriteria Kualitas Tinggi):
  → Engage TWO levels of sell-side liquidity:
    1. Short-Term Low (STL) — level pertama yang disweep
    2. Higher Time Frame (HTF) sell-side liquidity — level yang lebih besar

  → Kombinasikan dengan HTF buy model untuk perbandingan standard deviations
    antara titik A-B dan deviasi HTF

Re-entry Logic setelah Stop Loss:
  → Jika SL terkena tapi THESIS TIDAK BERUBAH (bias masih sama):
    → Re-enter dengan HALF position size dari trade pertama
    → "It's normal to experience losing trades, and you won't be able to avoid them"
  → Jika bias berubah: jangan re-enter
```

---

## Implementasi Pine Script

```pinescript
// Range Boundaries (Rule 4: Previous Day High/Low)
var float pdh = na   // Previous Day High
var float pdl = na   // Previous Day Low
var float midrange = na

if (hour == 0 and minute == 0)
    pdh := ta.highest(high, 390 / timeframe.multiplier)[1]
    pdl := ta.lowest(low,  390 / timeframe.multiplier)[1]
    midrange := math.avg(pdh, pdl)

// Equal Highs/Lows (Rule 3)
float recent_high = ta.highest(high, 20)
float recent_low  = ta.lowest(low, 20)
bool equal_high   = high[0] >= recent_high * 0.9998 and high[0] <= recent_high * 1.0002
bool equal_low    = low[0]  <= recent_low  * 1.0002 and low[0]  >= recent_low  * 0.9998

// Range Premium/Discount zones
bool in_premium  = close > midrange
bool in_discount = close < midrange

// Monday Range
var float monday_high = na
var float monday_low  = na
bool is_monday = dayofweek == dayofweek.monday
bool is_tuesday_plus = dayofweek > dayofweek.monday

if is_monday
    monday_high := ta.highest(high, 1440 / timeframe.multiplier)
    monday_low  := ta.lowest(low,  1440 / timeframe.multiplier)

// BSL/SSL sweep detection dari Monday range
bool monday_bsl_swept = is_tuesday_plus and high > monday_high
bool monday_ssl_swept = is_tuesday_plus and low  < monday_low

// Plot Range
plot(pdh, "PDH", color=color.red,   style=plot.style_stepline, linewidth=1)
plot(pdl, "PDL", color=color.green, style=plot.style_stepline, linewidth=1)
plot(midrange, "Midrange", color=color.gray, style=plot.style_stepline, linewidth=1)
plot(monday_high, "Monday High", color=color.fuchsia, linewidth=1)
plot(monday_low,  "Monday Low",  color=color.blue,    linewidth=1)
```

---

## Catatan Tambahan

- **Midrange adalah kuncinya**: jika midrange dari range yang dipilih tidak "masuk akal" secara visual, coba rule yang berbeda
- **Semua 4 rules bisa digunakan bersamaan**: konfirmasi terbaik ketika beberapa rule menunjukkan midrange yang sama
- Monday Range sangat powerful: market sering respek ke Monday high/low sebagai liquidity targets hingga Jumat
- **Breaker Block + 2 SSL levels** = setup kualitas tertinggi di dalam range trading
- Stop hunt sebelum re-entry ke dalam range = konfirmasi bahwa sisi yang disweep akan bertahan sebagai boundary
- Re-entry dengan half size: **kelola risiko** — jangan tambah posisi ketika bias tidak terbukti sepenuhnya
- **"Divergence doesn't necessarily mean there's a trade"** — narrative/context tetap kunci utama

---

---

# New Week/Day Opening Gap (NWOG / NDOG) Logic

## Definisi

**NWOG (New Week Opening Gap)** adalah range dinamis antara dua titik harga spesifik: **Friday close (17:00 NY)** dan **Sunday open (18:00 NY)**. Gap ini adalah real liquidity void — total absence of buying and selling — karena market tutup antara dua titik waktu tersebut.

**NDOG (New Day Opening Gap)** adalah perbedaan antara **5pm Close Price** dan **6pm Open Price** setiap hari. Konsep serupa dengan NWOG tetapi pada skala harian.

> NWOG adalah **dynamic fair value level** — karena uncertainty builds opportunity. Where there is a difference of opinion, there is a trade.

---

## Mengapa NWOG Penting

- **Algorithm delivers price** ke level-level NWOG untuk menawarkan fair value dan mendorong new business
- NWOG mereferensikan **older inefficiencies & liquidity voids** yang bisa dirujuk kembali weeks dan bahkan months kemudian
- Institutions akan **fill the gap before big move** — menjadikan NWOG sebagai strong support dan resistance
- **Real points of interest**: market didesain untuk gravitasi kembali ke level ini karena memungkinkan large fund (large fund) membawa order mereka ke marketplace

---

## Cara Menemukan NWOG di Chart

```
1. Gunakan Lower Time Frame (contoh: 5min chart)
   - JANGAN gunakan 1D chart
2. Sign the range antara:
   - Sunday opening price (18:00 NY)
   - Friday prior closing price (17:00 NY)
3. Gunakan "Regular Trading Hours" (bukan Electronic Trading Hours)
   - NWOG hanya terlihat pada Regular Trading Hours
```

---

## Consequent Encroachment (C.E.) NWOG

**C.E.** = Consequent Encroachment = midpoint / "middle point" dari gap atau inefficiency manapun.

```
Cara menemukan C.E. NWOG:
  - Anchor Fibonacci dari Friday Closing Price ke Sunday Opening Price
  - 50% level = C.E. NWOG = midpoint kita
  - Ini adalah Consequent Encroachment dari New Week Opening Gap
```

| Komponen | Keterangan |
|---|---|
| **NWOG High** | Harga lebih tinggi antara Friday close dan Sunday open |
| **NWOG Low** | Harga lebih rendah antara Friday close dan Sunday open |
| **NWOG C.E.** | 50% antara NWOG High dan NWOG Low (Fibonacci midpoint) |

---

## Cara Menggunakan NWOG

```
Perlakukan NWOG seperti FVG:
- Harga kemungkinan besar akan revisit dan react terhadap NWOG
- Harga juga merespek C.E. (Consequent Encroachment) dari NWOG
- Up-gap (Sunday open > Friday close) = acts as SUPPORT zone
- Down-gap (Sunday open < Friday close) = acts as RESISTANCE zone
```

### Best Practice

- **Record minimal 5 NWOG terbaru** untuk referensi
- ICT: "Having the prior 4 weeks NWOG on your chart gives you an X-ray view of how the algorithm will refer back to old areas of real FAIR VALUE"
- **Ketika beberapa NWOG berkumpul berdekatan** → market sering memasuki **range-bound phase**

---

## ICT Event Horizon PD Array

**ICT Event Horizon PD Array** = level setengah jalan antara dua NWOG yang berdekatan.

```
Event Horizon = halfway between NWOG A and the closest NWOG B

Karakteristik:
- Harga tidak akan bisa "escape" dari draw ke NWOG
- Ketika price mendekati NWOG namun belum mencapainya,
  Event Horizon akan menciptakan surge kuat ke arah NWOG tersebut
- Measure High-Low of NWOG dari 2 NWOG terdekat
```

> Event Horizon adalah "gravitational pull zone" — harga yang sudah terlalu dekat dengan NWOG tidak akan bisa berpaling; ia akan di-surge menuju NWOG tersebut.

---

## Implementasi Pine Script

```pinescript
// NWOG: New Week Opening Gap
// Deteksi: Sunday 18:00 NY = open minggu baru; Friday 17:00 NY = close minggu lalu

bool is_sunday_open = dayofweek == dayofweek.sunday and hour == 18 and minute == 0
bool is_friday_close = dayofweek == dayofweek.friday and hour == 17 and minute == 0

var float nwog_friday_close = na
var float nwog_sunday_open  = na

if is_friday_close
    nwog_friday_close := close

if is_sunday_open
    nwog_sunday_open := open

float nwog_high = math.max(nwog_friday_close, nwog_sunday_open)
float nwog_low  = math.min(nwog_friday_close, nwog_sunday_open)
float nwog_ce   = math.avg(nwog_high, nwog_low)   // Consequent Encroachment

bool nwog_up_gap   = nwog_sunday_open > nwog_friday_close   // support zone
bool nwog_down_gap = nwog_sunday_open < nwog_friday_close   // resistance zone

// NDOG: New Day Opening Gap (5pm Close vs 6pm Open)
bool is_5pm_close = hour == 17 and minute == 0
bool is_6pm_open  = hour == 18 and minute == 0

var float ndog_5pm_close = na
var float ndog_6pm_open  = na

if is_5pm_close
    ndog_5pm_close := close

if is_6pm_open
    ndog_6pm_open := open

float ndog_high = math.max(ndog_5pm_close, ndog_6pm_open)
float ndog_low  = math.min(ndog_5pm_close, ndog_6pm_open)
float ndog_ce   = math.avg(ndog_high, ndog_low)
```

---

## Catatan Tambahan

- NWOG dan NDOG adalah **liquidity voids nyata** — bukan sekadar referensi harga, melainkan zona di mana tidak ada transaksi yang terjadi
- Semakin besar gap (selisih Friday close vs Sunday open) → semakin signifikan NWOG tersebut
- **Multiple NWOGs yang overlap** → konfirmasi kuat zona support/resistance
- Event Horizon hanya valid ketika price sudah "terlalu dekat" ke salah satu NWOG tanpa mencapainya; setelah itu akan ter-surge ke NWOG target

---

---

# Liquidity Void and Fair Valuation Logic

## Definisi

### Fair Valuation

> **Fair Valuation adalah milik Market Maker, bukan milik trader.**

Ketika menganalisis price action, tanyakan:
- Apakah harga adalah Fair Value untuk Market Maker untuk **buy**?
- Apakah harga adalah Fair Value untuk Market Maker untuk **sell**?

Ketika harga **kembali** ke area Fair Value → Market Maker akan:
- Sell (jika harga terlalu tinggi dari perspektif mereka)
- Buy (jika harga terlalu rendah dari perspektif mereka)

Market Makers harus melakukan Fair Valuation untuk posisi short dan long mereka — dan juga untuk exit di kedua sisi marketplace.

> Pada HTF: selalu tanyakan — **"Di mana Market Maker melihat Fair Value?"**

---

### Liquidity Void (LV)

**Liquidity Void** = harga membuat pergerakan mendadak ke satu arah dengan range besar (dibandingkan ukuran candle terkini) dalam waktu singkat.

```
Karakteristik Liquidity Void:
  - Large candles (long body) dengan tiny wicks
  - Harga hanya di-set satu arah (one-sided price movement)
  - Little trading activity selama pergerakan
  - Price spent very little time at those prices
  - Kadang ada small gap antar candle-candle besar dalam LV
  - Setelah LV terbentuk → itu adalah area Fair Value bagi Market Maker
  - Market dengan probabilitas tinggi akan kembali untuk menutupnya
```

---

## 3 Langkah Menentukan Fair Value (Market Maker Framework)

```
1. Temukan total macro range dari larger price move
   → Apakah ini Premium atau Discount?

2. Di mana Equilibrium price point (dari larger price move) 
   relatif terhadap most recent trading range high dan low?
   → Apakah price di 1/3 bawah, 1/3 atas, atau tengah?

3. Di mana reference points yang akan dituju Market Maker?
   → FVGs, Liquidity Voids, Order Blocks
```

---

## Equilibrium

**Equilibrium** = garis 50% (atau midpoint) antara swing high dan swing low.

```
Cara analisis Equilibrium:
1. Mark Equilibrium pada current trading range
2. Draw Equilibrium pada next largest trading range
3. Dan seterusnya (multi-timeframe)
4. Tanya: apakah harga saat ini di Discount atau Premium di kebanyakan range?
5. Apakah harga di 1/3 bawah (discount) atau 1/3 atas (premium)?
```

### Perilaku Harga di Equilibrium

Ketika price berada **di Equilibrium** (Fair Value) → harga bisa ke dua arah. Untuk menentukan arah yang paling probable:

```
Analisis Equilibrium:
1. Di mana market structure paling terakhir break?
   → Apakah price break swing high atau swing low terbaru?

2. Di mana posisi price dalam overall big range?
   → Lower part (discount), upper part (premium), atau middle?

3. Perhatikan body vs wick candle di Equilibrium:
   → Jika body tetap di satu sisi Equilibrium sementara wick menyeberang
   → Ini sinyal bahwa price mungkin berbalik setelah wick menyeberangi Equilibrium
```

| Posisi Price | Interpretasi |
|---|---|
| Di bawah Equilibrium (Discount) | MM lebih likely buy; cari Bullish OB/FVG di Discount |
| Di atas Equilibrium (Premium) | MM lebih likely sell; cari Bearish OB/FVG di Premium |
| Di Equilibrium sendiri | Fair Value — ambiguous; lihat MSB terbaru untuk arah |

---

## Liquidity Void: Detail Trading Logic

### Sifat LV

```
Liquidity Void = Fair Value untuk Market Maker
  → Karena algorithm seeks to rebalance inefficiently traded price ranges
  → "Inefficiently traded" = harga bergerak terlalu cepat, sedikit transaksi terjadi
```

### Cara Trading Liquidity Void

```
1. Identifikasi Liquidity Void:
   - Cari serangkaian large body candles dengan tiny wicks
   - Pergerakan satu arah, cepat, meninggalkan sedikit "jejak trading"

2. Ketika price kembali ke LV:
   - Expect price to WICK UP AND DOWN saat mengisi LV
   - Karena MMs sedang selling long positions / buying back short positions
     yang diakumulasi sebelum LV terbentuk

3. Setelah LV terisi penuh:
   - Cari FVGs atau OBs lebih jauh di Premium/Discount
   - Di sanalah MM akan offset posisi mereka (dari sisi berlawanan move)

4. Timing fill:
   - Kadang LV terisi SEGERA setelah terbentuk
   - Kadang harga naik lebih tinggi dulu (deceiving traders), lalu baru turun mengisi LV
   - Ketika price bergerak AGRESIF dengan gap → kemungkinan besar akan lanjut lebih jauh
     sebelum akhirnya kembali mengisi LV
   - Strategy: place LIMIT ORDER di level LV untuk entry
```

---

## Konsolidasi dan Fair Value

```
Konsolidasi = Equilibrium = Fair Value

Aturan:
1. Ketika price bergerak dalam defined trading range (konsolidasi) → ada Equilibrium
2. Equilibrium dalam konsolidasi itu sendiri adalah Fair Value
3. Cari price untuk continue ke arah strong move KELUAR dari konsolidasi
4. Jika kita melihat expansion → prior to expansion was consolidation
   (Kita tidak tahu berapa lama konsolidasi berlangsung sampai expansion terjadi)
```

---

## Market Maker Framework (IOF — Institutional Order Flow)

**Cara termudah memahami IOF**: markets bergerak dari buy stops ke sell stops, dan dari sell stops ke buy stops.

```
Siklus Market Maker:
  Fair Value → Discount → Premium → Fair Value
  (atau sebaliknya: Fair Value → Premium → Discount → Fair Value)

Market bergerak antara 3 breakpoints:
  - DISCOUNT  (oversold zone, bawah Equilibrium)
  - PREMIUM   (overbought zone, atas Equilibrium)
  - FAIR VALUE (equilibrium zone, tengah)
```

```
Perspektif Market Maker:
  - Price action TIDAK random
  - Market Makers secara spesifik memindahkan harga ke mana mereka inginkan
  - Satu-satunya alasan price dideliver di harga lebih tinggi = MM net long
  - MM akan lakukan hal yang menguntungkan mereka, bukan retail traders
  - Ini membutuhkan perspective shift: lihat dari kacamata Smart Money, bukan Retail logic
  - Everything repeats — karena semua dikontrol oleh Smart Money (Market Makers)
```

---

## Analisis Konfluens: LV + OB + Equilibrium

Berdasarkan contoh chart dari buku (p311):

```
Setup Bearish (High Probability):
  1. Bearish OB terbentuk di atas
  2. Liquidity Void terbentuk (besar bearish move ke bawah)
  3. Price returns ke area Fair Value (Equilibrium)
  4. Inversion FVG terbentuk di zona tersebut
  5. OB baru (Bullish OB) di area bawah
  → Price kembali ke OB + Equilibrium sebelum lanjut ke premium

Setup Bullish (Discount → Premium):
  - Price hit swing high → Liquidity Void terbentuk di atas
  - Price return ke Equilibrium zone (konsolidasi)
  - Bullish OB + Equilibrium = zona entry untuk long
  - Target: Premium zone (Liquidity Void di atas terisi)
```

### Checklist Analisis Fair Value (3 Hal yang Harus Dipertimbangkan)

```
1. Overall range yang kita trade (macro range)
2. Equilibrium relatif terhadap range yang terakhir terbentuk
3. Liquidity voids dan liquidity pools di atas old highs / di bawah old lows
```

---

## Implementasi Pine Script

```pinescript
// Deteksi Liquidity Void (Bullish - up move void)
// LV terjadi ketika serangkaian candle besar bergerak satu arah

float body_size   = math.abs(close - open)
float atr14       = ta.atr(14)
bool  big_candle  = body_size > atr14 * 1.5

// Bullish Liquidity Void: large bullish candles
bool bull_lv_candle = close > open and big_candle

// Bearish Liquidity Void: large bearish candles
bool bear_lv_candle = open > close and big_candle

// Equilibrium dari swing range
// (Gunakan swing detection untuk high dan low)
bool  is_swing_high = high[2] > high[3] and high[2] > high[1]
bool  is_swing_low  = low[2]  < low[3]  and low[2]  < low[1]

var float last_sh = na
var float last_sl = na

if is_swing_high
    last_sh := high[2]
if is_swing_low
    last_sl := low[2]

float equilibrium = math.avg(last_sh, last_sl)   // 50% = Fair Value / Equilibrium

// Premium/Discount determination
bool price_in_premium  = close > equilibrium
bool price_in_discount = close < equilibrium

// Fair Value zone (within 10% of equilibrium)
float range_size  = last_sh - last_sl
bool  at_fair_value = math.abs(close - equilibrium) < range_size * 0.1
```

---

## Catatan Tambahan

- **"Fair Valuation is only for the Market Maker and not for the trader"** — trader menggunakan Fair Value sebagai referensi, bukan sebagai definisi harga yang "murah/mahal" dari sudut pandang retail
- **Liquidity Void ≠ FVG**: LV adalah pergerakan agresif berlapis candle besar; FVG adalah gap spesifik 3 candle. Namun keduanya sering co-exist dalam area yang sama
- **LV yang belum terisi = magnet harga** — algorithm akan selalu kembali untuk merebalance area yang tidak efisien
- **Ketika LV terisi** → harga seimbang (balanced); selanjutnya cari PD Array berikutnya (OB/FVG) di Premium atau Discount untuk offset MM positions
- **Buying below PDL = buying premium** (bukan discount) karena PDL adalah referensi liquidity di bawah; memahami ini memerlukan perspektif MM
- **IOF bergerak dalam 3 fase**: Discount → Fair Value → Premium (bullish cycle) atau Premium → Fair Value → Discount (bearish cycle)

---

---

# Day of the Week and Time of Day Logic

## Definisi

**Chapter 25** membahas bagaimana hari dalam seminggu dan waktu dalam sehari menentukan di mana high/low penting terbentuk, dan kapan window entry terbaik terbuka.

---

## Weekly High/Low Formation Probability

```
Weekly High/Low terbentuk 80% of the time antara:
  - Sunday/Monday Open  →  sampai  →  Tuesday London Open

Jika TIDAK terbentuk sebelum Tuesday London Open:
  - Kemungkinan besar terbentuk antara Tuesday London Open dan Wednesday London Open

→ Monday, Tuesday, Wednesday = hari paling probable untuk HOW/LOW minggu terbentuk
→ Tuesday dan Wednesday London Open = jendela kritis yang paling penting dipantau
```

### Kondisi Bullish/Bearish Weekly

```
Bullish kondisi:
  → Cari long ketika LOW MINGGU terbentuk
  → Low minggu paling sering terbentuk SEBELUM Tuesday/Wednesday London Open

Bearish kondisi:
  → Cari short ketika HIGH MINGGU terbentuk
  → High minggu paling sering terbentuk SEBELUM Tuesday/Wednesday London Open
```

---

## High/Low of Day Formation

```
Monitor 4 jam setelah 00:00 NY (Midnight):
  → Probabilitas tinggi HOD/LOD terbentuk dalam 4 jam tersebut
  → HOD/LOD paling sering terbentuk sekitar 04:00–04:30am EST

Untuk SELL day:
  → HOD paling sering terbentuk dalam 4 jam pertama setelah 00:00 EST (London Open KZ)
  → Ideal window untuk HOD: 02:00–05:00am EST
  → LOD terbentuk sekitar 10:00–11:00am EST
  → Jika selling dari London KZ → tutup trade 10:00–11:00am EST

Untuk BUY day:
  → Kebalikan dari Sell Day
```

> "Typically, the daily high or low is formed on a sharp counter trend direction on that day (Judas Swing/Stop Hunt)."

---

## Intraday Time Windows (EST)

| Waktu EST | Event / Signifikansi |
|---|---|
| **00:00** | NY Midnight Open — referensi harian; algo mulai operasi |
| **02:00–04:00** | Window terbentuknya HOD/LOD (probabilitas tinggi) |
| **04:00–04:30** | HOD/LOD paling sering terbentuk |
| **05:00** | London Session Open — Judas Swing atau Divergence |
| **05:00–06:00** | Pause setelah London move / Judas membentuk fractal ke arah market |
| **07:00** | Pre-NY session reference level |
| **07:20** | 20 menit setelah NY KZ mulai; futures mulai trading |
| **09:30** | NY Opening Bell (RTH begins) |
| **10:00–11:00** | Silver Bullet / Displacement window; London Close KZ dimulai |
| **12:00–13:00** | NY Lunch — sering konsolidasi atau reversal |
| **13:30–16:00** | PM Session (afternoon); Silver Bullet PM; full daily range |

---

## 05:00am EST — Critical Level

```
Pada 05:00am EST:
  - Expect Judas Swing atau SMT Divergence
  - Terbentuk di atas/bawah 02:00–04:00am price
  - Membentuk swing high/low ke arah market

Dalam sell trade:
  → Harga kembali ke higher of day → membentuk swing high → lanjut turun
  → ATAU: SMT Divergence terbentuk

Mark 05:00am EST open price:
  → Price often trades back to 02:00am price → optimal trade entry
  → Jika tidak ada Judas swing di London → expect Judas at 05:00am EST
```

---

## 07:20 EST — NY Futures Entry

```
07:20 EST = 20 menit setelah NY Killzone begin; futures contracts start trading

NY Trade Setup dari 07:20:
  → BUY: entry 10 points (ES/SP500) BELOW the 07:20 open price
  → SELL: entry 10 points (ES/SP500) ABOVE the 07:20 open price
```

---

## London Close / Late NY Close (Reversal Market Profile)

### London Close Killzone (10:00am–12:00pm EST)

```
London Close KZ: 10:00am–12:00pm (kadang sampai 13:00 EST)

Karakteristik:
  - Market encounters profit-taking sekitar 11:00am EST
    (London traders tutup, NY traders istirahat makan siang)
  - HOD/LOD bisa terbentuk juga di London Close
  - PA di London Close: retracement OFF HOD pada bullish days; OFF LOD pada bearish days
  - Biasanya 5-menit OTE setup; pergerakan SANGAT SHORT TERM

ICT: "Jika bullish dan daily close akan high relative to opening (up-close day),
  London Close adalah waktu di mana HOD biasanya terbentuk antara 10:00am dan noon"

Dua sifat London Close:
  1. Continuation Point: menciptakan continuation swing yang berlanjut ke NY afternoon
  2. Reversal Point: arah hari/minggu dapat berubah selama London Close
```

### London Close Trend Trade (Classic Reversal Market Profile)

```
Bullish Trend Trade (London Close Buy):
  1. Hari mulai sebagai Classic SELL day
  2. Berbalik di London Close/Late NY
  3. Price bergerak DI ATAS London high

Bearish Trend Trade (London Close Sell):
  1. Hari mulai sebagai Classic BUY day
  2. Berbalik di London Close/Late NY
  3. Price bergerak DI BAWAH London low

Kondisi aktif:
  - Harga trading ke HTF Optimal Trade Entry
  - Look for HTF S/R jika price tidak menyentuh S/R di London Open
```

---

## NY Session Entry Rules

```
NY Session:
  → BUY below 05:00 EST open price DAN below 07:00 EST open price
  → SELL above 05:00 EST open price DAN above 07:00 EST open price
```

---

## Implementasi Pine Script

```pinescript
// Deteksi hari-hari kritis dalam minggu
bool is_monday    = dayofweek == dayofweek.monday
bool is_tuesday   = dayofweek == dayofweek.tuesday
bool is_wednesday = dayofweek == dayofweek.wednesday

// London Open: 03:00am EST (08:00 GMT)
bool london_open_window = hour == 3 and minute == 0
// NY Midnight reference
bool ny_midnight = hour == 0 and minute == 0
// 05:00am EST (London Session)
bool london_05am = hour == 5 and minute == 0
// 07:00am EST
bool ref_07am = hour == 7 and minute == 0
// 07:20 EST — NY futures
bool ny_futures_open = hour == 7 and minute == 20
// London Close KZ start
bool london_close_kz = hour == 10 and minute == 0
// NY Opening Bell
bool ny_rtho = hour == 9 and minute == 30

var float midnight_open = na
var float london_05am_open = na
var float london_07am_open = na

if ny_midnight
    midnight_open := open
if london_05am
    london_05am_open := open
if ref_07am
    london_07am_open := open

// HOD/LOD Window — pertama 4 jam setelah midnight
bool hod_lod_window = hour >= 0 and hour < 4
```

---

## Catatan Tambahan

- **80% rule**: jika weekly high/low belum terbentuk pada Selasa London Open → sangat likely akan terbentuk antara Selasa dan Rabu London Open
- **04:00–04:30am EST** = golden window untuk HOD/LOD harian
- **Market rallies in sell day after 00:00–02:00am EST** — fase Judas awal sebelum real move turun
- Jika HOD/LOD tidak terbentuk di window biasanya → bias weak, lebih baik wait-and-see

---

---

# Daily Templates Logic

## Definisi

**Daily Templates** adalah pola intraday berulang yang menggambarkan bagaimana market bergerak dari satu session ke session berikutnya dalam sehari. Template ini membantu trader mengidentifikasi di mana entry dan exit terbaik berdasarkan profil hari.

---

## Template 1: London Swing to NY Open / London Close Reversal

**Reversal Market Profile**

```
BULLISH VERSION:
  Asian     → Open
  London    → DECLINE (Judas Swing / initial low) ← entry di sini
  NY Open   → RALLY ke HOD
  London Close → Final push up, lalu BERBALIK turun

BEARISH VERSION (mirror):
  Asian     → Open
  London    → RALLY (Judas Swing / initial high) ← entry di sini
  NY Open   → DROP ke LOD
  London Close → Final push down, lalu BERBALIK naik
```

### Detail Mekanisme

- Bullish version dimulai seperti Classic Buy Template: **decline below opening price** sebelum rally
- Setelah turun → buy entry terbentuk → harga rally ke HTF POI (contoh: bearish OB, FVG)
- Jika ini terjadi di NY Session → **Classic Market Reversal**
- Template digunakan untuk:
  1. Reach bearish OB pada HTF
  2. Turtle Soup Raid
  3. Close a range

### Timing Monitoring

```
Pantau:
  - 1 jam terakhir London session (04:00–05:00am EST)
  - 1 jam pertama NY session (07:00–08:00am EST)
  - Cari tanda reversal: break key S/R atau shift in trading volume
```

**Keunggulan**: memanfaatkan momentum overlap London–NY → rapid acceleration  
**Risiko**: jika market tidak berbalik seperti expected → increased volatility, unexpected gaps

---

## Template 2: The Classic Buy or Sell Day Template

**Template terbaik untuk trading** — wide range trending day.

```
CLASSIC BUY DAY:
  Asian Consolidation → London: DROP (Judas → initial low) → NY: RALLY (HOD) → London Close: Close

CLASSIC SELL DAY:
  Asian Consolidation → London: RALLY (Judas → initial high) → NY: DROP (LOD) → London Close: Close
```

### Karakteristik

```
- Unfolds mostly Monday, Tuesday, latest Wednesday during London session
- NY session gives retracement to CONTINUE trend set during London
- Daily range lasts 7–8 hours once profile established
- Trend usually lasts until 11:00am EST
```

### Aturan Entry Classic Buy Day

```
1. SELALU beli ketika market DROP di waktu yang tepat di key support (below opening price)
2. Jika price mulai ABOVE opening price → JANGAN langsung buy; tunggu price turun dulu
3. Jarak ideal dari open ke support: 7–12 points (ES/SP500)
4. Jika move dari open ke support di London > 12 points → tunggu NY trade
5. Take small profit: 5–7 points (ES) sebelum 12:00pm
6. Jika tidak bisa trade London → trade NY session (get in sync dengan London trade)
```

---

## Template 3: Range to NY Open / London Close Rally

```
Profil ini paling sering muncul selama:
  - NFP (Non-Farm Payroll)
  - FOMC
  - Interest Rate Announcements

Struktur:
  London    → Market DALAM KONSOLIDASI
  Sebelum news → price break London Lows (Judas/stop hunt)
  Setelah News Release → RALLY kuat ke atas
  London Close → Final rally / penutupan hari
```

---

## Template 4: Consolidation Raid on News Release

```
Profil ini juga muncul pada NFP, FOMC, Interest Rate Announcements:

1. Opening price → market KONSOLIDASI sebelum news
2. Selama news: price DROP untuk induce traders dan take stops
   (Pergerakan tidak harus besar, tapi HARUS break konsolidasi)
3. Setelah stops dibersihkan → price move ke TRUE DIRECTION

Cara trading:
  - Identifikasi key support level atau OB di BAWAH konsolidasi
  - Jika price REJECT di support/resistance dalam 5 menit setelah news → entry valid
  - Jika price TIDAK reject → leave the trade (analisis mungkin salah)
```

---

## Implementasi Pine Script

```pinescript
// Daily Template context — session boundaries (EST)

// London Session: 02:00am–05:00am EST (Early) / 03:00am–06:00am
bool in_london_session = (hour >= 2 and hour < 6)
// NY Session: 07:00am–12:00pm EST (AM)
bool in_ny_am = (hour >= 7 and hour < 12)
// London Close / Late NY: 10:00am–12:00pm
bool in_london_close = (hour >= 10 and hour < 12)
// NY Lunch: 12:00pm–13:30
bool in_ny_lunch = (hour == 12 or (hour == 13 and minute <= 30))
// PM Session: 13:30–16:00
bool in_pm_session = (hour == 13 and minute >= 30) or (hour >= 14 and hour < 16)

// Opening Price Reference
var float daily_open = na
if hour == 0 and minute == 0
    daily_open := open

// Classic Buy Day setup: price drops below daily open in London, then rallies
bool judas_swing_below_open = low < daily_open and in_london_session
```

---

## Catatan Tambahan

- **Classic Buy/Sell Day** adalah template paling reliable dan paling sering digunakan untuk profit besar
- Template 1 (Reversal) lebih complex; membutuhkan konfirmasi dari HTF yang kuat
- **Consolidation Raid**: 5 menit setelah news release = jendela konfirmasi penting; jika tidak ada rejection → abort
- Semua template memiliki fase Judas Swing sebagai elemen kunci — harga palsu ke satu arah sebelum ekspansi ke arah sebenarnya

---

---

# Previous Day High and Low as Liquidity Pool Logic

## Definisi

**Previous Day High (PDH)** dan **Previous Day Low (PDL)** adalah liquidity pool paling accessible bagi trader. Setiap hari, market cenderung gravitasi menuju salah satu dari level ini, menciptakan peluang untuk entry dan exit.

> PDH/PDL adalah **fondasi untuk menentukan daily bias** dan merupakan liquidity pool signifikan yang market targetkan setiap hari.

---

## Mengapa PDH/PDL Penting

```
PDH:
  - Acts as RESISTANCE: selling pressure ketika price mendekati PDH
  - Tempat di mana banks/institutions sering melakukan selling
  - Ketika price SWEEP PDH → potensi reversal ke bawah

PDL:
  - Acts as SUPPORT: buying interest ketika price mendekati PDL
  - Ketika price SWEEP PDL → potensi reversal ke atas

Potensi move: 25–50 points (ES/SP500) per trade dari PDH/PDL
```

### Referensi Multi-Hari

```
Setiap hari tanyakan:
  "Apakah hari ini kita sudah mengerjakan high/low hari kemarin?"
  "Atau hari sebelumnya? Atau hari sebelumnya lagi?"

→ Selalu refer ke LAST 3 DAYS (hari ini, kemarin, dua hari lalu)
→ Bukan hanya previous day — previous 3-day period
```

---

## Bullish dan Bearish Order Flow dengan PDH/PDL

| Order Flow | Dominasi | Target |
|---|---|---|
| **Bullish OF** | Buyers mendominasi → harga naik | BSL di ATAS PDH |
| **Bearish OF** | Sellers mendominasi → harga turun | SSL di BAWAH PDL |

---

## Cara Mengidentifikasi BSL (Buy-Side Liquidity) di Atas PDH

```
Langkah-langkah:
1. Analisis daily chart → identifikasi previous day's high
2. Cari Inside Bars: candle yang tidak menembus high/low hari sebelumnya
   → Inside bars = potensi reversal
3. Mark PDH → sebagai referensi untuk BSL pool
4. Identifikasi potential BSL pools di atas PDH
```

### Analisis Daily Chart untuk Trading Opportunities

```
1. Look for Break of Structure (BOS):
   - Identifikasi key S/R levels pada daily chart
   - Cari area price break above atau below levels = potential change of direction

2. Identify Daily POI (FVG, OB):
   - FVGs terjadi ketika price bergerak menjauhi fair value
   - Setelah BOS, cari daily FVG → gunakan sebagai reference untuk trading
```

---

## Cara Mengidentifikasi SSL (Sell-Side Liquidity) di Bawah PDL

```
Setiap hari market mencari liquidity → area di bawah PDL = SSL termudah

Ketika market break di bawah PDL:
  → Menunjukkan selling pressure → bearish order flow
  → Signal untuk look for short trade
  → Market mungkin lanjut mencari SSL berikutnya

When break below PDL occurs:
  → Potential bearish order flow continuation
  → Opportunity to sell
```

---

## NY Midnight Opening Price + PDH/PDL Context

```
NY Midnight (00:00 EST) = referensi harian kritis

Jika market trades ABOVE NY midnight open lalu mulai DECLINE:
  → Indikasi BEARISH order flow

Jika market konsisten trades DAN stays ABOVE NY midnight open:
  → Indikasi BULLISH order flow → cari beli saat break above PDH

Gunakan pada chart untuk memisahkan setiap trading day secara visual
```

---

## Trading PDL (Previous Day Low)

### Setup Buy (Bullish Context)

```
Skenario: Price dalam Expansion Swing naik; ada retracement ke bawah

1. Retracement normal ke bawah → masuk FVG → price menemukan buyers
   DI BAWAH PDL
2. Ini adalah setup: PDL di-sweep → reversal ke atas

Penting:
  - TIDAK hanya buy karena price below PDL
  - Cari KONFLUENS PD Arrays yang mendukung:
    → Discount array (FVG, OB) di bawah PDL
    → HTF institutional order flow mendukung bullish
```

```pinescript
// Buy setup below PDL
bool pdl_swept   = low < pdl and close > pdl    // sweep dan reclaim
bool fvg_below_pdl = bull_fvg and bull_fvg_top < pdl
bool buy_setup_pdl = pdl_swept and fvg_below_pdl and price_in_discount
```

---

## Trading PDH (Previous Day High)

### Setup Sell (Bearish Context)

```
Skenario: Price dalam Expansion Swing turun; ada retracement ke atas

1. Retracement normal ke atas → FVG terisi → price menemukan sellers
   DI ATAS PDH
2. Ini adalah setup: PDH di-sweep → reversal ke bawah

HTF Context (kunci utama):
  - Weekly/Daily/4h suggesting LOWER prices
  - Price trades up into Premium Array during retracement
  - Trades ABOVE PDH → ini adalah momen jual

Penting:
  - TIDAK hanya sell karena price above PDH
  - Cari KONFLUENS PD Arrays yang mendukung:
    → Premium array (FVG, OB) di atas PDH
    → HTF institutional order flow mendukung bearish
```

---

## Implementasi Pine Script

```pinescript
// PDH/PDL Detection
var float pdh = na
var float pdl = na

// Update pada awal hari baru (NY midnight)
bool new_day = hour == 0 and minute == 0 and dayofweek != dayofweek.saturday

if new_day
    pdh := high[1]   // previous day high (simplified — gunakan daily request)
    pdl := low[1]    // previous day low

// PDH/PDL sweep detection
bool pdh_swept = high > pdh and close < pdh   // sweep + close back below
bool pdl_swept = low < pdl and close > pdl    // sweep + close back above

// BSL above PDH
bool price_approaching_pdh = high >= pdh * 0.9995 and high <= pdh * 1.002

// SSL below PDL
bool price_approaching_pdl = low >= pdl * 0.998 and low <= pdl * 1.0005

// Referensi multi-hari (last 3 days)
float pdh_1 = request.security(syminfo.tickerid, "D", high[1])
float pdl_1 = request.security(syminfo.tickerid, "D", low[1])
float pdh_2 = request.security(syminfo.tickerid, "D", high[2])
float pdl_2 = request.security(syminfo.tickerid, "D", low[2])
float pdh_3 = request.security(syminfo.tickerid, "D", high[3])
float pdl_3 = request.security(syminfo.tickerid, "D", low[3])
```

---

## Catatan Tambahan

- **PDH/PDL = fondasi bias harian** — selalu mark sebelum sesi dimulai
- **Expansions Swings** selalu disertai retracement yang memberikan peluang trading; gunakan PDH/PDL sebagai target retracement
- **Konfluens adalah kunci**: PDH/PDL sweep saja tidak cukup; butuh FVG/OB + HTF OF yang mendukung
- **3-day lookback rule**: selalu periksa apakah market sudah mengerjakan PDH/PDL dari 3 hari terakhir
- **Inside bars di sekitar PDH/PDL** = potensi reversal yang kuat setelah sweep

---

---

# Hours of Operation Logic

## Definisi

**Chapter 28** membahas range-range session penting berdasarkan jam operasional yang digunakan untuk mengidentifikasi liquidity targets dan timing setup terbaik: PM Session, London Session Raid, NY Lunch Raid, dan AM Session.

---

## PM Session Ranges

**PM Session**: 13:30–16:00 EST (Regular Trading Hours only)

```
PM Session:
  - Schedule: 13:30–16:00 EST
  - Fokus: RTH (Regular Trading Hours) — exclude overnight activity
  - Tujuan: identifikasi HH dan LL dalam window ini

Opening Range:
  - Setelah Opening Bell (09:30 EST) → 30 menit pertama = Opening Range
  - Gunakan untuk menentukan posisi awal hari

Penggunaan PM Session BSL/SSL:
  - Bullish outlook + PM Session SSL (Lowest Low of PM) ter-swept → FAVORABLE untuk long
  - PM Session BSL/SSL grab = liquidity signifikan untuk Silver Bullet atau Judas Swing setup
```

| Level | Keterangan |
|---|---|
| **PM Session BSL** | High dari range 13:30–16:00 EST sesi sebelumnya |
| **PM Session SSL** | Low dari range 13:30–16:00 EST sesi sebelumnya |

---

## London Session Raid

**Digunakan ketika**: tidak dekat dengan PM Session range dalam 30 menit pertama setelah Opening Bell.

```
London Session Raid = range antara 02:00am dan 05:00am EST
  → Diidentifikasi pada ETH (Electronic Trading Hours) chart

London Session BSL = High dari range 02:00–05:00am EST
London Session SSL = Low dari range 02:00–05:00am EST

Skenario:
  - Jika bullish → target: London Session SSL ter-sweep → reversal naik
  - Jika bearish → target: London Session BSL ter-sweep → reversal turun
```

---

## Routine Harian (After 9:30 Opening Bell)

```
Rutinitas standar:
1. 30 menit pertama setelah 09:30 Opening Bell:
   → Assess posisi relative to PM Session range ATAU London Session range
   → Apakah ada run higher or lower? Atau market konsolidasi?

2. Tunggu Displacement antara 10:00am–11:00am EST:
   → Displacement ini yang sets the stage untuk Silver Bullet setup
```

---

## NY Lunch Raid

**NY Lunch Raid**: range antara 12:00pm–13:30 EST

```
NY Lunch Raid:
  - Didefinisikan antara 12:00–13:30 EST
  - Actual lunch: 1 jam (12:00–13:00), tapi algo mulai Macro lebih awal
  - Reference range: highest high dan lowest low antara 12:00–13:00

  NY Lunch BSL = High range 12:00–13:00
  NY Lunch SSL = Low range 12:00–13:00

  - Range set dalam 1 jam pertama
  - Raid bisa happen 30 menit setelah (sekitar 13:00)
  - Macro di 13:30 = sets tone dan pace untuk PM Session Silver Bullet
```

### Karakteristik Lunch Hour

```
NON-TRENDING day:
  - Lunch = KONSOLIDASI
  - Morning session moves sering REVERSED selama lunch
  - Setelah lunch: morning session creates low → price rallies ke lunch highs / morning highs

TRENDING day:
  - Lunch bukan konsolidasi — price continues direction dari morning
  - Keeps running into 15:00–16:00 = full daily range

BULLISH day:
  - AM: price naik
  - Lunch (12:00–13:00): konsolidasi
  - PM (14:00–16:00): REPEAT move yang sama seperti pagi, di LTF (fractal)
```

---

## AM Session Ranges

**AM Session**: 09:30am–12:00pm EST

```
AM Session Ranges:
  - Range: 09:30am–12:00pm EST
  - Digunakan sebagai referensi di AFTERNOON pada hari yang SAMA
  - Antisipasi reversal profile berdasarkan AM Session BSL/SSL

AM Session BSL = High dari 09:30–12:00
AM Session SSL = Low dari 09:30–12:00

Skenario menggunakan AM Ranges di sore hari:
  - Jika AM Session konsolidasi + lunch juga konsolidasi/directional
  → Refer ke trading activity PREVIOUS SESSION (electronic trading / ETH sebelumnya)
```

---

## Ringkasan Semua Session Ranges

| Session | Waktu EST | Range Components |
|---|---|---|
| **London Session** | 02:00–05:00 | London BSL / London SSL |
| **AM Session** | 09:30–12:00 | AM BSL / AM SSL |
| **NY Lunch** | 12:00–13:30 | Lunch BSL / Lunch SSL |
| **PM Session** | 13:30–16:00 | PM BSL / PM SSL |

---

## Implementasi Pine Script

```pinescript
// Session Range Tracking

// London Session Raid range (02:00–05:00am EST on ETH)
var float london_session_high = na
var float london_session_low  = na
bool in_london_raid_window = hour >= 2 and hour < 5

if in_london_raid_window
    london_session_high := na(london_session_high) ? high : math.max(london_session_high, high)
    london_session_low  := na(london_session_low)  ? low  : math.min(london_session_low, low)

// AM Session range (09:30–12:00)
var float am_session_high = na
var float am_session_low  = na
bool in_am_session = (hour == 9 and minute >= 30) or (hour >= 10 and hour < 12)

if in_am_session
    am_session_high := na(am_session_high) ? high : math.max(am_session_high, high)
    am_session_low  := na(am_session_low)  ? low  : math.min(am_session_low, low)

// NY Lunch range (12:00–13:00)
var float lunch_high = na
var float lunch_low  = na
bool in_lunch_window = hour == 12

if in_lunch_window
    lunch_high := na(lunch_high) ? high : math.max(lunch_high, high)
    lunch_low  := na(lunch_low)  ? low  : math.min(lunch_low, low)

// PM Session range (13:30–16:00)
var float pm_session_high = na
var float pm_session_low  = na
bool in_pm_window = (hour == 13 and minute >= 30) or (hour >= 14 and hour < 16)

if in_pm_window
    pm_session_high := na(pm_session_high) ? high : math.max(pm_session_high, high)
    pm_session_low  := na(pm_session_low)  ? low  : math.min(pm_session_low, low)

// Reset semua pada awal hari baru
if hour == 0 and minute == 0
    london_session_high := na
    london_session_low  := na
    am_session_high     := na
    am_session_low      := na
    lunch_high          := na
    lunch_low           := na
    pm_session_high     := na
    pm_session_low      := na

// Sweep detection
bool london_bsl_swept = high > london_session_high
bool london_ssl_swept = low  < london_session_low
bool pm_bsl_swept     = high > pm_session_high
bool pm_ssl_swept     = low  < pm_session_low
```

---

## Catatan Tambahan

- **PM Session BSL/SSL** dari hari sebelumnya = liquidity target hari berikutnya untuk Silver Bullet
- **London Session Raid range** (02:00–05:00 ETH) = referensi paling penting ketika tidak close to PM range
- **NY Lunch** = sering menjadi Judas periode — morning moves ter-reverse; jangan panic keluar trade jika ini terjadi di trending day
- **AM Session range** berguna untuk trading PM Session: jika AM = konsolidasi, maka PM bisa be reversal atau continuation
- **PM Session Silver Bullet Macro** dimulai 13:30 EST — ini adalah jendela fokus untuk PM Session setup
- Seluruh hierarchy: ETH London (02-05) → AM RTH (09:30-12) → Lunch (12-13) → PM (13:30-16)

---

---

# ICT Block Types — Advanced (Chapter 29)

## Breaker Block (Advanced)

### Definisi

Breaker Block adalah **Order Block yang ditembus oleh impulse move kuat tanpa pullback signifikan**, setelah sebelumnya berhasil menginisiasi pergerakan. Berbeda dari sekedar failed OB — tujuannya adalah manipulasi likuiditas: mengumpulkan stop-loss lalu berbalik arah.

### Formasi

```
Bullish Breaker Block:
  Low → High → Lower Low (LL) → Higher High (HH)
  Step 1: Bearish OB terbentuk saat Low pertama dibuat
  Step 2: Harga naik ke High (BSL diambil)
  Step 3: Harga turun ke LL (sweep SSL, kumpulkan stop beli)
  Step 4: Impulse naik menembus High (MSS bullish)
  → Bearish OB yang di-sweep = Bullish Breaker Block

Bearish Breaker Block:
  High → Low → Higher High (HH) → Lower Low (LL)
  Step 1: Bullish OB terbentuk saat High pertama dibuat
  Step 2: Harga turun ke Low (SSL diambil)
  Step 3: Harga naik ke HH (sweep BSL, kumpulkan stop jual)
  Step 4: Impulse turun menembus Low (MSS bearish)
  → Bullish OB yang di-sweep = Bearish Breaker Block
```

### Two Liquidity Stages

```
Stage 1: Short-term liquidity sweep (stop collection di level dekat)
Stage 2: HTF liquidity target (PD Array HTF menjadi tujuan akhir)

AB Leg = pergerakan awal yang membentuk setup
1 STD dari AB leg = target "bread & butter" (target minimum)
```

### Entry & Target

```
Entry options:
  - Scale at SSL/BSL yang disweep
  - Pada IFVG atau FVG yang terbentuk di leg B (retrace)
  - Di dekat zona breaker block sendiri

Target: 1 STD dari AB leg (minimum), HTF PD Array (optimal)
```

### Implementasi Pine Script

```pinescript
// Breaker Block detection — simplified
// Bullish Breaker: Bearish OB yang ditembus ke atas setelah LL dibuat
bool bull_breaker_formed = bear_ob_exists and low < bear_ob_bot and close > bear_ob_top

// Bearish Breaker: Bullish OB yang ditembus ke bawah setelah HH dibuat
bool bear_breaker_formed = bull_ob_exists and high > bull_ob_top and close < bull_ob_bot
```

---

## Order Block — Advanced (Chapter 29)

### Definisi Lanjutan

OB adalah **candle yang menginisiasi pergerakan akibat manipulasi market maker**. OB mewakili harga rata-rata entry big player.

- **Bullish OB** = candle close turun terendah di dekat support (down-close candle at key support)
- **Bearish OB** = candle close naik tertinggi di dekat resistance (up-close candle at key resistance)
- **Mean Threshold** = 50% dari range OB (titik tengah) — zona sensitif, harga sering hanya mencapai 50% lalu berbalik

### Lima Aturan Validasi OB

```
1. Trend Alignment   — OB searah dengan bias HTF
2. Liquidity Grab    — ada stop run sebelum OB terbentuk
3. Structural OB     — OB berada di level struktur pasar signifikan
4. With Imbalance    — diikuti oleh FVG / imbalance ke depan
5. LTF Confirmation  — konfirmasi MSS atau displacement di LTF
```

### Timeframe Alignment

| HTF Bias | OB Timeframe | Entry TF |
|---|---|---|
| Monthly | Daily OBs | 4h |
| Weekly | 4h OBs | 1h |
| Daily | 1h OBs | 15m |
| 4h | 15m OBs | 5m |
| 1h | 5m OBs | 1m |

---

## Reclaimed Order Block (Chapter 29)

### Definisi

Candle yang sebelumnya digunakan untuk membeli atau menjual harga, kemudian **direbut kembali** (reclaimed) oleh harga.

```
Bullish Reclaimed OB:
  OB sebelumnya (bearish) → harga turun melewatinya → harga kembali naik melewatinya
  → OB "direbut kembali" ke sisi bullish → berperan sebagai buy-side support

Bearish Reclaimed OB:
  OB sebelumnya (bullish) → harga naik melewatinya → harga kembali turun melewatinya
  → OB "direbut kembali" ke sisi bearish → berperan sebagai sell-side resistance
```

---

## Mitigation Block — Advanced (Chapter 29)

### Definisi Lanjutan

Mitigation Block adalah **failure swing pattern** — terbentuk ketika TIDAK ada liquidity grab sebelum impulse move, dan BOS terjadi sebelum breakout struktur.

### Perbedaan dengan Breaker Block

| | Breaker Block | Mitigation Block |
|---|---|---|
| **Swing sebelumnya** | Successful (ada stop run) | Failure (tidak ada stop run) |
| **Liquidity grab** | Ya (before impulse) | Tidak |
| **BOS** | Setelah sweep | Sebelum breakout |

### Formasi

```
Bullish Mitigation Block:
  Low → High → Higher Low (HL) → Higher High (HH)
  → Candle di Higher Low (HL) = Bullish Mitigation Block

Bearish Mitigation Block:
  High → Low → Lower High (LH) → Lower Low (LL)
  → Candle di Lower High (LH) = Bearish Mitigation Block
```

---

## Rejection Block (Chapter 29)

### Definisi

Rejection Block adalah **swing high atau swing low dengan wick panjang** di satu sisi, menunjukkan penolakan harga yang kuat di area tersebut.

### Formasi

```
Bearish Rejection Block:
  Swing high dengan upper wick sangat panjang
  → Harga menjalankan BSL di atas body candle lalu turun tajam
  → Wick panjang = zona distribusi

Bullish Rejection Block:
  Swing low dengan lower wick sangat panjang
  → Harga menjalankan SSL di bawah body candle lalu naik tajam
  → Wick panjang = zona akumulasi
```

### Syarat Valid

- Harus berada di swing high/low yang signifikan
- Wicks yang panjang (minimal 2-3x panjang body)

### Trigger & Entry

```
Bullish Rejection Block:
  Trigger: harga kembali ke zona wick bawah (below body, within wick)
  → Cari LTF bullish OB atau FVG di area wick untuk entry

Bearish Rejection Block:
  Trigger: harga kembali ke zona wick atas (above body, within wick)
  → Cari LTF bearish OB atau FVG di area wick untuk entry
```

### Implementasi Pine Script

```pinescript
// Rejection Block detection
float wick_ratio = 2.0  // wick setidaknya 2x body

// Bearish Rejection Block: upper wick > 2x body, berada di swing high
float body_size_rjb = math.abs(close - open)
float upper_wick_rjb = high - math.max(open, close)
bool bear_rejection_block = upper_wick_rjb > wick_ratio * body_size_rjb and ta.pivothigh(high, 3, 3)

// Bullish Rejection Block: lower wick > 2x body, berada di swing low
float lower_wick_rjb = math.min(open, close) - low
bool bull_rejection_block = lower_wick_rjb > wick_ratio * body_size_rjb and ta.pivotlow(low, 3, 3)
```

---

## Propulsion Block — Advanced (Chapter 29)

### Definisi Lanjutan

Propulsion Block adalah **candle yang sebelumnya melakukan trading ke dalam OB**, kemudian mengambil alih peran sebagai support/resistance.

> Sangat sensitif — harga tidak boleh melewati 50% (Mean Threshold) dari Propulsion Block. Jika terlewati, setup invalid.

```pinescript
// Cek validitas Propulsion Block
bool prop_valid_bull = close >= prop_bot and close <= prop_mid  // harga tidak lewati 50%
bool prop_valid_bear = close <= prop_top and close >= prop_mid
```

---

## Vacuum Block (Chapter 29)

### Definisi

Vacuum Block adalah **gap yang tercipta oleh event volatil** (NFP, session open, major news) di mana tidak ada transaksi di range tersebut. Tanpa transaksi = tanpa likuiditas = vakum.

Probabilitas tinggi harga kembali untuk mengisi gap tersebut.

### Tiga Skenario Pengisian

```
Skenario 1 (OB inside, stops above/below):
  OB berada di dalam gap, harga mengisi gap sampai stops terkumpul
  → Gap terisi sempurna

Skenario 2 (Partial fill):
  Harga mengisi sebagian gap, meninggalkan FVG di sisa range
  → Sisa FVG = area imbalance untuk pergerakan selanjutnya

Skenario 3 (Full fill):
  Harga mengisi seluruh gap dengan sempurna
  → Perfect Price Delivery — harga kembali ke origin lalu lanjut trend
```

### Implementasi Pine Script

```pinescript
// Vacuum Block: gap antara high/low candle non-adjacent (event-driven gap)
// Bullish Vacuum Block: gap di atas → harga kembali naik untuk mengisi
bool bull_vacuum = low > high[2]
float vac_top = low
float vac_bot = high[2]

// Bearish Vacuum Block: gap di bawah → harga kembali turun untuk mengisi
bool bear_vacuum = high < low[2]
float bear_vac_top = low[2]
float bear_vac_bot = high

// Fill check
bool bull_vac_filled = close >= vac_top
bool bear_vac_filled = close <= bear_vac_bot
```

---

---

# Breakaway Gap & RDRB — Redelivered Rebalanced PD Array (Chapter 30)

## Breakaway Gap

### Definisi

Breakaway Gap adalah **gap yang terbentuk setelah konsolidasi**, menandai awal dari trend baru. Berbeda dari gap biasa karena:
- Terbentuk setelah range-bound atau konsolidasi yang jelas
- Menunjukkan komitmen kuat dari institutional order flow
- Probabilitas tinggi untuk tidak segera terisi (berbeda dari ordinary gap)

```
Pattern:
  [Konsolidasi / ranging] → [Breakaway Gap ke atas/bawah] → [Trend baru dimulai]
```

---

## RDRB — Redelivered Rebalanced PD Array

### Definisi

RDRB adalah **pola price delivery di mana harga melewati PD Array dua kali** (pergi dan kembali), menciptakan Breakaway Gap di satu sisi yang akan menjadi target retracement berikutnya.

### Bullish RDRB

```
Struktur: UP → DOWN → UP

Fase 1: Buyside Delivery   — harga naik ke BSL (atas)
Fase 2: Sellside Delivery  — harga turun ke SSL (bawah), membentuk FVG di bawah
Fase 3: Buyside kembali    — harga naik lagi menembus Fase 1 high

FVG yang tersisa di bawah setelah Fase 2 = Breakaway Gap (bullish)
→ Harga akan kembali ke FVG ini setelah RDRB terbentuk (pullback ke breakaway gap)
```

### Bearish RDRB

```
Struktur: DOWN → UP → DOWN

Fase 1: Sellside Delivery  — harga turun ke SSL (bawah)
Fase 2: Buyside Delivery   — harga naik ke BSL (atas), membentuk FVG di atas
Fase 3: Sellside kembali   — harga turun lagi menembus Fase 1 low

FVG yang tersisa di atas setelah Fase 2 = Breakaway Gap (bearish)
→ Harga akan kembali ke FVG ini setelah RDRB terbentuk (rally ke breakaway gap)
```

### Implementasi Pine Script

```pinescript
// RDRB conceptual detection
// Bullish RDRB: UP → DOWN (FVG below formed) → UP again
// FVG yang tertinggal di bawah = Breakaway Gap = zona pullback entry

// Track FVG yang terbentuk saat harga turun (Fase 2)
bool bull_rdrb_phase2_fvg = bear_fvg  // FVG bearish terbentuk saat retrace

// Fase 3: harga naik kembali menembus Fase 1 high
// → Breakaway Gap = FVG yang terbentuk di Fase 2 = entry pullback
float rdrb_gap_top = bear_fvg_top     // Top of breakaway gap
float rdrb_gap_bot = bear_fvg_bot     // Bottom of breakaway gap
```

---

---

# Central Bank Dealers Range (CBDR) — Chapter 31

## Definisi

**Central Bank Dealers Range (CBDR)** adalah range harga yang terbentuk antara **14:00–20:00 NY Time** (2:00pm–8:00pm), yang berlanjut ke Asian session. Selama periode ini, Smart Money menganalisis market berdasarkan open positions untuk merencanakan pergerakan berikutnya.

> "The flout" = range tinggi dan rendah dari 14:00–20:00 NY Time (gunakan body candle, abaikan wick).

---

## Logika CBDR

- Range yang kecil (< 20-25 points ES/SP500) = CBDR lebih akurat untuk proyeksi London H/L
- Asian Range yang ideal: **10-15 points (ES/SP500)** sebelum Frankfurt Open
- Mark the **middle** (50% CBDR): sellers below middle, buyers above
- Draw **standard deviations** di atas dan di bawah range, extend horizontal lines di setiap deviasi

---

## Standard Deviations dari CBDR

| STD Level | Sell Day | Buy Day |
|---|---|---|
| +1 STD | HOD terbentuk di sini | — |
| +2 STD | TP area | LOD max extension |
| +3 STD | High impact news | High impact news |
| +4 STD | Extreme / NY reversal | Extreme / NY reversal |
| -1 STD | — | LOD terbentuk di sini |
| -2 STD | LOD max extension | TP area |

```
Sell Trade:
  High of day = 1 STD above CBDR
  Target      = 2 STD below CBDR
  Contoh: flout range 15 pts → HOD at +15 pts above → Target at -30 pts below

Buy Trade:
  Low of day  = 1 STD below CBDR
  Target      = 2 STD above CBDR
```

---

## Checklist High Probability London Normal Protraction

```
✓ CBDR < 20 points (ES/SP500)
✓ Asian Range 10-15 points (ES/SP500)
✓ 00:00–02:00 NY trends AGAINST daily bias (Judas/manipulation period)
✓ Protection max 1-2 STD of CBDR
```

---

## Panduan Penggunaan CBDR

- **Paling akurat**: Tuesday – Thursday
- **High impact news** → larger standard deviations akan tercapai
- **Selalu kombinasikan** dengan Daily Bias
- Berguna untuk menentukan LOD atau HOD yang akan terbentuk
- Jika rally setelah MNO tanpa retracement → pada 14:00 EST beli OB pertama di 15m/5m retracement

---

## Implementasi Pine Script

```pinescript
// CBDR: 14:00–20:00 NY Time
var float cbdr_high = na
var float cbdr_low  = na

// Adjusted for UTC chart (14:00 EST = 19:00 UTC in winter, 18:00 UTC in summer)
bool in_cbdr = (hour >= 19 and hour <= 23) or hour == 0  // approximate UTC

if in_cbdr
    cbdr_high := na(cbdr_high) ? high : math.max(cbdr_high, high)
    cbdr_low  := na(cbdr_low)  ? low  : math.min(cbdr_low, low)

float cbdr_mid  = math.avg(cbdr_high, cbdr_low)
float cbdr_size = cbdr_high - cbdr_low

float cbdr_1std_up = cbdr_high + cbdr_size
float cbdr_2std_up = cbdr_high + cbdr_size * 2
float cbdr_3std_up = cbdr_high + cbdr_size * 3
float cbdr_4std_up = cbdr_high + cbdr_size * 4
float cbdr_1std_dn = cbdr_low  - cbdr_size
float cbdr_2std_dn = cbdr_low  - cbdr_size * 2
float cbdr_3std_dn = cbdr_low  - cbdr_size * 3
float cbdr_4std_dn = cbdr_low  - cbdr_size * 4

bool cbdr_ideal = cbdr_size <= 25  // valid jika < 25 pts untuk ES/SP500

if hour == 0 and minute == 0
    cbdr_high := na
    cbdr_low  := na
```

---

---

# High Probability Daytrade Setups (Chapter 32)

## Prinsip Dasar

> Setup berdasarkan HTF terlebih dahulu. Daily atau 4h menentukan arah, support/resistance, dan institutional order flow. LTF (5m atau 15m) untuk eksekusi dan mengurangi stop.

**Hierarki Analisis:** Large institutions: Day → Week → Month. HTF forms slowly → cukup waktu perencanaan. Kondisi HTF direfine ke LTF untuk entry presisi.

---

## Setup Berdasarkan Bias Harian

### Bullish (Daily / 4h Bullish)

1. Gunakan **Previous Day Low to High** untuk retracement entries
2. Gunakan **Previous Day NY Session Low to High** untuk retracement entries
3. Gunakan **Previous Day Low** untuk Sell Stop Raid → akumulasi long
4. Fokus pergerakan dari **HTF Discount ke Premium PD Arrays**

### Bearish (Daily / 4h Bearish)

1. Gunakan **Previous Day High to Low** untuk retracement entries
2. Gunakan **Previous Day NY Session High to Low** untuk retracement entries
3. Gunakan **Previous Day High** untuk Buy Stop Raid → akumulasi short
4. Fokus pergerakan dari **HTF Premium ke Discount PD Arrays**

---

## Zona Entry — Buy Daytrades

```
1. Under Asian Range + 2-5 pts (ES/SP500)
2. FVG di bawah Short-term Low dari Previous Day NY Session
3. Bullish OB di bawah Short-term Low (PDY atau hari ini)
4. 1 STD CBDR + Discount PD Array di London Killzone
5. Inside Protraction lower (12am–2am EST) dengan PD Array
6. Filling Liquidity Void completing under Short-term Low
7. Post-MNO rally → 1st retrace ke 5m/15m +OB
8. 1-2 STD Asian Range + Discount PD Array
9. Turtle Soup: STL diambil dua kali tanpa upside
```

---

## Zona Entry — Short Daytrades

```
1. Above Asian Range + 2-5 pts (ES/SP500)
2. FVG di atas Short-term High dari Previous Day NY Session
3. Bearish OB di atas Short-term High (PDY atau hari ini)
4. 1 STD CBDR + Premium PD Array di London Killzone
5. Inside Protraction higher (12am–2am EST) dengan PD Array
6. Filling Liquidity Void completing above Short-term High
7. Post-MNO drop → 1st retrace ke 5m/15m -OB
8. 1-2 STD Asian Range + Premium PD Array
9. Turtle Soup: STH diambil dua kali tanpa downside
```

---

## Stop Loss Placement

### Buy Daytrades

| Setup | Stop Loss (ES/SP500) |
|---|---|
| CBDR + PDA overlap | 15 pts di bawah entry |
| Run under Asian Range | 20 pts di bawah |
| Sell Stop Raid | 15 pts di bawah low/entry |
| 1st retrace ke +OB | 5 pts di bawah LOD |
| 2nd return for Sell Stops | 15 pts di bawah LOD |
| Lainnya | 50% ADR 5 hari − Asian Range Low |

### Short Daytrades

| Setup | Stop Loss (ES/SP500) |
|---|---|
| CBDR + PDA overlap | 15 pts di atas entry |
| Run above Asian Range | 20 pts di atas |
| Buy Stop Raid | 15 pts di atas high/entry |
| 1st retrace ke -OB | 5 pts di atas HOD |
| 2nd return for Buy Stops | 15 pts di atas HOD |
| Lainnya | 50% ADR 5 hari + Asian Range High |

> ⚠️ **Jangan terburu-buru memindahkan SL.** Tunggu 40-50% daily range sebelum pindah ke breakeven. London bisa double-pass dan keluarkan di BE sebelum move terjadi.

---

## Take Profit — Buy Daytrades

- Partial di **10-15 pts** pertama (selalu)
- Scale out setiap **2 STD Asian Range / CBDR**
- At **Previous Day High +2-7 pts**
- At **50% dari 1h price range**
- **60-80%** di 5-day ADR projections
- Time-based: 5:00am NY → partial; sebelum 7:00am NYO → STH partial; 10:00–11:00am NY dalam rally → scale

## Take Profit — Short Daytrades

- Partial di **10-15 pts** pertama
- Scale out setiap **2 STD Asian Range / CBDR**
- At **Previous Day Low −2-7 pts**
- At **50% dari 1h price range**
- **60-80%** di 5-day ADR projections
- Time-based: 5:00am NY → partial; sebelum 7:00am NYO → STL partial; 10:00–11:00am NY dalam decline → scale

---

## High Probability Scalps

**Target:** 5-10 points (ES/SP500)

- Always look at previous **3 days** highs/lows (current day = Day 1; swing = 3 individual bars)
- **Swing High (valid)**: middle candle = highest high, lower highs di kiri dan kanan (3 candle pattern)
- **Swing Low (valid)**: middle candle = lowest low, higher lows di kiri dan kanan
- Bullish: run previous day(s) highs → Draw on Liquidity (DOL) = buyside liquidity
- Bearish: run previous day(s) lows → DOL = sellside liquidity

**Bullish Scalp Sequence:**
1. Daily swing high ditembus → bullish stage
2. Harga retraces → cari swing low (tidak menembus swing low terbaru)
3. Antisipasi 3rd candle (last candle swing low formation) high akan diraided hari berikutnya
4. Candle yang buka setelah swing low sebaiknya buka di bawah 3rd candle high

**Bearish Scalp Sequence:**
1. Daily swing low ditembus → bearish stage
2. Harga retraces → cari swing high (tidak menembus swing high terbaru)
3. Antisipasi 3rd candle (last candle swing high formation) low akan diraided hari berikutnya

---

## Implementasi Daily Bias dalam NY Session

### Bullish NY Setup (London Bullish)

```
Checklist:
1. Konfirmasi London bullish (attempt to go lower ditolak + daily bias bullish)
2. Tunggu 7:00am NY untuk stalk longs
3. 7:00am–9:00am NY = window setup terbentuk
4. Setelah 7am, tunggu retracement turun 7-10 pts (ES) idealnya
5. NY session retraces dari London swing high atau STH intraday
6. Entry pada 62% Fibonacci saat harga turun
7. Target: retest HOD atau PDH → fib targets 1 & 2

Jika tidak ada retracement 7-10 pts sebelum 9am → skip
```

### Bearish NY Setup (London Bearish)

```
Checklist:
1. Konfirmasi London bearish (rally above midnight NY open ditolak)
2. Tunggu 7:00am NY untuk stalk shorts
3. Retracement naik 10 pts (ES) terbentuk sebelum 9am
4. Entry pada 62% Fibonacci saat harga naik
5. Target: retest LOD atau PDL → fib targets 1 & 2
```

---

## Market Structure dalam Daytrade Context

```
Terminologi:
  LTH/LTL = Long-Term High/Low (daily chart)
  ITH/ITL = Intermediate-Term High/Low (multi-daily swings)
  STH/STL = Short-Term High/Low (intraday per session)

Aturan:
  - Daily = paling penting; bias dari daily; forecast 5-day horizon
  - Break ITH/ITL = significant structure break
  - Bearish: semua up-close candles = resistance (kecuali ada STH di atas = liq grab)
  - ITH tidak lebih tinggi dari 2 STH → market weak/bearish
  - Jika ITH/ITL ditembus → pindah ke sidelines, observasi

  FIB dalam daytrade (bearish):
    Draw dari LTL ke ITH → -1.5 STD = standard target deviation (swing start)
  FIB dalam daytrade (bullish):
    Draw dari LTH ke ITL → -1.5 STD ke bawah = target

  Pyramiding: risk terbesar di entry awal, lebih kecil di continuation
  ITH/ITL tidak boleh ditembus sampai objektif tercapai
```

---

## Ketika Mengharapkan Reversal

- H1 chart trading ke **old high/low** yang sebelumnya menunjukkan reversal jelas → probabilitas tinggi terulang
- NYO (NY Open) bisa reversal ATAU retrace ke CME open → ambil profit sebelum 7:00am EST
- **Jangan mencoba pick top dan bottom**
- Pertanyaan pertama: apa narrative saat ini? Apakah harga naik untuk stops atau turun untuk stops?

---

## Implementasi Pine Script (Daytrade Framework)

```pinescript
// ADR (Average Daily Range) 5 hari
float adr5 = ta.sma(high - low, 5)

// Fibonacci dari daily swing untuk NY entry
// Bullish: FIB dari swing low ke swing high → entry di 62% retrace
float swing_h = ta.highest(high, 10)
float swing_l = ta.lowest(low, 10)
float fib_62_bull = swing_h - (swing_h - swing_l) * 0.62  // entry zone bullish
float fib_62_bear = swing_l + (swing_h - swing_l) * 0.62  // entry zone bearish

// Stop loss references (ES/SP500 approximate — adjust per instrument)
float sl_cbdr_pda  = 15.0   // CBDR + PDA setup
float sl_asian_run = 20.0   // Asian Range sweep setup
float sl_ob_retrace = 5.0   // 1st retrace ke OB

// Swing detection 3-candle (for scalp setup)
bool swing_high_3c = high[1] > high[2] and high[1] > high[0]
bool swing_low_3c  = low[1]  < low[2]  and low[1]  < low[0]

// DOL (Draw on Liquidity) — previous day highs/lows
float pdh = ta.highest(high, 1)[1]  // previous day high
float pdl = ta.lowest(low, 1)[1]    // previous day low
```

---

---

# Quarterly Shifts & IPDA Data Ranges (Chapter 33)

## Quarterly Shifts

### Definisi

**Quarterly Shift** = setiap **3-4 bulan**, pasar menciptakan **intermediate-term turning point**. Shift ini dipicu oleh institutional order flow dan seasonal tendencies untuk menciptakan "new interest" di pasar.

```
Karakteristik:
  - Terjadi setiap 3-4 bulan (kadang 3-6 bulan)
  - Bisa berupa konsolidasi ATAU retracement dari swing sebelumnya
  - Strong uptrend → bisa konsolidasi (STL di daily, lalu naik lagi)
  - Diperkuat: SMT divergence + IPDA levels + seasonal tendencies
```

---

## 3-4 Month Market Shifts

### Smart Money Accumulation (Buy Programs)

```
Underlying vs. Benchmark (contoh: ES vs. DXY):
  1. Manipulation: divergence awal (SMT signal)
  2. Benchmark Lower Low — Underlying Higher Low (bullish divergence SMT)
  3. Underlying Lower Low — Benchmark Lower High
  4. Benchmark Higher High — Underlying Higher Low

DXY vs. ES/YM (NQ):
  2. DXY Lower Low — ES/YM Higher Low
  3. ES/YM Lower Low — DXY Lower High
  4. DXY Higher High — ES/YM (NQ) Higher Low
```

### Smart Money Distribution (Sell Programs)

```
Underlying vs. Benchmark:
  1. Manipulation: divergence awal (SMT signal)
  2. Benchmark Higher High — Underlying Lower High (bearish divergence SMT)
  3. Underlying Higher High — Benchmark Higher Low
  4. Benchmark Lower Low — Underlying Lower High

DXY vs. NQ/YM (ES):
  1. DXY Higher High — NQ/YM (ES) Lower High
  2. NQ/YM (ES) Higher High — DXY Higher Low
  3. DXY Lower Low — ES/YM (NQ) Lower High
```

---

## IPDA — Interbank Price Delivery Algorithm

### Definisi

**IPDA** adalah algoritma yang mencari level harga baru untuk likuiditas dan memindahkan harga ke area likuiditas baru. Mengirimkan price quotes ke institusi keuangan global — menentukan trading range harian, mingguan, bulanan, musiman, tahunan.

> Manipulasi pasar = forced necessity untuk market maker mempertahankan dan meningkatkan kapital.

---

## IPDA Data Ranges (Look Back)

```
Gunakan Daily TF ONLY (Mon–Fri, 1 candle = 1 trading day)

Look Back Ranges dari last market structure shift:
  20 Trading Days = Near-term reference (H/L in 20 days)
  40 Trading Days = Short-term reference (H/L in 40 days)
  60 Trading Days = Intermediate-term reference (H/L in 60 days)

Yang ditandai di setiap range:
  ✓ Highest High dan Lowest Low
  ✓ Old Price High dan Old Price Low
  ✓ Bearish OB dan Bullish OB
  ✓ FVG atau Liquidity Void
  ✓ Monthly, Weekly, 4H PD Arrays

Bagi 60-day range menjadi 50% → equilibrium (premium vs. discount)
```

### The Look Back Process

```
1. Identifikasi 60–40–20 Trading Days
2. Identifikasi Institutional Order Flow
3. Reference Points: Old High, Old Low, Bearish OB, Bullish OB, FVG/LV
4. Anchor vertical line ke previous market shift
```

### The Cast Forward

```
1. Antisipasi Market Shift dalam 20–60 Trading Days ke depan
2. Cast Forward 20 Days jika last shift was 40 days ago
3. Cast Forward 40 Days jika last shift was 20 days ago
4. Projected 3 Month Limit
```

---

## Panduan Penggunaan IPDA

| | Detail |
|---|---|
| **Kapan** | Hari pertama trading setiap bulan |
| **Dimana** | Daily Timeframe ONLY |
| **Mengapa** | Referensi range yang bisa dicapai market sepanjang bulan baru |

**Ketika kedua sisi 60-day range sudah di-clear:**
→ Market dalam equilibrium → cari range high/low di luar 60 hari terakhir → itu arah big move berikutnya

**IPDA tidak memberi sinyal buy/sell sendirian** — perlu blend time + price:
- Time: day of week, session, macros
- Price: PD Arrays, OBs, FVGs, Liquidity levels

---

## Implementasi Pine Script

```pinescript
// IPDA Data Ranges — Daily TF
// Gunakan pada daily chart atau request security dari daily

int lookback_20 = 20
int lookback_40 = 40
int lookback_60 = 60

float hh_20 = ta.highest(high, lookback_20)
float ll_20 = ta.lowest(low, lookback_20)
float hh_40 = ta.highest(high, lookback_40)
float ll_40 = ta.lowest(low, lookback_40)
float hh_60 = ta.highest(high, lookback_60)
float ll_60 = ta.lowest(low, lookback_60)

// Equilibrium 60-day range
float range_60_mid = math.avg(hh_60, ll_60)
bool in_premium_60 = close > range_60_mid  // premium zone
bool in_discount_60 = close < range_60_mid // discount zone

// IPDA Target Zones
bool near_ipda_high_20 = high >= hh_20 * 0.9995
bool near_ipda_low_20  = low  <= ll_20 * 1.0005
bool near_ipda_high_60 = high >= hh_60 * 0.9995
bool near_ipda_low_60  = low  <= ll_60 * 1.0005

// Cast Forward projection — anchor dari last significant structure shift
// (implementation requires tracking when last major H/L was created)
```

---

## Catatan Penting

- **Market tidak random** — ada quarterly rhythm di setiap instrumen
- **3-4 bulan sekali** terjadi sentiment shift yang menciptakan new interest
- IPDA + **SMT divergence** (DXY vs. Underlying) = konfirmasi quarterly shift
- Dalam quarterly shift: cari OB lama dengan banyak long wick → **Rejection Block** (bukan standard OB)
- Seasonal tendency + quarterly shift = high probability context
- Main idea: markets make quarterly shift every 3-4-6 months → finding H/L and PD arrays = trading context tanpa indicators
- **Disclosure**: IPDA data ranges alone tidak cukup — perlu blend dengan time dan price analysis

---

