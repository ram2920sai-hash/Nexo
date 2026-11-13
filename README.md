# ✦ NEXO — Smart Habit Tracker

A responsive, full-stack habit tracker with persistent SQLite storage, account authentication, a daily/weekly tracker, calendars, analytics, streaks, goal targets, and export/restore.

## Run on Windows 11

1. Extract the ZIP folder and open the extracted `nexo` folder in VS Code.
2. Open **Terminal → New Terminal** and run:

   ```powershell
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   pip install -r requirements.txt
   uvicorn server:app --reload
   ```

   If PowerShell blocks activation, open Command Prompt and run `.venv\Scripts\activate.bat`, or use `.venv\Scripts\python -m uvicorn server:app --reload` without activation.

3. Open **http://127.0.0.1:8000** in your browser. Create an account, and **save your one-time recovery code** in a password manager. Every account starts with 10 editable sample habits.
4. Stop the server using **Ctrl+C**. Restart with `uvicorn server:app --reload` when needed.

On macOS/Linux, activate with `source .venv/bin/activate` and start using the same `uvicorn` command.

## App features

- Individual secure accounts (salted PBKDF2 password hashes; HTTP-only SameSite session cookies), login/logout, reset password with recovery code, and customizable profile/timezone.
- Full habit CRUD, archive/unarchive, history reset, custom icons/categories, date-based check-ins, search, category filtering, and day/month/week navigation.
- Daily/weekly/monthly completion goals, daily completion circle, week bars, daily analytics, monthly trend, per-habit completion rankings, and clickable calendar.
- Streaks on days where **every active habit** is completed; current streak allows today to remain unfinished. Milestones at 1, 3, 7, 14, 30, 60 and 100 days.
- Optional per-habit browser reminders (notifications only when NEXO is open, with permission), light/dark theme, monthly XLSX/PDF exports, JSON backup/restore.
- Responsive interface for desktop and mobile.

## Important notes

- **Data persistence:** `data/nexo.db` is created automatically and remains on your computer between sessions. Back this file up or use **Settings → Backup JSON**. Never commit your `data/` folder to GitHub.
- **Notifications:** browser reminders only run while the page is open; reliable background/mobile push requires a separate scheduled notification service and public HTTPS deployment.
- **Password resets:** the displayed recovery code replaces email-based password resets in this local-first app. Losing both the password and code prevents account recovery without an administrator.
- **Deployment:** For public internet hosting, configure HTTPS (`NEXO_HTTPS=1`), persistent server storage, a production reverse proxy, rate limits, automated backups, and an email recovery provider. The included SQLite database is suitable for local/small-scale use; a managed PostgreSQL service is preferable for larger deployments. Do not expose the development server directly to the internet.
- The visual design follows the written specification. No reference video was included in this environment.

## Tests

```powershell
pip install pytest httpx
pytest -q
```

## Project layout

- `server.py` — FastAPI endpoints, authentication, database, analytics, downloads.
- `static/index.html` — Single-page app markup.
- `static/style.css` — Premium pastel layout, mobile responsiveness, dark mode.
- `static/app.js` — UI interactions, navigation, SVG chart rendering, notifications.
- `tests/test_app.py` — API integration test covering account isolation and main workflows.

## License

Use and adapt for your own learning and projects.


## Install NEXO as an app (PWA) on phones and computers

NEXO is an **installable Progressive Web App (PWA)**, not an Android APK or iOS App Store binary. Open your **deployed HTTPS URL** in a supported browser:

- **Android (Chrome):** select **Install app** (when offered) or Chrome menu → **Add to Home screen**.
- **iPhone/iPad (Safari):** Share → **Add to Home Screen** → Add.
- **Windows (Edge/Chrome):** click **Install app** in NEXO (if shown) or browser menu → **Apps → Install this site as an app** (Edge) / **Cast, save and share → Install page as app** (Chrome; wording can vary).
- Start from the home-screen/desktop icon thereafter. Users do **not** need Python, VS Code or the ZIP if you host the app online.

The service worker caches only public static assets; **habit tracking, login and analytics require an internet connection**. This is not a fully offline app.

### Deploying for public access

**GitHub Pages does not run Python/FastAPI or SQLite.** GitHub is suitable for storing the source code, but the full app must be deployed to a service supporting Python web apps and persistent storage, such as a VM, Railway with a persistent volume, or Render with a persistent disk (check provider terms/prices). One Python backend serves both UI and API, so you do not need separate GitHub Pages hosting. Use HTTPS (provided by most hosts).

Set start command: `uvicorn server:app --host 0.0.0.0 --port $PORT` (for Linux hosts). Configure `NEXO_DATA_DIR` to point to a **persistent mounted volume**, e.g. `/data/nexo`; set `NEXO_HTTPS=1`. Do not store `nexo.db` in public GitHub repositories. **Without persistent storage, user accounts and habit data can be lost on redeploy/restart.**

Important production requirements: SQLite with disk persistence is adequate for a small private deployment, but broader public usage needs additional hardening (rate limiting for authentication, CSRF defenses, account recovery/email verification, backups, monitoring, and privacy policy). Do not promise users secure production accounts until those are in place.


### Install button inside NEXO

The signup/login screen and the dashboard header both include **⬇ Install NEXO**. On browsers that support the PWA installation prompt, the button opens it. Otherwise, it displays device-specific installation instructions. Once installed in standalone mode, the button is hidden. An HTTPS-hosted, reachable FastAPI deployment is needed for other users to install it from your shared link. GitHub Pages can store static files but cannot host this Python app and its database by itself. Keep the database persistent on the deployment host.


### Windows timezone and password visibility fix (November 2026)

The signup screen previously showed `Unsupported timezone` on some Windows Python installations because the standard `zoneinfo` library cannot find the IANA timezone database without the `tzdata` Python package. This release includes `tzdata` in `requirements.txt`. After updating, run `python -m pip install -r requirements.txt` and restart the server. The login/signup/recovery password field now has an eye button for showing or hiding the entered password. Existing SQLite data is retained if you preserve the `data/` folder.
