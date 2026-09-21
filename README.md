# Daily Risk Calculator

A single-page, offline-capable position-sizing calculator for futures/prop-firm day traders. Enter your starting balance, current balance, and today's stop-loss (in points) — the tool calculates your risk tier, dollar risk per trade, exact position size (NQ minis / MNQ micros), and a daily loss "kill switch."

**[Open the live calculator](./index.html)** — or just open `index.html` in any browser. No build step, no dependencies, no server required.

## How it works

### 1. Inputs
- **Starting Account Balance** — the size your account started at. This never changes for the life of the account.
- **Current Account Balance** — update before each session; drives which risk tier you're in.
- **Today's Stop Loss (points)** — read off the chart, never invented to fit a desired size.

### 2. Risk tiers

Risk per trade scales with how far the current balance has grown above the starting balance:

| Tier | Balance Trigger | Per-Trade Risk |
|------|------------------|-----------------|
| Tier 1 — Base | Starting balance up to +2% | 0.66% |
| Tier 2 — Buffer | +2% to +4% above starting | 0.75% |
| Tier 3 — Building | +4% and above starting | 0.85% |

Dollar risk per trade is always `startingBalance × tierRiskPercent` — it's computed off the **starting** balance, not the current one, so a drawdown doesn't compound the pain.

### 3. Position sizing

Given the dollar risk and today's stop (in points):

```
totalMicros = floor(dollarRisk / (stopPoints × $2))   // $2/pt = 1 MNQ micro
minis       = floor(totalMicros / 10)                  // 10 micros = 1 NQ mini ($20/pt)
micros      = totalMicros % 10
```

Position size always rounds down — the cap is a cap, not a target.

### 4. Daily kill switch

```
maxDailyLoss = dollarRiskPerTrade × maxLosingTradesToday   // default 3 losses
```

Hit the cap, close the platform for the day.

## Disclaimer

This is a position-sizing tool, not trading advice, a strategy, or a signal service. It does not guarantee results. Trading involves risk of loss.
