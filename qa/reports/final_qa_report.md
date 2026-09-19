# P2P Market Monitor - Final QA Report

**Project:** P2P Market Monitor / P2P NOOB  
**QA iteration date:** 2026-09-19  
**Environment:** Windows, Python, Flask, local PostgreSQL, Bybit P2P endpoint, cached USD/RUB reference, Telegram bot, local browser  
**Final iteration status:** **COMPLETED**  
**Retest:** **PASS**  
**Targeted regression:** **PASS**

## 1. Executive summary

The QA iteration covered the implemented live-monitoring and historical-collection flows using
smoke, functional, negative, exploratory, endurance, research, retest, and targeted regression
activities.

The main user flow remained functional after the selected fixes. Testing discovered:
- one major open V2 data-quality / long-cycle defect;
- reproducible input-validation and active-monitor UX issues that were fixed and verified;
- additional open UX/product gaps;
- several deliberately deferred failure-injection scenarios.

The iteration is closed for the current selected scope, while BUG-001 remains open for separate research.

## 2. Test objective and scope

The objective was to verify that the current implemented build:
- performs its main user and background-monitoring functions;
- handles common invalid inputs and external failures safely;
- persists V2 market snapshots correctly;
- remains operational during long-running monitoring;
- does not regress around selected fixes.

### In scope
Web UI, user inputs, Bybit request handling, merchant/compatibility filtering, best-order selection,
USD/RUB reference usage, edge calculation, Telegram notifications, V1 monitoring, V2 snapshot
collection, retry/timeout behavior, PostgreSQL persistence, endurance and regression around fixes.

### Out of scope
Trade execution, real-money processing, production deployment, multi-user production load,
full security audit, future V2 support scoring, subscription/billing features.

## 3. Test activities and results

| Test type | Result | Notes |
|---|---|---|
| Smoke | PASS | Critical demo path exercised |
| Functional | PASS / PARTIAL | Core path worked; some planned standalone checks skipped |
| Negative | MIXED | Found real defects, UX issues and requirement gaps |
| Exploratory | COMPLETED | Two active-monitor UX/state issues discovered |
| Endurance V1 | PASS | ~12h 34m 56.6s; retries and stale-response protection exercised |
| Endurance V2 | ISSUE FOUND | BUG-001 discovered |
| Research | IN PROGRESS | Two network scenarios did not reproduce BUG-001 |
| Retest | PASS | Selected fixes verified |
| Targeted regression | PASS | Neighboring changed-area behavior remained functional |
| Full regression suite | NOT CLAIMED | Not all planned regression items were executed |

## 4. Key functional evidence

### Smoke
The critical path was exercised successfully:
- reference path;
- V1 runner;
- web UI;
- Telegram alert path;
- V2 runner;
- PostgreSQL snapshot save.

### V2 functional checks
Recorded checks included:
- pagination through the final page;
- completed snapshot save;
- `order_count` consistency with saved order rows;
- reference-rate persistence;
- stored order content review.

Standalone timestamp-consistency validation was skipped and is not counted as PASS.

## 5. Negative and exploratory findings

### Amount input
Pre-fix results:
- negative value: PASS (rejected);
- fractional amount: PASS (rejected);
- empty amount: PASS (supported as “any amount”);
- zero amount: FAIL;
- extremely large amount: REQUIREMENT GAP.

After the fix, zero amount is rejected while empty amount remains supported.

### Target profit
Pre-fix results:
- fractional values: PASS;
- letters: PASS (blocked);
- `-0`: FAIL;
- zero formatting: inconsistent;
- explicit leading `+`: allowed though the chosen product rule excludes it;
- numeric MIN/MAX: REQUIREMENT GAP.

Selected formatting/input issues were fixed and verified.

### Active-monitor exploratory findings
The session found:
- editable optional fields during active monitoring;
- START button not reflecting active state.

Positive observations:
- timer continued correctly;
- matching orders displayed correctly;
- page refresh preserved active state.

The two selected UX findings were fixed and verified.

### Network loss during V1
Core behavior passed:
- process stayed alive;
- retry/error path activated;
- no false Telegram alert was observed;
- valid matching results resumed after reconnection.

A remaining UX improvement is to show temporary connection/retry status in the UI.

### Missing reference
This remains open: the user can reach an active-looking monitoring state without clear feedback that
the reference value is unavailable.

## 6. Endurance evidence

### V1
Final observed heartbeat after **12h 34m 56.6s**:
- launched: 9031;
- completed: 9030;
- succeeded: 9030;
- failed: 0;
- processed: 9025;
- stale dropped: 1;
- Telegram sent: 100;
- Bybit retry events observed: 2.

### V2 normal baseline
Cycles 1-27:
- four pages per snapshot;
- collection time 35.191s to 40.957s;
- mean collection time 37.830s;
- 295 to 329 orders.

## 7. BUG-001 - major open defect

Cycle 28:
- Bybit retry recorded on page 4;
- snapshot `2361` saved with `299` orders;
- collection time `43424.645s`;
- total cycle time `43424.824s`;
- overrun approximately 12 hours;
- the delayed cycle created a risk that the reference context was stale relative to later-collected orders.

Cycle 29 then recovered automatically in about 30 seconds.

### Impact
- no fresh V2 snapshot while the cycle is stuck;
- one snapshot can span a very large real-time interval;
- market orders and reference context may no longer describe one coherent market moment;
- downstream historical analysis may treat the bad snapshot as ordinary data.

### Current research status
Two manual network experiments did **not** reproduce the long cycle:
1. network loss until retry exhaustion;
2. connectivity restored before request timeout.

Therefore the root cause is not claimed.

### Next research step
Add stage-level timestamps around request, normalization, reference and database-save stages; then repeat endurance testing.

## 8. Fix verification

### Retest: PASS
Verified:
- `amount=0` rejected;
- empty amount preserved;
- active fields locked;
- active control displays monitoring state and can stop monitoring;
- UI returns to ready state;
- zero-equivalent target profit normalizes to `0`;
- explicit `+` blocked;
- negative/fractional target profit preserved.

### Targeted regression: PASS
Related behavior remained functional, including valid inputs, monitor start/stop/restart, timer, refresh,
second-tab state, and the critical V1/Bybit/reference/result/Telegram path.

This report does **not** claim a full regression run of every project component.

## 9. Open items

### Confirmed open defect
- BUG-001 V2 abnormal long collection cycle.

### Open UX/product behavior
- missing-reference status in UI;
- temporary network/retry status visibility;
- 24-hour no-result auto-stop / message / reset lifecycle.

### Requirement gaps
- maximum accepted amount;
- target-profit MIN/MAX range.

### Deferred test scenarios
- Telegram transport failure injection;
- database failure / rollback test;
- malformed Bybit payload fail-closed test.

## 10. Final conclusion

The current QA iteration is complete for its selected scope.

**Retest: PASS**  
**Targeted regression: PASS**  
**Main implemented user flow: functional after selected fixes**  
**BUG-001: OPEN / RESEARCH REQUIRED**

The package intentionally keeps pre-fix FAIL evidence, post-fix verification, raw endurance data and the
final reconciled status together so the QA process remains traceable rather than presenting only the
successful end state.
