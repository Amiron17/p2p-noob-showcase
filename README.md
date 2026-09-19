# P2P NOOB

A portfolio showcase for a Python service that monitors **Bybit P2P RUB → USDT** offers, filters unsuitable merchants, compares the best compatible price with an external USD/RUB reference, and notifies the user when the result reaches a chosen target.

The core backend implementation is kept in a private repository. This public repository focuses on the product idea, architecture, engineering decisions, product demos, anonymized historical analysis, QA evidence, and a small UI template/style sample.

> Experimental decision-support project. It does not execute trades or provide financial advice.

## Problem

The cheapest P2P ad is not automatically the best choice.

A useful order also has to satisfy merchant-quality rules, user-specific restrictions, amount/payment constraints, and a price condition relative to an external FX benchmark.

P2P NOOB automates that routine instead of requiring constant manual market refreshes.

## Current system

### V1 — live monitoring

The live path:

- polls the Bybit P2P market;
- normalizes returned ads;
- applies fixed merchant-quality filters;
- applies user compatibility filters;
- selects the best compatible qualified order;
- compares it with the current USD/RUB reference;
- calculates the edge against the user's target;
- sends a Telegram alert when a new matching order appears;
- exposes the current state through a Flask web interface.

Because Bybit requests can take longer than the desired monitoring interval, V1 launches requests every **5 seconds** with up to **3 requests in flight** and processes the freshest completed response when results arrive out of order.

### V2 — historical collection

A separate collector builds broader market history for later analysis:

- one collection cycle per minute, start-to-start;
- sequential pagination;
- duplicate order IDs removed inside a snapshot;
- source page / source position preserved;
- USD/RUB reference stored with each applicable snapshot;
- market data persisted to PostgreSQL.

The historical dataset is used for market-history, merchant-history, anomaly, collector-health, and opportunity analysis.

## Demo

### Live monitoring

The web interface starts continuous monitoring, locks the active search parameters, tracks elapsed time, and displays the latest matching qualified order.

![Live monitoring demo](demos/web_monitor_demo.gif)

### Telegram alert

When a qualified order reaches the configured target, the monitor sends a Telegram notification with the order details and calculated edge.

![Telegram alert demo](demos/telegram_alert_demo.gif)

> Merchant names, order IDs, and merchant-specific links shown in the public demos are anonymized.

## Architecture

```text
                         Twelve Data
                              |
                              v
                        reference updater
                              |
                              v
                    shared USD/RUB cache
                       /             \
                      /               \
                     v                 v
              V1 live monitor      V2 collector
                    ^                  |
                    |                  v
                 Bybit P2P         PostgreSQL
                    |
                    v
             Flask UI / Telegram
```

## Engineering decisions backed by experiments

Several implementation choices came from measurement rather than assumption:

- an amount-free first page did **not** reproduce an amount-specific Bybit result set, so V1 and V2 were separated into user-specific live search vs reusable historical collection;
- reducing Bybit result size did not materially improve response time in the profiling run;
- overlapping requests improved live freshness;
- out-of-order responses required explicit freshest-response handling;
- FX reference fetching was isolated from V1/V2 so a slow provider could not block the live loop;
- longer runs were used to review retries, stale responses, scheduler behavior, Telegram delivery, and snapshot timing.

See [`EXPERIMENTS.md`](EXPERIMENTS.md) for the detailed findings.

## Historical database & analysis

The PostgreSQL collector accumulated a real multi-day history containing:

- **2,376 snapshots**;
- **581,962 stored market-order rows**;
- **887 distinct merchants**;
- **28.20 s** median V2 collection time;
- **39.83 s** p95 collection time;
- **0 declared-vs-saved order-count mismatches** in the public integrity summary.

![Historical P2P market vs reference](analysis/charts/market_price_vs_reference.png)

![V2 collection duration](analysis/charts/collection_duration_over_time.png)

The single extreme duration outlier is the same open V2 long-cycle issue documented by QA.

See [`analysis/README.md`](analysis/README.md) for all five charts, database-level metrics, anonymized history exports, integrity checks, and methodology.

## QA

A dedicated QA iteration was completed on **2026-09-19**.

Covered activities included smoke, functional, negative, exploratory, endurance, research, retest, and targeted regression testing.

Reproducible validation / UI issues found during the iteration were fixed and retested successfully.

**Retest:** PASS  
**Targeted regression:** PASS

One major issue remains open: a rare V2 cycle stayed active for roughly 12 hours after a Bybit timeout and later saved the delayed snapshot. The issue was not reliably reproduced by manual network-loss experiments and has a separate diagnostic research plan.

Start with [`qa/README.md`](qa/README.md) or the [`final QA report`](qa/reports/final_qa_report.md).

## Repository contents

```text
.
├── README.md
├── DEVELOPMENT_HISTORY.md
├── EXPERIMENTS.md
├── demos/
│   ├── web_monitor_demo.gif
│   └── telegram_alert_demo.gif
├── analysis/
│   ├── README.md
│   ├── charts/
│   └── data/
├── qa/
│   ├── README.md
│   ├── reports/
│   ├── planning/
│   ├── results/
│   ├── defects/
│   ├── research/
│   └── evidence/
├── templates/
│   └── index.html
└── static/
    └── style.css
```

The `templates/` and `static/` folders are a small public UI sample. The backend collector, database access layer, API integration code, credentials/configuration, and full production data remain private.

## Technology

**Python · Flask · PostgreSQL · psycopg · requests · Telegram Bot API · Twelve Data API · HTML/CSS**

## Development approach

The project was developed incrementally:

```text
question
→ experiment
→ measurement
→ design change
→ longer verification run
```

The focus has been to keep the implementation simple and add complexity only after a real limitation appears.

See [`DEVELOPMENT_HISTORY.md`](DEVELOPMENT_HISTORY.md) for the project timeline.

## Current limitations

This is still a prototype rather than a production service.

Current limitations include:

- only the RUB → USDT market is implemented;
- user compatibility checks do not cover every possible Bybit trading-preference condition;
- stale reference age is not yet enforced as a blocking rule;
- web state is in-process and is not designed for multi-user production deployment;
- the project depends on a Bybit web endpoint whose behavior can change;
- missing-reference UX still needs improvement;
- the V2 long-cycle defect remains open for research.

## Disclaimer

This project is experimental software built for learning, market analysis, and decision support.

It does **not** execute trades, guarantee merchant reliability, or provide financial advice.
