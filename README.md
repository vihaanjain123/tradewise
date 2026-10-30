# TradeWise

Learn technical analysis through the live Indian market. Every signal comes with a plain-English explanation, and everyone who signs up gets their own ₹1,00,000 virtual practice portfolio.

---

## A · Put it online (free, always on)

When you're done, TradeWise has a permanent link like `https://tradewise.onrender.com`. It works on any phone or computer and installs like an app. Your laptop can be off.

| Service | What it does | Cost |
|---|---|---|
| **GitHub** | stores the code | free |
| **Neon** | database: accounts, portfolios, signal history | free |
| **Brevo** | sends the daily emails and password resets | free (300 emails/day) |
| **Render** | runs the website | free |
| **cron-job.org** | visits the site every 10 min, which keeps it awake and runs the hourly check and daily emails | free |

### 1. GitHub: upload the code
1. Go to **github.com/new** and name the repo `tradewise`. Private is fine. Click **Create repository**.
2. Click **uploading an existing file** and drag in `tradewise.py`, `requirements.txt`, `render.yaml`, `README.md` and `.gitignore`.
   On a Mac, `.gitignore` is hidden: press **Cmd + Shift + .** in Finder to show it. It's optional anyway.
   **Don't** upload the `tradewise_data` folder.
3. Click **Commit changes**.

### 2. Neon: create the database
1. Sign up at **neon.com** (Continue with GitHub is quickest).
2. Create a project named `tradewise`. For the region, pick **AWS Asia Pacific (Singapore)**.
3. Click **Connect** and copy the connection string. It starts `postgresql://` and ends `sslmode=require`. Keep it for step 4.

### 3. Brevo: set up sending emails
1. Sign up at **brevo.com** (free plan).
2. Go to **Settings → Senders, domains & dedicated IPs → Senders → Add a sender**. Use your Gmail and confirm the code Brevo emails you.
3. Go to **Settings → SMTP & API → API keys → Generate a new API key**. Copy it (it starts `xkeysib-`).

### 4. Render: start the website
1. Sign up at **render.com** with GitHub.
2. Click **New → Blueprint** and pick your `tradewise` repo. Render reads `render.yaml` and asks for four values:

   | Key | Paste |
   |---|---|
   | `DATABASE_URL` | the Neon connection string |
   | `BREVO_API_KEY` | the Brevo key |
   | `MAIL_FROM` | the Gmail you verified in Brevo |
   | `ADMIN_EMAIL` | the email you'll sign up to TradeWise with (only this account can reset the shared algorithm account) |

3. Click **Apply**. The first build takes about 5 minutes.
4. Your link is at the top of the service page, e.g. `https://tradewise-xxxx.onrender.com`.

### 5. cron-job.org: keep it awake and on schedule
1. On Render, open your service → **Environment**. Copy the value of `CRON_SECRET`.
2. Sign up at **cron-job.org** → **Create cronjob**:
   - **URL:** `https://YOUR-LINK.onrender.com/cron/tick?key=YOUR_CRON_SECRET`
   - **Schedule:** every **10 minutes**
3. Click **Create**.

This one job does everything:
- It stops Render's free server from falling asleep. Render sleeps a site after 15 minutes with no visits.
- Mon–Fri, 9:20–15:20 IST: it runs the hourly signal check.
- It sends each person's daily summary at the time they chose.

### 6. Use it
- Open your link and **Sign up** with your `ADMIN_EMAIL`.
- **iPhone:** in Safari, tap Share → **Add to Home Screen**.
- **Android:** in Chrome, tap ⋮ → **Install app**.
- **Mac/PC:** in Chrome, click the install icon in the address bar.
- Share the link with your test users. Each one signs up and gets their own portfolio.

### Updating it later
Upload the new `tradewise.py` to GitHub (**Add file → Upload files**, same name, **Commit**). Render redeploys automatically in a few minutes. Accounts and portfolios are safe, because they live in Neon.

### Good to know
- **Free limits.** Render gives 750 free hours a month, and keeping it awake 24/7 uses about 744. So don't create a second free Render service.
- **If the site was asleep** (say, cron-job.org was paused), the first visit takes about a minute to load.
- **Yellow "sample data" banner** means Yahoo Finance refused the server's request. That sometimes happens to cloud servers. It clears on its own; press Refresh a few minutes later.
- **Logs:** on Render, open your service → **Logs**.
- **Emails in spam?** Open one and mark it "Not spam" once. Brevo may show the sender as `…@brevosend.com` because Gmail addresses can't be verified for outside senders.

---

## B · Run it on your own computer (optional)

```
/usr/local/bin/python3 -m pip install -r requirements.txt
/usr/local/bin/python3 tradewise.py
```
Your browser opens **http://127.0.0.1:5050**. The first time, sign up. The first account on your computer keeps your old practice trades and email settings.

- **Mac app in your Dock:** `/usr/local/bin/python3 tradewise.py --install-mac-app`
- **Emails on your computer:** Settings → **Sending account** → your Gmail and a Gmail App Password (myaccount.google.com/apppasswords).
- **Data** is in `tradewise_data/tradewise.db`.

| Command | What happens |
|---|---|
| `python3 tradewise.py` | runs and opens your browser |
| `python3 tradewise.py --app` | opens in its own window |
| `python3 tradewise.py --no-browser` | runs without opening anything |

## Email alerts

Each person chooses their own alerts in **Settings**. Alerts only use live prices, never the "sample data" shown when Yahoo can't be reached.

| Alert | When it's sent | Checked |
|---|---|---|
| Daily summary | portfolio value, today's change, holdings, NIFTY signal, at the time you pick | Mon–Fri |
| Price alert | a stock or NIFTY goes above/below your target (set it from the trade box; up to 10; fires once) | every 10 min, 9:15–15:35 |
| Stock signals | a stock turns BUY or SHORT. Choose: off / stocks I own / all 11 | hourly |
| Big moves | something you own is up or down another 3 / 5 / 10% from your buy price | hourly |
| NIFTY 50 signal | the market signal turns BUY or SHORT | hourly |

If several alerts happen at once, they're bundled into one email.

## Pages

| Page | What it does | Login? |
|---|---|---|
| Dashboard | NIFTY 50 signal, the six conditions in plain English, chart, history, trade box | no |
| Scanner | 11 stocks (Watchlist + Popular), filter by group or signal | no |
| Stock page | chart with 3- and 6-month projections, technical + business numbers, trade box | no |
| Learn | glossary with examples | no |
| Practice | your virtual portfolio + the algorithm's automatic account | yes |
| Settings | email updates, install the app, change password, log out, delete account | yes |

---
Educational tool — not financial advice.
