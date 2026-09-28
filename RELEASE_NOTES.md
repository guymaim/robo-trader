# Release notes

User-facing changes to Robo Trader, newest first.

For how we maintain this file, see the project’s internal contributor rules (`CLAUDE.md` / Cursor rules in the private workspace). Public readers: use this page to see what shipped; use [Issues](https://github.com/guymaim/robo-trader/issues) to report problems or request features.

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
