# Scan Job reported failed ~60s after its scan completed and published state

Found by the scheduled `slack-watch` QA run (run 126), 2026-10-26 22:42-23:0xZ.
Read-only: Slack read, Robo Trader GET-only, no orders, no settings changed.
All times UTC. Slack renders IST (UTC+2).

## The alert

`#robo-trader-alerts`, Alertmanager, cluster `robo-trader-k3s`:

| UTC | Alert | Severity | Body |
|---|---|---|---|
| 2026-10-26 22:12:31 | `RoboTraderScanJobFailed` | critical | Scan Job `robo-trader/robo-trader-scan-ibkr-paper-29ec8f33c750-29884191` failed |

Slot decode: `29884191 x 60 = 1793051460` = **2026-10-26T21:51:00Z**, the Job's own
scheduled start. (Same decode method used for 29879250, 29869110, 29864790 in earlier runs.)

This is the first `RoboTraderScanJobFailed` since the 2026-10-25T11:31:01Z RESOLVED,
and the first time the `29ec8f33c750` ibkr_paper scan CronJob has ever been the job
named in this alert - every prior send named `robo-trader-scan-parity-*`,
`robo-trader-scan-check-*`, or once `robo-trader-scan-ibkr-paper-a2fd764baf76` (#280).

## What the product says that scan actually did

`/api/logs/tenant-29ec8f33-c750-405a-8060-aa679a6bdb1a-ibkr_paper/tail` (admin, read-only):

```
[2026-10-26 21:51:02] scan: refreshing data + scoring (universe=all)
[2026-10-26 22:05:57] scan: ohlcv coverage=0.971 loaded=5732 universe=5903 unresolved=171
[2026-10-26 22:05:57] scan: proceeding with partial OHLCV universe (5732/5903; 171 unresolved)
[2026-10-26 22:11:31] scan: 0 PASS candidates on 2026-10-26 (top 10: [])
[2026-10-26 22:11:31] scan: saving via save_scan_state (merge scan-owned fields onto fresh state.json)
```

- Scan started **2 seconds after** the Job's scheduled start, ran **20m29s**, completed,
  and **published** via `save_scan_state`.
- The alert fired **60 seconds after** that publish.

Corroborated from a second source, `/api/admin/traders` at 22:5xZ: **both** `ibkr_paper`
rows read `last_scan_date 2026-10-26`, `scan_stale false`. The Last scan column added by
the #281 fix agrees that this tenant's scan landed today.

## Why this is worth fixing rather than muting

The owner's suite depends on this exact critical: #546 exists because
`RoboTraderScanNotSucceeded` was believed. A critical that fires one minute after a
successful publish trains the reader to discount it.

## What this evidence does NOT establish

Deliberately recorded so nobody over-reads it:

- This suite has no cluster or Alertmanager access, so it cannot say **why** the Job is
  reported failed. Three readings fit equally well:
  1. the Job wrapper exits non-zero after the scan succeeds;
  2. a post-`save_scan_state` step in the same Job fails;
  3. the Job exceeds a deadline shorter than the 20m29s the scan legitimately took
     (and a 20-minute scan under the slow disks of #265 is plausible).
- Reading 3 would make the alert technically true and still unhelpful, because nothing in
  it distinguishes "the scan did not publish" from "the scan published, the Job overran".
- #280 (closed `completed` 2026-10-04) recorded the same shape as a secondary note
  ("the first alert landed MID-scan, i.e. a duration / not-completed threshold tripping on
  a ~15min scan, not a code failure"). Its headline was a 6-hour outage in which nothing
  scanned at all, which is **not** what happened here, so #280 was not reopened.

## Same window, for context, and NOT part of this report

- The bot pool's own 2026-10-26 scan is mid-sweep, not failed: slots 1 and 2 reached
  `last_scan_date 2026-10-26` (s1 published 22:17:54Z, `0 PASS candidates on 2026-10-26`),
  slot 3 is still on `2026-10-23` with no 2026-10-26 scan line at 22:4xZ. The publish
  deadline is 2026-10-27T00:00Z and is **not breached**, so this is progress on #546, not a
  new defect. Across `/api/admin/traders`, 17 of 27 alpaca slots read 2026-10-26 and 10
  still read 2026-10-23 at 22:5xZ - consistent with a sequential sweep still running.
- `scan_stale` is `false` on every row still reading 2026-10-23, including 3-day-old ones.
  Not filed: the staleness rule appears to be keyed to the 2026-10-27T00:00Z deadline,
  which has not passed. The next run after 00:00Z should re-check whether it flips.
