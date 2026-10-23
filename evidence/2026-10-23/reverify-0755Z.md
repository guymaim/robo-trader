# Re-verify run — 2026-10-23 07:55Z

Check name: `reverify`. Scope cutoff: issues closed after the previous
re-verify finished, 2026-10-22T13:00:39Z. Read-only run; no order placed,
cancelled or confirmed; no sign-in attempted; no logout; no Settings or API
key touched; Alpaca not visited (no issue in scope involves it).

## Scope

24 issues closed since the cutoff, none already carrying `verified-by-claude`
(the `state=closed&since=` listing returned exactly these 24, no pull
requests, every `closed_at` after the cutoff):

#406 #409 #410 #414 #415 #416 #417 #418 #419 #420 #421 #422 #423 #424 #425
#426 #427 #428 #429 #430 #431 #432 #433 #434

## Site state, verified first-hand this run

| Time (UTC) | URL | Result |
|---|---|---|
| 07:57:17 | `/dashboard` | 302 -> `/auth/login?next=%2Fdashboard`, title "Robo Trader — Sign in" |
| 07:57:17 | `/admin` | 302 -> `/auth/login?next=%2Fadmin`, same sign-in page |
| 07:57:18 | `/traders` | 302 -> `/auth/login?next=%2Ftraders`, same sign-in page |
| 07:57:18 | `/` (public) | 200, title "Robo Trader — Systematic momentum trading", healthy |

The signed-in Robo Trader session has been dark since 2026-10-15T13:45Z
(~8 days). The public site is healthy. Nothing was typed on the sign-in page
and no button on it was clicked.

## Outcome

- **0 labelled** `verified-by-claude`
- **0 reopened**
- **24 commented** — each as "not re-testable as written", left closed

Nothing failed a reproduction. Nothing was reopened, because nothing was
observed that contradicts any closure: "could not verify" is not "still
broken" (PLAYBOOK §8, and the prompt's own instruction not to reopen on a
hunch).

## Why none of the 24 could be independently re-verified

All 24 are code-level findings from CodeRabbit-assisted backlog reviews of
**private** repositories — `robo-trader-engine`, `robo-trader-trade-loop`,
`robo-trader-alpaca`. The QA token cannot read any of them (checked this run:
`guymaim/robo-trader-engine`, `egedsoft/robo-trader-engine`,
`guymaim/robo-trader-trade-loop`, `egedsoft/robo-trader-trade-loop` all 404).
The issue bodies themselves say so: "live state not checked", "reading the
code only", "not reproducible today", "live state not applicable".

Four groups:

1. **Offline research scripts** (#428 #429 #430 #431 #432 #433 #434) —
   developer benchmark tools in the engine. No site surface at all, by the
   issues' own statement. Nothing to reproduce from outside.
2. **Trader / engine internals** (#409 #410 #414 #415 #416 #417 #418 #419
   #420 #421 #423 #424 #425 #426 #427) — the only surfaces that would show
   the behaviour (trader status pages, total-alpha charts, Compare Rules
   recomputed days) are signed-in, and the session is dark. The triggers
   themselves are damaged tracking files, malformed settings, and broker 403
   / 5xx / lost-reply conditions, none of which a read-only QA run may
   create.
3. **Admin parity check** (#422) — the empty-trading-day case lives on the
   admin parity form, which is signed-in only; and running a parity check is
   an action, not a read.
4. **#406** — the symptom is negative cash on an Interactive Brokers
   **real-money** trader, visible only on admin/trader pages (dark), and that
   trader is not the bot account, which PLAYBOOK §1 makes read-only. The
   public `RELEASE_NOTES.md` on `main`, entry "2026-10-22 (later)", does
   describe the fix (3 % margin on delayed quotes, later buys sized from cash
   really left, shortfall reported at the open) and matches the closure
   comment — but that is the project's own account of its change, not an
   independent reproduction, so it does not earn `verified-by-claude` under
   PLAYBOOK §8.

For the other 23, `RELEASE_NOTES.md` carries no entry at all: its newest
section is the same "2026-10-22 (later)" one, covering #405, #406 and #411.
Absence there is expected for non-user-facing engine work, and is not
evidence either way.

## What would make these re-testable

- Restoring the signed-in Robo Trader session would open #422 (admin parity
  form with an empty date range), #409/#410 (total-alpha charts), #424
  (Compare Rules recomputed days) and #406 (the IBKR trader's cash) to a
  read-only look.
- Read access for the QA token to `robo-trader-engine` /
  `robo-trader-trade-loop` / `robo-trader-alpaca` would make the remaining
  code-level fixes verifiable by reading the merged change rather than by
  trusting the closure comment.

## Pre-existing, carried not re-raised

- Owed owner reopens #349 #282 #288 (`state/pending-github`).
- `secrets.env` still holds `ALPACA_KEY_` / `ALPACA_SECRET_` pairs against
  PLAYBOOK §3/§5. Not read this run; sourced in a subshell for
  `GH_REPO`/`GH_TOKEN` only, token never printed.
- `state/runs.log` is ~1.0 MB; the owner may want to roll it.
