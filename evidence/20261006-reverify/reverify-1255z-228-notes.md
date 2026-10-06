# Re-verify evidence — 2026-10-06 12:55-13:02 UTC

Run: `reverify`, started 2026-10-06T12:56:59Z. Market CLOSED throughout
(is_open false, next open 2026-10-06T13:30:00Z).

## Scope

Issues closed since the previous re-verify finished (2026-10-06T08:03:39Z) and
not already carrying `verified-by-claude`:

- **#228** only — "Owner task (weekend 3-4 Oct): switch broker gateway sign-in to
  the new per-user method, then remove an old broad log permission", closed
  2026-10-06T12:28:50Z by egedsoft, label `enhancement`.

Verified two ways: closed-issue list sorted by `updated` desc (50 per page), and
a paginated `state=all&since=2026-10-06T07:00:00Z` sweep (page 1 = 9 items,
pages 2-3 empty). Everything else in the window is either open (#302 #301 #296
#277 #229), a PR, or closed before the cutoff (#287 closed 05:38:52Z and already
labelled `verified-by-claude`).

Outcome: #228 **left closed, not labelled, not reopened** — two of its three
steps are cluster-side and unverifiable from this suite. Comment:
https://github.com/guymaim/robo-trader/issues/228#issuecomment-6016840574

## Readings

### Trader -> broker account binding, from status-page titles, 12:59Z

| URL | HTTP | Title |
|---|---|---|
| `/status/alpaca` | 200 | `alpaca — grok-bot-robo-trader-1@egedsoft.co.il (PA3Y) — Robo Trader` |
| `/status/alpaca?slot=2` | 200 | `alpaca #2 — grok-bot-robo-trader-1@egedsoft.co.il (PA3V) — Robo Trader` |
| `/status/alpaca?slot=3` | 200 | `alpaca #3 — grok-bot-robo-trader-1@egedsoft.co.il (PA3O) — Robo Trader` |

Expected per PLAYBOOK section 6: slot 1 -> PA3V, slot 2 -> PA3Y, slot 3 -> PA3O.
Slots 1 and 2 are still crossed, i.e. **#277 survived the 12:28Z credential
switch**. Unchanged from the 07:54Z reading earlier today.

### Health, `/api/status/alpaca[?slot=N]`, 12:58:37-13:00Z

| Slot | ok | connected | broker_status_unknown | broker_error | fault | daily_loss_halted | open orders | equity | cash |
|---|---|---|---|---|---|---|---|---|---|
| 1 | true | true | false | null | null | false | 0 | 11158.34 | 0.05 |
| 2 | true | true | false | null | null | false | 0 | 11171.51 | 0.05 |
| 3 | true | true | false | null | null | false | 0 | 11258.57 | 0.01 |

`stats.trades` still 0 on every slot. Newest fill: slots 1-2
2026-09-22T13:40:08.743238Z, slot 3 2026-09-23T13:35:33.392607Z — no new trades.

Traders page 12:59:07Z: 3 / 3 Running, all three `Connected`, combined equity
$33,584. Admin table: all 24 trader rows across all users `running`.

### Pool logs straddling the 12:28Z switch

`/api/logs/{trader-alpaca,trader-alpaca-s2,trader-alpaca-s3}/tail?lines=10`,
newest lines:

```
slot 1  2026-10-06T12:51:55.543166Z  INFO  alpaca-9acb344b89e1     market closed; next open 2026-10-06T09:30:00-04:00 - sleeping 900s
slot 2  2026-10-06T12:54:01.126853Z  INFO  alpaca-s2-9acb344b89e1  market closed; next open 2026-10-06T09:30:00-04:00 - sleeping 900s
slot 3  2026-10-06T12:48:36.857175Z  INFO  alpaca-s3-9acb344b89e1  market closed; next open 2026-10-06T09:30:00-04:00 - sleeping 900s
```

30 lines read across the three slots, 100% INFO, 0 non-INFO. Nothing but 900s
closed-market sleeps across 12:28Z: no restart, no claim/release, no
`trader-started`, no `account_conflict`. Same shared worker `9acb344b89e1`, pod
`robo-trader-trade-worker-paper-68887d87c-rfnn8`, both before and after the
switch. The last startup lines in these logs are 07:44-07:48Z, i.e. the
site-wide outage already filed as #302, not the switch.

Implication recorded on the issue: no worker restart means the paper slots never
re-read credentials, so the switch had no opportunity to correct the binding
either (cf. #274).

## Limits

- Cluster-side steps of #228 — "turn the switch on" and "remove the old
  permission" — are not observable from the product. No kubectl or cluster-API
  access from either the device shell or the cloud container (403 at the egress
  proxy). Not labelled `verified-by-claude` for that reason.
- Not reopened: the crossed binding is already tracked by open High #277, and
  the closing note covers work this suite cannot inspect, so a reopen would be a
  hunch.

## Read-only confirmation

No order placed, cancelled or confirmed. No Settings or credential change. No
sign-in, no sign-out, no credential entered. Alpaca dashboard never opened. Other
users' traders read only through the admin read-only view and the status API.
