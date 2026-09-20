# Release notes

User-facing changes to Robo Trader, newest first.

For how we maintain this file, see the project’s internal contributor rules (`CLAUDE.md` / Cursor rules in the private workspace). Public readers: use this page to see what shipped; use [Issues](https://github.com/guymaim/robo-trader/issues) to report problems or request features.

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
