# WSTerminal Documentation

The user manual for **WSTerminal** — the FVG Pyramid Futures Engine by **W.S. Trading Co.**

!!! info "Coming soon"
    These docs are under construction. The framework is live so links from the
    bot, the marketing site, and email confirmations all resolve to a real
    page, but the actual content is being written. Check back closer to launch.

## What is WSTerminal?

WSTerminal is an automated futures trading bot. It detects **Fair Value Gaps (FVGs)**
on intraday candles, places retracement limit orders at configurable depths into the
gap, and supports pyramiding (adding contracts when additional same-direction FVGs
trigger). It runs as a packaged `.exe` on your own Windows machine and connects to
Interactive Brokers via IB Gateway or TWS.

## What you'll find here

- **[Getting started](getting-started/installation.md)** — install, license activation, first-run wizard
- **[Connecting your broker](broker/ib-gateway.md)** — IB Gateway and TWS setup, paper vs. live
- **[Configuration](configuration/overview.md)** — every config field explained in plain English
- **[Strategy](strategy/how-it-works.md)** — how FVG pyramid actually works
- **[Updates](updates.md)** — how the in-app update notifier works, what data persists
- **[Troubleshooting](troubleshooting.md)** — common issues + fixes
- **[License & billing](license-billing.md)** — managing your Whop subscription

## Need help?

Email **[support@ws-trading.co](mailto:support@ws-trading.co)** or join the
community Discord (link coming soon).
