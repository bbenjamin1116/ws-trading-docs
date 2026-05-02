# Trader Workstation (TWS) setup

Trader Workstation (TWS) is IBKR's full desktop trading platform —
charting, order book, manual order entry, market scanners, plus the
API connection WS Terminal uses. Heavier than [IB Gateway](ib-gateway.md)
but useful if you want IBKR's interactive UI alongside the bot.

The bot doesn't care which one you use. Both expose the same API on
local ports — only the port numbers differ.

| App         | Paper port | Live port |
|-------------|-----------:|----------:|
| IB Gateway  | 4002       | 4001      |
| **TWS**     | **7497**   | **7496**  |

If you're not sure which to pick: most buyers running the bot
unattended (24/7, on a VPS) prefer Gateway because it uses fewer
resources. Most buyers who want to chart and click-trade alongside
the bot prefer TWS.

!!! warning "Before you start"
    You need an IBKR account with **futures trading permissions
    enabled** AND an active **market data subscription**. See:

    - [Requirements → Broker Account](https://ws-trading.co/requirements.html#01-broker-account)
      for the futures-permission walkthrough
    - [Market data subscription](market-data.md) for the data feed
      setup — without it, the bot trades on delayed quotes and
      silently makes wrong decisions

## Step 1 — Download TWS

1. Go to <https://www.interactivebrokers.com/en/trading/tws.php>
2. Click the **TWS Latest** download for **Windows 64-bit**
   (or **TWS Stable** if you want fewer updates)
3. Save the installer (`tws-latest-standalone-x64-...exe`)

**Stable vs Latest** — Stable updates roughly quarterly, Latest can
update mid-month. For automated trading where you don't want surprises,
Stable is safer.

## Step 2 — Install

1. Run the installer
2. Accept the default install path (`C:\Jts\<version>\`)
3. Let the installer create a desktop shortcut

## Step 3 — First launch + login

1. Launch TWS from the desktop shortcut
2. The login window appears with your username/password fields
3. Below the password, choose your environment:
    - **Live Trading** — real account
    - **Paper Trading** — IBKR's demo, fake money. Use this first.
4. Click **Log In**

TWS will open with a default trading window — chart, order panel,
account summary, etc. You can ignore most of it for the bot's purposes;
only the API settings matter.

!!! info "Paper vs Live username"
    Same as IB Gateway — your IBKR account has two logins, a regular
    one for live and a `pXXXXXXXX`-format one for paper. Find the
    paper username in Client Portal under Account → Settings → Paper
    Trading Account.

## Step 4 — Enable the API in TWS settings

1. With TWS running, click **File** → **Global Configuration** (or
   **Edit** → **Global Configuration**, depending on TWS version)
2. In the left sidebar of the Global Configuration window, expand
   **API** → click **Settings**
3. On the API Settings page, verify:

    - **Enable ActiveX and Socket Clients** — must be **CHECKED**
    - **Read-Only API** — must be **UNCHECKED**
    - **Allow connections from localhost only** — checked is fine
    - **Socket port** — leave at the default
        - **7497** for paper trading
        - **7496** for live trading
    - **Master API client ID** — leave blank
    - **Bypass Order Precautions for API Orders** — your call (see
      the same callout in the IB Gateway doc — checking it stops
      manual confirmation prompts on flagged orders)

4. Click **OK** to save

## Step 5 — Verify trusted IPs

1. Still in API Settings → **Trusted IPs** section
2. The list should include `127.0.0.1`. If not, click **Create** and
   add it
3. If running the bot from a different machine, add that IP too

## Step 6 — Configure the bot to talk to TWS

1. Open WS Terminal's dashboard
2. Go to **Config** → find the **IBKR Connection** section
3. Set:
    - **IBKR Host** — `127.0.0.1` if TWS is on the same machine
    - **IBKR Port** — `7497` for paper, `7496` for live (NOT the same
      ports as Gateway)
    - **IBKR Client ID** — `1` (or any unique integer)
4. Click **Save changes**

Start the bot. You should see a connection-success message in the
dashboard within a second of clicking Start.

## Step 7 — Auto-restart for unattended operation

Same logic as IB Gateway — TWS auto-logs-out daily for security. If
the bot needs to run across daily logoffs, configure auto-restart:

1. In TWS, click **File** → **Global Configuration** → **Lock and Exit**
2. Set **Auto restart** time (e.g. 3 AM ET)
3. Set **Auto logoff** time (e.g. 11 PM ET)

The bot's connection will resume automatically after each restart.

## Common pitfalls

### "Can't connect" errors from the bot

In order of likelihood:

1. **TWS isn't running** — start TWS before starting the bot
2. **Wrong port** — TWS uses **7497/7496**, NOT Gateway's
   4002/4001. If you're switching between Gateway and TWS, the bot's
   `IBKR_PORT` config has to match whichever one is currently running
3. **API not enabled** — re-check Step 4
4. **Trusted IP missing** — `127.0.0.1` must be on the trusted IPs
   list
5. **Client ID conflict** — change `IBKR_CLIENT_ID` to a unique
   number if you have other tools also connecting

### Orders rejected with "no trading permission"

Your IBKR account doesn't have futures permissions enabled — see
[the requirements page](https://ws-trading.co/requirements.html#01-broker-account)
for the enrollment walkthrough.

### TWS keeps popping up confirmation dialogs

If you're seeing "Are you sure?" dialogs on every order, the
**Bypass Order Precautions for API Orders** setting is unchecked.
That's a personal-preference toggle — see Step 4.

### TWS uses too much RAM / CPU

That's the trade-off vs IB Gateway. If you don't need the charting
UI, switch to Gateway — it uses a fraction of the resources.

## TWS vs IB Gateway — side by side

| Feature                          | TWS         | IB Gateway  |
|----------------------------------|-------------|-------------|
| API connection (what bot needs)  | ✓           | ✓           |
| Resource usage                   | Heavy       | Light       |
| Charting UI                      | ✓           | ✗           |
| Manual order entry               | ✓           | ✗           |
| Account summary view             | ✓           | minimal     |
| Recommended for VPS / 24/7       | maybe       | yes         |
| Recommended for desktop alongside| yes         | no          |
| Default paper port               | 7497        | 4002        |
| Default live port                | 7496        | 4001        |

Most buyers can switch between Gateway and TWS freely — install both,
log into whichever one makes sense for that day's use, and just point
the bot's `IBKR_PORT` at the right port number.

## Where to go next

- [First-run wizard walkthrough](../getting-started/first-run.md) —
  point the bot at the TWS instance you just configured
- [Configuration → Risk](../configuration/risk.md)
- [Configuration → Strategy](../configuration/strategy.md)
