# Market data subscription

WS Terminal needs **real-time market data** from IBKR to function
correctly. Without the right subscription, IBKR sends delayed quotes
(15-20 minutes behind) and the bot's FVG detection runs on stale
candles — meaning it'll detect "FVGs" that already filled, place
limit orders at prices the market has already passed, and have no
correct view of where price actually is.

This is one of the easiest things to overlook and one of the most
expensive mistakes if you don't catch it.

!!! danger "If your data is delayed, the bot is broken"
    Delayed data is silently incorrect — the bot still runs, the
    dashboard still shows numbers, orders still get placed. They're
    just placed on stale data, with stale stops, against a market
    that's already moved. **Verify your data subscription is active
    and real-time before you trade live money.**

## What you need

For the contracts WS Terminal supports out of the box (`/MNQ`,
`/NQ`, `/MES`, `/ES`, `/MGC`, `/GC`, `/MCL`, `/CL`, etc. — all
CME/CBOT/COMEX/NYMEX), you need:

**US Securities Snapshot and Futures Value Bundle**

- **Cost:** a few dollars per month — verify current pricing on
  [IBKR's market data pricing page](https://www.interactivebrokers.com/en/pricing/market-data-pricing.php).
  Pricing changes from time to time
- **Covers:** CME, CBOT, COMEX, NYMEX — Level 1 (top of book) data
  for all major US futures contracts
- **Auto-waiver:** waived automatically when your monthly IBKR
  commissions reach a modest threshold. Active futures traders
  typically hit that threshold quickly, so most buyers effectively
  pay $0/month for data

That single bundle is enough for the bot to work correctly. You don't
need depth-of-book (Level 2) for the FVG strategy — Level 1 is fine.

## Subscriber Status — the critical setting

IBKR defaults every new account to **Professional** subscriber status,
which prices market data subscriptions at **roughly 10× the
non-professional rate**. A few-dollar bundle becomes a few-tens-of-
dollars bundle if your status isn't changed.

Most retail traders qualify as **Non-Professional** and should set
their status accordingly:

1. Log in to IBKR Client Portal
2. Go to **Settings → User Settings → Market Data Subscriber Status**
3. Read the qualifying criteria (basically: you don't trade for a
   firm, you don't have professional market-data terminals at work,
   you're not a financial advisor managing other people's money for
   compensation)
4. If you qualify, select **Non-Professional** and submit
5. IBKR processes the change — typically same day

This is a one-time setting change. After that, all your data
subscriptions are billed at retail rates.

!!! warning "Be honest about your status"
    The questionnaire is an attestation. If you're actually a
    professional trader (work for a firm, registered investment
    advisor, etc.) and claim non-professional status, IBKR can claw
    back the discount and the exchanges can charge back fees. Just
    pick the right one.

## How to subscribe

1. Log in to IBKR Client Portal at
   <https://www.interactivebrokers.com>
2. Click your account icon (upper right) → **Settings**
3. In the left sidebar, choose **User Settings → Market Data
   Subscriptions**
4. Find **US Securities Snapshot and Futures Value Bundle**
5. Click to enable it. IBKR may ask you to acknowledge the
   exchange's market data agreement
6. Submit

The subscription typically activates within a few minutes — sometimes
immediately, sometimes after the next exchange refresh cycle (which
happens overnight).

## How to verify it's working

1. Open IB Gateway (or TWS) and log in
2. Open WS Terminal's dashboard
3. On the main panel, the price ticker should show **live, moving
   prices** that update multiple times per second during market hours
4. If prices update only every 15-20 minutes (or show a single
   stale value), your data subscription is delayed or not active

You can also verify directly in TWS or Gateway:

- TWS: open a chart for any CME contract. The "last" price should
  tick visibly during market hours
- Gateway: less obvious since there's no chart UI, but the API
  should return non-stale tick timestamps. WS Terminal's dashboard
  ticker is the easiest verification

## Other data subscriptions you might need later

The single bundle above covers everything in the bot's default
contract list. If you configure WS Terminal to trade something else,
you may need a different subscription:

| If you trade               | You also need                                      |
|----------------------------|----------------------------------------------------|
| ICE futures (sugar, cocoa, etc.) | NYBOT/ICE Futures (Top of Book)             |
| Eurex futures (DAX, etc.)        | Eurex (Top of Book)                         |
| Foreign equity index futures     | Varies by exchange                          |
| Options on futures               | Add the corresponding options data feed     |

For our default contracts (US equity index futures, gold, crude),
the single bundle is all you need.

## Cost summary

For a typical buyer trading the bot's default `/MNQ` config:

| Cost                                     | Monthly      |
|------------------------------------------|-------------:|
| WS Terminal subscription (Basic)         | $39          |
| WS Terminal subscription (Pro, optional) | $99          |
| IBKR commissions per round trip on /MNQ  | ~$0.50–1.00  |
| IBKR market data bundle                  | a few dollars (auto-waived above a modest commission threshold) |
| Total monthly market data effective cost | typically $0 for active users |

For an active bot user trading several contracts a day, the data
fee is essentially $0 because the commission threshold gets hit.

## Common pitfalls

### "Why is my dashboard ticker frozen on a stale price?"

Either the bundle isn't subscribed, or it's subscribed but you're
still flagged as Professional and IBKR is silently delaying your
data because the higher-tier billing failed.

Check both:

- **Subscriptions:** Client Portal → Settings → Market Data
  Subscriptions → confirm "US Securities Snapshot and Futures Value
  Bundle" is listed as Active
- **Subscriber status:** Client Portal → Settings → User Settings →
  Market Data Subscriber Status → confirm you're set to
  Non-Professional (if you qualify)

### "I subscribed but data is still delayed"

Possible causes:

1. The change hasn't propagated yet — try logging out of Gateway/TWS
   and back in
2. The exchange agreement page wasn't acknowledged — go back to the
   subscription and complete the agreement step
3. You're trading a contract not covered by the bundle (see the
   "Other subscriptions" table above)

### "My monthly bill suddenly jumped"

Either the auto-waiver didn't trigger (commissions stayed below the
threshold for the month), or your subscriber status got reset to
Professional by an account update. Check both in Client Portal.

## Where to go next

- [IB Gateway setup](ib-gateway.md) — back to the broker setup
- [First-run wizard](../getting-started/first-run.md) — finish
  configuring the bot once your data subscription is verified
