# FearGreedChart API

Free, public API for an independent stock market Fear & Greed Index. No API key required. CORS enabled.

**Live site:** [feargreedchart.com](https://feargreedchart.com)

---

## What is this?

A 5-component sentiment index scoring the US stock market from 0 (Extreme Fear) to 100 (Extreme Greed), computed daily from public Yahoo Finance data:

| Component | Weight | Source |
|-----------|--------|--------|
| Market Volatility | 25% | VIX vs 20-day moving average |
| Market Momentum | 25% | S&P 500 vs 125-day moving average |
| Put/Call Ratio | 20% | CBOE options sentiment |
| Safe Haven Demand | 15% | Stock vs bond 20-day returns |
| Junk Bond Appetite | 15% | High yield vs investment grade spreads |

Not affiliated with CNN's Fear & Greed Index. Fully independent methodology.

---

## Quick Start

### JavaScript

```javascript
fetch('https://feargreedchart.com/api/?action=all')
  .then(r => r.json())
  .then(data => {
    const score = data.score.score;
    const label = score <= 20 ? 'Extreme Fear'
                : score <= 40 ? 'Fear'
                : score <= 60 ? 'Neutral'
                : score <= 80 ? 'Greed'
                : 'Extreme Greed';
    console.log(`Fear & Greed: ${score} — ${label}`);
  });
```

### Python

```python
import requests

data = requests.get('https://feargreedchart.com/api/?action=all').json()
score = data['score']['score']
print(f"Fear & Greed: {score}")

# Component breakdown
for c in data['score']['components']:
    print(f"  {c['name']}: {c['val']}/100 (weight: {c['wt']}%) — {c['raw']}")
```

### cURL

```bash
# Today's score
curl -s 'https://feargreedchart.com/api/?action=all' | jq '.score'

# Full history as CSV
curl -o feargreed-history.csv 'https://feargreedchart.com/api/?action=history&format=csv'
```

---

## Endpoints

Base URL: `https://feargreedchart.com/api/`

| Endpoint | Description | Cache |
|----------|-------------|-------|
| `?action=all` | Score + components + market data + history + backtest | 15 min |
| `?action=history` | Full daily score history since 2016 (JSON) | 15 min |
| `?action=history&format=csv` | Same as above, downloadable CSV | 15 min |
| `?action=crypto` | Crypto Fear & Greed + BTC/ETH prices + stock comparison | 10 min |
| `?action=insider` | Insider sentiment from SEC Form 4 filings | 30 min |
| `?action=liquidity` | US net liquidity (Fed balance sheet − TGA − RRP) | 6 hr |
| `?action=regime` | Market regime analysis combining all signals | 10 min |

Full documentation: [feargreedchart.com/api-docs](https://feargreedchart.com/api-docs)

---

## Example: Score + Components

```json
{
  "score": {
    "score": 42,
    "components": [
      {"name": "VOLATILITY", "val": 35, "wt": 25, "raw": "VIX 22.4 / MA20 19.8"},
      {"name": "MOMENTUM", "val": 58, "wt": 25, "raw": "SPY 542 / MA125 528"},
      {"name": "PUT/CALL", "val": 41, "wt": 20, "raw": "VIX/VXV 0.94"},
      {"name": "SAFE HAVEN", "val": 38, "wt": 15, "raw": "SPY-TLT -1.2%"},
      {"name": "JUNK BONDS", "val": 44, "wt": 15, "raw": "HYG-LQD +0.3%"}
    ]
  }
}
```

---

## Example: Market Regime

```json
{
  "regime": {
    "name": "Neutral with Undercurrents",
    "severity": "watch",
    "desc": "Markets look calm on the surface but signals underneath are active: insiders selling, liquidity contracting."
  },
  "current": {
    "fg_score": 48,
    "fg_zone": "neutral",
    "velocity": -3,
    "vix": 21.4,
    "insider_score": 32,
    "liq_score": 38
  }
}
```

---

## Embed Widget

Add a live Fear & Greed gauge to any website:

```html
<iframe
  src="https://feargreedchart.com/widget.html"
  width="320" height="310"
  style="border:none;border-radius:16px;"
  loading="lazy"
  title="Fear and Greed Index">
</iframe>
```

Four widgets available: [Fear & Greed gauge](https://feargreedchart.com/widget.html), [Insider Sentiment](https://feargreedchart.com/widget-insider.html), [US Liquidity](https://feargreedchart.com/widget-liquidity.html), [Market Regime](https://feargreedchart.com/widget-regime.html).

Widget picker and embed code generator: [feargreedchart.com/embed](https://feargreedchart.com/embed)

---

## Historical Backtest

The full history contains 2,500+ daily scores since 2016, all computed using the same 5-component methodology. When matched against S&P 500 forward returns:

| Zone | Entries | Avg 1M Return | Win Rate | Avg 6M Return |
|------|---------|---------------|----------|---------------|
| Extreme Fear (0–20) | 31 | +4.37% | 83% | +13.35% |
| Fear (21–40) | 114 | +1.72% | 69% | +8.62% |
| Neutral (41–60) | 255 | +1.22% | 72% | +6.65% |
| Greed (61–80) | 152 | +1.00% | 70% | +6.42% |
| Extreme Greed (81–100) | 7 | +0.36% | 71% | +2.38% |

Interactive backtest: [feargreedchart.com](https://feargreedchart.com) (scroll to Historical Backtest)

Past outcomes are not indicative of future results.

---

## Fair Use

No API key required. Data is cached server-side — polling faster than every 5 minutes returns the same response. For most use cases, fetching once per page load is sufficient.

Please keep attribution visible when embedding widgets.

---

## Links

- **Live index:** [feargreedchart.com](https://feargreedchart.com)
- **Methodology:** [feargreedchart.com/methodology](https://feargreedchart.com/methodology)
- **API docs:** [feargreedchart.com/api-docs](https://feargreedchart.com/api-docs)
- **Embed widgets:** [feargreedchart.com/embed](https://feargreedchart.com/embed)
- **History (CSV):** [feargreedchart.com/api/?action=history&format=csv](https://feargreedchart.com/api/?action=history&format=csv)
- **Twitter/X:** [@FearGreedChart](https://x.com/FearGreedChart)

---

*Not financial advice. Data may be delayed or inaccurate. Do not use as a basis for investment decisions.*
