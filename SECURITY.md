# Security notes

- Do not place the real Telegram bot token in Python source files.
- Keep `.env` out of Git; `.gitignore` already excludes it.
- Revoke any previously exposed token using `@BotFather` and use a fresh token.
- Set `ADMIN_IDS` only to trusted numeric Telegram IDs.
- Run one polling worker per bot token.
- Do not store or relay unrelated third-party verification codes.
- Do not enable number or SMS operations until an authorized provider, permitted use case, consent, retention rules, webhook verification, rate limits, and audit logging are established.
