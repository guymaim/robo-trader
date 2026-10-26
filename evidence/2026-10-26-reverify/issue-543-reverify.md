# Re-verify #543 — IBKR cash-flow check vs 'executions unavailable' — NOT RE-TESTABLE

Scheduled QA `reverify` run, 2026-10-26 ~14:04Z. Read-only live pass, signed in as the QA
bot, US market open.

## Why this one cannot be re-verified from a read-only live pass

The report has no live repro steps. Reproducing it as written needs **an Interactive
Brokers executions outage that overlaps a trade** — i.e. inducing a broker-gateway fault
and then having an order fill inside that window. Both halves are out of remit: the
playbook's hard limits forbid placing, cancelling or confirming any order, and there is
no read-only way to make the IBKR gateway drop executions.

## And this account has no IBKR trader to observe

Read at 14:04:46Z, `/api/home/traders` lists 5 traders: the three Alpaca slots
(`connected:true`) plus `IBKR Paper` and `IBKR Live`, both with `connected:null` and
`href:"/settings"` — not configured for this account. Confirmed directly at 14:04:5xZ:

| request | result |
|---|---|
| `/api/status/ibkr_paper?slot=1` | 404 `{"ok":false,"error":"no trader configured for this broker"}` |
| `/api/status/ibkr_live?slot=1` | 404 `{"ok":false,"error":"no trader configured for this broker"}` |
| `/api/status/ibkr?slot=1` | 404 `{"ok":false,"error":"unknown broker"}` |
| `/api/logs/trader-ibkr-paper-s1/tail` | 404 `{"error":"not found"}` |

Connecting one would mean changing Settings, which the playbook forbids.

The four Interactive Brokers traders the owner restarted on the fix are not this bot's,
so their logs and cash-flow records are not reachable from here either.

## Verdict

Left **closed**, **not** labelled `verified-by-claude`, **not** reopened. The fix is
reported as covered by tests (outage during a trade, outage past end of day, currency
conversions, first check hitting an outage) plus a log review of the four live IBKR
traders; none of that is independently checkable from a read-only live pass against this
account. Reopening on that basis would be a reopen on a hunch, which the standing
instruction forbids.

## Playbook compliance

No order placed, cancelled or confirmed. No sign-in, no sign-out. No Settings change
(the IBKR traders were deliberately left unconfigured). Read-only throughout.
