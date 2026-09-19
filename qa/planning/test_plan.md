# P2P NOOB — Test Plan

## 1. Objective

Verify that the current P2P NOOB build performs its main user and background-monitoring functions correctly, handles common failure conditions safely, and remains stable during continuous operation.

The focus of this test cycle is the current implemented version. New product features are out of scope unless required to fix a confirmed defect.

## 2. Scope

### In Scope
- Web interface startup and search flow
- User input handling
- Bybit P2P request/response handling
- Merchant quality filtering
- User compatibility filtering
- Best-order selection
- USD/RUB reference usage
- Edge calculation
- Telegram notifications
- V1 continuous monitoring
- V2 historical snapshot collection
- Retry and timeout handling
- PostgreSQL snapshot persistence
- Long-running stability
- Regression after bug fixes

### Out of Scope
- Trade execution
- Real-money transaction processing
- Production deployment
- Multi-user production load testing
- Full security audit
- Future V2 market-support scoring
- Future subscription/billing features

## 3. Test Types

### Functional Testing
Verify that implemented features produce the expected result under normal conditions.

### Negative Testing
Use invalid, missing, or unavailable inputs/dependencies and verify that the application fails safely and communicates the problem appropriately.

### Exploratory Testing
Use the application without a fixed step-by-step script to discover unexpected behaviour, edge cases, and interactions between components.

### Regression Testing
After each confirmed bug fix, re-check the affected feature and nearby functionality to make sure the fix did not break existing behaviour.

### Smoke Testing
Run a short critical-path check after code changes:
application starts → Bybit request works → result is processed → UI updates → Telegram path works → V2 can save a snapshot.

### Endurance Testing
Run V1/V2 continuously for several hours and review logs for failures, retries, stalls, stale responses, collection overruns, and recovery behaviour.

## 4. Test Environment

- Windows
- Python virtual environment
- Local PostgreSQL database
- Flask web application
- Bybit P2P external endpoint
- Twelve Data USD/RUB reference source
- Telegram bot integration
- Local browser

## 5. Entry Criteria

Testing can begin when:
- the current code starts successfully;
- required local secrets are available;
- PostgreSQL is running;
- Bybit and reference-rate services are reachable;
- logging is enabled.

## 6. Exit Criteria

The current test cycle can be considered complete when:
- critical user flows have been checked;
- major negative/error scenarios have been exercised;
- confirmed defects have bug reports;
- important fixes have regression checks;
- the endurance run has been reviewed;
- no known critical blocker prevents the main demo flow.

## 7. Public QA Deliverables

The public QA package contains:

- `../reports/final_qa_report.md`
- `../reports/final_qa_report.pdf`
- `test_plan.md`
- `../results/test_coverage_matrix.md`
- `../results/amount_negative_testing.md`
- `../results/target_profit_validation_testing.md`
- `../results/exploratory_testing.md`
- `../results/retest_and_targeted_regression.md`
- `../defects/bug_001_v2_long_cycle.md`
- `../defects/defect_and_gap_register.md`
- `../research/endurance_and_research_report.md`
- `../research/v2_long_cycle_research_plan.txt`
- `../evidence/v2_long_cycle_terminal.jpg`

## 8. Current Known Defect

BUG-001: a V2 collection cycle remained active for approximately 12 hours after a Bybit timeout and later saved the delayed snapshot as a normal historical snapshot.

The defect remains open and requires additional diagnostic logging plus a new endurance run before a targeted fix is implemented.
