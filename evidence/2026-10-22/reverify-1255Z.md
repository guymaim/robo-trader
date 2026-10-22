# Closed-issue re-verify — 2026-10-22 12:55Z

Run: `reverify`, started 2026-10-22T12:55:17Z. Device `macbookpro` up, browser
pane reachable. Read-only throughout.

## Scope

Issues closed in `guymaim/robo-trader` since the previous re-verify finished
(2026-10-22T08:02:18Z) and not already carrying `verified-by-claude`:

- **#404** — "Plan: buy at the official opening price with opening-auction orders (#371) — review and execute", closed 09:02:54Z, `state_reason: not_planned`, labels `enhancement` / `plan-to-execute`
- **#411** — "Adaptive rules could pick a different rule depending on one stock's price history", closed 09:27:59Z, `state_reason: completed`, no labels

Cross-check: `GET /issues?state=closed&since=2026-10-22T08:02:18Z` returned 3
items; 1 PR (#412) excluded; both remaining issues have `closed_at` after the
cutoff.

## Outcome

- 0 labelled `verified-by-claude`
- 0 reopened
- 1 commented (#411)
- #404 excluded from re-verify (not a defect report — see below)

## Site state, verified first-hand this run

The signed-in Robo Trader session is still dark (dark since 2026-10-15T13:45:25Z
per earlier runs; re-confirmed here, not inherited):

| Time (UTC) | Request | Result |
|---|---|---|
| 12:57 | `/dashboard` | 302 → `/auth/login?next=%2Fdashboard`, title "Robo Trader — Sign in" |
| 12:58 | `/rules/compare` | 302 → `/auth/login?next=%2Frules%2Fcompare` |
| 12:58 | `/journal` | 302 → `/auth/login?next=%2Fjournal` |
| 12:58 | `/` (public) | 200, title "Robo Trader — Systematic momentum trading" |

Public site healthy; only the authenticated surfaces are unreachable. No
sign-in was attempted (PLAYBOOK §2, §7): nothing typed, no password, no 2FA,
no button clicked on the sign-in page. Alpaca was not visited — no issue in
scope involves it.

## #404 — excluded, left closed, not commented

#404 is not a defect report. It is the owner's own plan-review issue for #371,
closed `not_planned` with the owner's decision comment of 09:02:53Z ("Not now
… the plan stays as written for later"), on the grounds that the Alpaca
traders are all paper accounts and paper does not fill on-open orders at the
auction price. The issue body itself states nothing was built, deployed or
ordered. There is therefore no original symptom to reproduce and no fix to
confirm. Same treatment as #253 and #260 in the 07:54Z run: an accepted or
deferred plan/feature issue is out of re-verify scope rather than a failed
repro. No label, no reopen, no comment — commenting would add noise to the
owner's own decision thread.

## #411 — not re-testable as written, left closed, commented

Original report: market breadth (an input to the learned model that adaptive
rules 01, 05, 06, 08, 11, 14, 15, 19 and 22 use to pick a daily strategy) was
computed on the trading calendar of whichever single stock had the longest
price history. A stock with gaps in that slot blanked breadth market-wide for
2026-10-19 and 10-20, so a morning re-check on a re-downloaded cache chose
"sit out" for days whose evening buy lists had been computed correctly, and
the daily scan checks reported a mismatch.

Every surface the report names is behind the signed-in area or outside the QA
token's reach:

- the daily scan mismatch report, the evening buy lists and the re-check run — signed-in only; session dark (table above)
- Compare Rules recomputed days (the closure comment says Compare Rules is recomputing for the affected rules) — `/rules/compare`, signed-in only, and additionally dependent on stored results
- the fix itself — `robo-trader-engine` (stated in the issue body as the internal ref); `guymaim/robo-trader-engine` and `egedsoft/robo-trader-engine` both return 404 to the QA token

What could be read: `RELEASE_NOTES.md` on `main` carries a 2026-10-22 (later)
entry for #411 describing exactly the fix claimed in the closure comment
(breadth now uses every trading day; days with complete data unchanged; Oct 20
and Oct 21 lists were correct). That is the developers' own account of the
change, not an independent reproduction, so it does not earn
`verified-by-claude` under PLAYBOOK §8.

No reopen: nothing failed a repro. The symptom was a mismatch on two specific
past dates (10-19, 10-20) under a since-replaced cache state, and nothing
observed this run contradicts the closure. Unverifiable is not broken.

Restoring the Robo Trader session would bring #411 within reach — the scan
mismatch history and the Compare Rules recompute are both visible to a
signed-in reader.

## Hard limits held

No order placed, cancelled or confirmed. No sign-in attempted, though a
Robo Trader sign-in page was served. No logout. No Settings or API-key change.
Alpaca not visited; Regenerate / Delete Account never on screen. No browser
storage or cookie token read. `secrets.env` sourced in a subshell for
`GH_REPO` / `GH_TOKEN` only; the token was never printed.

## Note for the owner (carried, not re-pushed)

`secrets.env` still holds `ALPACA_KEY_*` / `ALPACA_SECRET_*` pairs, against
PLAYBOOK §3 and §5 ("There are no Alpaca credentials, by the owner's
decision"). Not read, not used. Also still outstanding from earlier runs:
the owed reopens in `state/pending-github` (#349, #282, #288), and
`state/runs.log` is now ~1.0 MB and may want rolling.
