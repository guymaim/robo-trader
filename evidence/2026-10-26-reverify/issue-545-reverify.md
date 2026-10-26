# Re-verify #545 — per-trader status API 504s — VERIFIED FIXED

Scheduled QA `reverify` run, 2026-10-26. Read-only live pass, signed in as the QA bot
(`grok-bot-robo-trader-1@egedsoft.co.il`), browser pane, same-origin credentialed `fetch`
from `https://robo-trader.egedsoft.co.il`.

**US market OPEN** throughout (`/api/home/traders` → `"is_open":true`, next close
2026-10-26T20:00:00Z). This is a stronger condition than the two earlier post-fix spot
checks, which were both taken while the market was closed.

## Original failure (from the issue, 2026-10-26 10:46–10:56Z)

`GET /api/status/alpaca?slot=N` → HTML **504 Gateway Timeout** after 12.3–15.1 s.
Seven 504s and four slow 200s across ten minutes. `/api/home/traders` degraded to
~8.27 s. Credential-free endpoints were untouched in the same seconds (`/` 78 ms,
`/api/logs/.../tail` 95–109 ms) — the split that defined the defect.

## This run — 21 probes, 14:02:50Z → 14:06:31Z

| time (UTC) | request | status | content-type | ms |
|---|---|---|---|---|
| 14:02:51 | `/api/status/alpaca?slot=1` | 200 | application/json | 1758 |
| 14:02:52 | `/api/status/alpaca?slot=2` | 200 | application/json | 985 |
| 14:02:53 | `/api/status/alpaca?slot=3` | 200 | application/json | 278 |
| 14:02:53 | `/api/home/traders` | 200 | application/json | 176 |
| 14:04:13 | `/api/status/alpaca?slot=1` | 200 | application/json | 221 |
| 14:04:14 | `/api/status/alpaca?slot=1` | 200 | application/json | 201 |
| 14:04:14 | `/api/status/alpaca?slot=2` | 200 | application/json | 200 |
| 14:04:14 | `/api/status/alpaca?slot=3` | 200 | application/json | 191 |
| 14:04:14 | `/api/home/traders` | 200 | application/json | 194 |
| 14:04:14 | `/api/logs/trader-alpaca-s2/tail` | 200 | application/json | 75 |
| 14:04:14 | `/` | 200 | text/html | 40 |
| 14:05:02 | `/api/status/alpaca?slot=1` | 200 | application/json | 981 |
| 14:05:02 | `/api/status/alpaca?slot=2` | 200 | application/json | 193 |
| 14:05:02 | `/api/status/alpaca?slot=3` | 200 | application/json | 190 |
| 14:05:02 | `/api/home/traders` | 200 | application/json | 142 |
| 14:05:34 | `/api/status/alpaca?slot=1` | 200 | application/json | **2487** |
| 14:05:34 | `/api/status/alpaca?slot=1` | 200 | application/json | 199 |
| 14:05:35 | `/api/status/alpaca?slot=2` | 200 | application/json | 949 |
| 14:05:36 | `/api/status/alpaca?slot=3` | 200 | application/json | 918 |
| 14:05:36 | `/api/home/traders` | 200 | application/json | 185 |
| 14:05:36 | `/api/home/traders` | 200 | application/json | 201 |
| 14:06:31 | `/api/status/alpaca?slot=1` | 200 | application/json | 1039 |
| 14:06:31 | `/api/status/alpaca?slot=2` | 200 | application/json | 196 |
| 14:06:31 | `/api/status/alpaca?slot=3` | 200 | application/json | 212 |
| 14:06:31 | `/api/home/traders` | 200 | application/json | 196 |

**Every response 200 / `application/json` / `redirected:false`. Zero 504s, zero HTML
bodies from a JSON endpoint, zero aborts.** Slowest observed: **2487 ms**, i.e. ~6x
inside the 15 s gateway cutoff and ~5x faster than the fastest failing call in the issue.

The HTML status pages themselves also answer normally — `/status/alpaca?slot=1/2/3` →
200 `text/html`, 164,520 / 164,545 / 164,545 B in 124 / 74 / 92 ms at 14:04:38Z, none
containing a "gateway timeout" string.

The credential-free vs credential-dependent split that defined the bug is gone: at
14:04:14Z the credential-free endpoints answered in 40–75 ms and the
credential/price-dependent ones in 191–221 ms — a ~150 ms gap, against instant-vs-12-15 s
during the failure.

## What this run does NOT establish

The outside price service was answering normally throughout, so the **fallback path was
never exercised**: `/api/status/alpaca?slot=1` at 14:04:26Z carried live outside figures
(`qqq_pct 0.31`, `spy_pct 0.42`, `fx.rate 3.082350015640259`, populated `sectors`), and
the rendered status page shows QQQ/SPY today figures rather than a cached-fallback state.
So the shared-time-limit-and-skip behaviour described in the fix is verified only by the
absence of timeouts, not by observing a degraded lookup fall back.

The issue's second "Expected" bullet — credential-broker unavailability surfacing as a
visible degraded state — is not tested and is no longer applicable as written: the owner's
triage established the credential service was healthy and not the cause, correcting the
probable-cause section of the original report (which this QA run wrote).

## Scope note

`connected:true`, `broker_status_unknown:false`, `broker_error:null` on all three — i.e.
the health signals the issue complained were misleadingly green are green again, but this
time they agree with a status API that actually works.

## Playbook compliance

No order placed, cancelled or confirmed. No sign-in, no sign-out. No Settings or API-key
change. Alpaca dashboard not opened; `paper-api.alpaca.markets` not called. No browser
storage or token read. Browser pane tabId `seed`, `robo-trader.egedsoft.co.il` origin only.
