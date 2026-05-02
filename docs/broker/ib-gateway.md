# IB Gateway setup

IB Gateway is the lighter of IBKR's two desktop apps — no charting UI,
no order book, just the API connection that WS Terminal needs.
Recommended for unattended / VPS operation.

If you want IBKR's full charting and manual trading UI alongside the
bot, use [Trader Workstation (TWS)](tws.md) instead. The bot works
identically with either; this page assumes you've picked Gateway.

!!! warning "Before you start"
    You need an IBKR account with **futures trading permissions
    enabled** AND an active **market data subscription**. If you
    haven't done either, see:

    - [Requirements → Broker Account](https://ws-trading.co/requirements.html#01-broker-account)
      for the futures-permission walkthrough
    - [Market data subscription](market-data.md) for the data feed
      setup (this is the single most-overlooked prerequisite —
      without it, the bot trades on 15–20 minute delayed quotes
      and silently makes wrong decisions)

## Step 1 — Download IB Gateway

1. Go to <https://www.interactivebrokers.com/en/trading/ibgateway-stable.php>
2. Click the **Latest version** download for **Windows 64-bit**
3. Save the installer (`ibgateway-stable-standalone-x64-...exe`) to your
   Downloads folder

There are two channels — **Stable** and **Latest**. Use Stable. Latest
is for development testing and ships breaking changes more often.

## Step 2 — Install

1. Run the installer
2. Accept the default install path
   (`C:\Jts\ibgateway\<version>\`) — WS Terminal doesn't care where it
   lives, but the default path is documented across IBKR's own help
   articles which makes troubleshooting easier
3. Let the installer create a desktop shortcut

## Step 3 — First launch + login

1. Launch IB Gateway from the desktop shortcut
2. You'll see a small login window with two big toggles at the top:
   **IB API** and **FIX CTCI**

    - Choose **IB API** (the default) — that's what WS Terminal speaks.
      FIX is for institutional users.

3. Below the toggles, choose your environment:
    - **Paper Trading** — IBKR's demo account, fake money. Use this
      first.
    - **Live Trading** — your real account, real money.

4. Enter your IBKR username and password
5. Click **Log In**

You'll get a small green "API service started" notification once login
completes.

!!! info "Paper vs Live username"
    Your IBKR account has two logins — a regular one for live trading
    and a `pXXXXXXXX`-format one for paper trading. The paper username
    is shown in your Client Portal under Account → Settings → Paper
    Trading Account. Make sure you're using the right one when you
    select the matching environment in Gateway's login screen.

## Step 4 — Enable the API in Gateway settings

By default, Gateway accepts API connections but a few settings need
verification.

1. With Gateway running, click **Configure** (top-right of the
   Gateway window) → **Settings**
2. In the left sidebar, expand **API** → click **Settings**
3. On the API Settings page, verify:

    - **Enable ActiveX and Socket Clients** — must be **CHECKED**
    - **Read-Only API** — must be **UNCHECKED**
      (if checked, the bot can read positions but can't place orders)
    - **Allow connections from localhost only** — checked is fine for
      most users (the bot runs on the same machine as Gateway)
    - **Socket port** — leave at the default
        - **4002** for paper trading
        - **4001** for live trading
    - **Master API client ID** — leave blank
    - **Bypass Order Precautions for API Orders** — your call.
      Checking this stops Gateway from popping up a "are you sure?"
      dialog for orders that exceed certain risk thresholds. For
      automated trading, you generally want this checked, but
      understand what you're disabling.

4. Click **OK** to save

## Step 5 — Verify trusted IPs

1. Still in API Settings → **Trusted IPs** section
2. The list should include `127.0.0.1` (localhost). If it doesn't,
   click **Create** and add it
3. If you're running the bot on a different machine than Gateway
   (e.g. bot on your laptop, Gateway on a VPS), add that machine's
   IP here too

## Step 6 — Configure the bot to talk to Gateway

1. Open WS Terminal's dashboard
2. Go to **Config** → find the **IBKR Connection** section
3. Set:
    - **IBKR Host** — `127.0.0.1` if Gateway is on the same machine
    - **IBKR Port** — `4002` for paper, `4001` for live
    - **IBKR Client ID** — `1` (or any unique integer if you have other
      tools also connecting)
4. Click **Save changes**

When you start the bot, you should see a connection-success message
in the dashboard. If Gateway is running and the API is enabled, the
bot connects in under a second.

## Step 7 — Auto-restart for unattended operation

IB Gateway logs you out daily for security — typically once every 24
hours, sometime after midnight ET. You'll need to log back in unless
you configure auto-restart.

1. In Gateway, click **Configure** → **Settings**
2. In the left sidebar, choose **Lock and Exit**
3. Set **Auto restart** to your preferred time (usually a few hours
   before the next trading session — e.g. 3 AM ET works well for
   US futures)
4. Set **Auto logoff** to a time that doesn't conflict with trading
   hours (e.g. 11 PM ET, after the futures session closes for the
   day)

With auto-restart configured, Gateway will log itself back in with
the credentials you provided at startup, and the bot's connection
will resume automatically.

!!! info "Two-factor authentication and auto-restart"
    If your IBKR account has 2FA enabled (highly recommended), the
    auto-restart flow may require an additional setup step. IBKR's
    "IB Key" mobile app handles this. Check IBKR's auto-restart
    documentation in their Help Center for the current setup walk-
    through if you hit issues.

## Common pitfalls

### "Can't connect" errors from the bot

In order of likelihood:

1. **Gateway isn't running** — the bot needs Gateway up before you
   start the bot
2. **Wrong port** — paper is 4002, live is 4001. The bot's `IBKR_PORT`
   config must match what Gateway is using
3. **API not enabled** — re-check Step 4 (Enable ActiveX and Socket
   Clients must be checked)
4. **Trusted IP missing** — `127.0.0.1` must be on the trusted IPs
   list (Step 5)
5. **Client ID conflict** — if another tool (TWS, Excel API, custom
   script) is using the same client ID at the same time, Gateway
   rejects the second connection. Change the bot's `IBKR_CLIENT_ID`
   to a unique number

### Orders rejected with "no trading permission"

Your IBKR account doesn't have futures permissions enabled. See
[the requirements page](https://ws-trading.co/requirements.html#01-broker-account)
for the futures-permission walkthrough — it's a separate enrollment
on the IBKR side, takes 1-3 business days for approval.

### Gateway is logging me out at midnight

That's the daily auto-logoff. Configure auto-restart per Step 7 to
fix it.

### Connection drops mid-session

Brief network blips usually resolve themselves — the bot reconnects
automatically. If you're getting frequent drops:

- Check your internet stability (wired beats Wi-Fi)
- If on a VPS, check the VPS provider's uptime status
- Confirm Gateway isn't being killed by Windows energy/sleep settings
  (set Windows to never sleep when on AC power)

## Running Gateway alongside TWS

You can't log into both Gateway and TWS with the same IBKR username
at the same time — IBKR enforces single-session-per-username.

Two options if you want both:

1. **Switch between them** — only one running at a time. Bot's port
   config matches whichever is active (4002/4001 for Gateway,
   7497/7496 for TWS).
2. **Use a secondary IBKR username** — IBKR lets you create
   additional API-only usernames under your account in
   Client Portal → Settings → Users. The bot connects via Gateway
   on the secondary username while you use TWS interactively on the
   primary.

For most buyers, option 1 is enough. Option 2 is for power users
who want to manually trade alongside the bot.

## Where to go next

- [First-run wizard walkthrough](../getting-started/first-run.md) —
  point the bot at the Gateway you just configured
- [Configuration → Risk](../configuration/risk.md) — set your stop,
  take-profit, and daily caps
- [Configuration → Strategy](../configuration/strategy.md) — pick
  your contract, candle timeframe, FVG settings
