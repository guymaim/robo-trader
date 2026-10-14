# Re-verify run B — 2026-10-14 06:24–06:42 UTC

**Two `reverify` runs fired concurrently today**, one second apart
(`state/runs.log`: `06:24:13+00:00 reverify start` and
`06:24:14+00:00 reverify start`). Run A wrote
`evidence/20261014-reverify/` and posted its comments and labels at
06:40:40–06:41:26Z. This is run B. The two did not coordinate, so this
directory is kept separate and run B posted only where it had evidence run A
did not.

Read-only, both runs: no order placed, cancelled or confirmed; no Settings
value changed; no credential or 2FA code entered; no sign-in attempted;
nothing clicked on Alpaca. Market CLOSED (next open 2026-10-14T13:30:00Z),
3 / 3 running, `session_today: false`.

## What run B added

### #334 — reopened as VERIFIED FIXED (label added)
Run A concluded "cannot be re-tested as written" after finding `/api/scans*`
404. The fix is observable in the bot account's own trader logs
(`/api/logs/<name>/tail`), 19 minutes after the issue closed:

    trader-alpaca     2026-10-12T17:59:47.998820Z WARNING  scan: buy list of 11 candidates for
                                                           2026-10-09 refused: scan coverage None
                                                           not verified (needs 0.97); reading a
                                                           verified scan instead
    trader-alpaca     2026-10-12T17:59:48.014993Z WARNING  scan: the stored scan of
                                                           rules/02-aggressive-mid-hold-quick-profit.json
                                                           for 2026-10-09 has coverage None
                                                           (needs 0.97); not using it, requested
                                                           a fresh scan
    trader-alpaca-s2  2026-10-12T17:59:55.558931Z WARNING  … 32 candidates for 2026-10-09 refused …
    trader-alpaca-s3  2026-10-12T18:00:02.765181Z WARNING  … 56 candidates for 2026-10-09 refused …
    trader-alpaca     2026-10-12T18:21:11.413333Z INFO     scan: 69 PASS candidates on 2026-10-09
                                                           (top 10: ['MRNA','IOVA','OKTA', …])

Trader 1's refused list is 11 candidates for 2026-10-09 — the count and date
this issue reports. Re-scan yielded 69, against the ~69 the issue says
complete data gives. No buy from the refused list on any of the three; newest
buy remains TEAM 2026-10-09T19:57:33Z. Later scans: 67 (10-12), 70 (10-13).

Not exercised: the write-side guard ("a result below the threshold is never
stored or shared") — no sub-threshold scan has happened since the fix.

### #335 — two more "Done when" boxes confirmed (comment only, still no label)
- `/new` pre-selects `rules/04-balanced-long-hold.json`; `/rules/compare`
  sorted by Avg rank ascending has **04. Balanced long hold (6.00)** top
  (then 09 at 7.00, 01 at 7.40). Simulator default = page's best rule.
- Existing traders unchanged: 02 / 04 / 06 on traders 1 / 2 / 3, matching
  2026-10-06, read from the home cards, `/api/admin/traders.rules_name` and
  each log's `trader started: rules=…`.
- `syncEnableConfirm()` keeps `#er_confirm` disabled until an `er_rule` radio
  is picked (library radio counts only once its `<select>` has a value).

Still unverified end-to-end: the Enable click and the new-trader default —
both need a state change. Noted: the dialog's 30/60/90-**day** selector reads
against a `/rules/compare` that offers 1/3/6/12/18/24-**month** columns only.

## Run B agreed with run A, no second comment posted

#277 not reopened — status-page titles at 06:28:59Z: slot 1 `(PA3Y)`,
slot 2 `(PA3V)`, slot 3 `(PA3O)`, i.e. the pairing the owner declared intended
on 2026-10-07, unchanged. **PLAYBOOK.md §4 and §6 still carry the
pre-rotation expectation (t1 -> PA3V, t2 -> PA3Y); until they are updated the
next reconcile will raise a false "traders swapped" finding.** Run B did not
edit PLAYBOOK.md — an owner decision recorded in a GitHub comment is not
authority for this check to rewrite the owner's playbook. Flagged to the owner.

#295 #297 #303 #304 #305 #306 #311 #323 #338 verified fixed — run B reached
the same conclusion independently on each. Additional detail not worth a
second comment:
- #305 a *fresh load* of `/admin#traders` does select the Traders tab, and
  ArrowLeft moves selection, focus and hash. But an **in-page hash change is
  not watched**: going from `/admin#signups` to `/admin#traders` without a
  reload leaves Sign-ups selected. Minor; the issue's acceptance ("a link or a
  page reload opens the same tab") holds.
- #303 there is **no `beforeunload` handler**, so the "leaving the page with
  unsaved changes shows a warning" item is not implemented. The issue marks it
  "(nice to have)", so it does not block the fix.
- #304 Report emails rows measured: switch centre vs first text line differ by
  **0.3 px** on all three rows, switch left 0 / text left 58 on all three,
  row gap 12 px / 12 px. `::after` renders "ON" / "OFF", so state is not
  colour-only. Caveat: the switches carry `role="switch"` but **no
  `aria-checked`**; Chrome maps the native `checked` state, so it announces,
  but the attribute is formally required for the role.
- #311 per-slot cache `rt.status.v1|<user>|/api/status|alpaca|<slot>|ILS`
  populated at 06:31:47Z; code paths `setConnBadge('updating…', …,
  'Showing the last data. ' + e.message + ' Retrying.')` and
  `'Showing data from ' + agoText(…) + ' while the latest loads.'`, plus a
  dedicated `signed out` badge.

## Not re-testable, agreed with run A, no second comment
#296 (run A's test was better than run B's — it caught slot 3's `fault` being
SET at 06:33:44Z after a fresh ERROR while null at 06:31:47Z with a 14h32m-old
one; run B could not separate "clears on recovery" from "clears on restart"
because every error episode it saw was followed by a `trader started`),
#298, #320, #324.

Out of scope, untouched: #208 #231 #233 #234 #235 #237 #244 #245 #248 #254.

## Carried forward, no action taken
- `state/pending-github/20261006-reopen-288-comment.md` is a reopen for **#288**
  drafted by the 2026-10-06 soak run and **never posted** (GitHub was
  unreachable that evening). #288 closed 2026-10-04, i.e. outside this run's
  window, and the 2026-10-05 re-verify already adjudicated it "not re-testable
  as written". Run B did not post 8-day-old stall evidence blind. Left closed,
  queue file left in place, flagged to the owner.
- Alpaca dashboard redirected to `/account/login` at 06:28Z (run A). No
  sign-in attempted, per playbook §2 / §7, so no broker-side corroboration
  this run.
- `secrets.env` contains `ALPACA_KEY_1..3` / `ALPACA_SECRET_1..3`, while
  PLAYBOOK.md §3 states "There are **no Alpaca credentials**, by the owner's
  decision." Keys were not read or used. Flagged to the owner as a
  playbook/filesystem disagreement.
- Traders restarted repeatedly around `cred-broker unavailable` errors
  (trader 1: `ERROR in loop` 2026-10-13 15:57/15:58, 17:05/17:06, 17:56/17:57,
  18:57/18:58 and 2026-10-14 06:31:38/06:31:59, each followed by
  `trader started`). Left to the soak / senior-qa checks, which also ran today.
