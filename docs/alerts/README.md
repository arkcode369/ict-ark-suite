# Alert Templates & JSON Webhook Recipes

Cara pakai TradingView alerts dengan webhook JSON ke trading bot, Discord, Telegram, atau backend custom.

## 1. Cara setup alert di TradingView

1. Tambahkan indikator/strategy ke chart.
2. Klik **Alarm clock icon → Create Alert** (atau Alt+A).
3. **Condition**: pilih indikator → pilih `alertcondition()` yang relevan (mis. `ICT-PD: New bullish FVG`).
4. **Options**: set frequency:
   - **Once Per Bar Close** (rekomendasi default — tidak repaint).
   - **Once Per Bar** (lebih cepat tapi bisa fire pre-close, hati-hati).
5. **Notifications**: aktifkan webhook URL dan paste salah satu template di bawah ke "Message" field.

> TradingView placeholders yang bisa kamu pakai di message: `{{ticker}}`, `{{exchange}}`, `{{interval}}`, `{{close}}`, `{{high}}`, `{{low}}`, `{{volume}}`, `{{time}}`, `{{timenow}}`, `{{strategy.order.action}}`, `{{strategy.order.contracts}}`, `{{strategy.position_size}}`, `{{strategy.market_position}}`.

## 2. Discord webhook

```json
{
  "content": "**ICT-PD New Bullish FVG** on `{{ticker}}` {{interval}}\nClose: {{close}}\nTime: {{time}}",
  "username": "ICT/SMC Suite",
  "embeds": [{
    "title": "Setup detected",
    "description": "Bullish FVG formed — DOL = nearest BSL",
    "color": 4915330,
    "fields": [
      {"name": "Ticker",   "value": "{{ticker}}",   "inline": true},
      {"name": "TF",       "value": "{{interval}}", "inline": true},
      {"name": "Price",    "value": "{{close}}",    "inline": true}
    ]
  }]
}
```

## 3. Telegram bot

Webhook URL: `https://api.telegram.org/bot<BOT_TOKEN>/sendMessage`

```json
{
  "chat_id": "<YOUR_CHAT_ID>",
  "parse_mode": "Markdown",
  "text": "*ICT-MS MSS Bullish*\nTicker: `{{ticker}}` `{{interval}}`\nClose: `{{close}}`\nTime: `{{time}}`"
}
```

## 4. Generic JSON for trading bot / backend

```json
{
  "source": "tradingview",
  "indicator": "ICT/SMC Suite",
  "event": "ICT-OTE: Sweet spot 70.5% touched",
  "ticker": "{{ticker}}",
  "exchange": "{{exchange}}",
  "interval": "{{interval}}",
  "price": "{{close}}",
  "ts": "{{timenow}}",
  "side": "long",
  "raw_message": "{{strategy.order.action}}"
}
```

## 5. Strategy entry/exit (untuk auto-trading bot)

Untuk strategy script (`strategies/*.pine`), TradingView akan fire alert tiap order. Pakai placeholder `strategy.*`:

```json
{
  "secret": "<YOUR_SHARED_SECRET>",
  "action": "{{strategy.order.action}}",
  "contracts": "{{strategy.order.contracts}}",
  "ticker": "{{ticker}}",
  "position_size": "{{strategy.position_size}}",
  "market_position": "{{strategy.market_position}}",
  "price": "{{close}}",
  "time": "{{time}}"
}
```

> **Action values**: `buy`, `sell`, `long`, `short`, `cover` — tergantung strategy code.

## 6. Setup model presets (rekomendasi alert combo)

| Setup | Modul terlibat | Alerts ke-fire |
|---|---|---|
| **PD-FVG entry** | 01 PD + 02 LIT + 03 MS | bull FVG, recent SSL sweep, MSS bullish |
| **CRT Model #1** | 04 Time + 05 Q + 11 OTE | London KZ start, OTE sweet spot touched |
| **Sweep + Reclaim** | 02 LIT + 03 MS | BSL/SSL sweep, MSS opposite-trend |
| **Mean reversion** | 08 STDV | Devil's Mark approach, +2σ deviation hit |
| **PO3 daily** | 04 + 06 CRT | NY open + manip sweep + 1st distribution candle |

## 7. Tips

- **Bedakan signal alert vs execution alert**: signal alert (di indikator) untuk notifikasi, execution alert (di strategy) untuk auto-trade.
- **Throttle**: TradingView free akun cuma 1 alert simultaneous. Akun Pro+ unlimited expiration. Pakai "Once Per Bar Close" untuk hindari spam.
- **Test webhook dulu**: pakai webhook.site atau ngrok untuk lihat payload masuk sebelum hubungkan ke production bot.
- **Sharedsecret**: selalu sertakan field `secret` di JSON dan validasi di backend — TradingView IP webhook bisa dispoof.
- **Time zones**: `{{time}}` is bar's open time in UTC. `{{timenow}}` is alert fire time. Hati-hati untuk reconciliation.
