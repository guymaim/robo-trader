# Re-verify — issue #490 (`safeguards.hard_daily_loss_tripped`)

**Verdict: FIXED.** Labelled `verified-by-claude`.

Scheduled Robo Trader QA `reverify` check, read-only, 2026-10-27.
Signed in as `grok-bot-robo-trader-1@egedsoft.co.il`. Market **closed**
(Tuesday pre-open; next US open 2026-10-27T13:30Z).

## Why this run could give a verdict and 2026-10-26 could not

The 2026-10-26T09:02Z re-verify confirmed one half of the report (the key is
now written on every trader, slot 2 carrying an explicit `false`) and recorded
the other half as untestable: the closing note says stale markers are cleared
"at today's first session start", and `day_start_date` was still `2026-10-23`
at that read, i.e. before any clearing was due. It set an explicit condition:

> If the markers still read this way after today's session start has run and
> `day_start_date` has rolled, then the clearing did not take and this should
> be reopened at that point.

`day_start_date` has since rolled to **2026-10-26**, so a new trading day's
session start has run. The condition is now decidable, and it resolves in
favour of the fix.

## Reads

`GET /api/status/alpaca?slot=N` — the three calls named in the issue's
"How to reproduce" — twice, 45 s apart, identical both times
(`updated_at` 2026-10-27T08:58:15-16Z and 08:59:00-01Z).

| slot | `day_start_date` | `safeguards.day_start_equity` | equity now | day vs that baseline | `portfolio.day_return_usd` | `hard_daily_loss_tripped` | `daily_loss_halted` | `daily_loss_tripped_date` |
|---|---|---|---|---|---|---|---|---|
| 1 (PA3Y) | 2026-10-26 | 9392.19 | 9346.42 | −45.77 | +12.56 | `false` | `false` | `null` |
| 2 (PA3V) | 2026-10-26 | 9439.88 | 9415.08 | −24.80 | −17.35 | `false` | `false` | `null` |
| 3 (PA3O) | 2026-10-26 | 10032.47 | 9967.04 | −65.43 | +1.06 | `false` | `false` | `null` |

All three HTTP 200, `application/json`, `redirected: false` (session live, no
`accounts.google.com` redirect). `safeguards` key set **identical on all
three**: `day_start_date, day_start_equity, daily_loss_halted,
daily_loss_tripped_date, hard_daily_loss_tripped`.

## What that settles, against each half of the report

1. **Stale marker cleared — the half that could not be tested on 10-26.**
   `hard_daily_loss_tripped` was `true` on slots 1 and 3 on 2026-10-24 and
   again on 2026-10-26T08:57Z. It is now `false` on all three, with
   `day_start_date` rolled to 2026-10-26. The clearing took.
2. **One-branch write — already confirmed on 10-26, still holds.** The key is
   present on all three traders, not only on the branch that sets it.
3. **The three-field contradiction is gone.** `hard_daily_loss_tripped: false`
   now sits consistently beside `daily_loss_halted: false` and
   `daily_loss_tripped_date: null`. No trader asserts a trip that did not happen.
4. **No inversion against either baseline.** The 10-26 run flagged that slot 3
   was up +13.42 carrying `true` while slot 2 was down −91.48 carrying `false`.
   Today slot 1 is up +12.56, slot 3 up +1.06, slot 2 down −17.35, and all
   three carry `false` — the flag no longer tracks the sign of the day in
   either direction, against the `safeguards` baseline or the `portfolio` one.
5. **Not a false negative from an idle engine.** All three are `running`
   (`3 / 3`), `connected: true`, `last_scan` 2026-10-26, cash bit-identical to
   the 07:59Z reconcile (6873.55 / 7706.93 / 5567.84). Against the
   `safeguards` baseline every trader is modestly **down** today (−0.49 %,
   −0.26 %, −0.65 %) — far inside the unchanged 7 % hard ceiling, so `false` is
   the correct answer rather than a flag that has simply stopped being written.

## Observation, not a finding, not filed

The report noted the flag is surfaced in the status page's "Raw status data"
block. That block today renders only the signals state (`last_scan`,
`regime`, `pending_buys`, `rules_signature`); `hard_daily_loss_tripped` does
not appear in it, and "Signals & safeguards" shows only Last scan and Pending
buys. The API remains the surface the issue's repro steps name, and that is
what was verified. Recorded here rather than filed.

## Limits honoured

Read-only: Robo Trader GET only, same-origin credentialed reads of
`/status/alpaca`, `/api/status/alpaca?slot=1,2,3` and `/api/home/traders`.
No order placed, cancelled or confirmed. No sign-in attempted, no logout, no
Settings or API-key change. Alpaca was **not opened at all** this run and
`paper-api.alpaca.markets` was never contacted. No token, cookie or
browser-storage read. The only page interactions were expanding the page's own
read-only disclosure sections.
