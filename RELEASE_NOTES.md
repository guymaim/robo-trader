# Release notes

User-facing changes to Robo Trader, newest first.

For how we maintain this file, see the project’s internal contributor rules (`CLAUDE.md` / Cursor rules in the private workspace). Public readers: use this page to see what shipped; use [Issues](https://github.com/guymaim/robo-trader/issues) to report problems or request features.

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
