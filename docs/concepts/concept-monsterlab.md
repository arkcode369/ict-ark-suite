# concept-monsterlab.md

> Source 1: Standard Deviation + Power of Three by po3trader (MonsterLab), 14 pages
> Source 2: IOF + Standard Deviation Theory by po3trader (MonsterLab), 12 pages
> Source 3: Power of Three: The Devil's Mark by po3trader (MonsterLab), 14 pages
> Source 4: Standard Deviation + MMXM by po3trader & TraderDext3r (MonsterLab), 17 pages
> Concept Credits: Michael Huddleston (ICT) & TraderDext3r (MonsterLab Founder)
> IOF, Institutional Order Flow, Standard Deviation, STDV, PO3, AMD, Devil's Mark, Silver Bullet Zone, MMXM, STDV+MMXM, Sell Side Curve, Buy Side Curve, Manipulation Confirmation, IPDA Lookback per PO3, ABC/123

---

# IOF — Institutional Order Flow

## Definisi dan 3 Pertanyaan Kunci

```
IOF = Directional movement of Price to seek Liquidity (Upside or Downside)

3 Pertanyaan wajib sebelum menentukan bias:
  1. Where did Price come FROM?
     → Identifikasi asal pergerakan: IRL atau ERL?
  2. Where is Price NOW?
     → Posisi saat ini: apakah di IRL atau ERL?
  3. Where is Price likely to GO?
     → Proyeksi: dari IRL → ERL, atau dari ERL → IRL

Logic inti:
  → Price bergerak IRL > ERL (dari internal ke eksternal)
  → Price bergerak ERL > IRL (dari eksternal ke internal)
  → Ketika IRL diambil → ERL menjadi Draw on Liquidity berikutnya
  → Ketika ERL diambil → IRL menjadi Draw on Liquidity berikutnya

IRL (Internal Range Liquidity) = FVG, Volume Imbalance, BISI/SIBI, OB, inefficiency
ERL (External Range Liquidity) = Old Swing Highs & Lows (Buy Stops / Sell Stops)
```

## Market Efficiency Paradigm

```
Retail percaya bahwa aksi buy/sell pressure mereka menggerakkan pasar
  → Retail = circle besar (jumlah banyak tapi tidak berpengaruh)

Smart Money (large banks & institutions) = real influencer
  → Smart Money MENCARI LIKUIDITAS dan menarik price ke sana
  → "Think like Smart Money"

IPDA (Interbank Price Delivery Algorithm):
  → AI yang mendeliver price seefisien mungkin
  → IPDA di-draw ke: SSL/BSL (Old Highs & Lows) atau Inefficiency/Imbalance (FVG/LV)
  → Key: Market Efficiency Paradigm + IPDA = cara memahami IOF dan membangun bias
```

## IOF Framework — Contoh Aplikasi

```
Contoh 1 (Bullish bias):
  → Price dari: IRL (SIBI + LV)
  → Price sekarang: ERL (Old Low) — SSL diambil
  → Price akan ke: IRL (kembali ke dalam dealing range) → BIAS BULLISH

Contoh 2 (Bearish bias switch):
  → Price dari: ERL (Old Low) — sudah diambil
  → Price sekarang: IRL (FVG di Premium) — dalam Premium zone
  → Price akan ke: ERL (Related Equal Lows) → BIAS BEARISH

Contoh 3 (Bullish lagi):
  → ERL (equal lows) diambil → price sekarang di ERL
  → Look back to IRL → bias kembali bullish

Note: "Price is fractal — proses ini berlaku di SEMUA timeframe"

Top-Down IOF Process:
  Step 1: Monthly TF  → tentukan Long Term Bias (ERL taken? look IRL)
  Step 2: Weekly TF   → tentukan Shorter Term Draw (lebih dekat ke price)
  Step 3: Daily TF    → lihat Bearish/Bullish PD Arrays yang di-respect vs di-disrespect
                        Bearish OB = last up-candle = "Resistance" untuk new selling
                        Bullish OB = last down-candle = "Support" untuk new buying
  Step 4: LTF         → struktur, STDV projections, PD Arrays untuk entry
  NOTE: "Institutional Order Flow comes first. No narrative, no trade."
```

---

# Power of Three (PO3) — AMD Framework

## Elemen Dasar PO3

```
PO3 = Power of Three = AMD:
  A = Accumulation  → range / konsolidasi (price builds up orders)
  M = Manipulation  → false break (sweep liquidity opposing true direction)
  D = Distribution  → expansion ke Draw on Liquidity

Berlaku untuk SEMUA PO3 candle: Monthly, Weekly, Daily, 4H, 1H
  → Setiap candle berisi tiga fase ini

Indikator TradingView: "HTF Power of Three" by toodegrees

OLHC (Bullish PO3):
  Open → Low (Manipulation down) → High (Distribution up) → Close
  → Bearish candle pada HTF PO3 indicator = kita MENGHARAPKAN ini hanyalah Manipulation
  → Price membuat Low of OLHC, kemudian reversal ke atas untuk Distribution

OHLC (Bearish PO3):
  Open → High (Manipulation up) → Low (Distribution down) → Close
  → Bullish candle pada HTF PO3 indicator = Manipulation phase
  → Price membuat High of OHLC, kemudian reversal ke bawah untuk Distribution
```

## TF untuk Identifikasi Fase PO3

```
Monthly PO3  → gunakan 4H TF untuk identifikasi setiap fase
Weekly PO3   → gunakan 1H TF untuk identifikasi
Daily PO3    → gunakan 15m / 5m TF untuk identifikasi
4H PO3       → gunakan 5m TF untuk identifikasi
```

## IPDA Lookback Sinkron dengan PO3

```
Setiap PO3 memiliki lookback period yang sesuai:
  Monthly PO3  = 3 months data → 20-40-60 Day Lookback
  Weekly PO3   = 3 weeks data  → 5-10-15 Day Lookback
  Daily PO3    = 3 days data   → 1-2-3 Day Lookback
  4H PO3       = 12 hours data → 4-8-12 Hour Lookback

Cara penggunaan:
  1. Tentukan PO3 yang sedang diperdagangkan
  2. Gunakan lookback period yang sesuai untuk identifikasi PD Arrays relevan
  3. Transpose PD Arrays dari HTF ke LTF untuk entry
  4. Gunakan "3 Previous Candles" sebagai sample untuk STDV projection
```

---

# Standard Deviation Theory — STDV

## STDV Formula & Level Makna

```
STDV Levels (diukur dari discernible price leg dengan Fibonacci):
  1.0 - 1.5 STDV = Re-accumulation / Re-distribution (Silver Bullet Zone / SBZ)
  2.0 - 2.5 STDV = Smart Money Reversal (SMR) — zona reversal/retracement
  4.0 STDV        = Terminus — Smart Money Reversal / Max Extension

Aturan:
  → Jika price close KUAT di atas 2.5 STDV → ekspansi berlanjut ke 4.0 STDV (Terminus)
  → Jika price GAGAL close di atas 2.5 STDV → retracement/reversal ke Equilibrium
  → Retracement vs Reversal: HANYA bisa ditentukan oleh HTF premise
```

## Cara Mengukur STDV per Fase PO3

### Accumulation → Projeksi Manipulation

```
Bullish PO3 (OLHC):
  → Ambil LOWEST discernible price leg dari Accumulation (low ke high)
  → Anchor Fib: 0 = swing LOW, 1 = swing HIGH
  → Project ke bawah → 2-2.5 STDV = zona antisipasi Reversal Manipulation
  → Manipulation leg SEHARUSNYA mencapai 2-2.5 STDV ini

Bearish PO3 (OHLC):
  → Ambil HIGHEST discernible price leg dari Accumulation (high ke low)
  → Anchor Fib: 0 = swing HIGH, 1 = swing LOW
  → Project ke atas → 2-2.5 STDV = zona antisipasi Reversal Manipulation
```

### Manipulation → Projeksi Distribution

```
Bullish Distribution:
  → Ambil LOWEST discernible price leg DOWN sebelum MSS (last move yang membuat swing terendah)
  → Ini adalah LAST MOVE dari Manipulation phase yang membuat lowest swing
  → Anchor Fib dari low ke high dari leg ini
  → Project ke atas → mengukur upside untuk Distribution phase

Bearish Distribution:
  → Ambil HIGHEST discernible price leg UP sebelum MSS (last move yang membuat swing tertinggi)
  → Anchor Fib dari high ke low dari leg ini
  → Project ke bawah → mengukur downside untuk Distribution phase

Rule dari Distribution:
  → 2-2.5 STDV = ZONA target untuk Reversals & Retracements
  → 4 STDV = Terminus = Smart Money Reversal (max extension)
  → Price algorithm: jika Manipulation mencapai 2 STDV → Distribution mencapai 4 STDV
  → "Price runs on algorithm and moves according to the previous range"
```

## Manipulation Phase — 3 Konfirmasi Selesai

```
Untuk konfirmasi bahwa Manipulation phase selesai dan Distribution akan dimulai:
  1. HTF POI (>= 4H) nested within 2-2.5 STDV zone
  2. SMT Divergence (dengan correlated asset/index)
  3. Market Structure Shift (MSS)

"As one phase ends, the other begins."

Saat 2-2.5 STDV Manipulation terpenuhi:
  → HTF PO3 indicator mungkin menunjukkan candle berlawanan arah
  → JANGAN TERTIPU: itu hanya Manipulation phase yang sedang selesai
  → Tunggu konfirmasi 3-poin di atas sebelum entry Distribution
```

---

# Silver Bullet Zone (SBZ)

## 1.0 — 1.5 STDV

```
SBZ = Silver Bullet Zone = 1.0 - 1.5 STDV dalam Distribution phase

Mengapa kuat?
  → Price berada dalam Expansion/Distribution phase → seeking HTF Liquidity
  → Di dalam 1-1.5 STDV → expect Re-Accumulation / Re-Distribution
  → Ini = pullback/retest sebelum continuation ke 2-2.5 STDV

2 Skenario entry dari SBZ:
  1. SBZ Retracement ke IRL (any Internal Range Liquidity)
     → Cukup untuk entry, tapi probabilitas sedang

  2. [Higher Probability] IRL yang NESTED di dalam Silver Bullet Zone
     → IRL (FVG, OB, BISI, imbalance) yang tepat berada di dalam 1-1.5 STDV
     → Confluence antara STDV zone + specific IRL = entry terkuat

Contoh penggunaan:
  → Distribution Phase bearish berjalan → price retrace ke 1-1.5 STDV
  → Di dalam 1-1.5 STDV ada Bearish OB (IRL)
  → Price mitigasi OB → entry sell → continuation ke 2-2.5 STDV target
```

---

# The Devil's Mark

## Definisi

```
Devil's Mark = tipe Power of Three khusus di mana:
  → Candle OPENS dan langsung melakukan EXPANSION
  → TANPA menciptakan wick (tidak ada opposite wick saat open)

Implikasi:
  → Highly likely price AKAN KEMBALI untuk mencetak wick tersebut
  → Wick akan tercetak dalam LIFESPAN dari PO3 yang sedang aktif

"Price will come back to print the wick within the current PO3."
```

## Identifikasi Devil's Mark + STDV

```
Langkah:
  1. Identifikasi Devil's Mark: candle opens → ekspansi langsung tanpa wick

  2. Tentukan STDV saat ini menggunakan IPDA lookback dari PO3 tersebut:
     → Gunakan "most discernible price leg" dari lookback untuk proyeksi

  3. Cek posisi opening candle dalam STDV:
     a. Opens dalam 2.0 - 2.5 STDV → expect RETRACEMENT / REVERSAL to print wick
     b. Opens dalam 1.0 - 1.5 STDV → expect RE-ACCUMULATION / RE-DISTRIBUTION
                                       continuation ke 2-2.5 STDV

  4. Bila Devil's Mark + 2-2.5 STDV:
     → Antisipasi reversal/retracement untuk cetak wick
     → Drop ke LTF untuk cari entry di arah wick yang akan tercetak

  5. Bila Devil's Mark + 1-1.5 STDV:
     → Antisipasi re-accumulation/re-distribution
     → Continuation ke 2-2.5 STDV sebelum wick tercetak
```

## Proses LTF Setelah Identifikasi

```
Setelah Devil's Mark + STDV teridentifikasi (contoh 4H PO3):

  1. Identifikasi Main Range menggunakan IPDA lookback (60-day lookback untuk Daily TF)
  2. Proyeksikan Main Range dengan Fib tool (low to high)
  3. Konfirmasi: "Current Weekly Candle opens within 2-2.5 STDV = Devil's Mark"
  4. Drop ke LTF (15m/30m):
     → Identifikasi LTF Ranges
     → Konfirmasi price dalam Premium Market (retracement/reversal zone)
     → Tunggu Weekly PO3 candle reversal untuk cetak wick low
  5. Entry: konfirmasi struktur LTF bahwa wick akan tercetak
  6. Dalam 4H PO3 Devil's Mark: price completes 4 STDV saat mencetak bottom wick

Contoh timeline 4H Devil's Mark:
  10:00 → 4H candle opens, immediately expands upside, NO bottom wick = Devil's Mark
  → Main Range identified + 2-2.5 STDV zone confirmed
  → Drop to 1m: expect 4H to print bottom wick
  → Price prints bottom wick + completes 4 STDV (Terminus)
```

---

# STDV + MMXM Integration

## Market Maker Models + Standard Deviation

```
MMXM = Market Maker X Model
  → MM Sell Model (MMSM): PD Array HTF (A) → PD Array HTF (B) [sell side curve]
  → MM Buy Model (MMBM):  PD Array HTF (B) → PD Array HTF (C) [buy side curve]

Framework: PDA HTF (A) → PDA HTF (B) → PDA HTF (C)
  Think: A → B → C atau 1 → 2 → 3 atau Original Consolidation → Discount Array → Distributions

Untuk setiap leg MMXM:
  → Identifikasi Opening PO3 yang sedang aktif dalam leg tersebut
  → Identifikasi A, M, D phases dari PO3
  → Gunakan STDV untuk project dan validate setiap fase
```

## Sell Side of the Curve (MMSM) — Step by Step

```
1/ Price mencapai PD Array HTF (A):
   → Kombinasikan dengan STDV dari range sebelumnya
   → 2-2.5 STDV dari previous range = Retracement/Reversal ke PD Array HTF (B)
   → Ini = "Action Point" untuk mulai MMSM

2/ Anchor STDV untuk MMSM:
   → Gunakan sample size (most discernible price leg) di PD Array HTF (A)
   → Project MMSM ke PD Array HTF (B) dengan Fibonacci

3/ Pada 1.0 - 1.5 STDV = Re-Distribution Zone (Silver Bullet Zone):
   → Expect retracement ke dalam IRL untuk continuation
   → Entry opportunities di IRL yang nested dalam SBZ

4/ Pada 2.0 STDV:
   → Expect retracement ke IRL untuk continuation ke 2.5
   → HANYA jika price memiliki "unfinished business" di PD Array HTF (B)

5/ Pada 2.5 STDV = PD Array HTF (B):
   → Antisipasi Smart Money Reversal (SMR) untuk MMBM (buy model)
   → Ini = Action Point untuk start Buy Side of Curve
```

## Buy Side of the Curve (MMBM) — Step by Step

```
1/ Price masuk ke PD Array HTF (B) + 2.5 STDV = ACTION POINT:
   → Antisipasi SMR → start MMBM
   → "Price reaches PD Array HTF B + 2.0-2.5 STDV = Action Point / SMR"

2/ Anchor STDV untuk MMBM:
   → Ambil sample size (lowest discernible leg) di PD Array HTF (B)
   → Project MMBM ke PD Array HTF (C)

3/ Pada 1.0 - 1.5 STDV = Re-Accumulation Zone (Silver Bullet Zone):
   → Expect retracement ke IRL untuk continuation
   → Entry dari IRL nested dalam SBZ

4/ Pada 2.0 STDV:
   → Expect retracement ke IRL
   → Cara menentukan retracement vs reversal = lihat HTF:
     "Does price have unfinished business above (PD Array HTF C)?"
     → Jika ya = hanya retracement → tunggu konfirmasi dari SMT (correlated asset lows)
     → "Break structure to the upside for continuations"

5/ Pada 2.5 STDV:
   → Same dynamic: expect retracement untuk re-accumulations
   → IF unfinished business → continuation ke 4.0 STDV (Terminus)

6/ Pada 4.0 STDV = TERMINUS:
   → Smart Money Reversal → start MMSM ke PD Array HTF berikutnya
```

---

# Step-by-Step: Full STDV + MMXM + IOF Process

## Template Analisis

```
STEP 1 — HTF IOF (Monthly/Weekly):
  → Tanya 3 pertanyaan IOF untuk tentukan Long Term Bias
  → Monthly: ERL taken → look IRL (misal BISI) = Bearish Bias Long Term
  → Weekly: scale down, same process untuk Shorter Term Draw

STEP 2 — IPDA Lookback (3 Previous Candles dari PO3 yang dipilih):
  → Pilih PO3 yang akan diperdagangkan (Monthly/Weekly/Daily/4H)
  → Lookback = 3 previous candles dari TF tersebut
  → Identifikasi PD Arrays dalam lookback window
  → Tanda discernible price leg untuk STDV projection
  → Tempel/transpose ke LTF untuk identifikasi entry zone

STEP 3 — 3 Pertanyaan STDV:
  → Where did price come from? → identifikasi previous range
  → Where is price now? → tentukan STDV zone saat ini
  → Where is price likely to go? → proyeksikan ke STDV berikutnya

STEP 4 — Identifikasi PO3 Phase (AMD):
  → Accumulation, Manipulation, atau Distribution?
  → Jika Manipulation: sudahkah 2-2.5 STDV terpenuhi?
  → Cek 3 konfirmasi: HTF POI nested + SMT + MSS

STEP 5 — Devil's Mark (jika ada):
  → Cek apakah candle PO3 opens tanpa wick dan langsung ekspansi
  → Kombinasikan dengan STDV zone saat ini
  → Antisipasi wick printing dalam lifespan PO3

STEP 6 — LTF Entry:
  → Drop ke LTF yang sesuai dengan PO3 yang dipilih
  → Identifikasi LTF range + Premium/Discount
  → Cari IRL nested dalam Silver Bullet Zone (1-1.5 STDV)
  → Entry: SMT konfirmasi + MSS + IRL mitigation

STEP 7 — Targets:
  → Internal Liquidity atas/bawah 50% (EQ) untuk TP1
  → External Liquidity / Swing High untuk TP2
  → 2-2.5 STDV Distribution untuk TP ultimate
```

## ABC / 123 Simplifikasi

```
Think ABC atau 123 untuk meringkas MMXM:
  A (1) = Original Consolidation / PD Array HTF (A) = start point MMXM
  B (2) = Discount Array / PD Array HTF (B) = SMR point (Action Point)
  C (3) = Distributions / PD Array HTF (C) = target MMBM

Weekly PO3 Recap Example (USDJPY):
  1. HTF: IOF Bullish, SMT at lows, price at Weekly Volume Imbalance
  2. 3 Previous Candles (3-week lookback): outlined PD Arrays + Discount Arrays
     → Discernible price leg projection: 2.0-2.5 of previous range = Retracement/Reversal
  3. ABC: A = Original Consolidation; B = Discount Array; C = Distributions
  4. Weekly PO3 in play (AMD): A complete, M phase → D phase anticipated
  5. Bonus: Devil's Mark identified on Weekly → wick akan tercetak
  → RESULT: Enter bullish dari PD Array HTF (B) + 2.5 STDV Action Point
```

---

# Confirmation Tool — SMT

```
SMT (Smart Money Tool) = Divergence dengan correlated asset
  → Digunakan untuk konfirmasi Smart Money Reversal
  → Digunakan untuk menentukan retracement vs reversal di 2.0/2.5 STDV

Penggunaan:
  → Saat price mencapai 2.0/2.5 STDV + cek apakah correlated asset juga di extremum
  → SMT = correlated asset membuat NEW low/high tapi instrument yang ditrade TIDAK
  → Signal: probable reversal/retracement confirmed oleh divergence

Targets via SMT:
  → "Confirm continuations by using crash correlation lows (SMT)"
  → Jika SMT divergence terkonfirmasi di PD Array HTF (B) → MMBM dapat dimulai
```

---

# Referensi Praktis

## Entry Model po3trader (4H PO3 + STDV)

```
Pairs: NAS (Nasdaq), SPX (S&P 500), GU (GBP/USD)
Session: NY AM session (primary)
PO3: 4H PO3
STDV: Standard Deviation overlay

Entry sequence:
  1. Konfirmasi HTF IOF (Monthly → Weekly → Daily)
  2. Identifikasi 4H PO3 phase (A, M, atau D)
  3. Projection STDV dari manipulation leg
  4. Entry di IRL nested dalam 1-1.5 STDV (SBZ) dalam Distribution phase
  5. Target: 2-2.5 STDV (SMR zone) atau 4 STDV (Terminus) tergantung HTF
```

## Entry Model Disciplined_Trader (4H PO3 + STDV + MMXM)

```
Pairs: NQ (Nasdaq E-mini), ES (S&P 500 E-mini)
Model: 4H PO3 + STDV + MMXM (ketiga elemen dikombinasikan)
  → MMXM memberi framework makro (PDA A → B → C)
  → PO3 memberi timing dalam MMXM leg
  → STDV memberi precision untuk entry dan target
```
