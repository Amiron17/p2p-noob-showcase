# Development History

This public repository is a portfolio showcase.  
The core implementation is developed in a private repository.

The project evolved iteratively through API exploration, market experiments, profiling, reliability work, UI development, and QA.

## Sep 9 — Initial prototype

- explored the Bybit P2P endpoint for RUB → USDT;
- normalized raw order data into a consistent internal structure;
- added merchant quality filters;
- selected the best qualified order by price;
- started shaping the project around a real user scenario rather than a static data script.

## Sep 12 — Amount-independence research

The first historical collector was tied to a user-entered amount.

I tested whether historical snapshots could instead represent the wider market independently from one user's amount.

This led to a split between two responsibilities:

- **V1** — user-specific live search;
- **V2** — broader amount-independent market snapshots for later analysis.

The database model was updated accordingly.

## Sep 13 — PostgreSQL market history

- introduced PostgreSQL snapshot storage;
- stored order identity, merchant data, prices, limits, payment methods, liquidity fields and trading restrictions;
- added snapshot metadata such as collection timestamps and market side;
- handled duplicate order IDs inside a snapshot;
- started exploring market support around candidate prices using nearby independent merchants.

## Sep 15–16 — Live monitoring and product interface

- profiled live Bybit requests and identified the external endpoint as the main latency bottleneck;
- tested smaller page sizes and found that they did not materially improve response time;
- moved from sequential polling to overlapping requests;
- added handling for out-of-order responses so fresher results take priority;
- added Telegram alerts;
- built a Flask-based web interface for entering search conditions and viewing results.

## Sep 18 — Reliability pass

- separated USD/RUB fetching from V1 and V2;
- introduced a shared reference-rate cache;
- used atomic cache replacement to avoid partial writes;
- kept the last successful reference value when temporary API failures occur;
- reduced high-volume diagnostic logging after profiling;
- added safer error logging so authenticated URLs and API keys are not exposed;
- ran longer V1 and V2 sessions to review stability under continuous operation.

## Sep 19 — QA iteration

The project went through a dedicated QA cycle covering:

- smoke testing;
- functional testing;
- negative testing;
- exploratory testing;
- endurance testing;
- research testing;
- retest;
- targeted regression.

The QA pass found and verified fixes for:

- zero-amount validation;
- ambiguous active-search controls;
- editable parameters during active monitoring;
- target-profit zero / explicit-plus formatting behavior.

Retest and targeted regression both passed.

The endurance run also exposed a major open defect in V2: one collection cycle remained active for roughly 12 hours after a Bybit timeout and later saved the delayed snapshot. Manual network-loss experiments did not reproduce the same behavior, so the issue remains open under a separate diagnostic research plan.

## Current status

The project currently has two main technical paths:

- **V1:** live user-specific monitoring and Telegram alerts;
- **V2:** historical market collection for later analysis.

The public showcase now contains:

- architecture and product documentation;
- live web and Telegram demos with anonymized identifiers;
- development history;
- measured experiments and design decisions;
- PostgreSQL history analysis with public-safe charts and exports;
- structured QA documentation and evidence;
- a small UI template/style sample.

The core backend implementation remains private while the showcase focuses on the engineering process and verifiable project outcomes.
