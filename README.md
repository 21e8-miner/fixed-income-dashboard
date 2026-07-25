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
| Market price / day change | Yahoo via local `server.py` **or** overnight `data/funds.json` | yes |
| Curve tape (13w, 5y, 10y, 30y, SGOV, TLT) | Yahoo (local server) | yes |
| Stated yields | `data/funds.json` updated by GitHub Action | **weekday mornings** |
| Duration / tax treatment | Desk book (manual when structure changes) | rare |

**We never invent yields.** If Yahoo has no usable fund yield, the prior print is kept. Stale as-of dates (>45 days) light the status badge orange.

### Morning automation (GitHub Actions)

Workflow: `.github/workflows/update-yields.yml`

| Trigger | When |
|---------|------|
| `schedule` | Weekdays **13:00 UTC** (~9am Eastern daylight) |
| `workflow_dispatch` | Manual “Run workflow” in the Actions tab |

What it does:

1. Checks out the repo  
2. Runs `python scripts/update_yields.py` (yfinance)  
3. Writes `data/funds.json` (yields + last prices when available)  
4. Commits & pushes only if the file changed  

```bash
# local dry-run
python3 scripts/update_yields.py --dry-run

# local write
python3 scripts/update_yields.py
```

> **Caveat:** Yahoo’s fund `yield` field is a free proxy — usually close to distribution / SEC-style yield, **not** a guarantee of issuer SEC 30-day. BOXX and other odd structures often return 0 and are left on the last manual/issuer print. Confirm against the issuer page before trading size.

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
