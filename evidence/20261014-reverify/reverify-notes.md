# Re-verify run — 2026-10-14 06:24–06:40 UTC

Scheduled `reverify` check against the live site, bot account
`grok-bot-robo-trader-1@egedsoft.co.il`. **Read-only**: no order placed,
cancelled or confirmed; no Settings value changed; no credential or 2FA code
entered anywhere; no sign-in attempted; nothing clicked on Alpaca.

Market **CLOSED** (`is_open:false`, next open 2026-10-14T13:30:00Z). 3 / 3
running. `session_today: false`.

Scope: 24 issues closed since the previous re-verify finished
2026-10-06T13:03:18Z and not carrying `verified-by-claude`.

## Alpaca dashboard unavailable

`https://app.alpaca.markets/paper/dashboard/overview` redirected to
`/account/login` at 06:28 UTC. Per playbook §2/§7 no sign-in was attempted, so
broker-side corroboration was not possible this run. Everything below is
Robo-Trader-side only.

## Verified fixed

| # | What was checked |
|---|---|
| 295 | `/api/home/traders` now returns `pnl_today` for all three traders under the exact gate from the report (`session_today: false`, market closed): -72.45 / -79.58 / -62.45, byte-identical to `/api/admin/traders`; `totals.pnl_today: -214.48`; cards and COMBINED tile render them. |
| 296 | Fault flag no longer latches. At 06:31:47Z slot 3 `fault: null` with its newest ERROR 14h32m old, while slots 1/2 were set with ERRORs 9s old. Slot 3 logged a new `ERROR in loop` at 06:32:05Z and its `fault` was SET at 06:33:44Z. The flag follows recent errors and clears. |
| 297 | Panel is now "Recent sign-ins" with a 4-row table (When / Browser and system / Network / Signed in with) and reworded copy that no longer promises a device list. Session endpoints still 404, which the issue allowed as the alternative resolution. |
| 303 | Save button per section (Appearance, Display currency, Notifications, Slack, Report emails, per-trader forms). Change handlers only toggle the Save button's `disabled`; the POST lives in the save functions. `switch.js` contains no fetch. No setting was toggled to test this. |
| 304 | All 23 former checkboxes are `input[type=checkbox][role=switch]` with accessible names; Report emails rows render switch + label on one line with even spacing (screenshot). |
| 305 | `role="tablist"` with 5 tabs (Sign-ups, Users, Traders, Rule files, Runs); selected tab in the URL hash and updated on switch (`#signups` -> `#traders`). |
| 306 | Six `.otp-box` inputs injected per code field on /settings/security, wrapped in a labelled `role="group"`; `static/js/otp-input.js` implements paste, Backspace and `onComplete` auto-verify. **No code was typed** (hard limit), and the other three surfaces named in the issue need a sign-in or an order, so they were not exercised. |
| 311 | /status/alpaca?slot=2 renders "Updating…" then the data; no "error" label and no `role=alert` error element. The only "error" text on the page is inside the Recent activity log lines. |
| 323 | Run result page shows "$0.00 Commissions paid — no order fees in this run's execution model" in the summary and a per-trade "Fees $" column in the trades table. |
| 338 | Rules column present, sortable, display name shown, `.json` filename in the cell `title`, on both the Home "All tenants" table and the Admin "Traders" table. |

## Still broken — reopened

**#301** — see `301-*.jpg`. Slots 1 and 2 no longer return identical
`closed_orders` / `activities`, and the $19,999.90 over-report is gone, but the
history is still wrong, now by omission.

```
slot 1  closed_orders_meta {total: 6,  has_more: false, since: "2026-09-20", scoped: true}
slot 2  closed_orders_meta {total: 1,  has_more: false, since: "2026-09-22", scoped: true}
slot 3  closed_orders_meta {total: 16, has_more: false, since: "2026-09-22", scoped: true}
```

Slot 3 still carries its five 2026-09-22 opening buys. Slots 1 and 2 — the two
traders whose orders collided on `client_order_id` — carry none, over a window
that starts before those orders with `has_more: false`. Slot 2 holds MRNA, HPE,
MRVL and DELL and its Executions table lists exactly one row, a TXG sell on
13 Oct, and "Closed trades 0 trades" because the matching buy is missing.

## Not re-testable as written (left closed, commented)

- **#298** needs a real broker disconnect; `daily_loss_halted: false` on all three is the only observable.
- **#320** the monitoring alert is not visible from the site. One failed run is still listed: `20260921_073808_robo_trader09_balanced_615a2c`, 2026-09-21T07:38:08Z, `stale: worker gone (no Job)`.
- **#324** no Alpaca buy happened on this account after the issue closed (2026-10-10T10:05Z); newest buy is OKTA 2.164902 on 2026-10-09, which predates the fix.
- **#334** scan-coverage is a worker/store change with no site endpoint (`/api/scans*` all 404); the trader in the report belongs to another tenant.
- **#335** the enable-confirm dialog is present (`#er_dialog`: selected rule, "Best 5 rules", 30/60/90-day switch, link to Compare Rules, "Enable with this rule file"). The default-rule-for-new-traders, simulator default and existing-traders-keep-their-rule criteria need a trader to be created and were not tested.

## Out of scope for a re-verify

Owner tasks and competitor ideas carry no repro steps and were not re-tested:
#208, #231, #233, #234, #235, #237, #244, #245, #248, #254.

## #277 — not reopened

Current bindings read from the status-page titles at 06:26:52Z: slot 1 `(PA3Y)`,
slot 2 `(PA3V)`, slot 3 `(PA3O)`. That is the pairing the owner declared
intended when closing this issue on 2026-10-07, and it is unchanged. **Note that
PLAYBOOK.md §4 and §6 still carry the pre-rotation expectation (t1 -> PA3V,
t2 -> PA3Y), so the next reconcile run will raise a false "traders swapped"
finding until those two sections are updated.**
