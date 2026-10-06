# reverify run 2026-10-06 (07:55Z start)

Scheduled QA check "reverify" (10:54 / 15:54 Israel time). Read-only run, done
from the signed-in browser pane on macbookpro.

## Scope

Issues closed in guymaim/robo-trader since the previous reverify finished
(2026-10-05T12:57:42Z) and not already carrying `verified-by-claude`.

Verified two ways:
- closed issues sorted by `updated` desc, newest close = #287 at 2026-10-06T05:38:52Z
- paginated `state=all&since=2026-10-05T12:00:00Z` sweep (pages 2 and 3 empty):
  #302 #301 #298 #296 #277 open, #300 #299 are PRs, #287 the only close.

Scope = exactly one issue: **#287**.

Market CLOSED throughout (pre-market; next US open 2026-10-06T13:30:00Z), so the
only buying pass available to look at was the 2026-10-05 one the issue was closed on.

## #287 - cash left idle after whole-share buys - VERIFIED FIXED

Original report (2026-10-02, live IBKR, entry amount $4,000): after a whole-share
buy the full entry amount was counted as used, so the leftover was not offered to
the next candidate and every remaining candidate was skipped with
`$0 below min trade $250`, leaving ~$751 idle.

Account identified as g***@gmail.com / ibkr_live (user a2fd764b), matched on the
deposit the report describes: the status page's Deposits & withdrawals shows
`2026-10-02 +$4,900.11 deposit - confirmed`, and the Executions table reproduces
the reported pass exactly:

| time (UTC) | symbol | qty | price | amount |
|---|---|---|---|---|
| 2026-10-02 13:55 | MU | 3 | $1,086.14 | $3,258.42 |
| 2026-10-02 13:56 | UMC | 34 | $26.14 | $888.70 |

i.e. the report's "three shares cost about $3,261" and "34 shares cost about $891",
and the second buy sized at $4,903 - $4,000 rather than at the cash really left.

The discriminating evidence is the skip line. Under the reported bug the whole
budget is consumed, so the next candidate is sized at $0. In the 2026-10-05 pass
on the same trader:

    [2026-10-05 13:36:06] BUY TEAM $751 (price ~188.37)
    [2026-10-05 13:36:06] buy skip CORT: $186 below min trade $250
    ... 13 more candidates, all "$186 below min trade $250"
    [2026-10-05 13:36:07] buys complete: 1 orders ['TEAM']

$186 = $751 - 3 x ~$188.37 (the estimated whole-share cost at sizing time). The
actual fill was 3 x $192.21 = $576.62, leaving $174.38; cash now reads $171.53.
So the leftover IS carried into the sizing of the next candidate, which is the
issue's Expected. It was skipped only by the intact $250 minimum-trade limit.

The same shape on the other three IBKR traders the fix went to - each fully
invested and each reporting its real residual cash in the skip line ($19 / $73 /
$61), never $0 - rules out the $186 being a coincidence of one trader.

### Honest limits of this verification

- A second buy actually *funded* by recycled leftover has not happened yet: on
  every IBKR trader on 2026-10-05 the leftover fell under the $250 minimum. What
  is proven is the sizing input (real remaining cash, not $0); the end-to-end
  second fill is not yet observable.
- The issue's secondary note - the simulator's Realistic whole-share model must
  size the same way - was NOT re-tested. That needs a new simulation run, which
  is a write; this check is read-only.
- Neither limit contradicts the Expected, so the issue was labelled
  `verified-by-claude` and left closed rather than reopened.

## Read-only confirmation

No order placed, cancelled or confirmed. No Settings value changed. No Alpaca
page opened. No sign-in, no credential entered, no logout. Other users' traders
were read through the admin read-only status page and the log tail API only.
Product clicks: none - navigation and GETs only.
