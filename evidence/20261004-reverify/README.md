# reverify run — 2026-10-04 12:55–13:05 UTC

First `reverify` run; no prior reverify line existed in `state/runs.log`, so there
was no watermark. Scope taken as **the batch of issues closed on 2026-10-04**
(#277, #274, #262, #279, #282, #206 — all closed 12:00:25–12:02:34Z, i.e. about
an hour before this run) and not the September backlog, which carries the
predecessor `verify-by-grok-bot` label and was already handled by that bot.
#290 was skipped: it was a duplicate this suite filed in error on 2026-10-02 and
closed as `not_planned` the same minute, so there is no fix to re-test.

Market CLOSED (closed 2026-10-02 20:00Z, next open 2026-10-05 13:30Z) — Sunday,
expected per playbook section 1. Read-only throughout: no order placed or
cancelled, no Settings or credential change, nothing clicked on the Alpaca
dashboard, no sign-in attempted. Robo Trader and Alpaca sessions both alive
(dashboard, not login; no accounts.google.com redirect).

| Issue | Verdict | Action |
|---|---|---|
| #277 account binding crossed | **STILL BROKEN** | reopened + comment + evidence file |
| #274 Restart does not respawn worker | not re-testable (repro needs key rotation + Settings + Restart click — all forbidden) | comment, left closed |
| #262 saving keys does not restart | not re-testable (same) | comment, left closed |
| #279 stale scan with healthy badges | not re-testable today (market closed; scan current for the last session) | comment, left closed |
| #282 Velero backup gap | not verifiable from the product, no cluster access | comment, left closed |
| #206 Goldilocks resources | side security finding FIXED (401 basic auth on all 4 paths, anonymous); main resource work needs kubectl | comment, left closed, **no** verify label |

No `verified-by-claude` label applied this run. #206 was the only candidate and
only its P2 side finding could be tested; the primary ask was not verifiable.

Detail on #277: `evidence/20261004-reverify/277-binding-still-crossed.md`

## Goldilocks auth check (#206 side finding)

Browser fetch with `credentials: 'omit'`, 2026-10-04 13:00Z:

    /                        401 basic  (58-byte body)
    /namespaces              401 basic
    /dashboard/robo-trader   401 basic
    /api/robo-trader         401 basic

Body: `User authentication failed. Missing username and password.`
Previously 200 and public. Note this also removes the one product-level read
path into current requests/limits, so the resource half of #206 can now only be
verified with `kubectl` or from the deploy repos.

## App state at run time (unchanged from the 11:49Z slack-watch run)

3/3 running + connected, `broker_error` null, kill switches off, daily-loss halt
ok, 0 open orders, cash 0.05/0.05/0.01, equity 10867.55 / 10878.76 / 10910.59
(combined 32656.90), 16 positions 5/5/6, `scan 2026-10-02` on all three (correct
— 10-02 was the last session), days live 15/13/13, 24 admin rows all running.
Slot logs clean: unbroken 900s closed-market sleeps to 12:52:34 / 12:50:51Z,
no non-INFO line since the 2026-10-03 11:55–11:56Z Alpaca 500 burst already
recorded against #288.

## Notes for the next reverify run

- Watermark: use `2026-10-04T13:05Z`; next run should pick up issues closed after
  that and not carrying `verified-by-claude`.
- `javascript_tool` WORKED this run (contrast the 2026-10-04 11:49Z slack-watch
  note that it was dead): same-origin fetches and DOM reads both returned real
  values.
- `/api/logs/<name>/tail?lines=N` returns `{ok, lines:[json-per-line]}` — parse
  each element as JSON (`ts`/`level`/`msg`). `lines=1500` still returns 500, so
  500 appears to be the real server cap on this endpoint.
- Slot status pages are `/status/alpaca`, `/status/alpaca?slot=2`,
  `/status/alpaca?slot=3`; the `PA3…` prefix is in the rendered page title and
  heading, not in `/api/status/alpaca` JSON.
