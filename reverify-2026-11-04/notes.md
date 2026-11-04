# Closed-issue re-verify — 2026-11-04

Scheduled QA check `reverify`. Read-only against the live site, bot account
`grok-bot-robo-trader-1@egedsoft.co.il` (role `light_admin`), via the signed-in
browser pane. All times UTC. Market CLOSED throughout (pre-open; `is_open false`,
`next_open 2026-11-04T14:30:00Z`, `next_close 21:00:00Z`).

Robo Trader session LIVE at 08:56:51Z — `GET /` 200, `<h1>Your traders</h1>`,
no `accounts.google.com` redirect.

## Scope

Window: issues closed since the previous `reverify` run's START, 2026-11-03T13:55:02Z
(that run finished 14:03:18Z), excluding any already carrying `verified-by-claude`.

Confirmed two ways at 08:5xZ:
- search API `is:issue is:closed closed:>2026-11-03T13:55:02Z` → `total_count 4`,
  items **{568, 567, 564, 550}**.
- non-search `/issues?state=closed&sort=updated` pages 1–2 (200 items, 180 issues) →
  exactly `{550, 564, 567, 568}` in window; oldest `updated_at` seen 2026-10-18T03:45:35Z,
  so nothing in scope fell off the page.
- None of the four carried `verified-by-claude`. PAT sane: `GET /repos` 200,
  `open_issues_count` 48 (down from 50 at the 2026-11-03 run).

## Verdicts

| Issue | Verdict | Action |
|---|---|---|
| #550 `/rules/compare` 500 | **Fixed** | labelled `verified-by-claude` + comment |
| #564 IBKR cash-flow false full-balance withdrawal | **Fixed** | labelled `verified-by-claude` + comment |
| #567 market badge reads open on a holiday | **Not re-testable by this account** | comment, left closed, NOT labelled |
| #568 RuleOptimizerStalled | n/a — retracted misfiling | no action |

Nothing reopened. Nothing filed.

---

## #550 — `/rules/compare` returned HTTP 500

Reproduction re-run exactly as written, 08:56:58–08:57:05Z.

```
GET /rules/compare?qa=1793782618841-0  -> 200  text/html; charset=utf-8  203713 bytes
GET /rules/compare?qa=1793782619580-1  -> 200  text/html; charset=utf-8  203713 bytes
GET /rules/compare?qa=1793782620061-2  -> 200  text/html; charset=utf-8  203713 bytes
HEAD /rules/compare                    -> 200
GET /rules/compare?scope=all           -> 200  203713 bytes
GET /rules/compare?a=01-adaptive-learned-modes-cash-invested.json&b=02-aggressive-mid-hold-quick-profit.json
                                       -> 200  203713 bytes
GET /rules/compare?ids=                -> 200  203713 bytes
```

All seven carry `<title>Rules Comparison — Robo Trader</title>`, `<!DOCTYPE html>`,
`<html lang="en" dir="ltr" data-theme="dark">`, 4 `<table>`, 129 `<tr>`.

Security headers present on every response (their absence was the secondary defect):
`content-security-policy`, `strict-transport-security`, `x-frame-options: DENY`,
`x-content-type-options: nosniff`.

Nav: the only `Rules` anchor in the left-hand navigation has `href="/rules/compare"`.

Rendered in the pane at 08:59:47Z: full site shell, "Rules Comparison" heading,
Comparison day picker, "Best rule per period" and Ranking sections populated with
real rule data. Not a 200-wrapped error page.

## #564 — failed IBKR account refresh reported a withdrawal of the whole cash balance

Read from `/api/logs/<name>/tail?lines=2000` at 08:57:31Z, all four IBKR traders.
Log names taken off the `/logs` page hrefs:

- `tenant-29ec8f33-c750-405a-8060-aa679a6bdb1a-ibkr_live`   (LIVE money)
- `tenant-29ec8f33-c750-405a-8060-aa679a6bdb1a-ibkr_paper`
- `tenant-a2fd764b-af76-4ff5-b0f6-b97e50bbfc5d-ibkr_live`   (LIVE money)
- `tenant-a2fd764b-af76-4ff5-b0f6-b97e50bbfc5d-ibkr_paper`

The trigger condition in the report — the nightly `ConnectionError('Socket disconnect')`
account refresh failure — has recurred 8 times since the fix (Nov 3 and Nov 4 on all four
traders). All 8 took the held branch:

```
29ec8f33/ibkr_live
[2026-11-02 05:10:18] [ibkr] account refresh failed (ConnectionError('Socket disconnect')); using cached account values
[2026-11-02 05:10:19] cash flow: unexplained USD cash delta $-264.27 — confirming on next poll     <- LAST FALSE LINE
[2026-11-03 05:00:16] [ibkr] account refresh failed (ConnectionError('Socket disconnect')); using cached account values
[2026-11-03 05:00:16] cash flow: account cash could not be read; cash-flow check held until it can
[2026-11-04 05:00:18] [ibkr] account refresh failed (ConnectionError('Socket disconnect')); using cached account values
[2026-11-04 05:00:18] cash flow: account cash could not be read; cash-flow check held until it can

29ec8f33/ibkr_paper   last false 2026-11-02 05:10:39 ($-6,234.89); held 11-03 05:00:17, 11-04 05:00:13
a2fd764b/ibkr_live    last false 2026-11-02 05:08:20 ($-3,647.91); held 11-03 05:01:32, 11-04 05:01:28
a2fd764b/ibkr_paper   last false 2026-11-02 05:10:33 ($-4,235.88); held 11-03 05:00:49, 11-04 05:00:37
```

Zero `unexplained USD cash delta` lines dated after 2026-11-02 anywhere in the four
2,000-line tails. The refresh failure itself is unchanged and still nightly, so the check
is reaching the same branch on the same trigger and taking the new behaviour — this is a
positive test, not a quiet window.

`/api/admin/traders` at 08:58Z: `withdrawals_usd` 0 on all four; `deposits_usd / flow_count`
3,276.78 / 2 (`29ec8f33` live), 4,900.11 / 1 (`a2fd764b` live), 0 / 0 on both paper —
unchanged from the issue. No phantom withdrawal persisted.

Sibling #543 path still holding: `cash flow: executions unavailable; cash-flow check held
until they can be read (since 2026-11-01)`, last 2026-11-01 14:22:41Z.

Carried, not a reopen: `account refresh failed (TimeoutError())` failures (2026-11-03
05:15:22Z, 2026-11-03 20:50:42Z, 2026-11-04 05:15:23Z on `29ec8f33/ibkr_live`) emit no
cash-flow line at all, held or otherwise — a different point in the poll cycle. The held
line is evidenced only on the socket-disconnect path.

## #567 — market badge can read open on a holiday

Not re-testable by this account as written; left closed and unlabelled.

1. No IBKR page to read a badge on. Both IBKR slots unconfigured on the bot account
   (Home: "IBKR Paper — not set up", "IBKR Live — not set up").
   `/status/ibkr` -> 200 but renders `ibkr_paper — … not configured`;
   `/api/status/ibkr` -> `404 {"ok":false,"error":"unknown broker"}`;
   `/status/ibkr_live` and `/status/ibkr_paper` -> 404.
   The chip is filled client-side from `data.market.is_open`
   (`setText('st_market', mkt.is_open ? 'OPEN' : 'CLOSED')`), so with the endpoint 404-ing
   the badge stays at its placeholder `—`. Verified at 08:59:22Z: `badge_st_market: "—"`.
   The four configured IBKR traders belong to other users — read-only per PLAYBOOK 1.
2. No date in range exercises the defect. 2026-11-04 is an ordinary session where a
   weekday clock and the real calendar agree. No endpoint evaluates the calendar for
   another date: `/api/market/calendar`, `/api/market/status`, `/api/market/clock`,
   `/api/market/hours`, `/api/calendar`, `/api/market` all 404, with and without `?date=`.

Corroboration only, not a test: the market block is served identically from
`/api/home/traders`, `/api/status/alpaca` and `/api/status/alpaca?slot=2` at 08:59:0xZ
(`is_open false`, `next_open 2026-11-04T14:30:00Z`, `next_close 2026-11-04T21:00:00Z`),
and the badge renders that server value rather than computing one — so no second,
page-local weekday clock remains on any surface this account can see.

DECISIVE TEST, for a run on those dates: 2026-11-26 (Thanksgiving) the badge should read
closed, next open Nov 27; 2026-11-27 it should read closed after the 1:00 pm ET early
close, next open Nov 30. Needs an account with a configured IBKR trader.

## #568 — RuleOptimizerStalled

No action, deliberately. Opened 08:01:54Z and closed 08:02:50Z the same morning by the
daily senior-QA run itself, as a misfiling: a stale `/tmp` payload from the 2026-10-19
Slack alert watch was picked up, so the body is a verbatim duplicate of #369 and describes
nothing observed on 2026-11-04. There is no live-site reproduction to run — the report was
retracted by its own author, not fixed — so `verified-by-claude` does not apply, there is
nothing to reopen, and a further comment would only add noise to an issue whose closing
comment already says all of this.

## Playbook compliance

No order placed, cancelled or confirmed. No sign-in attempted. Nothing typed into any
field. No password or 2FA entered. Never logged out. No Settings or API-key change.
Alpaca never opened, so Regenerate / Delete Account were never reachable. No cookie,
storage or session-token read. All Robo Trader reads were same-origin credentialed GETs.
Writes this run: 3 GitHub comments, 2 labels, 1 evidence commit. No issue state changed.

## Carried forward

- Alpaca browser session still lapsed (dark since 2026-10-31T15:55Z, ~89h at run start).
  Not touched by this check; `reconcile` owns it.
- `ALPACA_KEY_1/2/3` + `ALPACA_SECRET_1/2/3` still present in `secrets.env` against
  PLAYBOOK 3's "no Alpaca credentials, by the owner's decision". Not read into use.
  Carried, not re-raised.
- Backlog: ~160 closed issues lack `verified-by-claude` if you go back to 2026-10-20,
  almost all coderabbit code-review items with no live-site repro path. Deliberately out
  of scope; sweeping it is a different and much larger job.
