# Re-verify: #546 — daily scan run has not succeeded since 2026-10-23

- **Check:** `reverify` scheduled QA run, 2026-10-28 08:55–09:0xZ (all times UTC).
- **Verdict:** the reported symptom is **gone**. Labelled `verified-by-claude`.
- **Method:** read-only GETs through the signed-in browser session on
  `https://robo-trader.egedsoft.co.il`. No order placed/cancelled/confirmed,
  no sign-in attempted, no logout, no Settings or API key touched, Alpaca
  never opened.

## 1. The reported symptom, re-read against the live site

`GET /api/status/alpaca?slot=1|2|3` — all three `200 application/json`,
`redirected:false` (835 / 899 / 397 ms), read at `updated_at`
2026-10-28T08:57:09–08:57:10Z.

| | slot 1 | slot 2 | slot 3 | as filed (2026-10-26 14:45Z) |
|---|---|---|---|---|
| `last_scan_date` | **2026-10-27** | **2026-10-27** | **2026-10-27** | 2026-10-23 ×3 |
| `pending_buys.signal_date` | 2026-10-27 | 2026-10-27 | 2026-10-27 | 2026-10-23 ×3 |
| `pending_buys.written_at` | 2026-10-27T22:17:43Z | 2026-10-27T22:23:53Z | 2026-10-27T22:32:45Z | 2026-10-23 |
| `pending_buys.symbols` | `[]` | `[]` | `[]` | `[]` |
| `pending_buys.coverage` | 0.97272573 | 0.97272573 | 0.97272573 | — |
| `rules_path` | rules/02 | rules/04 | rules/06 | — |
| `connected` | true | true | true | true |
| `open_orders` | 0 | 0 | 0 | 0 |
| `broker_error` | null | null | null | null |
| `safeguards.day_start_date` | 2026-10-27 | 2026-10-27 | 2026-10-27 | 2026-10-26 |

`last_scan_date` has advanced **2026-10-23 → 2026-10-27** on all three, and the
candidate sets carry `written_at` timestamps from Tuesday evening
(22:17:43Z / 22:23:53Z / 22:32:45Z), i.e. the normal evening-scan cadence
described in the owner's 2026-10-27 11:56Z comment. The three-day stall the
issue reported is not reproducible.

## 2. Fleet-wide, not just the three bot traders

`GET /api/admin/traders` — `200`, `redirected:false`, 31 rows:

    last_scan_date : { "2026-10-27": 31 }
    scan_stale     : { "false": 31 }
    running_state  : { "running": 31 }

All 31 tenants scanned 2026-10-27 and **none** is flagged `scan_stale`. On
2026-10-27 ~14:0xZ the suite recorded all 31 rows still at 2026-10-26, so the
advance is real and happened on the evening run, uniformly.

## 3. Empty `symbols` lists — now consistent with the owner's explanation

`pending_buys.symbols` is still `[]` on all three. Per the owner's comments
this is the market filter, not a missing scan, and that is corroborated
first-hand this run rather than taken on trust:

- slot 3 `regime`: `{"date":"2026-10-27","label":"07-balanced-mid-hold.json","sitout":true}`,
  rendered as `"07. Balanced mid hold · sitting out · since 2026-10-27"`.
- slot 1 `benchmarks.qqq_total_pct` −2.73 (`qqq_total_usd` −272.70), consistent
  with the stated "Nasdaq-100 under its 50-day average".

An empty candidate set with a fresh `signal_date` and `scan_stale:false` is a
scan that ran and selected nothing — a different state from the filed one,
where the newest set was three days old.

## 4. Pool worker logs

`GET /api/logs/trader-alpaca-s2/tail` and `-s3/tail` — both `200`, 200 lines,
clean ~15-minute closed-market cadence through to 08:51:43Z (s2) and
08:58:13Z (s3). `-s1/tail` returns `404 {"error":"not found"}`; slot 1 is not a
pool trader (PLAYBOOK §4: slots 2 and 3 are the pool traders), so that 404 is
expected and is not part of this symptom.

## 5. What this run did NOT verify

- **The alert's current state was not re-read first-hand.** The browser pane
  reached a Slack workspace sign-in page, and PLAYBOOK §2 forbids attempting a
  sign-in, so `#robo-trader-alerts` was not read. The suite's own ledger
  records `RoboTraderScanNotSucceeded` **RESOLVED 2026-10-27T21:55:31Z** after
  80h25m firing — cited from `state/runs.log`, **inherited, not re-verified
  here**.
- **The retry mechanism has not yet faced a scheduled run.** The fix is a
  retry on the morning check's first database reads; the owner verified it with
  a manual run. The morning check runs Tue–Sat 07:30 ET, so the first scheduled
  post-fix run is **2026-10-28 11:30Z**, ~2.5h after this read. This re-verify
  therefore confirms that the reported symptom has cleared, not that the retry
  holds under the scheduled job.
- **The "Expected behaviour" second half is still unimplemented**, by the
  owner's own statement ("The in-app signal for a failed scan […] is not part
  of this fix"). Noted, not treated as grounds to reopen — it is a scoping
  decision by the owner, and `scan_stale` does now read `false` fleet-wide,
  which is the signal that would carry it.

## Re-check condition for the next run after 2026-10-28 11:30Z

If `RoboTraderScanNotSucceeded` fires again after the 11:30Z scheduled morning
check, that is the retry failing under the real job and #546 should be reopened
with that alert's timestamps. A *silent* 11:30Z run plus `last_scan_date`
advancing to 2026-10-28 on the evening scan is the confirmation the owner was
waiting for.

---
Evidence for issue #546, written by the `reverify` scheduled QA check
(PLAYBOOK §8). Read-only run; nothing written to any working branch.
