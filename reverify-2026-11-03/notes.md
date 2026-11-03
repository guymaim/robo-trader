# Re-verify run 2026-11-03 (scheduled QA, read-only)

Bot account `grok-bot-robo-trader-1@egedsoft.co.il`, role `light_admin`. All times UTC.
Market state: US session CLOSED at the time of reading (checks ran 13:55-14:00Z; next open 14:30:00Z).

Scope: issues closed in `guymaim/robo-trader` since the previous re-verify run
(2026-11-02T08:54:56Z) that did not already carry `verified-by-claude`: **#563, #552, #365**.

Read-only throughout: GET endpoints only. No order placed, cancelled or confirmed;
no sign-in attempted; nothing typed into any field; no setting changed; Alpaca not opened.

---

## #563 — regime-router rule invalidates its own pending buy list on every scan

**Verdict: no longer reproduces on the one scan available since the fix.**

A deploy landed between the report and now: `trade_loop` went `0.1.58` -> `0.1.59`
on both rule-31 traders.

```
[2026-11-02 10:39:52] trader started: rules=rules/31-adaptive-falling-market-switch.json ... trade_loop=0.1.59 engine=0.1.52   # 29ec8f33 ibkr_live
[2026-11-02 10:40:02] trader started: rules=rules/31-adaptive-falling-market-switch.json ... trade_loop=0.1.59 engine=0.1.52   # a2fd764b ibkr_live
```

Exactly one scan has run under 0.1.59 (the Monday 2026-11-02 post-close scan).
Full block for `29ec8f33` ibkr_live, from `/api/logs/tenant-29ec8f33-...-ibkr_live/tail?lines=2000`:

```
[2026-11-02 21:46:34] scan: refreshing data + scoring (universe=all)
[2026-11-02 21:48:24] scan: ohlcv coverage=1.000 loaded=5902 universe=5902 unresolved=0
[2026-11-02 21:48:25] scan: regime router enabled — loading macro data + classifying regime (classifier=falling_market)
[2026-11-02 21:55:48]   falling_market: {'30-falling-market-diversified-wider-trail-earlier-profit.json': 704, '02-aggressive-mid-hold-quick-profit.json': 352}
[2026-11-02 21:58:51] scan: regime=30-falling-market-diversified-wider-trail-earlier-profit.json active_rules=30-... (sit-out — no new entries)
[2026-11-02 21:58:52] scan: 0 PASS candidates on 2026-11-02 (top 10: [])
[2026-11-02 21:58:53] scan: saving via save_scan_state (merge scan-owned fields onto fresh state.json)
```

No `scan: rules changed` line, and no `deleting the pending list`, anywhere after the deploy.
The only two such lines in the retained window are the original ones from the report:

```
[2026-11-01 10:41:01] scan: rules changed (scanned with rules/01-adaptive-learned-modes-cash-invested.json, trader runs rules/31-adaptive-falling-market-switch.json); deleting the pending list of 51 candidates and scanning again   # 29ec8f33
[2026-11-01 14:52:31] scan: rules changed (scanned with rules/30-falling-market-diversified-wider-trail-earlier-profit.json, trader runs rules/31-adaptive-falling-market-switch.json); deleting the pending list of 0 candidates and scanning again   # a2fd764b
```

Why the 11-02 scan discriminates fixed from broken rather than merely being a quiet run:
the stored pending list going into it had been scanned with `rules/30-...` (the router's
selected sub-rule on 11-01), while `/api/admin/traders` still reports the configured rule as
`31-adaptive-falling-market-switch.json`. The old comparison was parent-vs-selected, so
`30 != 31` would have fired — and Evidence 1 of the report shows it firing on a pending list
of 0 candidates, so list size is not what suppressed it. The check sits at the same point in
the flow as before (immediately ahead of `regime router enabled`) and was passed without firing.

Not exercised, and still open:

- Expectation 3 (a rerun with no new market data must not replace a session's list) — needs a
  non-trading-day rerun. None has occurred under 0.1.59; the next opportunity is the weekend of
  2026-11-07/08.
- Expectation 4 (`last_scan_date` is the session scanned, not the date the process ran) — not
  distinguishable today. At 13:56Z all 31 traders report `last_scan_date: 2026-11-02`, and the
  scan scored `on 2026-11-02`; the scan ran on the same calendar day as the session it scanned,
  so the two readings coincide whether or not the field changed. The weekend rerun is the test.
- The regime-change log path (expectation 3's "logged as a regime change rather than as rules
  changed") was not exercised: the stored rule and the newly selected rule were both `30-...`.

## #552 — two pool loops silent 2h39m; PoolPassStale never fired and `fault` self-cleared

**Verdict: not re-testable as written. Left closed.**

Reproducing it needs a pool loop to stop completing passes mid-session. That cannot be caused
read-only, and PLAYBOOK section 2 forbids acting on the traders.

Current state instead, 2026-11-03 13:47-13:59Z, all three pool slots healthy — so neither
detection mechanism has a condition to be tested against:

```
trader-alpaca     last 13:15:38 / 13:30:58 / 13:46:13Z   "market closed; next open ... sleeping 900s"   0 ERROR lines
trader-alpaca-s2  last 13:17:05 / 13:32:17 / 13:47:36Z   same                                           0 ERROR lines
trader-alpaca-s3  last 13:17:20 / 13:32:35 / 13:47:59Z   same                                           0 ERROR lines
```

Unbroken ~900s cadence on all three. `/api/home/traders` at 13:58Z: `fault: null` on all three
alpaca slots, `running_state: running`, `connected: true`. With no faulted trader there is
nothing to observe the latch-vs-timer behaviour on, and the alert rule's own configuration is
not exposed on any app surface this account can read.

## #365 — traders vs simulator: remaining differences explained; fix the check

**Verdict: not re-testable by this account. Left closed.**

The issue carries no repro steps (it is a plan: "not before the weekend of Oct 24-25"), and its
three items are each out of reach from here:

1. The comparison page's "unexplained" labelling — `/admin/trader-parity` returns **HTTP 403**
   to this account. PLAYBOOK section 3 gives the bot `light_admin`, and `/admin/parity` itself
   states "starting a check needs a full admin". The page that would have to be read is not
   readable here.
2. The nightly comparison's missing retry after a database connection drop — needs a DB drop.
   Cannot be caused read-only. `/admin/parity` lists only the last ~2 days of scheduled runs.
3. The morning scan check failing on a post-scan dividend adjustment — needs a dividend
   adjustment landing after a scan. Today's scheduled run was clean, which does not test it:
   `2026-11-03T10:30:00Z -> 10:58:55Z  robo-trader-scan-parity-29895030  succeeded`.

---

Endpoint note for later runs: `javascript_tool` on the browser pane **works** (the 2026-11-02
12:46Z slack-watch handover recorded it as broken — that note is stale). IBKR pod log ids are
`tenant-<user_uuid>-ibkr_live` / `-ibkr_paper`; `/api/logs/<name>/tail?lines=2000` works,
`lines=4000` returns empty.
