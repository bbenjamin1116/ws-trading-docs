# Troubleshooting

!!! info "Under construction"

## What this page will cover

Common issues and fixes:

- **Bot won't connect to IBKR** — port mismatch, API not enabled, Trusted IPs, Gateway not running, TWS session conflict
- **License key rejected** — typo, expired/cancelled subscription, internet connectivity to `api.ws-trading.co`
- **Web dashboard won't load** — port already in use, firewall blocking, wrong URL
- **No trades firing** — config window, FVG size threshold, daily limit reached, multi-entry-only-in-profit gating
- **Where the logs are** — `multi_entry_bot.log` location, what to send when asking for support
- **Bot crashed / froze** — how to diagnose, when to restart, what state survives
- **Cloud sync issues** — `/internal/trade` 401s, license tier mismatch, HMAC errors

Each entry will have a symptom, the most likely cause, and a verification step.
