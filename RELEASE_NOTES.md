# Release notes

User-facing changes to Robo Trader, newest first.

For how we maintain this file, see the project’s internal contributor rules (`CLAUDE.md` / Cursor rules in the private workspace). Public readers: use this page to see what shipped; use [Issues](https://github.com/guymaim/robo-trader/issues) to report problems or request features.

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
