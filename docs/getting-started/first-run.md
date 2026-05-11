# First-run wizard

The first time you launch WS Terminal, the dashboard opens with a
wizard overlay that walks you through activation and initial setup.
This page covers what each step does and what to enter.

If you've already completed the wizard once, it doesn't fire again on
subsequent launches. You can re-trigger it by deactivating your
license (Settings → License → Deactivate) and restarting the bot.

## Before you start the wizard

Make sure you have:

1. **An active WS Terminal subscription on Whop** — Basic or Pro
2. **IBKR account with futures permissions** ready to go (or at least
   approved — see [Requirements](https://ws-trading.co/requirements.html))
3. **Either IB Gateway or TWS installed** with the API enabled — see
   [IB Gateway setup](../broker/ib-gateway.md) or
   [TWS setup](../broker/tws.md)
4. **The Whop sign-in tab handy** — your default browser should be
   set to the one you actively use. The wizard opens a Whop sign-in
   tab in your default browser.

## Step 0 — License activation

The wizard opens to a license activation step with two tabs:

### Tab 1: "Sign in with Whop" (recommended)

1. Click the **Sign in with Whop** button
2. Your default browser opens a new tab to Whop's sign-in page
3. Sign in with the same Whop account you used to subscribe
4. Approve the requested permission (we ask for read-only access to
   your membership info — that's how we look up your license)
5. The browser tab shows a "License activated — close this tab" page
6. Switch back to the WS Terminal dashboard tab — the wizard
   automatically advances to step 1

### Tab 2: "I have a license key"

For buyers who got their license key from support directly (rare),
or anyone where the OAuth flow doesn't work for some reason.

1. Paste your license key (looks like `wsterm_xxxxxxxxxxxxxxxxxxxxxx`)
2. Click **Continue**
3. The bot validates the key with the cloud and advances to step 1

!!! info "What gets saved"
    Once activated, your license key is stored at
    `%APPDATA%\WSTerminal\license.json`. It survives across .exe
    updates — you only do the activation once per machine, not once
    per version.

## Step 1 — IBKR prerequisites checklist

The wizard shows a 4-item checklist of broker prerequisites:

1. **You have an IBKR account** — check this if true
2. **Your IBKR account has futures permissions enabled** — check this
   if true. If not, the wizard links out to the requirements page
3. **You have IB Gateway or TWS installed** — check this if true
4. **You've enabled the API in Gateway/TWS settings** — check this
   if you've completed Step 4 of the broker setup

The wizard doesn't actually verify these — it's a self-checklist. You
can skip if you want; it's there as a sanity check before you waste
time configuring the bot against a broker that's not ready.

Click **Continue** when done.

## Step 2 — Broker connection

Configure how the bot connects to your broker.

| Field             | Default       | What to enter                                    |
|-------------------|--------------:|--------------------------------------------------|
| **IBKR Host**     | `127.0.0.1`   | Leave as is if Gateway/TWS is on the same machine |
| **IBKR Port**     | `4002`        | `4002` (Gateway paper), `4001` (Gateway live), `7497` (TWS paper), `7496` (TWS live) |
| **IBKR Client ID**| `1`           | Leave as is unless you have other tools using ID 1 |

!!! warning "Start with paper, not live"
    The wizard defaults to port `4002` (Gateway paper). **Keep it
    there until you've watched the bot run for at least a few
    sessions** and confirmed everything works. Switching to live
    (port 4001 / 7496) triggers a 5-checkbox legal acknowledgment
    modal — that's a deliberate friction step so live trading is
    never accidental.

Click **Continue**.

## Step 3 — Pick a preset

The wizard shows a preset picker. Both tiers see the same two
recommended presets:

1. **WS Terminal Standard** (default, recommended) — the operator's
   recommended preset and the configuration the published equity
   curve is based on. ATR-based stops scaled to current volatility,
   fixed 200-point take-profit, trailing-stop arms at 80% of TP,
   multi-entry with risk-balanced add-on stops. Designed for
   consistent compounding with shorter time-underwater stretches
   than alternative configurations tested.
2. **WS Terminal Max** — the same FVG strategy and same ATR-based
   stop, but tunes the risk knobs harder for buyers who want higher
   absolute returns and accept larger swings. Trailing stop arms
   earlier (70% of TP) and trails tighter; add-on entries keep the
   original stop so a fully-scaled-in position carries wider total
   risk in exchange for larger upside on extended moves.

### Pro additionally sees

3. **Custom presets** — any presets you've saved previously appear
   here. Pro licenses can create, rename, and delete custom presets
   from the Config tab once the wizard is done.

Pick one and click **Continue**. The bot loads the preset's values
into the config; both tiers can adjust position sizing (initial
contracts, max cap, per-add-on quantities) from the Config tab.
Pro licenses can additionally fine-tune every other parameter
(stops, take-profit, trailing, breakeven, schedule, multi-entry
policy) from the same tab.

!!! note "Hypothetical-performance disclosure"
    Both preset descriptions above reference backtest results. Past
    backtest performance is not indicative of future returns. See
    the [Disclaimer page](https://ws-trading.co/disclaimer.html) for
    the full NFA-prescribed hypothetical-performance disclosure.

## Step 4 — Confirm + start

The wizard shows a summary of what you've selected: license tier,
broker connection, active preset.

- Click **Finish** — the wizard closes, you're at the main dashboard
- The bot does NOT auto-start. To begin trading, click **Start Bot**
  on the dashboard's main panel

## What the dashboard looks like after the wizard

The main dashboard has three areas:

1. **Top status bar** — bot state (Stopped / Running / In Position /
   Day Done), license tier badge, current symbol price ticker
2. **Left content area** — live position info, today's stats,
   recently-detected FVGs, pending orders
3. **Sidebar nav** — Dashboard, Trade History, Equity Curve,
   Backtester (Pro), Config, Settings

You can now:

- Click **Start Bot** to begin trading on whatever broker connection
  you configured
- Edit settings on the **Config** page if you want to deviate from
  the preset
- View past trades on **Trade History**
- Sign out via Settings → License → Deactivate

## Common first-run issues

### "Sign in with Whop" doesn't open a new tab

Your default browser may not be set, or the bot's pop-up was blocked.
Manually copy the URL from the wizard's "Waiting for browser sign-in"
message and paste it into your browser.

### Whop sign-in succeeds but wizard says "We couldn't find a WS Terminal membership on your Whop account"

Three possible causes:

1. You signed in with the wrong Whop account (a different one than
   the one that bought WS Terminal)
2. Your subscription was cancelled or refunded
3. The webhook from Whop hasn't yet processed your purchase. Wait
   a minute and try again. If it persists for more than 5 minutes,
   contact support.

### "Connection failed" when bot tries to reach IBKR

See the "Common pitfalls" section in
[IB Gateway setup](../broker/ib-gateway.md#common-pitfalls). The most
common causes are: Gateway not running, wrong port, API not enabled.

### Wizard re-appears on every launch

Means the license didn't persist. Check that
`%APPDATA%\WSTerminal\license.json` exists and contains
`"valid": true`. If the folder doesn't exist or the file is empty,
something is wrong with the bot's write permissions to APPDATA — try
running as Administrator once.

## Where to go next

- [Configuration → Strategy](../configuration/strategy.md) —
  understand the FVG, entry, and pyramid settings
- [Configuration → Risk](../configuration/risk.md) —
  set your stop, take-profit, and daily caps
- [Strategy → How it works](../strategy/how-it-works.md) —
  deeper explanation of what the bot does
