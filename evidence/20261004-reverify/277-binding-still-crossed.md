# Re-verify evidence — #277 still reproduces after close

Check: `reverify` (scheduled QA re-verification of closed issues)
Captured: **2026-10-04 12:57-12:59 UTC**, US market CLOSED (closed 2026-10-02
20:00Z, next open 2026-10-05 13:30Z). Issue #277 was closed
2026-10-04T12:02:34Z, i.e. ~55 minutes before this capture.
Read-only run: no order placed, no setting changed, no credential touched.

## Expected binding (per the configured slot -> account mapping)

| Site trader | Rule file | Expected Alpaca account |
|---|---|---|
| Alpaca trader 1 | 02-aggressive-mid-hold-quick-profit | PA3V5VCC40SA |
| Alpaca trader 2 | 04-balanced-long-hold | PA3YDWGV0I9V |
| Alpaca trader 3 | 06-adaptive-risk-scaled-short-hold | PA3OOI9HVR0P |

## Observed binding — still crossed on traders 1 and 2

| Site trader | Rule file (from trader log) | Bound account | Verdict |
|---|---|---|---|
| Alpaca trader 1 | 02-aggressive-mid-hold-quick-profit | **PA3YDWGV0I9V** | WRONG (trader 2's account) |
| Alpaca trader 2 | 04-balanced-long-hold | **PA3V5VCC40SA** | WRONG (trader 1's account) |
| Alpaca trader 3 | 06-adaptive-risk-scaled-short-hold | PA3OOI9HVR0P | correct |

Three independent sources agree.

### Source 1 — rendered status-page titles (live, 12:57-12:58Z)

    /status/alpaca          -> "alpaca — grok-bot-robo-trader-1@egedsoft.co.il (PA3Y) connected"
                               Rules: 02. Aggressive mid hold quick profit
    /status/alpaca?slot=2   -> "alpaca #2 — grok-bot-robo-trader-1@egedsoft.co.il (PA3V) connected"
                               Rules: 04. Balanced long hold
    /status/alpaca?slot=3   -> "alpaca #3 — grok-bot-robo-trader-1@egedsoft.co.il (PA3O) connected"
                               Rules: 06. Adaptive risk scaled short hold early profit

### Source 2 — the workers' own account lines (/api/logs/<name>/tail)

Most recent `account:` line in each slot log (market has been closed since,
so this is the last account read; no restart has occurred since):

    trader-alpaca     2026-10-02T20:54:48.282763Z INFO account: PA3Y… equity $10,878.00 (paper) trading_mode=paper
    trader-alpaca-s2  2026-10-02T20:54:48.405330Z INFO account: PA3V… equity $10,889.19 (paper) trading_mode=paper
    trader-alpaca-s3  2026-10-02T20:54:41.739193Z INFO account: PA3O… equity $10,920.47 (paper) trading_mode=paper

Startup lines show the crossing was established at the last worker start and
has not been re-bound since — no `claim-account`, no startup line, no restart
anywhere in the log window 2026-10-02T18:43Z -> 2026-10-04T12:52Z:

    trader-alpaca     2026-10-02T20:54:46.747483Z pool session: claimed by robo-trader-trade-worker-paper-68887d87c-88gns (token 134)
    trader-alpaca     2026-10-02T20:54:48.306706Z claim-account: claimed
    trader-alpaca     2026-10-02T20:54:52.173439Z trader started: rules=rules/02-aggressive-mid-hold-quick-profit.json ...
    trader-alpaca-s2  2026-10-02T20:54:46.750057Z pool session: claimed by robo-trader-trade-worker-paper-68887d87c-88gns (token 135)
    trader-alpaca-s2  2026-10-02T20:54:48.433904Z claim-account: claimed
    trader-alpaca-s2  2026-10-02T20:54:52.262519Z trader started: rules=rules/04-balanced-long-hold.json ...

Note: rule 02 started and then read PA3Y without halting, so the
account-number assertion suggested in the issue is not in effect on this
worker generation.

### Source 3 — broker-side figures, exact to the cent

Alpaca paper dashboard, `GET /api/v1/paper_accounts/{id}/trade_account/margin`,
read from the network log at ~12:59Z. Prices are frozen post-close
(`balance_asof: 2026-10-02`), so there is no read-skew to explain a mismatch.

| Alpaca account | account id | equity | cash |
|---|---|---|---|
| PA3V5VCC40SA | 0b6cbcad-8610-4f86-867b-49922f24de5b | 10878.76 | 0.05 |
| PA3YDWGV0I9V | 7daf7c23-ed1d-4a27-a8bf-a6b56ee0beb8 | 10867.55 | 0.05 |
| PA3OOI9HVR0P | bc5d1dc2-4d51-43f6-9196-25d5c1dcfd59 | 10910.59 | 0.01 |

Against the site at 12:57Z:

| Site trader | Site equity | Matches account | Should be |
|---|---|---|---|
| Alpaca trader 1 | $10,867.55 | PA3YDWGV0I9V (exact) | PA3V5VCC40SA |
| Alpaca trader 2 | $10,878.76 | PA3V5VCC40SA (exact) | PA3YDWGV0I9V |
| Alpaca trader 3 | $10,910.59 | PA3OOI9HVR0P (exact) | PA3OOI9HVR0P |

Position lots agree with the same crossing (trader 1 holds the
TXG 25.360298771 / DELL 3.50285484 lot set, trader 2 the
TXG 25.319534118 / DELL 3.509616397 set, each marked "2026-09-22 (adopted)"
on slot 2).

## Why every other health signal still says healthy

As the issue predicted: header `3 / 3 Running`, all three cards `running`,
status `ready` / `connected`, `broker_error` null, kill switch off,
daily-loss halt ok, 0 open orders, cash exact (0.05/0.05/0.01), position
quantities exact to 9 dp, equity delta $0.00 against the bound account.
The reconcile passes because each trader is compared against whatever
account it is attached to.

## Capture method note

Evidence here is textual rather than a screenshot: the account prefix and the
broker figures are text/JSON, and the raw `margin` responses carry full
precision where the rendered page rounds to whole dollars.
