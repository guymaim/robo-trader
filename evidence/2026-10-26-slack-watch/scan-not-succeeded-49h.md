# [QA] Daily scan run has not succeeded since 2026-10-23 — evidence

Captured by the `slack-watch` scheduled check, run 2026-10-26 14:44–14:50Z.
All figures read first-hand; Robo Trader read GET-only through the signed-in
browser session, no writes to the product.

## 1. The alert

`#robo-trader-alerts`, Alertmanager on `robo-trader-k3s`:

    [FIRING] RoboTraderScanNotSucceeded (critical)
    Scan CronJob robo-trader/robo-trader-scan-check has not succeeded
    since its last scheduled run
    cluster=robo-trader-k3s namespace=robo-trader

- First fired **2026-10-24T13:30:31Z**, and has been **firing continuously
  since** — no RESOLVED message in the channel at any point.
- Latest 6-hourly re-send **2026-10-26T14:10:32Z** (message ts
  `1793023832.103339`).
- Duration at capture (14:47Z): **49h16m**, spanning three calendar days
  (10-24, 10-25, 10-26).

## 2. The app agrees, first-hand

`GET /api/status/alpaca?slot=1|2|3`, read 14:45:08–14:45:10Z, all `200`,
`application/json`, `redirected:false`:

| | slot 1 | slot 2 | slot 3 |
|---|---|---|---|
| `state.last_scan_date` | **2026-10-23** | **2026-10-23** | **2026-10-23** |
| `state.pending_buys.signal_date` | 2026-10-23 | 2026-10-23 | 2026-10-23 |
| `state.pending_buys.symbols` | `[]` | `[]` | `[]` |
| `pending_buys.written_at` | 2026-10-23T22:02:17.97Z | 2026-10-23T22:18:02.51Z | 2026-10-23T22:32:19.55Z |
| `connected` / `running` | true / running | true / running | true / running |
| `open_orders` | 0 | 0 | 0 |
| `rules_path` | rules/02 | rules/04 | rules/06 |

So the newest scan artifact on every slot is Friday 2026-10-23. Two
scheduled scan runs (10-25, 10-26) have produced nothing.

## 3. Why it matters now: this is a LIVE session

Same read, `market` block:

    "timestamp": "2026-10-26T14:45:08.756315Z"
    "is_open": true
    "next_close": "2026-10-26T20:00:00Z"
    "next_open":  "2026-10-27T13:30:00Z"

- The US market opened **13:30Z**; at capture it had been open **1h17m**.
- `session_today: true` and `safeguards.day_start_date: 2026-10-26` on all
  three — the app has rolled into today's session.
- The three traders are therefore running a live session whose only
  candidate set is **Friday's**, with `pending_buys.symbols` empty.

This is the first open-market session affected. The 10-24 and 10-25
sessions were a Saturday and a Sunday (market closed), so the first two
days of the alert had no live trading to affect.

## 4. Relationship to #516 — stated, not asserted

The previous `slack-watch` run mapped this alert to #516 ("A non-finite scan
count-check setting stops the daily scan run"). **#516 is now closed as
`completed`** (2026-10-25T20:26:50Z) with:

> Fixed and live since Sunday 2026-10-25, 16:30 ET. […] If you still see the
> problem, reply here.

16:30 ET = **2026-10-25T20:30Z**, i.e. **18h17m** before this capture, which
includes the 10-26 scheduled scan run. The scan has still not succeeded.

Two things follow, and this report deliberately does not choose between them:

1. #516's fix did not resolve this alert; or
2. **#516 was never this alert's cause.** Its own body says the failure needs
   "an operator to set a non-finite value", calls it "unlikely", and records
   under Scope: *"Checked by reading the code only; I did not look at the
   values currently configured on the live system."* It was a code-reading
   finding, not a diagnosis of this live alert.

The earlier mapping of this alert to #516 was an inference by this QA suite,
not a verified cause. It is recorded here so nobody re-derives it as fact —
the same correction that was needed for run 120's `#545` cause.

## 5. What was ruled out

- **Not the pool loops.** `RoboTraderPoolPassStale` has **not** fired. Both
  pool trader logs (`/api/logs/trader-alpaca-s2|s3/tail`) run a clean
  closed-market cadence up to the open (s2 …12:57:41 / 13:12:51 / 13:28:14 /
  **13:29:32.634Z**; s3 …12:57:24 / 13:12:41 / 13:28:02 / **13:29:12.670Z**,
  the last two lines on each shortening to "sleeping 60s" as the open
  approached). Both then go quiet through the open session. Per the
  2026-10-14 soak analysis of #357, that silence is **normal**: these loops
  write on action only (`stop_trigger_mode=close` on both rules 04 and 06),
  4h+ open-session gaps were recorded on days every check called healthy, and
  `service.log_mtime` is not a liveness signal. `log_mtime` matches each log's
  last line exactly. PoolPassStale's silence here is **correct**.
- **Not the status API / #545.** All probes this run 189–1023 ms, `200`,
  `application/json`.
- **Not a broker fault.** `broker_error: null`, `broker_errors: []`,
  `broker_status_unknown: false`, `kill_switch: false`,
  `daily_loss_halted: false` on all three.
- **Not the memory family (#265).** `KubeMemoryOvercommit` RESOLVED
  2026-10-26T14:01:27Z and was not firing during this capture.

## 6. Duplicate check

39 open issues read from the live API 14:46Z; none covers this. The scan-
adjacent opens are #336 (scan safety follow-ups, enhancement), #405 (price
history Oct 21 bar), #365, #346, #483, #251 — none is this symptom. #516,
the one issue that was, is closed. `state/known-open.md` checked.

## 7. Read-only confirmation

Robo Trader: GET only — `/`, `/api/status/alpaca?slot=1|2|3`,
`/api/logs/trader-alpaca-s1|s2|s3/tail`. No order placed, cancelled or
confirmed. No sign-in, no sign-out, no Settings change, no API-key change.
Alpaca was not opened by this check. No token or browser-storage read.
Slack: read-only, 3 reads, one channel, zero posts/replies/reactions.
