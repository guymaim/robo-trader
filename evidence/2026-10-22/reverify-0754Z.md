# Closed-issue re-verify — 2026-10-22 07:55-08:05Z

Run: `reverify`, started 2026-10-22T07:54:59Z. Read-only. Nothing was placed,
cancelled or confirmed; no sign-in attempted; no Alpaca page visited.

**Outcome: 0 labelled `verified-by-claude`, 0 reopened, 20 commented.**

## Scope

Issues closed in `guymaim/robo-trader` since the previous re-verify finished
(2026-10-21T13:02:38Z), excluding pull requests, none already carrying
`verified-by-claude`:

22 issues — #253 #260 #302 #375 #376 #377 #378 #381 #384 #385 #386 #387 #388
#389 #390 #392 #394 #395 #397 #398 #401 #403

Two are not bug reports and were excluded from re-verification: **#253** and
**#260** are accepted `idea-from-competitors` feature requests, so there is no
original report to reproduce. The remaining **20** were assessed individually.

Cross-check: `issues?state=closed&since=2026-10-21T13:02:38Z&per_page=100&sort=updated`
returned 31 items, of which 9 were PRs (#396 #399 #400 #402 #407 #391 #393 #382
#383) and 22 were issues, all with `closed_at` after the cutoff.

## Live-site state, verified first-hand this run (not inherited)

Browser pane on `macbookpro`, built-in browser, anonymous (`credentials:'omit'`).

Public surface **healthy**, 2026-10-22T07:57:34-07:57:37Z:

| Path | Status | Latency |
|---|---|---|
| `/` | 200 | 292ms |
| `/legal/terms` | 200 | 222ms |
| `/legal/privacy` | 200 | 190ms |
| `/help/alpaca` | 200 | 158ms |
| `/auth/login` | 200 | 173ms |
| `/.well-known/security.txt` | 200 | 139ms |

Zero 5xx, zero timeouts, no Cloudflare error interstitial.

Signed-in surface **dark**. Cookie length 0 at 07:57:25Z. All 8 protected routes
probed returned `opaqueredirect`: `/dashboard` `/runs` `/settings` `/journal`
`/admin` `/rules/compare` `/status/alpaca?slot=1` `/api/status/alpaca?slot=1`.
`/dashboard` followed to `https://robo-trader.egedsoft.co.il/auth/login?next=%2Fdashboard`,
title "Robo Trader — Sign in", at 07:57:48Z. Public home 200, so this is an auth
lapse (dark since 2026-10-15T13:45:25Z, ~8 days), not an outage. **No sign-in was
attempted** — a sign-in page was served, nothing was typed, no password, no 2FA,
no button clicked.

Live build: `/static/js/rule-editor.js` 200, 33,839B, Last-Modified
2026-10-22T06:46:16Z — a deploy fresher than PR #402 (05:37:18Z), consistent with
the #401/#403 closures at 07:02-07:26Z.

No public asset carries the relevant UI text: `/static/js/rules-compare.js`,
`/static/js/compare.js`, `/static/js/pnl-by-symbol.js`, `/static/js/trader.js`,
`/static/js/needs-review.js`, `/static/js/sim.js`, `/static/js/admin.js`,
`/static/js/status.js`, `/static/js/app.js` all 404. `rule-editor.js` searched for
`5 of 6`, `5-of-6`, `ATR`/`atr`, `size limit`, `sizeLimit`, `max_position`,
`falling`, `regime`, `needs review`/`needs_review`, `share of`, `% of total`,
`drawdown`, `equity curve`, `unknown`, `misspell`, `typo`, `unrecognis(z)ed` —
**zero hits** (the one `periods` hit is unrelated over-fitting prose). So none of
the closed items could be corroborated even indirectly from a public bundle.

## Per-issue outcome — none re-testable as written

No issue was reopened. Per the standing rule, an issue is not reopened because a
repro step no longer applies; where it cannot be re-tested, that was said in a
comment and the issue left closed.

**Signed-in UI, session dark (4):**

- **#390** P&L by symbol share column — the table lives on a trader page and a
  simulation result page, both auth-gated. Closed with no comment, so there is no
  stated fix to check either.
- **#395** Compare Rules 5-of-6 wording — `/rules/compare` redirects to sign-in.
  The closing comment claims "the new text is shown" on the live site; that claim
  could not be confirmed or contradicted from outside the session.
- **#401** cleared needs-review flag returns — admin banner on an Alpaca trader
  page; needs a session *and* an unvalued transfer as the newest cash flow, a
  condition the reporter could not confirm exists on any account today.
- **#403** trader page hides later needs-review flags — same page, same gate,
  plus a recorded prior clear.

**No live-site surface at all (16).** Engine, runner, helper, CI and infra code,
most in other repositories, with no route on `robo-trader.egedsoft.co.il` that
exercises them read-only: #375 #376 #377 (robo-trader-engine — regime sizing,
falling-market schema, per-candidate ATR), #381 (rule 31 falling-mode memory;
no trader bound to rule 31), #386 #387 #392 #394 (Compare Rules scoring, behind
the same auth gate and dependent on stored results), #378 #388
(robo-trader-sim-runner), #384 #385 (IB Gateway status helper and its 2FA
escalation messages — IBKR, not the Alpaca bot account), #389 (main-branch test
skip gate, GitHub Actions), #397 #398 (robo-trader-k3s backup storage cleanup
workflow).

**#302 — site-wide outage 2026-10-06, closed as covered by #265.** Not a
re-testable report: reproducing it would mean reproducing an outage. The symptom
is absent right now — the public sweep above is clean and fast, and
`/.well-known/security.txt`, the one path that stayed up during the incident,
answers 200 alongside everything else. But that is *not* evidence of a fix. The
root cause recorded on 2026-10-07 is slow/failing disks under shared storage,
tracked in #265, and the same comment said the issue "cannot be closed by a
software change today". The closure routes it to #265 rather than claiming a fix,
and whether those disks have been replaced is not observable read-only from the
site. So: no `verified-by-claude`, and no reopen — the closure is a valid
duplicate-routing, not a false "fixed".

## Note for the owner

Nothing in this run's scope was verifiable, and that is structural rather than
unlucky: 16 of 20 items are backend/CI/infra findings from CodeRabbit-assisted
code review with no live-site surface, and the other 4 sit behind the signed-in
session, dark for ~8 days. A closed-issue re-verify that reads the live site can
only cover the user-facing subset. Restoring the Robo Trader session would bring
#390 #395 #401 #403 and the Compare Rules group within reach.

## Hard limits held

No order placed, cancelled or confirmed. No sign-in attempted, though a sign-in
page was served. No logout. No Settings or API-key change. Alpaca not visited at
all this run — no account page, no Regenerate, no Delete Account. No browser
storage or cookie token read. Browser tab closed at end of run.
