# P2P NOOB

## Product Overview

**Build:** v0.1.0

### Goal

Find the most profitable Bybit P2P USDT/RUB order among offers that meet predefined merchant-quality criteria and implemented user search parameters.

### Target User Flow

The user optionally provides:

- desired exchange amount;
- payment method;
- target profit.

The system searches the current Bybit P2P market, applies the implemented search and quality filters, compares eligible offers with the cached USD/RUB reference rate, and returns the best matching order.

## Current High-Level Architecture

### Reference Rate Flow

→ retrieve current USD/RUB reference rate through Twelve Data API  
→ create or update cached `.json` file with the latest valid rate.

### Order Search Flow

→ user search parameters through Web UI  
→ Bybit P2P market monitoring  
→ read cached USD/RUB reference rate  
→ apply search and quality filters  
→ select the best matching order  
→ return result to the user.

### Market History Flow

→ scan Bybit P2P USDT/RUB market and collect market snapshot  
→ read cached USD/RUB reference rate  
→ store market data in PostgreSQL for historical analysis.

## Coming Soon

- Analysis of the selected order relative to the current market.
- Merchant reliability analysis based on historical database records.
- Additional user-specific eligibility checks.
- User notifications for matching opportunities.

## Environment

- **OS:** Windows 10 x64
- **Python:** 3.14.5
- **Database:** PostgreSQL 18.6
- **Browser:** Google Chrome 153.0.8010.53
- **External services:** Bybit P2P web endpoint, Twelve Data API
- **Runtime:** Local machine
- **Network:** Internet connection required

## QA Documentation

- [`test_plan.md`](test_plan.md) — component functionality, key risks, planned checks, test types, and expected results.
- [`test_results.md`](test_results.md) — executed checks, actual results, statuses, retest results, and targeted regression results.
- [`bug_report.md`](bug_report.md) — significant confirmed defects and ongoing investigations, including severity, priority, impact, evidence, and reproduction history.
- [`automation_candidates.md`](automation_candidates.md) — checks that are useful candidates for future automation.

## Note on Historical Findings

Some test cases intentionally preserve the original pre-fix behavior that failed during testing.

Where a defect was fixed, the documentation keeps both the original finding and the current post-fix status instead of rewriting the earlier result as if the failure had never occurred.
