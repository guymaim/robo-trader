# QA re-verify — 2026-10-05, 07:55–08:10 UTC

Scheduled `reverify` run. Bot account `grok-bot-robo-trader-1@egedsoft.co.il`,
paper only, read-only throughout. Market **CLOSED** (last session 2026-10-02;
next open 2026-10-05 13:30Z). No order placed or cancelled, no Settings value
changed, nothing clicked on Alpaca, no sign-in, no credential entered.
The only clicks on the product were the **Options** disclosure toggle on the
Alpaca trader 1 card (expand only) and page navigation.

Scope: the 14 issues closed since the previous re-verify finished
(2026-10-04T13:05:32Z) and not carrying `verified-by-claude`.

## Verified fixed — label applied

### #268 Status badge said "Pod" on pool traders
Status pages at 08:01–08:05Z. Chip row now reads **`ready / Pool`** and
**`OFF (pool) / Approve buys`** on all three slots:

| page | chip | Approve-buys label | `/api/status/alpaca…` `runtime` |
|---|---|---|---|
| `/status/alpaca`          | ready / Pool | OFF (pool) | `pool` |
| `/status/alpaca?slot=2`   | ready / Pool | OFF (pool) | `pool` |
| `/status/alpaca?slot=3`   | ready / Pool | OFF (pool) | `pool` |

Verify step 2 of the issue ("slot 1 still says Pod") no longer applies:
slot 1's own `runtime` is now `pool`, so `Pool` is the correct badge there —
the chip is being driven by the trader runtime, which is the fix that was asked
for. The lease worker id is not appended to the chip; the issue listed that as
optional ("pool, or the current worker id").
Verify step 4 holds: `/logs/trader-alpaca`, `-s2`, `-s3` all load (HTTP 200)
and the s2 tail is pool-sourced — logger `robo_trader.pool.alpaca-s2-9acb344b89e1`,
latest line 2026-10-05T07:54:01Z.
Verify step 5 holds: kill switch false on all three, rules unchanged
(02 / 04 / 06), `connected: true`, `broker_error` null.
Verify step 3 (badge follows a failover) is not testable read-only.

### #272 Checkboxes had accessible name "on"
`/settings` at 07:58–08:04Z. All 20 checkboxes across the five trader cards now
carry both a wrapping `<label>` and an `aria-label` equal to their visible text;
none reports `"on"`. Accessibility tree for the expanded Alpaca trader 1 Options
panel reads `checkbox "Require approval before buying"` and
`checkbox "Require approval before selling"`.

### #278 Admin "Days running" one day lower
08:00:54Z (`/api/admin/traders`), 08:01:11Z (`/api/status/alpaca…`),
08:01:21Z (home + All-tenants table). All three surfaces now agree:

| trader | start_date | home card | status API | admin API | admin table |
|---|---|---|---|---|---|
| Alpaca trader 1 | 2026-09-20 | 16d live | 16 | 16 | 16 |
| Alpaca trader 2 | 2026-09-22 | 14d live | 14 | 14 | 14 |
| Alpaca trader 3 | 2026-09-22 | 14d live | 14 | 14 | 14 |

### #281 Scan freshness not visible for ibkr_paper
The All-tenants table now has a **Last scan** column, populated for every broker
including the two `ibkr_paper` rows. `/api/admin/traders` gained
`last_scan_date` and `scan_stale`. At 08:01Z both `ibkr_paper` rows read
`2026-10-02` / `scan_stale: false` — 2026-10-02 being the last completed
session, so not stale.

### #283 Admin table rows indistinguishable
The `User` column now renders the masked email **plus a short user id**, e.g.
`g***@egedsoft.co.il (9acb344b)`. The rows that previously collided are now
distinct: two different users share the masked string `g***@egedsoft.co.il` and
both hold an `alpaca #2` trader, and they now read `(9acb344b)` and `(3bb2da95)`.
`/api/admin/traders` returns a `user_ref` field alongside `user_id`.

## Reopened

### #273 Hidden IBKR restart checkbox still in the accessibility tree
Measured on a fresh `/settings` load at 08:04:20Z. One thing changed and one
did not.

Changed: the control is now `disabled` on the three Alpaca cards (and still
enabled on the two IBKR cards, correctly) — so it has left the tab order.

Unchanged: it is still rendered, and still hidden only by zero-sizing —
the same technique the issue identified as insufficient.

| card | labelSize | display | visibility | opacity | aria-hidden | hidden attr | inert | disabled |
|---|---|---|---|---|---|---|---|---|
| IBKR Live       | 0x0 | flex | visible | 1 | no | no | no | **false** |
| IBKR Paper      | 0x0 | flex | visible | 1 | no | no | no | **false** |
| Alpaca trader 1 | 0x0 | flex | visible | 1 | no | no | no | **true** |
| Alpaca trader 2 | 0x0 | flex | visible | 1 | no | no | no | **true** |
| Alpaca trader 3 | 0x0 | flex | visible | 1 | no | no | no | **true** |

A name-based query of the page's accessibility tree with the Alpaca trader 1
Options panel expanded still returns
`checkbox "Also restart the connection to Interactive Brokers"` on all five
cards, the Alpaca ones included. The issue's Expected asks for the control to
leave **both** the accessibility tree and the tab order; half of that is in
place.

## Commented, left closed — not re-testable by this suite

- **#280** scan CronJob `robo-trader-scan-ibkr-paper` failing/hanging. The
  condition lives in the cluster and this run has no cluster or Alertmanager
  access. One read-only datum now exists that did not when the issue was filed
  (via the #281 fix): both `ibkr_paper` traders report
  `last_scan_date 2026-10-02`, `scan_stale: false`, i.e. a scan did complete for
  `ibkr_paper` after the 2026-10-01 21:46Z onward outage window. That is
  consistent with the job recovering but does not establish that the CronJob
  failure is fixed, and the second ask (post resolved notices) is Alertmanager
  config, invisible from the product.
- **#288** `RoboTraderPoolPassStale` silent through a 24-minute no-pass gap.
  The threshold/`for:` reshape is Alertmanager config — not testable. The second
  ask, "surface last-completed-pass time per trader in the product", is not
  visible: the status pages and the admin table expose `Last scan`, not last
  completed pass. Not reopened on that alone, because the primary defect cannot
  be assessed from here either way.
- **#291** `RoboTraderPoolTraderUnleased` fires after recovery; no resolved
  notices. Entirely Slack-receiver and Alertmanager configuration. No worker
  roll has occurred since 2026-10-02 20:54:46Z (s2) / 20:54:40Z (s3), so the
  condition is not provokable read-only, and the channel posts firing
  notifications only — silence there reads equally as fixed or as unchanged.

## Out of scope for re-verify

#252, #256, #257, #258, #259 — `idea-from-competitors` feature ideas closed as a
batch within 8 seconds (2026-10-04T19:13:34–42Z). They carry no defect and no
repro steps, so there is no original report to reproduce. Left untouched and
uncommented; the run watermark moves past them.

## Noted in passing, no action taken

- The home cards show "The trader reported a problem. Open the log for the
  details." on **all three** Alpaca traders at 08:01:21Z, while
  `/logs/trader-alpaca-s2` holds nothing but clean `market closed; sleeping 900s`
  lines back to 2026-10-03T05:48Z. Already filed and open as **#296** (which
  describes it on traders 1 and 2); it is now on all three.
- Status page titles read slot 1 → `(PA3Y)`, slot 2 → `(PA3V)`, slot 3 →
  `(PA3O)`, so the crossed account binding of **#277** is still in force.
  #277 is already open (reopened 2026-10-04); no action.
- `/api/status/alpaca…` returns `title` without the `PA3…` prefix
  (`"alpaca #2 (grok-bot-robo-trader-1@egedsoft.co.il)"`) while the rendered
  page title does carry it. Noted for the reconcile check, not filed.

Other users' rows in the admin table were read only to confirm the #283 and
#281 fixes and are not reproduced here beyond the two masked-email collisions
that are the subject of #283.
