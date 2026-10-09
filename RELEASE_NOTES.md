# Release notes

User-facing changes to Robo Trader, newest first.

For how we maintain this file, see the project’s internal contributor rules (`CLAUDE.md` / Cursor rules in the private workspace). Public readers: use this page to see what shipped; use [Issues](https://github.com/guymaim/robo-trader/issues) to report problems or request features.

---

## 2026-10-09 — "Today" keeps showing after the market closes

### Changed

- **Today's profit and loss no longer turns into a dash when the market is closed.** On your trader page, your home cards and totals, and the admin's all-traders table, "Today" now shows how your account moved compared with the previous close, including after-hours prices. This matches the Today column in your portfolio table. The QQQ and SPY comparisons still appear only during a session.

---

## 2026-10-08 — See your recent sign-ins

### Added

- **Settings → Security now lists your last 10 sign-ins.** Each line shows the time (UTC), the browser and system, a partly hidden network address (for example `203.0.113.*`) and how you signed in, so you can spot a sign-in you don't recognise. Only completed sign-ins of your own account appear. ([#297](https://github.com/guymaim/robo-trader/issues/297))

### Changed

- **The page no longer promises a list of signed-in devices.** It cannot show who is signed in right now; it says so plainly. The **Sign out everywhere else** button works as before.

---

## 2026-10-08 — Clearer status labels on your trader page

### Changed

- **The status strip on your trader page uses plain words.** "Pod" or "Pool" is now **Connection** (whether the trader is connected to your broker). **Approve buys** shows just ON or OFF. **Open confirm** is now **Buy check after open**, and the technical line next to your rules is a plain sentence that only appears when your rules use that check. Nothing about how your trader behaves has changed.

---

## 2026-10-08 — Rule name and converted profit on your home cards; one display currency in the header

### Added

- **Each trader card on your home page now shows the rules it follows**, for example "Rules: 04. Balanced long hold" or the name you gave your own rule.
- **Today and Total P&L on the home cards, and the totals at the top, now show the amount converted to your display currency** (for example "≈ ₪1,250") under the dollar figure. Choose US dollar to hide it. The rate is indicative and for information only.

### Changed

- **The Display currency choice moved out of the trader page.** It now sits in the header of every signed-in page, next to Theme, and applies to all your traders. It is also in Settings.

---

## 2026-10-08 — Pause new buys on a trader

### Added

- **You can pause new buys on any trader from its page.** While paused, the trader does not open new positions, but your stop-loss, take-profit and time-stop exits and Sell now keep working. Orders already sent to your broker are not cancelled. Choose an end time of up to 7 days, or pause until you resume. The trader page and your home card show when buys are paused. The trader notices a pause or resume within a few minutes (up to 5 while the market is open). After you resume, new buys come from the next evening scan. Resuming a live trader asks for your Authenticator code; pausing never does. Your broker connection and Authenticator are not changed. ([#248](https://github.com/guymaim/robo-trader/issues/248))

### Changed

- **Pause new buys is clearer.** You now see one button at a time: **Pause** when buys are on, **Resume** when they are paused. A large status shows **RUNNING**, **PAUSED** or **WAITING** (waiting for your trader to pick up your last request, up to 5 minutes while the market is open), with a word and an icon, not only a color. The status changes the moment you ask, and the buttons are off while a request is waiting. While it is waiting you can press **Cancel request**. The home card shows a matching "Waiting" tag. ([#248](https://github.com/guymaim/robo-trader/issues/248))

---

## 2026-10-08 — A "Last scan" list on your trader page; a text summary under the run charts

### Added

- **Your trader page now has a "Last scan" section above your positions.** For each stock the latest scan considered, it shows one line: bought, skipped, or rejected, with the reason in plain words (for example, "Opened 6.2% above the signal close; your cap is 5.0%" or "The broker refused the order: not enough cash"). If nothing was bought because the market is closed or buying is paused, it says so instead of showing an empty list. It shows the first 5 lines, with a button for the rest, up to 30 lines. It works for paper and live traders, only displays information, and never shows account numbers or keys. ([#260](https://github.com/guymaim/robo-trader/issues/260))

### Changed

- **Simulation results come with a plain-text summary of the equity curve.** It gives the start and end value, the return, and the deepest drop from a previous high, compared with the benchmarks. The charts scroll inside their own box on phones. Older runs without a stored daily equity series say "Chart needs a new run" instead of showing an empty chart. ([#253](https://github.com/guymaim/robo-trader/issues/253))

---

## 2026-10-08 — Order history no longer mixes when a trader moves to another broker account

### Fixed

- **A trader's orders, executions and trades no longer show another account's history.** If the broker account behind a trader's keys changes (for example, two traders had their keys swapped), the Closed orders, Executions and Trades pages could list the other account's orders and fills, and the invested total could be roughly double. When the trader notices its account changed, it now starts its history from that point and hides the earlier orders and fills of the other account. A normal restart changes nothing. The two affected test traders were repaired. For about a day, six traders briefly showed empty order history because of an early version of this change; it was reverted and their full history is back. ([#301](https://github.com/guymaim/robo-trader/issues/301))

---

## 2026-10-08 — The trader page shows your last numbers while it loads

### Fixed

- **The trader page no longer starts empty, and no longer ends in a bare "error".** While the latest data loads, the page now shows the last numbers it has for that trader (the badge says "updating…"), then replaces them. If a refresh fails, the numbers stay on screen with a "stale" badge that says why (the server was slow, or you were signed out, with a sign-in link) and the page tries again by itself. Slow outside price lookups no longer hold the page up. The saved copy stays on your device only, belongs to your sign-in, and is removed when you log out. ([#311](https://github.com/guymaim/robo-trader/issues/311))

---

## 2026-10-07 — Rules comparison is easier to scan; profit and loss by stock, a trade Journal

### Added

- **A "P&L by symbol" table on simulation results and trader pages.** It shows how much of the closed-trade profit or loss came from each stock, with its share of the total and the number of trades. On a trader page it covers only that trader's own trades and says whether the account is paper or live. Your headline returns and the QQQ/SPY comparison are unchanged. ([#245](https://github.com/guymaim/robo-trader/issues/245))

- **A Journal page for your own trades.** See your paper and live fills and your simulated trades in one list, add a short private note (up to 500 characters) and a tag (planned, mistake, news or rule) to any row, and download the filtered list as a CSV file. Only you can see your journal and notes. Simulated trades are marked as hypothetical. ([#244](https://github.com/guymaim/robo-trader/issues/244))

### Changed

- **Rules comparison marks the best and worst result.** In each period the highest and lowest return are marked with the words "best" and "worst" (not only by color), and the rule name stays in view as you scroll across the periods. Sort links now say 1, 3, 6, 12, 18 and 24 months; old bookmarked links still work. ([#254](https://github.com/guymaim/robo-trader/issues/254))

---

## 2026-10-07 — A recovered trader clears its warning; a broker disconnect no longer stops buying

### Fixed

- **The "The trader reported a problem" notice now goes away by itself.** After a short error that the trader recovered from (for example, the broker briefly answering with an error), the notice used to stay on the trader card until the trader was restarted. It now clears once the trader has been running normally for a few minutes. Serious problems (wrong keys, rejected orders, a halt, not enough cash) still stay until they are resolved. ([#296](https://github.com/guymaim/robo-trader/issues/296))
- **Admin: "Today" in the All tenants table matches the trader cards.** Outside the trading session the table showed an overnight figure while the cards showed a dash; both now show a dash until the session starts. This affects admins only. ([#295](https://github.com/guymaim/robo-trader/issues/295))
- **A broker disconnect no longer triggers a false "Max daily loss" stop.** If the broker connection dropped while an account was being read, the trader could see a value of $0 and halt buying for the day as if the account had lost money. The trader now treats an empty account read as a connection problem and does not run the loss check on it. No real loss was involved. ([#298](https://github.com/guymaim/robo-trader/issues/298))

---

## 2026-10-07 — Save buttons, on/off switches and Admin tabs

### Changed

- **Settings only change when you click Save.** Report emails, each trader's options (rule file and the two approval switches), the automatic IB Gateway sign-in, the color theme and the display currency now each have a **Save** button. Picking something only marks it; nothing is stored until you click Save, and the button stays off until you have changed something. ([#303](https://github.com/guymaim/robo-trader/issues/303))
- **Every checkbox on the site is now an on/off switch.** The switch shows ON or OFF in words, not only by color, and works with the keyboard and screen readers. In Settings → Report emails the switches and their text now line up on the same row. ([#304](https://github.com/guymaim/robo-trader/issues/304))
- **The Admin page is split into tabs** (Sign-ups, Users, Traders, Rule files, Runs) instead of one long page. A tab has its own link, so a reload or a shared link opens the same tab. ([#305](https://github.com/guymaim/robo-trader/issues/305))

---

## 2026-10-07 — Easier Authenticator code entry

### Changed

- **The 6-digit Authenticator code is now six boxes.** It looks the same everywhere the site asks for it: signing in, confirming it is you, Sell now, and setting up or replacing your Authenticator in Security settings.
- **It checks itself.** As soon as you type the sixth digit the code is sent; there is no need to press Verify.
- **You can paste the code.** Paste all six digits into any box (spaces are ignored) and they fill in for you. ([#306](https://github.com/guymaim/robo-trader/issues/306))

---

## 2026-10-06 — Choose your report emails: daily, weekly, monthly

### Added

- **Pick which report emails you get.** Settings → Notifications has three checkboxes: **Daily**, **Weekly** and **Monthly**. Weekly and monthly are on and daily is off unless you change them. If you had turned email off before, the weekly and monthly reports start off too.
- **One email for all your traders.** Each report covers every trader you have turned on: your profit and return for the period next to QQQ and SPY, your account value, the stocks bought and sold, what you hold now, and the top candidates from the latest scan for the next open. If you have both real-money and paper traders, the email says so next to the totals. A box at the top tells you when a trader needs your attention (for example, it is locked or stopped). Each trader has a link to its own page.
- **The weekly report** arrives Friday after the US close. It shows the week day by day, how many sales made a profit, and your best and weakest trade.
- **The monthly report** arrives after the last trading day of the month. It shows the month week by week, how many days were up, the best and worst day, the deepest dip, money you added or withdrew, and the year so far.

### Changed

- **The old end-of-day summary, one email per trader, is replaced by the daily report.** It is off by default; tick **Daily** in Settings to get it, now as one email for all your traders.
- **"Email alerts" in Settings now covers only warnings and errors** from your traders. The reports have their own checkboxes.
- **Privacy Policy updated (6 October 2026)** to describe the report emails and their defaults. You will be asked to confirm it once the next time you use the site.

---

## 2026-10-04 — Clearer Home cards, Israel time, and plain "Sell now" confirmations

### Added

- **Home shows how each trader is doing without opening it.** Each card has a Paper or Live label, a Connected or Not connected label, and the date of its last scan ("No scan yet" for a new one). If the trader hit a broker rejection or a startup problem in the last three days, the card says so in one plain sentence with a link to that trader's log. ([#252](https://github.com/guymaim/robo-trader/issues/252))
- **A new account sees what to do next.** Before a broker is connected, Home shows one "Connect a broker" panel instead of empty figures. An empty Runs page says "No simulations yet" and links to New Run. The getting-started checklist disappears once all four steps are done. Skipping the two-step sign-in step is remembered in that browser only. ([#258](https://github.com/guymaim/robo-trader/issues/258))
- **Times show Israel time with UTC beside it,** for example "29 Sep 2026, 16:36 Israel time (13:36 UTC)". This applies to the runs list, a run's page, trader orders and activity, and the Rules Comparison page. The clock in the page header and the time in the Sell now box are unchanged. ([#256](https://github.com/guymaim/robo-trader/issues/256))
- **Simulations say where they are.** The runs list shows queued, running, done or failed, and "running for 12m 5s" on a run in progress. A queued run's page tells you that you can leave and come back. ([#259](https://github.com/guymaim/robo-trader/issues/259))

### Changed

- **"Sell now" and "Decide" say exactly what will happen.** The box names Paper or Live, the stock, the number of shares, and that it is a market order that cannot be taken back. The final button reads like "Sell 10 AAPL". On a live account the cursor starts on Cancel. Your authenticator code is still asked, as before. ([#257](https://github.com/guymaim/robo-trader/issues/257))

### Fixed

- **Accessibility:** the "Extra days" box in the max-hold dialog now has a name for screen readers, and the small switches and checkboxes on the rules pages are big enough to tap (at least 24 by 24 pixels). Our accessibility work is not finished; see [#116](https://github.com/guymaim/robo-trader/issues/116).

---

## 2026-10-04 — Trader page names how your trader runs correctly

### Fixed

- **The trader page said "Pod" for every trader, even when yours runs in the shared trading group.** The label now says **Pool** for those traders, and the buy-approval chip says "(pool)" instead of "(pod)". Only the wording changed. Your trader, its rules and its broker connection are the same as before. ([#268](https://github.com/guymaim/robo-trader/issues/268))

---

## 2026-10-04 — Settings checkboxes are clearer for screen readers; admin tables are easier to read

### Fixed

- **The checkboxes under a trader's Options in Settings now announce what they do.** A screen reader reads "Require approval before buying" or "Require approval before selling" instead of a bare "on".
- **Alpaca traders no longer offer the Interactive Brokers restart option.** It does not apply to them, so keyboard and screen-reader users no longer land on it.
- **For administrators: "Days running" is now the same on every page.** The admin table showed one day less than the trader's own page; the go-live day now counts as day 1 everywhere.

### Added

- **For administrators: the user tables show a short user reference next to the email,** so rows of the same person or similar emails can be told apart. Emails stay partly hidden for read-only admins.
- **For administrators: a new "Last scan" column** shows the date of each trader's latest stock scan, with a "stale" mark when a trading day was missed.

---

## 2026-10-02 — Ten new rules in the rules library (14 to 23)

### Added

- **The rules library has ten new rules, numbered 14 to 23.** Each one is a changed version of one of the rules 01 to 13, and its name says which: for example **18. Balanced long hold time stop** is based on **04. Balanced long hold**. The rules 01 to 13 have not changed.
- **Eight of them use a time stop.** A time stop sells a position that has not gained enough after a set number of trading days. The two rules whose names end in "retuned" (14 and 23) have no time stop; they change other settings. Every new rule also changes some of these: the stop-loss distance, the trailing stop, take-profit, how long a position can be held, how many buys a day, and how big each buy is. Some also change which stocks qualify.
- **The numbers 14 to 23 are only the order they were added, not a rank.** The numbers 01 to 13 still show the order of an earlier test.
- **You can pick them like any other rule:** in New Run, in a trader's settings, and as the start of your own rule in My Rules.
- **The Rules Comparison page includes them.** They also appear on each earlier saved day: these results are worked out in the background over the next day or two, one day at a time. Until a day is finished it shows the rules it had before.

These rules were found by testing many settings on past prices. Past results do not predict future results: a rule tuned on past data often does worse on new data. Try a new rule in a paper account before you use it with real money.

---

## 2026-10-02 — Rules Comparison: choose which saved days are counted

### Added

- **"All saved days together" on the Rules Comparison page now has a "Days counted" choice.** You can count:
  - all saved days (as before),
  - the last saved days (you enter how many),
  - this month, last month, the last 90 days, or this year,
  - the first saved day of each week in the last 12 months,
  - the first saved day of each month in the last 2 years,
  - dates you choose (from and to).

  After you press **Apply**, every number in that section is worked out again from those days only: the short lists (never lost money, most often first, and so on), the table of each rule, and the table by period. The section says which days were counted and lists them. The choice stays when you sort the table or pick another comparison day. **Count all saved days** goes back to everything.

  The table for the day shown at the top of the page does not change. These are simulated results and they do not predict what a trader will earn.

---

## 2026-10-02 — Interactive Brokers: deposits in another currency are counted

### Fixed

- **A deposit made in a currency other than US dollars is now counted as money you put in.** Before, if you deposited shekels (or another currency) into your Interactive Brokers account and converted them to dollars, the trader page did not add the deposit to **Money you put in**. It showed the new money as profit, so **Profit**, **Total return** and **Today** were too high. Now the deposit is recorded:
  - when the money arrives, if the trader sees it before you convert it, or
  - when you convert it, if you convert it first.

  The deposit shows on the page within about half an hour when the market is closed, and within about ten minutes when it is open. You get the usual "Recorded a deposit" notice. A withdrawal in another currency is recorded the same way.
- **Money in another currency is never treated as dollars you can trade with.** Until you convert it, a deposit in another currency is not used for buying. Before this fix, the trader could read it as available dollars for a short time after it arrived.
- **A deposit is no longer missed when the trader restarts right after the money arrives.**
- **Alpha total (vs QQQ and vs SPY) is right on the day of a deposit.** On the day a deposit was recorded, **Alpha total** could count the deposit as return until the market opened, while **Total return** next to it was already right. Both now leave the deposit out.

If you made such a deposit before this update, it is not added by itself. Ask the site admin to record it.

---

## 2026-10-01 — New Alpaca keys are used right away; Restart shows how it went

### Fixed

- **Saving new Alpaca API keys now restarts that trader by itself.** Before, a running trader kept the keys it had started with. If you regenerated your keys at Alpaca and saved the new ones in Settings, the trader kept failing with "unauthorized" until you pressed **Restart trader** ([#262](https://github.com/guymaim/robo-trader/issues/262)). Now the save restarts the trader once, and the new keys are used within about a minute.
- **A trader whose keys were rejected by Alpaca now tries the saved keys again by itself.** It no longer keeps retrying with the old keys.
- **Disable and then Enable now restarts the trader even when done within a few seconds.** Before, a quick off-and-on could leave the trader running as it was, while Settings showed it as stopped.

- **The trader page shows the right rule file for a trader that has run a long time without a restart.** After more than about a day without a restart, the page could show the default rule (and its hold days and entry size) instead of the rule you chose. Only the page was wrong: the trader kept trading with the rule you chose.

### Added

- **Restart trader now tells you what is happening.** After you press **Restart trader** in Settings (or save new Alpaca keys), the card shows **Restarting…** with the time waited, and then the result:
  - **Restarted. The trader is running and connected to the broker.**
  - or the reason it could not start, in plain words. For example: the broker rejected the saved keys, or the keys belong to a different broker account than the one this trader was linked to.

  It usually takes less than two minutes. You can stay on the page; it updates by itself.

---

## 2026-10-01 — Rules Comparison: all saved days together

### Added

- **The Rules Comparison page now sums up every saved day in one place.** Until now the page showed one trading day at a time. A new section, **All saved days together**, below the day's table, shows how each library rule did across every saved comparison day:
  - **Short lists:** rules that never lost money in any period, rules that never ranked below 8, rules that were always ahead of QQQ, the rule with the best average rank, the rule most often first and most often in the top 3, and the rule with the smallest worst fall. When no rule qualifies, the page names the closest one and how often it met the test.
  - **One line per rule:** average rank, worst rank, times first, times in the top 3, results with a profit, worst single result, results ahead of QQQ, worst fall from a peak, average win rate, average profit factor and trades a year.
  - **By period:** each rule's average rank and average return in each of the six periods (1 month to 24 months), so you can see which rules lead the short periods and which lead the long ones.

  It updates by itself when each day's comparison finishes. Select a column heading to sort by it. Your own rules are not counted in this section.

  **Read it with care.** The saved days are close together, so their periods cover almost the same dates; this is not many separate tests. These are simulated results. They do not predict what a trader will earn, and nothing on this page changes your trader or your default rule.

---

## 2026-09-30 — IBKR traders: back online faster, exits no longer stuck, paper market data tip

### Fixed

- **After an IB Gateway restart, IBKR traders show as online again within about a minute.** Before, the trader page could show **offline** for up to 15 minutes after the gateway restarted, even though the gateway was already signed in again. Trading itself was not affected.
- **An IBKR position could stop being managed after a sale or trim that failed during a connection drop.** If the gateway was down at the moment a trim or sale was sent, the trader marked that stock as "waiting for confirmation" and then skipped all later exit checks for it, including the trailing stop. That mark now clears on the next trading day if the broker shows the order never went through, as it already did on Alpaca. One paper account was affected; no live account was.

### Added

- **IBKR paper guide: market data sharing when you also run IBKR Live.** If you run both an IBKR Paper and an IBKR Live trader, open IBKR Client Portal → **Settings** → **Paper Trading Account** and set **Share real-time market data subscriptions with paper trading account** to **No**. With sharing on, IBKR gives your market data only to the live session, so the paper trader shows **Market data locked (10197)** and cannot price buys or check exits. The [IBKR paper setup](https://robo-trader.egedsoft.co.il/help/ibkr-paper) guide and the message on the trader page now explain this. Paper only? Either setting works.

---

## 2026-09-30 — "Today return" and the equity chart agree

### Fixed

- **On Alpaca accounts, each day's point on the equity chart now uses the 4 pm close.** The chart on your trader's page stored each day's value using after-hours prices, so a day's step on the chart could differ from **Today return**. Each day's point is now the regular-session close.
- **"Today return" could be too high when you still held a stock your broker had delisted** (for example after a takeover). The broker's previous-close figure left that stock out, while your current value included it. **Today return** now measures from the same closing value the chart shows, and the daily loss limit uses the same starting value.

Chart points recorded before this change are left as they were. The difference is small and does not change your total return. Nothing to do.

---

## 2026-09-30 — Alpaca paper traders can run in a new shared engine

### Changed

- **Some Alpaca paper traders now run in a new shared trading engine.** We are moving Alpaca paper traders, one at a time, from a separate engine per trader to one shared engine that runs many traders side by side. It uses less computing power per trader and lets us add traders faster. Your trader keeps the same rules, positions, history, settings and account, and it trades the same way. The trader page, logs, **Sell now**, approvals and alerts look and work the same. **Nothing to do.** A move usually happens while the market is closed and takes a few minutes; during those minutes, changes to that trader (for example approving a buy or **Sell now**) are refused with a short "being moved, try again in a few minutes" message. IBKR traders and live Alpaca accounts are not affected.

---

## 2026-09-29 — Check your IBKR authenticator key before you rely on it

### Added

- **Test key button for automatic IBKR sign-in.** In **Settings**, on your IBKR Live trader, next to **Save key**, there is now a **Test key** button. It shows the 6-digit code your authenticator setup key makes right now and how many seconds it stays valid. Compare it with the code in your authenticator app: if they match, the key is right; if not, paste the key again. To test a key you already saved, leave the box empty and enter your vault passphrase. The code is worked out in your browser only; your key is not saved or sent anywhere by this button.

---

## 2026-09-29 — Compare Rules: earlier days are being added

### Added

- **Compare Rules history now reaches back to early September.** The **Comparison day** menu on the Compare Rules page is getting three earlier days: 18, 11 and 4 September 2026, one week apart. They are computed in the background when no other comparison is running, so they appear one at a time over the next few days, newest first. Each one shows the table as it would have looked on that day, with all six periods ending on that day. The **Latest** results are not affected while this runs.

---

## 2026-09-29 — "Money you put in" no longer counts a new account's starting money twice

### Fixed

- **A new Alpaca account could show double the money you put in.** On your trader's page, an account that started with $10,000 could show **Money you put in** as $20,000 and a **Profit** of about -$10,000, although nothing was lost. The starting money was counted once as your starting capital and again as a deposit. It is now counted once. Accounts that showed the wrong numbers corrected themselves; nothing to do. Your trades and your account at the broker were never affected; only the numbers on the page were wrong.

---

## 2026-09-29 — Compare Rules keeps every day's results

### Added

- **See how the rules ranked on earlier days.** The Compare Rules page now keeps each trading day's comparison. A **Comparison day** menu at the top lists every saved day, newest first; **Latest** is the default. Pick a day and select **Show** to see that day's table as it was: ranks, returns, drawdown, trades, win rate and all six periods. A note says which day you are viewing, with a link back to the latest results. Sorting and the "library only" view keep the day you chose. Your own rules appear on a past day only if they were compared that day, and only you see them. Results for your own rules are kept for about the last week of trading days, so older days show the library rules only. History starts with 25 September 2026. ([#239](https://github.com/guymaim/robo-trader/issues/239))

---

## 2026-09-28 — Your own rules have one name everywhere; broker emails reach everyone

### Fixed

- **Your own rule files now show the same name on every page.** In New Run and in the rule lists in Settings, a rule you saved could show up under a long generated name (for example `qqq-above-ma20-trailing-10-tp-30-hold-60d-my-rule`) while My Rules showed the name you typed. Every page now shows the name you typed. A rule saved before My Rules existed shows its file name. ([#222](https://github.com/guymaim/robo-trader/issues/222))
- **The "your broker needs you to act" email now reaches you even if you turned alert emails off.** It is about your account, not a trading alert, so it no longer depends on that setting. Other alert and summary emails still follow your setting.

### Changed

- **Saving broker logins is better protected.** When you save broker credentials in Settings, the key your browser makes is now sealed before it leaves your browser, so nothing between you and the app ever holds your encrypted login and its key together. Logins you saved before keep working; nothing to do.

---

## 2026-09-28 — Equity chart starts where your account started

### Fixed

- **The equity chart on your trader's page now starts at the amount you started with.** For a new account, the chart used to start at the first day's closing value, so a gain or loss on the first day was missing from the chart and from the QQQ and SPY lines next to it. For example, an account that started with $10,000 and closed its first day at $10,412 showed a chart starting at $10,412, which made the account look like it was losing money when it was actually up. The chart and the QQQ and SPY lines now start at $10,000. Your Total return and other numbers were already right and do not change.
- **No more weekend points on the equity chart.** The chart showed Saturday and Sunday as extra days, and sometimes a move on Sunday evening even though the market was closed. Weekends are now left out. A weekend day when you added or withdrew money is still shown.

---

## 2026-09-28 — Email when your broker needs you to act

### Added

- **An email when your broker blocks your trader's orders for a reason only you can fix.** For example, Interactive Brokers sometimes asks you to confirm your account with a code they emailed you before they accept orders. Your trader now sends you one email that explains what happened, lists the orders that were not placed, and gives the steps to fix it, with a button to your trader's page. You get at most one such email per problem per day, even if many orders are blocked. It covers Interactive Brokers account verification, missing trading permissions, account restrictions and missing market data subscriptions, and Alpaca accounts that are blocked from trading.

### Fixed

- **An Interactive Brokers trader now tries again the same day after rejected orders.** Before, orders that Interactive Brokers rejected were still counted as using your cash, so the trader bought nothing more that day even after you fixed the problem. Now it retries on its next check.

---

## 2026-09-28 — My Rules: make, change and compare your own rule files

### Added

- **My Rules page.** A new **My Rules** link in the menu lists your own rule files next to the library rules. For each rule you can turn it on or off, change it, make a copy, read its explanation, run a simulation, download it, rename it or delete it.
- **Rule editor.** Choose **New rule** and start from an existing rule (a library rule or one of your own), from scratch, or from a JSON file you upload. Then edit in one of two ways, and switch between them at any time without losing anything:
  - **Guided steps** show one group of settings at a time (for example Trend, Pullback, 52-week position, AVWAP, Entry), in four parts: what to buy, when to buy, how much, and when to sell.
  - **Full editor** shows every setting on one page, and has a **JSON** tab with the same rule as text.
- **An explanation for every setting.** Select the **?** next to a setting to read what it does, the value the rule started with, and the values that are allowed.
- **Review before you save.** The last step shows what the rule does, lists every value you changed, and points out things worth a second look, such as a rule with no stop or a very large position size. A rule with a value outside the allowed range cannot be saved.
- **Compare now.** Runs your rule in the same six periods as the Compare Rules page (1, 3, 6, 12, 18 and 24 months). It takes about 40 minutes; results appear one by one.
- **Your rules on the Compare Rules page.** Every rule of yours that is turned on is listed on the Compare Rules page, marked **Yours**, and ranked together with the library rules. Only you see your rules. They are refreshed after each trading day; until a new result is ready the earlier one is shown, marked ⟳ with its date. **Show the library only** gives the ranking without your rules.

### How it works

- **You can save as many rules as you like (up to 100) and have 3 turned on at a time.** Only a rule that is turned on can be picked for a simulation or a trader, and only those are compared. If you need more than 3, ask us.
- **A rule that a trader is using is locked.** It cannot be changed, turned off or deleted while a trader uses it or while it is your default rule. To change it, make a copy, change the copy, and pick the copy in the trader's settings. The trader then starts a fresh scan with the new rule.
- **Using your own rule with real money needs two extra steps.** The rule's comparison must be finished for the rule as it is now, and you type LIVE after seeing its results. Paper traders can use any rule of yours that is turned on.
- **New Run keeps working as before.** When you run a simulation with edited criteria, the rule is kept in My Rules, turned off. Running the same edit again uses the same rule, so your list does not fill up with copies.
- **Simulated results are hypothetical.** Past simulated results do not predict future results. A rule that is changed again and again until it looks best on past periods often does worse afterwards.

### Fixed

- **Explain rule now works for your own rule files.** It used to answer "Unknown rules file" for a rule you had saved or uploaded.
- **The Compare Rules page no longer slides sideways** while results are still being computed.
- **Rule names are checked more carefully** ([#223](https://github.com/guymaim/robo-trader/issues/223)). A name can no longer contain hidden characters, such as text-direction marks or zero-width spaces, that could make it look like a different name. Each of your rules now needs its own name (upper and lower case count as the same); if you pick a name you already use, you are asked to choose another. Copies get a number, for example "my rule copy (2)". Names in any language still work. Rules you saved earlier keep their names; hidden characters are left out when they are shown, and two rules with the same name are shown as "name" and "name (2)".

---

## 2026-09-28 — Simulations include trading costs by default

### Changed

- **New simulations now use the Realistic execution model by default** ([#191](https://github.com/guymaim/robo-trader/issues/191)). A new run includes a trading cost on every buy and sale and follows the live trader's timing: no re-buy of a stock for a few days after selling it, buys that had no cash tried again later that day, and take-profit sales at the price the trader's regular checks would see. Results will usually be lower than before and closer to what a real account gets. You can still choose **Idealised** (exact fills, no costs) on the New Run page. A rule file with its own execution settings keeps them. Runs you made earlier are unchanged and still show which model they used; compare runs only when the model matches.

---

## 2026-09-28 — Simulation Runs shows your own runs

### Fixed

- **The Simulation Runs page now lists only your own runs** ([#213](https://github.com/guymaim/robo-trader/issues/213)). Admin and support accounts used to see every user's runs mixed in with their own, with no way to tell whose was whose. Everyone now sees their own runs by default. Admin and support accounts can switch to **All users' runs**, which adds an **Owner** column (support accounts see the email partly hidden, for example `m***@example.com`; runs with no recorded owner show "unknown").

## 2026-09-28 — Clearer period names on the rules comparison page

### Changed

- **The rules comparison page names its periods by length.** The columns now read 1 month, 3 months, 6 months, 12 months, 18 months and 24 months, instead of names like "Last year" and "Last 1.5 years".

---

## 2026-09-28 — Changing a trader's rule file takes effect at the next open

### Fixed

- **A new rule file now picks the next buys right away.** When you changed a trader's rule file after that day's scan had already run, the trader kept the old rule file's candidate list. It correctly refused to buy from it, but it then bought nothing until the next evening's scan, so the new rules only started buying a trading day later. Now, when the rule file changes, the old list is deleted and a new scan with the new rules runs right away, so the next open buys from the new rules' list ([#217](https://github.com/guymaim/robo-trader/issues/217)).

---

## 2026-09-28 — Admin pages fit on phones

### Fixed

- **The admin pages no longer slide sideways on a phone.** Wide tables on the admin pages now scroll inside their own box, and the rest of the page stays in place ([#189](https://github.com/guymaim/robo-trader/issues/189)).

---

## 2026-09-28 — Support staff no longer see your email address in trader log links

### Fixed

- **Your email address is better protected from our read-only support role.** Since [#187](https://github.com/guymaim/robo-trader/issues/187), support staff with read-only access see email addresses shortened (for example `g***@gmail.com`). The trader log list and log pages still showed each user's full address in the log names, page titles and links. They now show the shortened address, and links to a trader's log no longer contain any part of your email address. The same applies to the read-only view of your trader. ([#212](https://github.com/guymaim/robo-trader/issues/212))

---

## 2026-09-28 — Compare Rules: realistic costs, live updates, sorting and column choice

### Changed

- **The Compare Rules page now fills in as it goes.** Each rule's result for each period appears as soon as its simulation finishes, instead of after all of them. Until a new result is ready, the page shows the previous one, marked ⟳ with the date it is from, and a line at the top shows how many simulations have finished. A period is ranked again once all of its results are in. The page reloads by itself every minute while it updates, and you can turn that off.
- **Results come in about twice as fast,** because two simulations now run at the same time.
- **Interruptions no longer lose work.** If a comparison is interrupted, it continues where it stopped instead of starting over. A new comparison starts once per trading day.
- A result that failed in the new comparison is shown as failed. The page never shows an older number in its place.
- **Trading costs are now included.** Every simulation on the page uses the **Realistic** execution model from Run Simulation: an estimated 0.08% slippage on each buy and sale, the live trader's wait before buying a stock again, its retry of buys that had no cash, and take-profit sales at the next price check instead of exactly at the target. Returns are therefore lower than before, and earlier (no-cost) results are not mixed in. These are still estimates, not live results.
- **Sort by any column.** Select any column heading to sort the table by it, and select it again to reverse the order.
- **Choose your columns.** Hide the columns you don't need (for example max drawdown, trades or profit factor). They are hidden in every period at once, and your choice is remembered in your browser.

Simulated results are hypothetical and do not guarantee future results.

---

## 2026-09-27 — Realistic simulations match the live trader more closely

### Fixed

- **Realistic runs no longer make buys a real account never makes.** When a day's buys at the open had no cash, the simulator used to try them again at the next two opens, even after a day that had already bought something and even on days the market filter said not to buy. The live trader does something different: it tries again only when no buy at the open went through, and it buys the same day, once that day's sales free cash. The simulator now does the same. On one account this difference had made the simulation look about 7 percentage points better than the real result. Realistic runs made before today used the old behaviour, so a rerun can show a different result. Idealised runs, and the Compare Rules page, are unchanged.
- **Accounts that trade whole shares only no longer retry a take-profit sale of less than one share on every check.** If the take-profit sale works out to less than one whole share, nothing is sold, and the next take-profit target is measured from the current price. The simulator's whole-shares option now does the same.

### Added

- **An optional fee per order in the simulator.** A rule file can set a flat fee in dollars on every buy and sale (`execution.order_fee_usd`). Interactive Brokers charges about $1 per order; Alpaca charges none. The default is no fee.

Simulated results are hypothetical and do not guarantee future results.

---

## 2026-09-27 — Traders only buy from their own rule file's picks

### Fixed

- **A trader's first buys could follow the wrong rule file** ([#207](https://github.com/guymaim/robo-trader/issues/207)). If you set up a trader and then switched its rule file within the first minutes, its first scan could still run with the old rule file, and the trader bought that file's picks at the next open. Now the old first scan is cancelled and a new one runs with the new rules. The trader also checks which rules produced each list of stocks, and it does not buy from a list made with different rules. It waits for the next scan instead.

---

## 2026-09-27 — Compare every library rule, updated nightly

### Added

- **A new "Compare Rules" page** ([#198](https://github.com/guymaim/robo-trader/issues/198)), linked next to Runs. Every rule in the library is simulated over the last 30 days, 3 months, 6 months, 1 year, 1.5 years and 2 years, and ranked by return in each period. Rules with the same result share a rank. The page updates automatically every weeknight after the market closes, and rules added to the library later are included automatically.
- The figures come from the same simulator as **Run Simulation**, with $10,000 starting capital, all tickers, and the **Idealised** execution model (no trading costs). Real trading costs lower returns, sometimes by a lot over long periods ([#191](https://github.com/guymaim/robo-trader/issues/191)).
- If a simulation fails, the page shows it as failed. It is never left out or shown as zero. Each result lists the settings it ran with (the rule file's version and take-profit trigger), and a rule is marked **changed** when those differ from the previous night.
- The page is for information only. It does not change your default rule or any trader.

Simulated results are hypothetical and do not guarantee future results.

---

## 2026-09-27 — Traders: time stop support, and Robo Trader wording in trader logs

### Added

- **The live traders can now apply the optional time stop** ([#196](https://github.com/guymaim/robo-trader/issues/196)), using the same calculation as the simulator: once a position has been held the set number of trading days, it is sold near the close if its gain is still below the threshold. It is off unless a trader's rule file turns it on, and none of the 13 library rules do, so no existing trader changes behaviour.

### Fixed

- **Trader logs and messages say Robo Trader** ([#115](https://github.com/guymaim/robo-trader/issues/115)). The startup message about who runs the daily scan, the trader help text and the missing-keys error no longer use the old internal name. Older setting names keep working.

---

## 2026-09-27 — Simulator: realistic trading costs, and clearer results

### Added

- **A "Realistic" execution model in the simulator** ([#191](https://github.com/guymaim/robo-trader/issues/191)). On the new-run page, choose **Execution model: Realistic** to include what a real account pays and does: a 0.08% cost on every buy and every sale, no re-buy of a stock for 3 trading days after selling it, buys that had no cash retried for 2 more days, and an intraday take-profit that sells at the price the trader's regular checks would see (a brief touch of the target can be missed). The default is still **Idealised** (every order fills at the exact price with no costs), so earlier results do not change. Idealised results are usually higher than a real account gets.
- **The new-run page lets you pick the take-profit trigger** (at the close, or intraday when the price touches the target).
- **Results now say how orders were filled.** Each result page shows the take-profit trigger and the execution model, with the assumptions in plain words, and a **win rate per position**, where a position's partial take-profit sales and its final sale count as one trade. The existing win rate counts every partial sale as its own win, so it reads higher.
- **Run history has a "Fills" column** and marks runs of the same rule file as **not comparable** when they used a different take-profit trigger or execution model. Two runs of the same rule can differ a lot for this reason alone.
- **Optional time stop for rule files** ([#196](https://github.com/guymaim/robo-trader/issues/196)). A rule file can now sell a position that has gone nowhere: after a set number of trading days, it is sold at the close if its gain is still below a threshold. It is off unless a rule file turns it on, and none of the 13 library rules do. Trade lists show these sales as "time stop". Test versions of the Balanced long hold and Aggressive mid hold quick profit rules with a 10-day time stop exist for paper testing; they are not in the rule library yet.

Simulated results are hypothetical and do not guarantee future results.

---

## 2026-09-27 — Screen-reader labels in Settings

### Fixed

- **Every field in Settings now has a name that screen readers read out**, including the Interactive Brokers authenticator setup key and its passphrase, which previously only had grey hint text. We also added a check that keeps every form field on the site labelled. The accessibility statement is unchanged for now; an updated version is being reviewed. ([#116](https://github.com/guymaim/robo-trader/issues/116))

---

## 2026-09-27 — Rule names show their rank

### Changed

- **Each rule's name now starts with its rank number**, for example **04. Balanced long hold** instead of just "Balanced long hold". The number (1 to 13) is the rule's place in our fixed historical test, which already set the order of the list; now you can see it in the name too, in the simulator, in trader settings and everywhere else a rule name appears. The rules themselves and your traders' settings are unchanged. Past results are not a promise of future returns. ([#199](https://github.com/guymaim/robo-trader/issues/199))

---

## 2026-09-27 — See when a trader could not be started

### Added

- **Your trader card now tells you when the system could not start your trader.** Before, if something on our side stopped a trader from starting, the card simply never showed it running and you had to ask us. Now the home page card shows **not starting** with a short explanation in plain words, and the same message appears on the trader's panel in Settings. If a settings change could not be applied yet, the card shows **update failed** and your trader keeps running with its previous settings. We keep retrying automatically, and our admins see the same error, so you do not need to do anything. The message disappears as soon as the trader starts.

---

## 2026-09-27 — Rule files have plain-language names, and everyone gets the same 13

### Changed

- **Rule files are renamed so the name says how the rule behaves.** Names combine how long a rule holds a stock (short, mid, long) with its style, for example `balanced-long-hold` or `aggressive-mid-hold`. Rules that switch approach with market conditions start with `adaptive`. The rule you knew as robo_trader07 is now **aggressive-mid-hold-quick-profit**, and robo_trader09_balanced is now **balanced-long-hold**. Your traders keep the same rule as before, under a new name.

  | Was | Now |
  |---|---|
  | robo_trader07 | aggressive-mid-hold-quick-profit |
  | robo_trader09_balanced | balanced-long-hold |
  | robo_trader16_t30_voltarget_maxhold20_tp25 | adaptive-risk-scaled-short-hold-early-profit |
  | robo_trader16_t30_voltarget_maxhold20 | adaptive-risk-scaled-short-hold |
  | robo_trader16_t30_voltarget | adaptive-risk-scaled-mid-hold |
    | robo_trader16_balanced | balanced-mid-hold |
  | robo_trader06_optimized | balanced-mid-hold-patient-exit |
  | robo_trader11_ml_adaptive | adaptive-learned-modes |
  | robo_trader12_tier_adaptive | adaptive-market-modes |
  | robo_trader15_qqq_ml_floor | adaptive-learned-modes-cash-invested |
  | robo_trader05_sweep_best | diversified-long-hold |
  | robo_trader04_leaders | cautious-long-hold-large-companies |
  | (an optimizer scratch file) | concentrated-mid-hold |

- **All 13 rules now take profit intraday.** A take-profit target is triggered as soon as the day's price reaches it, instead of at the close. This was already how the former robo_trader07 worked; the other rules used the close. Results in the simulator for those rules change accordingly, and traders on balanced-long-hold or adaptive-risk-scaled-short-hold-early-profit will see profits taken at the target during the day.
- **All 13 rules are available to every user** in the simulator and in trader settings. The simulator's default rule is still the former robo_trader07, now **aggressive-mid-hold-quick-profit**. This is a default only. Past simulated results are hypothetical and do not guarantee future results.
- **Your existing runs and defaults follow the new names.** Old runs show the new rule name, and a default rule you had chosen keeps pointing at the same rule.
- **The rule list is now the same 13 rules for everyone.** Any other rule was removed, including rules users created themselves. A rule that a trader is still using was not removed.

---

## 2026-09-26 — Guided setup picks a rule file, phone layout, daily summary email and fixes

### Changed

- **The simulator now starts from robo_trader07 by default.** The site-wide default rule file used to be robo_trader09_balanced; it is now robo_trader07, which did better in roughly four out of five historical test periods (2017–2026) and had smaller drawdowns. This is a default only: robo_trader09_balanced is still available in the rule picker, and existing traders keep their current rules. Past simulated results are hypothetical and do not guarantee future results ([#192](https://github.com/guymaim/robo-trader/issues/192)).
- **Guided Alpaca setup now asks for a rule file.** A new step lets you pick the rules your trader follows, with **robo_trader07** preselected. A new trader is never left without rules: if you skip the choice, it uses robo_trader07. Existing traders keep their current rules ([#164](https://github.com/guymaim/robo-trader/issues/164)).
- **Guided setup asks whether to allow automatic buy and sell.** On **paper** accounts it is allowed by default, so trades run without asking you first. On **live** (real-money) accounts it still starts off, and every existing live safeguard stays in place. An admin decision for your account still wins ([#165](https://github.com/guymaim/robo-trader/issues/165)).

### Fixed

- **The daily summary email now reports the right trading day.** An email sent before the market opened, or on a weekend, used to summarize the new day before anything had traded, so it showed no buys, no sells and a 0% day. It now summarizes the last completed trading session, with that session's date, buys, sells and day P/L ([#175](https://github.com/guymaim/robo-trader/issues/175)). After the close, the email now arrives at about 16:15 New York time instead of 16:00.
- **The daily summary email now lists the day's buys and sells.** Some summary emails showed "Buys today (0)" and "Sells today (0)" even on days the trader had traded, because the email could not read that day's trades. It now shows every buy and sell from the session. Emails already sent are not changed.
- **The trader page shows P/L in % on phones.** On narrow screens the Portfolio table only showed P/L in dollars. The percentage now appears under the dollar amount for both unrealized and today's P/L ([#174](https://github.com/guymaim/robo-trader/issues/174)).
- **Accounts with a non-English name (for example Hebrew) can now start their trader.** A display name written entirely in a non-Latin alphabet used to stop a new trader from starting at all. Your name still appears as you wrote it; the trader now starts normally.
- **Negative amounts read -$76.72** instead of $-76.72 on the trader, home and run pages.
- **Run pages fit on a phone screen.** A long run name no longer pushes the whole page sideways, and the trades table scrolls inside its own box ([#162](https://github.com/guymaim/robo-trader/issues/162)).
- **The "admin only" error page shows clean text** instead of garbled characters ([#161](https://github.com/guymaim/robo-trader/issues/161)).
- **Opening `/static/` no longer loops** between redirects; it shows a plain "not found" page ([#170](https://github.com/guymaim/robo-trader/issues/170)).

### Security

- **Visits over plain `http://` are sent to `https://`** with the same address, so sign-in and consent pages are not used over an unencrypted connection ([#169](https://github.com/guymaim/robo-trader/issues/169)).

---

## 2026-09-25 — Sign in without ticking the agreement boxes again

### Changed

- **Signing in no longer asks you to tick the five agreement boxes every time** (18+, Terms of Use, Privacy Policy, Risk Disclosure and Investment Disclaimer, and "software, not a broker"). You agree once when you **sign up**, and that agreement is kept on record. Signing in is now a single click on **Sign in with Google**. ([#171](https://github.com/guymaim/robo-trader/issues/171))
- **Signing up works as before:** all five boxes are still required.
- **You are only asked again when a document changes.** If we update the Terms, Privacy Policy, Risk Disclosure or Investment Disclaimer, you will see a reminder and be asked to confirm the new version.
- **If we have no record of you agreeing,** for example an account created before we kept these records, you are asked to confirm the agreements once before you can continue.

---

## 2026-09-25 — Interactive Brokers live: optional automatic sign-in, no more phone approvals

### Added

- **IBKR live can now sign in by itself.** Until now, every time the Interactive Brokers connection restarted (updates, restarts, and IBKR's own weekly re-authentication) you had to approve an IB Key notification on your phone, and trading waited until you did. You can now give Robo Trader your IBKR **authenticator setup key** instead, and it completes the sign-in for you. Set it up from **Settings → IBKR Live (real money)**; the new guide **Help → IBKR automatic sign-in** walks through enabling Mobile Authenticator at IBKR step by step.
- **It is optional and you stay in control.** A checkbox on the IBKR Live card turns automatic sign-in on or off. Off is exactly today's behaviour (IB Key on your phone). Turning it off restarts the connection and asks for one approval on your phone. IB Key stays active at IBKR as your backup, and the guide tells you to keep the same key in your phone's authenticator app.
- The setup key is encrypted in your browser together with your IBKR password, with your vault passphrase. Robo Trader's servers cannot read it.
- **Change the key without retyping your login.** The key has its own **Save key** and **Remove key** buttons under your IBKR Live credentials, next to the automatic sign-in checkbox. Enter the same vault passphrase you saved your login with; your login details stay as they are. Saving your login again keeps an existing key too.

### Notes

- Available for **IBKR Live** only. Paper accounts do not ask for a second factor.
- IBKR offers Mobile Authenticator by region. If your Secure Login System page shows only IB Key, the guide includes the message to send IBKR support to have it enabled.
- If automatic sign-in fails three times in a row, Robo Trader stops trying, alerts you, and the status page shows what happened. Trading resumes once you fix the key or switch back to IB Key.

---

## 2026-09-25 — Add money to your broker account; returns that ignore deposits and withdrawals

### Added

- **You can now add money to your broker account, and Robo Trader notices by itself.** Deposit at Alpaca or Interactive Brokers as usual; nothing to set up or type in. The new cash is used to **buy more stocks** at the next scans. A deposit never makes the trader **sell** anything.
- **Total return is now a true time-weighted return (TWR),** the same measure IBKR PortfolioAnalyst shows. Deposits and withdrawals no longer count as profit or loss. A **TWR | MWR** switch on the trader page also shows the money-weighted return (your own experience, which depends on *when* you added money). Hover over "Money you put in" to see each deposit and withdrawal.
- **"Money you put in" and "Profit $"** on the trader page: what you put in (starting money + deposits − withdrawals) and what the strategy actually made on it.
- **Deposits and withdrawals are marked on the equity chart** (⬆ / ⬇), and the QQQ / SPY comparison lines receive the same money on the same day, so the comparison stays fair.
- **Each recorded deposit or withdrawal sends you a short notification.** If it wasn't one (for example a large dividend), an admin can mark it "not a deposit" and the numbers correct themselves.
- **Non-USD deposits (for example ₪ at Interactive Brokers):** the trader page shows a banner and you get one alert a day until you convert the money to USD in IBKR (Trade → FX). Robo Trader never trades currencies itself, and shekels are never counted as money it can spend.

### Changed

- **Today's P&L excludes money you added or withdrew today.** Before, a $10,000 deposit showed up as "+$10,000 today" for one day.
- **A withdrawal no longer looks like a loss.** It cannot trip the daily-loss stop, cannot make the trader cut position sizes as if the market had crashed, and no longer wipes the equity chart's history (a large withdrawal used to be mistaken for a paper-account reset).
- **The daily summary email** now shows Total return (TWR, with MWR), Profit and Money put in.
- **"Total P&L" no longer drifts after about 60 days.** The starting value of your account is now recorded once, so the number keeps measuring from your real start. Traders running longer than that may see a corrected figure.
- The help pages for Alpaca and Interactive Brokers have a new **"Adding money"** section.

### Notes

- **Transferring shares in (ACATS) is not a deposit.** Shares you transfer in are adopted and managed by the trader, including being sold under the strategy's exit rules. Ask before transferring if that is not what you want.
- The starting value recorded for existing traders is the same figure the page showed before, so your totals do not jump at the switch.

---

## 2026-09-24 — Two-factor setup keeps the code you scanned

### Fixed

- **Setting up two-factor authentication (2FA) no longer breaks if you leave the page halfway through.** Before, if you scanned the barcode in your authenticator app, left Settings → Security before typing the 6-digit code, then came back and clicked **Show QR** again, you got a new barcode. The code from your first scan was then rejected as invalid. Now, for 10 minutes, you get the same barcode again, so the code from your app works. After 10 minutes you get a new barcode; scan that one instead.

---

## 2026-09-24 — Code to turn off a trader, table and Settings fixes

### Changed

- **Turning off a trader now asks for your authenticator code, like turning it on.** If you've set up two-factor authentication (2FA), **Disable** on a trader's Settings card asks for your code. If you haven't set up 2FA, you can still turn a trader off without one. Turning on *approval before buying* never asks for a code, so you can always stop new automatic buys right away. ([#103](https://github.com/guymaim/robo-trader/issues/103))

### Fixed

- **Buttons in sorted tables work again.** After you sorted a table, for example Admin → Users, clicking buttons in its rows (such as **Options**) sometimes did nothing. Sorting no longer interferes with clicks.
- **Escape closes a user's Options panel** on Admin → Users right after you open it.
- **The trader name box in Settings shows the name you can edit.** On traders you hadn't renamed yet (for example Interactive Brokers traders), the box showed the default name as grey hint text that disappeared when you typed. It now holds the actual name, so you can select and change it. ([#148](https://github.com/guymaim/robo-trader/issues/148))

---

## 2026-09-24 — Sector colors in your portfolio, clearer performance notes, simulations wait in line

### Changed

- **The Portfolio table shows each position's sector as a colored square.** The color matches that sector's slice in the Sector allocation chart on the same page, so you can see at a glance which positions make up each slice. Hover over the square (or use a screen reader) to get the sector name. ([#152](https://github.com/guymaim/robo-trader/issues/152))
- **Simulations wait in line instead of being refused.** If someone else's simulation is already running when you start one, yours is now *queued*: the run page shows its place in line and it starts on its own when a slot frees up. You can cancel a queued run. You can still run one simulation of your own at a time. ([#110](https://github.com/guymaim/robo-trader/issues/110))

### Added

- **A performance note next to your account figures.** Your desk and each trader page now say plainly what the equity, P&L, return and "vs QQQ / SPY" numbers are: they come from your broker account (paper accounts use simulated fills, not real money), they may be delayed, and past performance does not indicate future results. Simulation results keep their "Hypothetical performance" note. ([#89](https://github.com/guymaim/robo-trader/issues/89))

---

## 2026-09-24 — Find your saved rules in the rule list

### Fixed

- **Rules you saved yourself now always show the name you saved them under.** Before, a rule you edited and saved (for example `custom_20260923_2025`) could show up in the New run, Settings and admin rule lists with only a name made from its settings, like `qqq-above-ma20-trailing-20-tp-30-hold-60d`. That made it hard to find. Now your saved name is always added at the end: `qqq-above-ma20-trailing-20-tp-30-hold-60d-custom-20260923-2025`. Built-in rules keep their current names.

---

## 2026-09-23 — Two-factor only for live trading, "Sign out everywhere", Sell now status, nicer daily emails

### Changed

- **Two-factor authentication (2FA) is now optional for signing up and for paper trading.** You're only required to set up an authenticator app before live (real-money) trading: connecting, enabling or approving buys on a live Interactive Brokers trader. Without 2FA you'll see "2FA required before live trading" with a link to set it up. Stopping a trader or selling is never blocked. ([#144](https://github.com/guymaim/robo-trader/issues/144))
- **The daily summary email now arrives as a formatted email with the Robo Trader logo.** Your key figures are at the top, with tables of the day's buys, sells and open positions, and a button to that trader's own status page (including your second and third Alpaca traders). The log-style first line is gone. Warning and error alert emails get the same look. ([#133](https://github.com/guymaim/robo-trader/issues/133))
- **"Save name" moved to Settings.** Rename a trader from its card in Settings (each Alpaca trader has its own field). The trader page still shows the name and has a "Rename in Settings" link. Names you already saved are unchanged. ([#148](https://github.com/guymaim/robo-trader/issues/148))

### Added

- **Guided, step-by-step Alpaca setup.** New to Alpaca? Choose **Start guided setup** on the Alpaca card in Settings, or on your desk when you have no traders yet. Robo Trader walks you through it one step at a time: it opens Alpaca's sign-up, login and paper dashboard pages for you and waits while you finish each one, then you click a button to continue. The last steps let you paste your API keys (encrypted in your browser, as in Settings) and turn the trader on. It shows your progress, lets you go back a step, and remembers where you stopped. It also warns you if you paste a live (real-money) key instead of a paper key. The written Alpaca guide is still available. ([#151](https://github.com/guymaim/robo-trader/issues/151))
- **"Sign out everywhere else"** in Settings → Security signs out all your other sessions (other browsers and devices) and keeps you signed in on this one. Replacing your authenticator app also signs out your other sessions.
- **See what happened after Sell now.** After you press Sell now, the trader page shows the request as *Queued*, with roughly how long until the trader's next check. It then shows *Sold* or *Refused by broker* with the broker's reason. The button is disabled while a request for that stock is waiting, so it can't be sent twice.
- **See each position's trailing-stop price.** The Portfolio table on the trader page has a new **Trail stop** column. It shows the price at which the trader would sell each stock if its trailing stop is hit, using the same calculation the trader uses. Hover over it to see how far that is below the current price. The stop moves up as the stock makes new highs and never moves down. ([#149](https://github.com/guymaim/robo-trader/issues/149))
- **Admins can add rule files by picking them from a list.** They tick rule files from the server's rules folder (with readable names) instead of typing file names. ([#142](https://github.com/guymaim/robo-trader/issues/142))
- **Admins can allow or block live (real-money) trading per user.** A user who is blocked sees a clear message in Settings and cannot connect or enable a live trader. Paper trading, stopping a trader and selling are never affected, and a live trader that is already running is not stopped. Accounts created before 24 September 2026 stay allowed; **new accounts start with live trading off** until an admin allows it. ([#146](https://github.com/guymaim/robo-trader/issues/146), [#155](https://github.com/guymaim/robo-trader/issues/155))
- **Admins can limit how many Alpaca traders a user may have.** When you reach the limit, Settings explains why you cannot add another. Lowering a limit never stops or removes traders you already have. Accounts created before 24 September 2026 keep their current limit; **new accounts start with 1 Alpaca trader** until an admin raises it. ([#147](https://github.com/guymaim/robo-trader/issues/147), [#155](https://github.com/guymaim/robo-trader/issues/155))
- **Admin users table: per-user settings behind an "Options" button.** Enable or disable, reset 2FA, remove, automatic-trading and live-trading permissions and the Alpaca trader limit are all changed from a per-user Options panel. The table itself shows the current values. ([#156](https://github.com/guymaim/robo-trader/issues/156))

### Notes

- If an administrator resets your 2FA, you're signed out everywhere. Paper trading keeps working, and live trading waits until you set 2FA up again.
- The new email look and the Sell now result details reach Interactive Brokers traders with their next update.

---

## 2026-09-22 — Take profit as soon as the target is reached

### Changed

- **Take-profit now happens during the trading day, not only near the close.** For traders on the "450% optimizer winner" strategy, when a position reaches its take-profit target (+30%), the trader sells the take-profit portion at its next price check (within about 5 minutes) instead of waiting for the end of the day. Trailing stops and other exits work the same as before.

### Notes

- This uses the trader's regular price checks. No take-profit order is left waiting at the broker.

---

## 2026-09-22 — More reliable overnight Interactive Brokers reconnects, and a manual reconnect option

### Fixed

- **Interactive Brokers gateways now reconnect automatically most nights**, instead of occasionally needing someone to notice and step in the next day. This affects both live and paper IBKR traders.

### Added

- **Restart your Interactive Brokers connection yourself.** In Settings, next to **Restart trader**, there's now a checkbox — "Also restart the connection to Interactive Brokers" — for when a gateway looks stuck or disconnected. It briefly interrupts and reconnects just the broker connection, separately from the trader itself.

### Notes

- Interactive Brokers still requires you to approve a login on your phone roughly once a week, for their own security reasons — this is unchanged and unrelated to the fix above.

---

## 2026-09-22 — Up to 3 Alpaca paper traders per account

### Added

- **Run up to three Alpaca paper traders.** In Settings, use **Add Alpaca trader** to add a second or third Alpaca trader next to your first. Each trader has its own card, its own trading settings and its own start/stop, and its own status page. ([#119](https://github.com/guymaim/robo-trader/issues/119))
- **Each trader needs its own Alpaca paper API keys.** Create a separate Alpaca paper account (with its own keys) for every trader you add.
- **One paper account, one trader.** An Alpaca paper account should be linked to only one Robo Trader trader, so two traders never trade the same account. If you try to save API keys that are already used by another of your traders, you get a clear message instead. Removing a trader frees its keys and its slot for reuse.
- **Limit of three.** At three traders the **Add Alpaca trader** button is disabled and explains why; remove one to add another.
- Paper accounts only, as before. Your existing Alpaca trader is not touched: it stays your first trader with the same settings, and Interactive Brokers traders work exactly as they did.

---

## 2026-09-21 — English-only site and a currency of your choice

### Added

- **Choose your display currency.** Trader pages show your values in US dollars and, underneath, converted to a currency you pick: on the trader status page (next to the exchange-rate note) or in Settings. Israeli shekel stays the default, so nothing changes until you choose another. Available: ILS, EUR, GBP, CAD, AUD, CHF, JPY, SEK, NOK, DKK, PLN, INR, SGD, HKD, MXN, BRL, or USD only (no conversion). The choice is remembered in your browser, so it applies per browser rather than per account.
- Converted amounts come with a note that the rate is indicative (from Yahoo Finance, with its time) and for information only. US dollars remain the main value everywhere.

### Changed

- **The website is now English only.** The English / Hebrew switch (added earlier today) has been removed, along with the Hebrew text on the rule explainer.

---

## 2026-09-20 (evening) — Invitations, two-factor sign-in, accessibility, better emails

### Added

- **Invite by email:** admins can invite someone by email. They get a welcome message and, when they sign in with Google using that address and accept the agreements, their account is already approved. Invitations expire after 14 days. ([#120](https://github.com/guymaim/robo-trader/issues/120))
- **Two-factor sign-in for new accounts:** accounts created from 21 September 2026 must set up an Authenticator app before using Robo Trader; existing accounts see a reminder to do the same. Changing your Authenticator now asks for a code from the current one, and administrators can reset two-factor authentication for someone who lost their device. ([#112](https://github.com/guymaim/robo-trader/issues/112))
- **Automatic trading permission:** new accounts start without permission to switch their trader to automatic buying and selling; an administrator can allow it per person. Existing accounts are unaffected. Stopping a trader, requiring approval, selling now and restarting are never blocked. ([#107](https://github.com/guymaim/robo-trader/issues/107))

### Changed

- Emails from Robo Trader now have a consistent, branded layout with a logo, a button to the app and links to the privacy policy, terms and notification settings. ([#111](https://github.com/guymaim/robo-trader/issues/111))
- **Accessibility:** charts come with a written summary of the key figures and a data table; menus, sortable table headers, dialogs and run links work fully with the keyboard; text and field borders are easier to read, especially in the light theme; the live log viewer has a Pause button and no longer reads out every line by default. The accessibility statement now lists what was checked and what is still missing. ([#116](https://github.com/guymaim/robo-trader/issues/116))

---

## 2026-09-20 (later) — Runs, language, safer account actions

### Added

- **Stop a running simulation** from its run page, the Runs list or the New Run page. It is shown as "cancelled", not failed. ([#31](https://github.com/guymaim/robo-trader/issues/31))
- **English / Hebrew switch** in the header with a clear selected state; it translates the header menu, run pages and the rule explainer. ([#82](https://github.com/guymaim/robo-trader/issues/82))
- **Authenticator step-up:** enabling a trader or saving/deleting broker credentials now asks for your Authenticator code if you have one set up. Stopping a trader never needs a code. ([#103](https://github.com/guymaim/robo-trader/issues/103))
- New runs appear on the Runs page immediately, in-progress runs show live progress, and runs whose worker stopped are marked failed with an explanation. ([#30](https://github.com/guymaim/robo-trader/issues/30), [#32](https://github.com/guymaim/robo-trader/issues/32), [#33](https://github.com/guymaim/robo-trader/issues/33))

### Fixed

- **Runs and charts:** the Runs table fits on one screen and becomes cards on phones; trade prices are rounded consistently; gate-blocker and short-run charts are easier to read. ([#27](https://github.com/guymaim/robo-trader/issues/27), [#35](https://github.com/guymaim/robo-trader/issues/35), [#76](https://github.com/guymaim/robo-trader/issues/76), [#80](https://github.com/guymaim/robo-trader/issues/80), [#81](https://github.com/guymaim/robo-trader/issues/81))
- **Rules and explanations:** rule pages, run pages and logs use the Robo Trader name; New Run rule criteria have readable names and AND/OR is a dropdown; the Hebrew explainer displays mixed English and numbers correctly; explanations match each rule's actual values; rule names no longer show a backtest return figure. Simulated results now carry a short "hypothetical, not actual trading" notice. ([#28](https://github.com/guymaim/robo-trader/issues/28), [#43](https://github.com/guymaim/robo-trader/issues/43), [#47](https://github.com/guymaim/robo-trader/issues/47), [#55](https://github.com/guymaim/robo-trader/issues/55), [#59](https://github.com/guymaim/robo-trader/issues/59), [#69](https://github.com/guymaim/robo-trader/issues/69), [#79](https://github.com/guymaim/robo-trader/issues/79), [#89](https://github.com/guymaim/robo-trader/issues/89))
- **Set as default** now works for your own rules and is remembered per user; the max-hold "Decide" dialog no longer shows "held 4/0 trading bars"; the log page shows honest connection status and reconnects automatically. ([#36](https://github.com/guymaim/robo-trader/issues/36), [#121](https://github.com/guymaim/robo-trader/issues/121), [#45](https://github.com/guymaim/robo-trader/issues/45))
- **Sign-in:** the server now verifies that every agreement was accepted and records which version of each document you agreed to. Password and secret fields are in proper forms. ([#41](https://github.com/guymaim/robo-trader/issues/41), [#62](https://github.com/guymaim/robo-trader/issues/62))

---

## 2026-09-20 — Security, clarity and accuracy fixes from our QA pass

### Fixed

- **Sign-in and sessions:** Repeated failed sign-in or 2FA attempts are now temporarily blocked. Sign-out is now a button in the menu (a link can no longer sign you out by accident), redirects after sign-in only ever stay inside the app, and security contact details are published at the standard `/.well-known/security.txt` location. ([#83](https://github.com/guymaim/robo-trader/issues/83), [#84](https://github.com/guymaim/robo-trader/issues/84), [#85](https://github.com/guymaim/robo-trader/issues/85), [#86](https://github.com/guymaim/robo-trader/issues/86))
- **Privacy in what you see:** The header no longer shows a build code, and broker account numbers and server file paths are hidden from logs, activity and simulation progress. ([#26](https://github.com/guymaim/robo-trader/issues/26), [#44](https://github.com/guymaim/robo-trader/issues/44), [#67](https://github.com/guymaim/robo-trader/issues/67))
- **Simulations and results:** Short simulations no longer show an annualised return, and positions still open when a simulation ends are reported separately instead of counting as trades in win rate. The New Run form now stops you with a clear message when dates or capital are invalid, rules you save or upload are checked the same way, and the "Rules used" panel shows normal characters. ([#34](https://github.com/guymaim/robo-trader/issues/34), [#65](https://github.com/guymaim/robo-trader/issues/65), [#66](https://github.com/guymaim/robo-trader/issues/66), [#75](https://github.com/guymaim/robo-trader/issues/75), [#77](https://github.com/guymaim/robo-trader/issues/77), [#78](https://github.com/guymaim/robo-trader/issues/78))
- **Status and Settings:** "Approve buys" and the "open confirm" rule are shown separately, safeguards appear as status chips, unset broker cards stay collapsed until you click Set up, and the email and Slack notification badges reflect their real state. ([#37](https://github.com/guymaim/robo-trader/issues/37), [#40](https://github.com/guymaim/robo-trader/issues/40), [#48](https://github.com/guymaim/robo-trader/issues/48), [#49](https://github.com/guymaim/robo-trader/issues/49), [#50](https://github.com/guymaim/robo-trader/issues/50))
- **Times and counts:** Times on the home and trader pages now show their time zone, the live-days count matches on both pages, and weekends show dashes instead of mixed 0.00% figures. ([#38](https://github.com/guymaim/robo-trader/issues/38), [#46](https://github.com/guymaim/robo-trader/issues/46), [#56](https://github.com/guymaim/robo-trader/issues/56))
- **Pages:** A missing simulation run now shows a proper not-found page. Signed-in users no longer see "Sign in" on public pages (they see "Back to desk"), and the Logs page wording is clearer for regular users. ([#57](https://github.com/guymaim/robo-trader/issues/57), [#60](https://github.com/guymaim/robo-trader/issues/60), [#74](https://github.com/guymaim/robo-trader/issues/74))

### Changed

- The Accessibility Statement now says plainly which parts of the app are not yet accessible, and the Privacy Policy is more detailed. Legal pages remain drafts pending review.
- The home-page preview card is clearly marked as example data, not a result.
- The developer API reference page is no longer available.

---

## 2026-09-19 — Email alerts on by default

### Changed

- Daily summary and warn/error email alerts are **on by default** for your Google login address. Turn them off anytime in Settings → Notifications.

### Notes

- Buy/sell fills still stay on Slack only.
- Account emails (sign-up, approval, disable) were already sent without this toggle.

---

## 2026-09-19 — Admin Remove user button

### Fixed

- On the Admin users list, **Remove** now works reliably. Click **Remove**, then **Confirm remove?** within a few seconds. (Some browsers block pop-up confirm dialogs, which previously made Remove appear to do nothing.)

### Notes

- Removing a user stops their traders and drops them from the allow-list. They cannot sign in again until an administrator re-approves their email.

---

## 2026-09-19 — Re-signup after account removal

### Fixed

- After an administrator disables and removes an account, signing in again with the same Google account no longer shows **Forbidden — session user not found**. The old session is cleared and the public sign-in page is shown so you can request access again.

### Notes

- Invite-only access is unchanged: after removal, an administrator must approve the email again before a new account is created.

---

## 2026-09-19 — Email alerts for account events and daily summaries

### Added

- Optional email alerts in Settings → Notifications. When enabled, you receive:
  - a daily market-close summary (fills, open portfolio, day and total P/L)
  - warn/error operational alerts
- Account emails (always sent when mail is configured, no Settings toggle required):
  - sign-up received / welcome
  - admin approve or reject
  - admin disable or remove account
  - trader enabled / disabled
- Enabling email alerts sends a one-time test message to confirm delivery before the preference is saved.

### Notes

- Buy/sell fills stay on Slack only (not emailed).
- Daily-summary and warn/error emails default on (see “Email alerts on by default” above); disable in Settings to opt out.
- Existing Slack notifications are unchanged.
- Invite-only access is unchanged.

---

## 2026-09-19 — Public community repository

### Added

- Public GitHub repository for documentation, release notes, and issue tracking.
- Getting-started guide and issue-opening / issue-tracking documentation.
- GitHub issue templates for bugs, feature requests, and questions.

### Notes

- The live app remains invite-only: Google sign-in, then admin approval before trading is enabled.
- Application: [https://robo-trader.egedsoft.co.il](https://robo-trader.egedsoft.co.il)
