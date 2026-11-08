# BD OTP PRO — Python Telegram Bot

A Python-only Telegram bot foundation for a legitimate business-owned SMS/OTP workflow. It includes Telegram registration, account details, admin-only statistics, SQLite persistence, and a Render Background Worker blueprint.

**Scope:** Number allocation, SMS sending/receiving, and payment processing are disabled until an authorized telecom/SMS provider and the required business integration are configured. This project is not a public OTP inbox and must not be used to intercept or relay third-party verification codes.

## Project files

```text
BD-OTP-PRO/
├── app/
│   ├── __init__.py
│   ├── config.py
│   ├── database.py
│   ├── keyboards.py
│   ├── main.py
│   └── handlers/
│       ├── __init__.py
│       ├── common.py
│       ├── account.py
│       └── admin.py
├── data/
│   └── .gitkeep
├── bot.py
├── pod.py
├── requirements.txt
├── requirement.txt
├── .python-version
├── .env.example
├── .gitignore
└── render.yaml
```

## Python version and dependencies

- Python 3.12
- aiogram 3.x
- aiosqlite
- python-dotenv

The standard dependency filename is **`requirements.txt`** (plural). `requirement.txt` is included as a compatibility copy because it was requested, but Render is configured to install from `requirements.txt`.

## Run locally on Windows

Open PowerShell in the project root:

```powershell
py -3.12 -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `.env` and set `BOT_TOKEN` to a **fresh token** generated through `@BotFather`. Do not paste the token into source code or commit `.env`.

Start the bot:

```powershell
python bot.py
```

The requested compatibility launcher also works:

```powershell
python pod.py
```

Stop the local bot with `Ctrl+C`.

## Configure an administrator

1. Start the bot and send `/id`.
2. Copy the numeric Telegram ID returned by the bot.
3. Set it in `.env`:

```env
ADMIN_IDS=123456789
```

For multiple admins, separate IDs with commas:

```env
ADMIN_IDS=123456789,987654321
```

Restart the bot after changing `.env`. If `ADMIN_IDS` is empty, no user has admin permissions.

## Render deployment

This bot uses Telegram long polling, so deploy it as a **Background Worker**, not a Web Service.

1. Revoke the token that was previously exposed in chat by using `@BotFather`, then generate a new token.
2. Push the extracted project to a GitHub repository. Use a private repository if possible.
3. Confirm `.env` is not being committed; `.gitignore` excludes it.
4. In Render, choose **New → Blueprint** and connect the repository.
5. Render reads `render.yaml`. Provide the new `BOT_TOKEN` when prompted.
6. Apply the Blueprint. It installs `requirements.txt` and starts `python bot.py`.
7. Check the worker logs for `Bot connected successfully: @...`.
8. Add your numeric Telegram ID to Render's `ADMIN_IDS` environment variable and redeploy/restart.

The Blueprint attaches a 1 GB persistent disk at `/var/data` and stores SQLite there. Persistent disks require a paid Render service. Check Render's current pricing before deploying. Keep a single worker instance and do not run the same bot token locally at the same time.

## Bot commands

| Command | Purpose |
|---|---|
| `/start` | Register and open the menu |
| `/help` | Show help |
| `/id` | Show Telegram numeric ID |
| `/admin` | Admin menu (configured admins only) |
| `/stats` | Registration statistics (configured admins only) |

## Important security notes

- Never commit `.env` or share the bot token.
- If the token is exposed, revoke it with `@BotFather` and replace it in every environment.
- Use only authorized numbers and provider APIs for business-owned applications.
- Do not store unrelated third-party OTPs or use this bot for account-verification bypasses.
- One active polling process per bot token. Running a local copy while Render is active can cause Telegram polling conflicts.

## Current feature status

Implemented: bot connection, user registration, user account view, main menu, support placeholder, admin authorization, registration statistics, SQLite persistence.

Not yet active: real number inventory, dedicated/temporary number provisioning, SMS delivery/receiving, and payments. Those require authorized provider integrations and further implementation.
