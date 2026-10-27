# Issue #544 re-verify — Interactive Brokers "vs QQQ / SPY since start"

Scheduled QA run `reverify`, 2026-10-27. All times UTC. **Read-only: GET requests only**,
no order placed/cancelled/confirmed, no sign-in, no logout, no Settings or API-key change,
Alpaca never opened. Other users' traders were read through the admin read-only view and
never acted on.

Issue closed 2026-10-27T10:51:39Z by `egedsoft`; fix stated live "Tuesday 2026-10-27, 06:47 ET".

## Verdict: FIXED

---

## 0. Deploy corroboration (first-hand)

All four Interactive Brokers traders restarted at **2026-10-27 10:47:44–10:47:45** and now
report `trade_loop=0.1.58 engine=0.1.52`. The previous start (2026-10-26 10:23:28) was
`trade_loop=0.1.57`. 10:47:44Z = **06:47 ET**, matching the stated release time to the minute.

Source: `/api/logs/tenant-{user_id}-{broker}/tail` (admin, read-only), read 2026-10-27 13:59:32Z.

```
[2026-10-27 10:47:44] trader started: rules=rules/02-aggressive-mid-hold-quick-profit.json
  ... endpoint=ibkr://robo-trader-gateway-ibkr-paper-29ec8f33c750... trade_loop=0.1.58 engine=0.1.52
```

---

## 1. The figures are populated — all four IBKR traders

`GET /api/admin/trader-status/{user_id}/{broker}?ccy=USD&slot=1`, read **2026-10-27 14:01:09Z**.
Every field is a number; none null, none absent.

| trader | qqq_total_pct | qqq_total_usd | spy_total_pct | spy_total_usd | alpha_vs_qqq_total_pct | alpha_vs_spy_total_pct | benchmark curve points (QQQ / SPY) |
|---|---|---|---|---|---|---|---|
| 29ec8f33 / ibkr_paper | +0.30% | $20.17 | +0.27% | $18.16 | +1.38% | +1.41% | 48 / 48 |
| 29ec8f33 / ibkr_live  | -3.98% | -$455.35 | -0.71% | -$98.81 | +0.53% | -2.74% | 23 / 23 |
| a2fd764b / ibkr_paper | +0.60% | $59.99 | +1.25% | $124.56 | +4.40% | +3.75% | 34 / 34 |
| a2fd764b / ibkr_live  | +5.93% | $355.45 | +3.63% | $332.38 | +4.70% | +7.00% | 66 / 66 |

Both `charts.benchmark_curves.QQQ` and `.SPY` are full daily series spanning each trader's whole
run — not a single point, not a flat line. The issue's "Expected" is satisfied for **both**
indices, not only QQQ.

`/api/admin/traders` at **14:02:39Z** independently shows `vs_qqq_total_pct` non-null on all four
IBKR rows (0.23 / 1.31 / 5.17 / 5.79 — the figures move with the live market).

---

## 2. The rendered page shows the named tiles filled in

`/admin/trader/a2fd764b-af76-4ff5-b0f6-b97e50bbfc5d/ibkr_live`, rendered text read **14:01:54Z**.
The page banner reads "Read-only — admin view of g\*\*\*@gmail.com's trader. Sell, approve and
reject actions are not available here."

```
VS QQQ  +0.12%  Alpha today
VS SPY  +0.43%  Alpha today
QQQ TOTAL  +5.93% Return  $355.45 $ return
SPY TOTAL  +3.63% Return  $332.38 $ return
...
+4.70%  vs QQQ since start
+7.00%  vs SPY since start
```

`vs QQQ since start` and `vs SPY since start` — the exact labels the issue reported as empty —
both carry values. A screenshot taken at ~14:02:57Z shows the same two tiles reading +5.09% and
+7.07%; the drift between the two reads is the live market (US market was open), not instability.

The equity chart now also plots both benchmark series:

```
Equity curve: 66 points from 2026-07-28 to 2026-10-27.
Portfolio: starts at $9,827 (2026-07-28), ends at $15,335 (2026-10-27)...
QQQ: starts at $9,827 (2026-07-28), ends at $15,082 (2026-10-27)...
SPY: starts at $9,827 (2026-07-28), ends at $15,059 (2026-10-27)...
```

---

## 3. The warning is absent from all four IBKR trade-loop logs

`/api/logs/tenant-{user_id}-{broker}/tail?lines=2000` (2000 is the endpoint's cap; `lines=8000`
returns 422), read **13:59:32Z**. Zero lines match `/qqq|spy|benchmark|history/i` in any of the four:

| trader | log window covered | matching lines |
|---|---|---|
| 29ec8f33 / ibkr_paper | 2026-10-21 18:55:32 → 2026-10-27 13:55:45 | 0 |
| 29ec8f33 / ibkr_live  | 2026-10-23 16:45:23 → 2026-10-27 13:55:43 | 0 |
| a2fd764b / ibkr_paper | 2026-10-22 19:41:23 → 2026-10-27 13:55:52 | 0 |
| a2fd764b / ibkr_live  | 2026-10-23 14:49:54 → 2026-10-27 13:57:37 | 0 |

**Stated plainly:** these windows reach back *before* the fix (to 10-21 and 10-22 on the two paper
traders) and contain no matching line there either. So this check confirms the warning is not
being logged now, but it does **not** demonstrate a before/after transition — the pre-fix warning
was presumably emitted somewhere this tail does not cover. The decisive evidence for this issue is
section 1 and 2: the user-visible figures the issue said "stay empty" are populated on all four
traders, for both QQQ and SPY.

The only warnings in those windows are unrelated and pre-existing: `[ibkr] account refresh failed
(ConnectionError/TimeoutError); using cached account values`, and `WARNING: trading with REAL MONEY
via IBKR` on the two live traders.

---

## Market state

US market **open** (page header read "10:02 ET" at 14:01Z). Per PLAYBOOK §6 the benchmark figures
move between reads while the market is open; that movement is expected and is not a finding.

## Scope

Nothing was filed, reopened or changed beyond adding `verified-by-claude` and one comment to #544.
Issue #547, also closed inside this run's window, was left closed with a comment explaining it
cannot be re-tested as written yet.
