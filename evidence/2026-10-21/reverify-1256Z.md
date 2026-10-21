# Closed-issue re-verify — 2026-10-21 12:56–13:01 UTC

Run: `reverify`, scheduled. Browser pane on "macbookpro", read-only, anonymous
(`credentials:'omit'`) throughout. Robo Trader public home answered 200, so the
site is up; the signed-in session is lapsed (see below).

## Scope

Issues closed in `guymaim/robo-trader` since the previous reverify finished
(2026-10-21T08:03:32Z) and not already carrying `verified-by-claude`:

| Issue | Closed | Outcome this run |
|---|---|---|
| #374 | 10-21T10:41:21Z | **Verified fixed** — labelled `verified-by-claude` |
| #379 | 10-21T10:56:52Z | Not re-testable (needs a signed-in session) — left closed |
| #380 | 10-21T10:18:30Z | Not re-testable (subject repo unreachable) — left closed |

## Session state (first-hand, this run)

- `document.cookie` length 0 at 12:57:48Z.
- `GET /dashboard` (credentials included, `redirect:'manual'`) →
  `opaqueredirect`; following it landed on
  `/auth/login?next=%2Fdashboard`, title "Robo Trader — Sign in".
- Public home `/` → 200, title "Robo Trader — Systematic momentum trading".

So this is an auth lapse, not an outage. No sign-in attempted: nothing typed,
no password, no 2FA, no button clicked on the sign-in page.

## #374 — anonymous route-enumeration oracle

Original report: after the #367 fix, a signed-out visitor could tell an
existing protected route (302) from a nonexistent one (404), mapping the
private surface including `/admin`, `/admin/parity` and `/api/admin/*`.

Re-probed the exact two path sets from the issue body, 43 paths, anonymously
at 12:58Z:

- All 22 paths that previously answered **302** → opaque redirect to
  `/auth/login?next=<path>`.
- All 21 paths that previously answered **404** → the *same* opaque redirect
  to `/auth/login?next=<path>`.

Public sections still serve the #367 page, so that improvement is kept:

- `/legal/nonexistent`, `/help/nonexistent`, `/guides/nope` → 404,
  title "Page not found — Robo Trader", body 7635 B.
- `/legal/terms` → 200, "Terms of Use — Robo Trader".

### No residual oracle in the response body

Raw body lengths differ only by the echoed `next=` parameter, so equal-length
paths were compared directly (12:59:29Z):

| Existing route | Unknown route | Raw len | Normalised len |
|---|---|---|---|
| `/admin` | `/abcde` | 10949 / 10949 | 10943 / 10943 |
| `/metrics` | `/qrstuvw` | 10953 / 10953 | 10943 / 10943 |
| `/api/runs` | `/api/zzzz` | 10955 / 10955 | 10943 / 10943 |
| `/admin/parity` | `/admin/zzzzzz` | 10963 / 10963 | 10943 / 10943 |
| `/api/admin/users` | `/api/admin/zzzzz` | 10969 / 10969 | 10943 / 10943 |

(normalised = request path replaced by a fixed token)

Byte-level hashes still differed, so two fetches of the *same* route were
compared (12:59:42Z): they differ too, at index 514, in the CSP
`<script nonce="...">` value — `6l9x_OolBw0aOZ7j5eZSGg` vs
`TQ58c0jpkzWH7-c1roCXxw`, same length, per request. That accounts for the
whole residual difference, so existing and nonexistent protected routes are
indistinguishable to a signed-out visitor by status, redirect target or body.

Corroborating deploy evidence: `/static/js/rule-editor.js` answers 200
anonymously with `Last-Modified: Wed, 21 Oct 2026 11:32:47 GMT`, consistent
with the "live since ~11:30 UTC" claim in the fix comment.

**Conclusion: genuinely fixed.**

## #379 — Compare Rules "(old model)" labels

The fix comment's claim is a signed-in observation: "0 of the 70 saved days
show (old model)", none queued to recompute, and a stale comparison stopping
itself within 3 hours. `/rules/compare` is auth-gated and the session is
lapsed, so the saved-day labels and the recompute queue were not observed this
run. No public asset exposes the compare page's state: `/static/js/compare.js`,
`/static/js/rule-compare.js`, `/static/js/rules-compare.js` and
`/static/js/compare-rules.js` all 404. The 3-hour self-stop is also a
multi-hour behaviour that a single read-only pass cannot establish.

Not re-testable as written → commented, left closed. No reopen.

## #380 — committed `__pycache__` files

The issue concerns a *private deployment repo* (internal ref
`robo-trader-k3s#758`), not `guymaim/robo-trader`, and does not touch the site.
`GET /repos/guymaim/robo-trader-k3s` with the QA token → **404** (absent or
outside the token's scope), so the claim "no cache files are left on main"
cannot be checked. For completeness, a code search for `extension:pyc` within
`guymaim/robo-trader` returned 0 results, which is consistent with the fix but
is not the repo the issue is about.

Not re-testable as written → commented, left closed. No reopen.

## Hard limits

No order placed, cancelled or confirmed. No sign-in attempted, no credentials
or 2FA entered, nothing typed into any form. No logout. No Settings or API-key
change. Alpaca not visited this run, so Regenerate / Delete Account never on
screen. No browser-storage or cookie token read. Evidence committed to
`qa-evidence` only.
