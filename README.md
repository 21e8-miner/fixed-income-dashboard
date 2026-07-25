# Fixed Income Pro — Auto-Updating

Bravias Financial desk for **yield · duration · tax efficiency** across cash / Treasury / ladder ETFs.

**→ [Open the dashboard](https://21e8-miner.github.io/fixed-income-dashboard/)**

## What it does

| View | Purpose |
|------|---------|
| **Scatter** | Stated yield vs duration (color = state-tax treatment) |
| **Table** | Sortable book with live price, after-tax yield, tax savings |
| **Duration ladder** | Bucket averages for sizing the curve |

After-tax yield uses a **37% federal** top rate and your **state tax slider**, scaled by each fund’s state-exemption fraction.

## Live prices vs stated yields

| Layer | Source | Auto? |
|-------|--------|-------|
| Market price / day change | Yahoo via local `server.py` | yes |
| Curve tape (13w, 5y, 10y, 30y, SGOV, TLT) | Yahoo | yes |
| Stated / SEC yields & duration | Issuer pages (embedded as-of) | manual update of embedded table |

**We never invent yields.** Stale as-of dates (>45 days) light the status badge orange.

## Local run

```bash
cd fixed-income-dashboard
python3 server.py
# → http://127.0.0.1:8770/
```

```bash
curl -s http://127.0.0.1:8770/api/health
curl -s 'http://127.0.0.1:8770/api/quotes?symbols=SGOV,TLT,^TNX' | python3 -m json.tool
```

Deps: Python 3 + `yfinance` / `pandas` (auto-installed on first run if missing).

## Export

**Export CSV** downloads the current after-tax book for the selected state rate.

Not investment advice. Internal research only.
