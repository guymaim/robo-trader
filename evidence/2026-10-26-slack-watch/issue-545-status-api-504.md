# Evidence — issue #545

Per-trader status API returns HTML 504 after 12-15s while the product reports 3/3 healthy.

Captured by the scheduled QA `slack-watch` run, 2026-10-26, signed-in session, same-origin
credentialed `fetch` from the Robo Trader origin. US market closed (pre-open, opens 13:30Z).

## Probe log (UTC)

| time | request | status | elapsed |
|---|---|---|---|
| 10:46:xx | /api/status/alpaca?slot=1 | 504 text/html 6479 B | — |
| 10:47:45 | /api/status/alpaca?slot=1 | no response | aborted at 12004 ms |
| 10:47:45 | /api/status/alpaca?slot=2 | no response | aborted at 12002 ms |
| 10:48:17 | /api/home/traders | no response | aborted at 10004 ms |
| 10:48:37 | /api/home/traders | 200 | 8272 ms |
| 10:48:55 | /api/status/alpaca?slot=1 | 504 | 15037 ms |
| 10:49:17 | /api/status/alpaca?slot=2 | 504 | 15029 ms |
| 10:49:37 | /api/status/alpaca?slot=3 | 200 42921 B | 12309 ms |
| 10:50:08 | /api/status/alpaca?slot=1 | 504 | 15039 ms |
| 10:50:52 | /api/status/alpaca?slot=1 | 504 | 15032 ms |
| 10:50:52 | /api/home/traders | 200 | 8274 ms |
| 10:52:38 | /api/status/alpaca?slot=1 | 200 38347 B | 12412 ms |
| 10:52:38 | /api/status/alpaca?slot=2 | 200 31055 B | 12444 ms |
| 10:54:16 | /api/status/alpaca?slot=1 | 504 | 15063 ms |
| 10:54:16 | /api/home/traders | 200 | 8356 ms |

Six 504s and four slow 200s across nine minutes — sustained.

Unaffected in the same seconds (no broker credentials needed):

| time | request | status | elapsed |
|---|---|---|---|
| 10:48:17 | /api/logs/trader-alpaca-s2/tail | 200 | 95 ms |
| 10:48:37 | / | 200 52928 B | 78 ms |
| 10:50:08 | /api/logs/trader-alpaca-s2/tail | 200 | 109 ms |

Baseline for comparison, from the previous scheduled run at 09:01Z: 10 same-origin
credentialed GETs, all 200 / application/json / redirected:false, 342-2097 ms.
Historic slowest across runs 114-119: 2376 ms. Current is 5-40x that.

## Credential-broker errors in the pool logs, same morning

```
slot 2  07:49:52 ERROR  ERROR in loop: cred-broker unavailable (unreachable: ConnectError, HTTP None) (retry in 135s)
slot 3  07:49:28 ERROR  ERROR in loop: cred-broker unavailable (unreachable: ConnectError, HTTP None) (retry in 129s)
slot 3  07:49:57 ERROR  ERROR in loop: cred-broker unavailable (unreachable: ConnectError, HTTP None) (retry in 129s)
```

Both loops retried and recovered; no further non-INFO lines in the last 40 lines of either log.

## What the product said at the same time

`/api/home/traders`, read at 10:45:19Z and again at 10:48:37Z / 10:50:52Z / 10:54:16Z:

- 3/3 running, `connected:true` on all three
- `fault:null`, `kill_switch:false`, `daily_loss_halted:false`
- `snapshot_missing:false`, `snapshot_stale:false`, `lock_state:unlocked`
- `open_orders:0` on all three
- cash 6873.55 / 7706.93 / 5567.84 (unchanged from the 07:57Z reconcile)
- 6 positions, 9-dp quantities unchanged
- `start_date` 2026-09-20 / 2026-09-22 / 2026-09-22, `days_running` 37/35/35, no restart
- `last_scan_date` 2026-10-23 on all three
- market `is_open:false`, `next_open` 2026-10-26T13:30:00Z

Pool passes completing normally on the ~15-minute cadence, so `RoboTraderPoolPassStale`
is correctly silent:

```
slot 2 ... 09:54:19, 10:09:38, 10:25:01, 10:40:21
slot 3 ... 09:54:05, 10:09:27, 10:24:42, 10:39:49
```

## Concurrent Alertmanager state (#robo-trader-alerts)

- `KubeMemoryOvercommit` (warning), cluster `robo-trader-k3s`, namespace empty —
  FIRING since 2026-10-26T09:01:27Z, no resolve sent, 1h54m at the time of writing.
  Onset of the API degradation falls inside this episode (between 09:01Z and 10:45Z).
  Mechanism not proven; may belong with #265.
- `RoboTraderScanNotSucceeded` (critical) — firing since 2026-10-24T13:30:31Z, app agrees
  (`last_scan_date` 2026-10-23), maps to #516.
- `RoboTraderTraderParityAlert` (warning) ~152h (#365); `KubeJobFailed` (warning) ~45h.
- `RoboTraderPoolPassStale` — not firing, and correctly so (see pass timestamps above).

## Collection conditions

Read-only pass. No order placed, cancelled or confirmed. No sign-in, no sign-out.
No Settings or API-key change. Alpaca dashboard not opened; `paper-api.alpaca.markets`
never called. No browser-storage or session-token read. Slack read-only: 2 reads, 0 writes.
