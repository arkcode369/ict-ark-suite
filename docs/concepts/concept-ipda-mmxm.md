# IPDA, MMXM & Protraction Profiles

> Source 1: IPDA Lookback Final — Time & Price Theory by afyz / TraderDext3r / PO3Trader / theMMXMtrader (Concepts: Michael Huddleston), 9 pages
> Source 2: Protraction Profiles Daily — Time & Price Theory by afyz (Concept: Michael Huddleston / alextlaz), 12 pages
> Source 3: Weekly Profiling Handbook — Simplified from _amtrades "Weekly Range Protocol" & "Market Profiling Guide", 10 pages
> IPDA, Lookback Ranges, MMXM, SMR, PDA Matrix, ERL, IRL, STDV, PO3, AMD, Protraction Profiles, Judas Swing, Classic/Delayed/Judas/Alternative, FV Model of Liquidity, Weekly Profiling, Intraweek Reversal, TGIF

---

# IPDA — Interbank/Institutional Price Delivery Algorithm

## Price Delivery Continuum

```
IPDA = sistem algoritmik yang mengarahkan price delivery dari discount ke premium dan vice versa

Dua bentuk liquidity yang dicari IPDA dalam setiap lookback period:
  1. External Range Liquidity (ERL) ~ Old Swing Highs & Lows
  2. Internal Range Liquidity (IRL) ~ Inefficiencies dalam Premium atau Discount dari Dealing Range
       → FVG, Order Block, Rejection Block, Breaker Block

Price Delivery Continuum:
  → Price coded untuk mencari dua bentuk liquidity ini
  → Oscillasi antara IRL dan ERL sepanjang waktu
  → "Discount to Premium" dan "Premium to Discount" secara bergantian

4 IPDA Market Phases (urutan):
  Consolidation  |  Retracement/Protraction  |  Reversal  |  Expansion

Untuk menentukan current delivery state:
  → Tentukan sisi mana dari Market Maker Curve yang sedang terjadi
  → Asumsikan price akan terus ke arah tersebut SAMPAI Draw on Liquidity (DOL) terpenuhi
    DAN ada Higher Timeframe Smart Money Reversal (SMR)
```

---

# Lookback Ranges — Sinkronisasi Timeframe

## IPDA Lookback Periods

```
Timeframe     Lookback Period
Daily         20, 40, 60 Days
H1            3 Days
M1/M5         3 H4 Candles

Cara penggunaan:
  → Tentukan PDA yang signifikan dalam konteks lookback period yang relevan untuk TF yang dianalisis
  → Liquidity yang terbentuk dalam lookback period = reference untuk price seeking targets

Contoh H1 MMSM:
  → Gunakan 3-day IPDA lookback
  → Overlay dengan STDV projections
  → Lookback liquidity pools harus line up dengan STDV levels
  → Daily → H1 Market Maker Sell Model

Contoh Daily MMSM:
  → Gunakan 20 & 40 day IPDA lookback
  → Overlay dengan STDV projections
  → Weekly → Daily Market Maker Sell Model
  → Reaccumulation dapat terlihat dengan akurat di SD2 dan SD2.5

"Time & Price are fractal — gunakan lookback sync untuk frame timeframe chart"
```

## PDA (Price Delivery Array) Matrix

```
PDA Matrix = keseluruhan array PDA yang tersedia untuk price delivery:
  ERL (External Range Liquidity):
    → Old Swing Highs & Lows
    → Prominent Highs / Lows dari lookback period

  IRL (Internal Range Liquidity):
    → Fair Value Gaps (FVG / BISI / SIBI)
    → Order Blocks (OB)
    → Rejection Blocks (RB)
    → Breaker Blocks (BB)
    → Equilibrium dari dealing ranges

Qualification:
  Bullish:  price seeks PREMIUM arrays above market price
  Bearish:  price seeks DISCOUNT arrays below market price
```

---

# Market Maker Models (MMXM)

## Struktur Dasar MMXM

```
MMXM = Market Maker Model — dua sisi kurva:
  Buy Side of Curve  (MMBM - Market Maker Buy Model):
    → Price seeking premium market
    → Bullish delivery continuum

  Sell Side of Curve (MMSM - Market Maker Sell Model):
    → Price seeking discount market
    → Bearish delivery continuum

SMR (Smart Money Reversal):
  → MEMBALIK Price Delivery Continuum
  → Shifts liquidity targets:
    IF bullish (buy side) → price seeks premium arrays ABOVE market
    IF bearish (sell side) → price seeks discount arrays BELOW market
  → Frame SMR menggunakan >= H4 PDA dalam bentuk ERL atau IRL

"MMXM's are Efficiency Engines"
```

## FV Model of Liquidity — Mekanisme MMXM

```
Dalam HTF bullish orderflow, sell side MMXM curve = efficient retracement engine:
  → Smart Money reaccumulates longs dalam Fair Value dari dealing range
  → Hedged short positions di-offset dari local premium

Protraction dari weekly range = optimal liquidity distribution ke engineered buy stops

Proses MMXM (buy day example):
  1. Timed accumulation melalui commercial hedging (sell side curve)
     → Protraction = Order Accumulation opposing DOL
  2. Offset accumulation into Fair Value + offset distribution expanding toward premium
     → "Semakin dalam ke sell-side curve, semakin dekat ke Fair Value → lebih banyak counterparty liquidity ke MMs"
     → Progressive Accumulation = Hedging under sell side delivery
  3. Majority inventory (long positions) dialokasikan saat Fair Value Time
     → CISD (atau MMSM → MMBM) menginstruksikan expansion ke arah accumulated orders
     → Protraction → Expansion = Session Delivery (NY & London)

Takeaway:
  → "Jika kita bisa apply ini ke intraday profile (Protraction Template) → kita dapat understand
    fase IPDA delivery per session"
  → Ini memungkinkan kita menginfer price dynamics antara London dan NY session
    dalam konteks daily range
```

---

# STDV Projections — Standard Deviations

## Fibonacci Settings

```
Fibonacci Levels yang digunakan: {1, 1.5, 2, 2.5, 4}

Level  Makna
-1     Symmetric Move (pencerminan dari manipulation leg)
-2     Consolidation / Reversal Zone (target pertama)
-2.5   Consolidation / Reversal Zone (target lanjutan)
-4     Max Extension / Terminus (target akhir — sangat jauh)

Note: "If price displaces through SD2.5 it is likely to continue to SD4 ~ Terminus"
```

## Cara Anchoring Fibonacci

```
Setting Fib Level 0 ke 1:
  Level 0 = Swing Low/High = ERL (titik manipulation/SMR)
  Level 1 = Candle yang broke structure = MSS (candle yang konfirmasi CISD)

Proses lengkap:
  1. Di HTF POI (>= H4 PDA dalam bentuk ERL atau IRL), tunggu price:
     → Raid old low/high (manipulation)
     → MSS (market structure shift / CISD) terkonfirmasi
  2. Trace Fib dari 0 ke 1 berdasarkan definisi di atas
  3. Project ke depan:
     → SD1  = target symmetric — equilibrium
     → SD2  = consolidation / reversal zone — cari confluence ERL atau IRL di sisi berlawanan
     → SD2.5 = reversal zone lanjutan
     → SD4  = terminus — max extension

IPDA Targets pada setiap level:
  → Lihat sisi berlawanan dari MMXM curve untuk confluence dengan:
    a) ERL = Prominent Highs/Lows
    b) IRL = Inefficiency dalam P/D dealing range

Alternative Anchoring (4H candle dengan long wick di HTF POI):
  → Jika manipulation leg terlalu besar / tidak jelas
  → Anchor Fib dari low/high of 4H candle ke open of same candle (order pairing)
  → Sering digunakan dalam weekly profile STDV projections
```

---

# PO3 (Power of Three) dalam Konteks MMXM

## AMD Framework

```
PO3 = AMD = Power of Three:
  A = Accumulation  (konsolidasi / ranging)
  M = Manipulation  (false break — sweep liquidity)
  D = Distribution  (expansion ke DOL)

Setiap candle (dari monthly hingga M1) mengandung A, M, D

"Satu fase mengakhiri yang lain secara otomatis,
 dan setiap fase memproyeksikan fase berikutnya dengan bantuan STDV"

Cara framing PO3 dalam konteks MMXM:
  → Frame PO3 move di sekitar area SMR
    menggunakan lookback synchronization timeframes
  → Zoom in ke area manipulation → analyze apa yang terjadi di sana
  → Manipulation leg of PO3 = yang digunakan untuk anchor STDV projection

  Bullish PO3:
    A: Accumulation phase (range / konsolidasi)
    M: Manipulation lower → raid sell stops → engage discount PDA
    D: Distribution higher → expand ke buy stops (DOL)

  Bearish PO3:
    A: Accumulation phase
    M: Manipulation higher → raid buy stops → engage premium PDA
    D: Distribution lower → expand ke sell stops (DOL)
```

---

# Protraction Profiles — Daily Session Delivery

## Definisi Protraction

```
Protraction = manipulasi price yang OPPOSING terhadap daily DOL

"Protraction is the study of how the daily wick is formed"
  → Sell wick dalam buy day = bullish daily candle
  → Buy wick dalam sell day = bearish daily candle

"By understanding how London trades, we can make inferences about
 PA during New York Open — crafting an edge in the session"

Protraction biasanya terjadi:
  → Di atas atau di bawah TRUE DAY OPEN
  → Atau Midnight Opening Price (MOP)
```

## 3 Profile Conditions

```
1. VALID   → ada meaningful manipulation sebelum NYO 8:30 AM EST
2. VOID    → TIDAK ada manipulation sebelum NYO 8:30 AM EST
3. ALTERNATIVE → DOL sudah terpenuhi di London Session (tidak dicover di PDF ini)
```

---

## Protraction Profile #1 — Classic (VALID)

```
Timing:    12:00 Midnight — 2:00/3:00 AM EST
Kondisi:   Manipulasi SEBELUM London Open (2 AM EST)

Karakteristik:
  → Manipulate/Protract ke >= H4 PDA sebelum London Open
  → London: expand toward DOL (price delivery dimulai dari sini)
  → New York: CONTINUATION toward DOL

Syarat manipulation:
  → Engage H1 atau higher TF PDA: FVG, OB, BB, ERL
  → Idealnya di DISCOUNT dari most recent dealing range
  → Opposing daily DOL
  → Low/High yang terbentuk saat protraction = LOW/HIGH OF DAY
     (tidak boleh dilanggar)

NY Entry: OTE Breaker (70.5% Fibonacci Level)
  → "Classic Protraction = NY OTE BB"
  → Breaker Block yang terbentuk di NY session
  → 70.5% fib dari manipulation leg = Optimal Trade Entry

Summary: Manipulate 12-3 AM → London expand → NY continuation dengan OTE Breaker entry
```

---

## Protraction Profile #2 — Delayed (VALID)

```
Timing:    2:00/3:00 AM — 8:00 AM EST (Late London / Pre-NY)
Kondisi:   Manipulasi SEBELUM NYO, TAPI SETELAH London Open

Karakteristik:
  → Manipulate/Protract di Late London (setelah 3 AM) atau Pre-NY (sebelum 8:30)
  → Low/High yang terbentuk saat protraction = LOW/HIGH OF DAY
  → New York: REVERSAL of direction toward DOL (BUKAN continuation seperti Classic)

Syarat manipulation:
  → Engage H1 atau higher TF PDA: FVG, OB, BB, ERL
  → Idealnya di discount dari most recent dealing range, opposing daily DOL

Timing khusus: "Sangat umum terjadi pada Red Folder 8:30 News!"
  → News 8:30 sering menjadi penyebab delayed protraction
  → NY akan reverse dari low/high yang dibentuk di sekitar 8:30

NY Entry: FVG Entries (dapat juga menggunakan OB, BB, atau PDA lain)
  → "Delayed Protraction = NY FVG"
  → FVG yang terbentuk setelah 8:30 HIN = entry point untuk NY reversal
  → NY session = NY Reversal Profile

Summary: London expand tanpa manipulation → Pre-NY manipulation 3-8 AM → NY reversal dengan FVG entry
```

---

## Protraction Profile #3 — Judas Swing (VOID)

```
Timing:    9:30 AM EST (Equities Open)
Kondisi:   TIDAK ada manipulation sebelum NYO → Void Profile

Karakteristik:
  → London begins expanding toward DOL TANPA protracting/manipulating terlebih dahulu
  → Algorithmic function expected to deliver Judas Swing biasanya di 9:30 AM
  → NY Open (9:30) = manipulation point → sweep London Low/High
  → LOD/HOD terbentuk di 9:30 manipulation
  → Setelah LOD/HOD terbentuk → NY expands toward DOL

Mekanisme:
  → 9:30 equities open: manipulation lower engages H1 or higher TF PDA
    (FVG, OB, BB, ERL) → forms LOD/HOD
  → ITM low/high dari Judas Swing = LOW/HIGH OF DAY
  → NY will sweep London low/high then expand to DOL

"If we do not see manipulation pre-NYO → expect a Judas Swing"

NY Entry: TURTLE SOUP Entries
  → "Judas Swing = NY TS"
  → HTF PDA = H1 Rejection Block (atau PDA lain)
  → Masuk setelah NY sweeps London Low/High dan terbentuk reversal

Summary: London expands tanpa manipulation → 9:30 Judas Sweep London High/Low → NY expansion to DOL
```

---

## Protraction Profile #4 — Alternative

```
Kondisi: Daily DOL sudah terpenuhi di London Session (pre-NYO)
Detail: Tidak dicover dalam PDF ini
```

---

## Tabel Perbandingan Protraction Profiles

```
Profile      Timing Protraction   NY Expectation       NY Entry Type
Classic      12:00–2/3 AM EST     Continuation         OTE Breaker (70.5%)
Delayed      2/3–8:00 AM EST      Reversal             FVG (after 8:30 HIN)
Judas        VOID (no protraction) Sweep London L/H    Turtle Soup
Alternative  London DOL met       (tidak dicover)      —

RULES:
  IF protraction (manipulation opposing DOL) terjadi sebelum NYO:
    → Classic (12-3 AM):  NY continuation
    → Delayed (3-8 AM):   NY reversal
  IF TIDAK ada protraction sebelum NYO:
    → Judas Swing di 9:30 equities open
  IF DOL sudah met di London:
    → Alternative profile
```

---

# Weekly Profiling — Handbook

## Economic Calendar (Simplified)

```
HIN (High Impact News) Hierarchy:

HIGH IMPACT:
  → Non-Farm Payroll (NFP)
  → Consumer Price Index (CPI)
  → FOMC Press Conference

MEDIUM IMPACT:
  → Retail Sales
  → Producer Price Index (PPI)
  → Prelim Gross Domestic Product (GDP)
  → FOMC Meeting Minutes

LOW IMPACT:
  → 10:00 Red Folder News (external market events)
  → Bank holidays (half or closed trading days)

Rules:
  1. Avoid trading days PRIOR to first HIN day of the week (low probability)
  2. Avoid Monday: no significant HIN, possible Judas on weekly range, used as data
  3. HIN is used to set expectations for WHEN low resistance price runs can occur
     (NOT to predict weekly profiles at start of week)
  4. HIN does NOT always form the high or low of the week

HIN lebih likely:
  a) MANIPULATION untuk intraweek reversal IF hari-hari sebelumnya konsolidasi/
     gagal engage HTF PDA opposing weekly DOL
  b) EXPANSION toward weekly DOL IF intraweek reversal sudah terjadi setelah
     engaging HTF PDA opposing weekly DOL

HIN yang paling likely define weekly range:
  NFP, CPI, FOMC Press Conference
```

## Intraweek Reversal Criteria

```
3 kriteria untuk mengidentifikasi intraweek reversal:
  1. Price engages >= H4 PDA (minimal H4 timeframe PDA)
  2. Hourly CISD (H1 CISD sebagai konfirmasi)
  3. Economic calendar sebagai confluence untuk frame weekly profile

3 price tendencies untuk intraweek reversals (berlaku di daily chart):
  1. Price reverses at EXTERNAL LIQUIDITY (old swing highs/lows)
  2. Price retraces to 50% of the DEALING RANGE
  3. Price leaves CONSOLIDATIONS at the external range

High Quality Reversal Structure:
  → PDAs yang mean thresholdnya dihormati oleh CANDLE BODIES setelah reversal:
    - Order Blocks (OB)
    - Fair Value Gaps (FVG)
    - Rejection Blocks (RB)
  → Jika bodies close PAST mean threshold → reversal LOW QUALITY dan vulnerable

Konfirmasi tambahan:
  → Displacement away setelah reversal
  → Hourly CISD (hanya sebagai konfirmasi ketika narrative sudah ditentukan sebelumnya)

Setelah intraweek reversal dikonfirmasi:
  → Trade expansion days yang mengikutinya
  → Price cenderung menghormati order blocks saat expanding
```

## 4 Weekly Profiles

### Profile 1 — Classic Expansion

```
Reversal Day: Selasa (atau awal minggu)
Trigger Conditions:
  Mon: Konsolidasi / accumulate ATAU shallow opposing run dari weekly opening price
  Tue: Manipulation ke >= H4 PDA opposing DOL → intraweek reversal

Expected Behavior:
  Mon: Consolidate/Accumulate
  Tue: Manipulation ke >= H4 PDA opposing DOL → Intraweek Reversal
  Wed: Expansion toward DOL
  Thu: Expansion toward DOL / reach DOL
  Fri: Reach DOL / Close within 20-30% of weekly range (TGIF)

Economic Calendar Ideal:
  Mon: No news
  Tue: HIN paired with reversal
  Wed: HIN drives expansion
  Thu: HIN drives expansion
  Fri: No news

VOID conditions (jika salah satu ini terjadi → BUKAN Classic Expansion):
  → Monday expands toward DOL
  → Tuesday expands toward DOL tanpa manipulate ke >= H4 HTF PDA

Notes:
  → Observe Monday's daily range → Tuesday manipulation biasanya akan LEAVE Monday's range
  → PDA menarik untuk reversal: Rejection Block, Order Block, FVG

Reversal Structure:
  1. Mon/Tue engage >= H4 HTF PDA
  2. H1–H4 candle CISD + SMT (idealnya)
```

### Profile 2 — Midweek Reversal

```
Reversal Day: Rabu
Dua Skenario:

SKENARIO 1: Mon-Tue Accumulation/Consolidation
  → Mon dan Tue konsolidasi (bisa sebagai kelanjutan dari konsolidasi sebelumnya)
  → IPDA Market Cycles: HARUS ada manipulation ke >= H4 HTF PDA sebelum reversal
  → Wed: Engage >= H4 HTF PDA → Intraweek Reversal
  → Thu: Expansion toward DOL (BULK — probability tertinggi)
  → Fri: Expansion toward/reach DOL (continuation)

SKENARIO 2: Mon-Tue Retracement
  → Mon dan Tue retrace (expand) setelah expansion di hari-hari sebelumnya
  → IPDA: Tidak perlu konsolidasi sebelum midweek reversal
  → Wed: Engage >= H4 HTF PDA → Intraweek Reversal
  → Thu/Fri: sama dengan Skenario 1

VOID conditions:
  → Mon dan Tue terus-menerus expand toward weekly DOL
  → (tanpa retracement, tanpa accumulation, tanpa manipulation)

Notes:
  → SMT pada Daily antara NQ/ES pada Tue/Wed → Friday likely close back dalam range (TGIF)
    Contoh bullish: ES reversal Tue (Classic) tapi NQ lower low Wed (Midweek) → NQ TGIF

PDA menarik untuk reversal: ERL, Rejection Block, Order Block, FVG, Breaker Block
Reversal Structure:
  1. Wednesday engages >= H4 HTF PDA
  2. H1–H4 candle CISD + SMT (idealnya)
```

### Profile 3 — Consolidation Reversal

```
Reversal Day: Kamis
Kondisi:
  Mon–Wed: Consolidation/Accumulation (bisa kelanjutan konsolidasi ATAU
           konsolidasi setelah expansion besar)
  → IPDA: Market cycles antara konsolidasi dan expansion —
    setelah max ~3 large daily displacement candles, expect konsolidasi

Expected Behavior:
  Mon: Accumulation/Consolidation
  Tue: Accumulation/Consolidation
  Wed: Accumulation/Consolidation
  Thu: Engage external range high/low konsolidasi (tambahan: >= H4 PDA)
       → Intraweek Reversal
  Fri: Expansion toward DOL (BULK — probability tertinggi)

Economic Calendar Ideal:
  Mon–Wed: No news
  Thu: HIN paired with reversal
  Fri: HIN drives expansion

VOID conditions:
  → Mon, Tue, Wed engage external consolidation range (keluar range terlalu dini)
  → Thu/Early Fri tidak engage external consolidation range

Tips:
  → HINDARI trading internal ranges — swing highs/lows DALAM consolidation range = low probability
  → Hanya engage jika external range highs/lows disentuh Thu/Fri

Target Prices:
  → Thu: target external range consolidation low → equilibrium dari high-low range
  → Fri: target consolidation low/high opposing dari yang digunakan untuk reversal

PDA menarik untuk reversal: Rejection Block, Order Block, FVG
Reversal Structure:
  1. Thursday engages external consolidation range low/high (ideally + >= H4 PDA)
  2. M15–H1 candle CISD + SMT (idealnya)
```

### Profile 4 — Seek and Destroy (HINDARI)

```
Kapan: NFP Week, minggu sebelum NFP, minggu setelah NFP,
       berada antara 2 HTF PDA, atau summer months

LOW PROBABILITY — DIREKOMENDASIKAN UNTUK TIDAK DITRADE

Karakteristik:
  → Sulit membedakan antara fase A, M, D
  → Semua minggu membangun range yang menyerang sell & buy side liquidity bolak-balik
  → Bereaksi kuat di equilibrium

"Manipulation" dalam profile ini:
  → Setiap sweep dari liquidity hari sebelumnya bisa dianggap manipulation
  → Bisa coba catch trade pada sweep — hold maksimum ke 50% current trading range
  → HIGH RISK & LOW PROBABILITY

Range: Broadening Formation — expansion tidak dapat diprediksi arahnya
```

## TGIF (Thank God It's Friday)

```
TGIF = ekspektasi Friday untuk RETRACE 20-30% kembali ke dalam weekly range

3 Kondisi yang HARUS terpenuhi semua:
  1. HANYA pada: Classic Expansion Week
     ATAU Midweek Reversal week yang diverged dengan correlated asset's classic expansion week
  2. Expansion pada Thursday yang MEETS DOL (DOL tercapai pada Thursday)
  3. SMT sebagai confluence

Jika semua terpenuhi:
  → Friday cenderung retrace 20-30% kembali ke dalam weekly range
  → BUKAN continuation ke DOL
```

## Simplified Protocol (_amtrades / Weekly Profiling Handbook)

```
8 Langkah:
  1. HINDARI Monday
  2. HINDARI trading days sebelum first HIN event of the week
  3. Frame >= H4 HTF PDA untuk potential reversal point
  4. Tentukan weekly profile yang likely berdasarkan data hari-hari yang sudah print
  5. Tunggu H1 (M30 refinement) untuk CISD
  6. JANGAN mencoba antisipasi reversal sebelum konfirmasi
  7. Cari entries dalam HIGH PROBABILITY EXPANSIONS setelah reversal terkonfirmasi
  8. Entry HANYA during a Killzone (London / NY session hours)
```

---

# Koneksi Antar Konsep

## IPDA → Protraction → Weekly Profile

```
Hierarchy analisis (top-down):

STEP 1: IPDA LOOKBACK
  → Identifikasi lookback period yang relevan (20/40/60 day untuk Daily, 3 day untuk H1)
  → Definisikan PDA Matrix: ERL dan IRL dalam lookback range
  → Tentukan current side of MMXM curve (buy side atau sell side)

STEP 2: WEEKLY PROFILE
  → Gunakan price action hari-hari yang sudah print sebagai data
  → Identifikasi profile: Classic / Midweek Reversal / Consolidation Reversal
  → Frame intraweek reversal: >= H4 PDA + H1 CISD + HIN calendar

STEP 3: SMR (Smart Money Reversal)
  → Identifikasi di >= H4 PDA (ERL atau IRL)
  → MSS konfirmasi = candle yang broke structure
  → Anchor STDV Fib: Level 0 = swing point, Level 1 = MSS candle

STEP 4: STDV PROJECTIONS
  → SD2 dan SD2.5 = consolidation/reversal zone → cari ERL/IRL confluence di sisi berlawanan
  → SD4 = terminus
  → Pastikan levels line up dengan IPDA lookback liquidity pools

STEP 5: PROTRACTION PROFILE (intraday)
  → Classic (12-3 AM manipulation): NY = continuation, entry OTE Breaker
  → Delayed (3-8 AM manipulation): NY = reversal, entry FVG after 8:30
  → Judas (no manipulation pre-NYO): 9:30 sweep London H/L, entry Turtle Soup

STEP 6: ENTRY
  → Sesuaikan entry type dengan protraction profile
  → Konfirmasi dengan CISD pada timeframe yang sesuai
```

## Fair Value sebagai Pusat Gravitasi

```
"Fair Value is King"

Principle:
  → Semakin jauh ke sell-side curve (semakin jauh dari fair value) = lebih banyak order pairing
  → Algoritma mengalokasikan majority inventory DI fair value time
  → Setelah fair value dicapai → CISD → expansion

Implikasi:
  → Protraction = price moving AWAY from fair value (opposing DOL)
  → Tujuan protraction = accumulate orders di fair value atau di bawahnya (bullish)
  → Setelah accumulation selesai → expansion kembali ke DOL (dari IRL ke ERL)

"IRL → ERL adalah siklus dasar price delivery"
```
