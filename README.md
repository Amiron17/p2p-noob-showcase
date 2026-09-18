# P2P NOOB

A Python service that helps find best **Bybit P2P RUB → USDT** offer available.

## Why this exists

Set your conditions, press `START`, and the service keeps checking the market for you. It filters out unsuitable offers, compares the best remaining order with an external USD/RUB reference rate, and sends a Telegram alert when the result reaches your target.

## What it does

- continuously monitors Bybit P2P;
- filters merchants by fixed quality criteria;
- applies user filters such as amount, payment method, KYC region and recent P2P activity;
- compares the best qualified P2P price with USD/RUB from Twelve Data;
- calculates the current edge against the user's target;
- sends a Telegram notification when a matching order appears;
- shows the latest result in a small Flask web interface;
- collects broader market snapshots in PostgreSQL for later analysis.

## Example

A user wants to buy **100,000 RUB worth of USDT** by bank transfer and is willing to accept an edge of at least **-1.0%**.

They enter the conditions and start the monitor.

The service keeps checking Bybit in the background. If a compatible merchant appears with a price that meets the target, the user gets a Telegram message with:

- merchant name;
- P2P price;
- USD/RUB reference;
- calculated edge;
- limits;
- merchant activity and completion rate;
- direct Bybit link.

No manual market watching is required.

> Experimental portfolio project. It does not execute trades or provide financial advice.
