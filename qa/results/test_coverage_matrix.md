# Test Coverage Matrix

| Area | Status | Main result |
|---|---|---|
| Smoke | PASS | Critical path exercised: reference, V1, web UI, Telegram, V2, PostgreSQL snapshot save |
| Functional - V1/Web | PASS / PARTIAL | Main implemented user flow worked; some planned checks were not executed as standalone cases |
| Functional - V2 | PASS / PARTIAL | Pagination, snapshot save, order-count consistency, reference persistence and stored order content checked; timestamp-consistency case was skipped |
| Negative - amount | MIXED -> FIXED | Negative/fractional/empty behaved correctly; zero failed before fix; amount maximum remains undefined |
| Negative - target profit | MIXED -> FIXED | Fractions/letters behaved correctly; zero formatting and explicit plus were fixed; MIN/MAX range remains undefined |
| Negative - network loss | PASS with UX gap | V1 survived outage/retries and resumed without false Telegram alert; explicit network-status UI remains a possible improvement |
| Negative - missing reference | FAIL / OPEN | Search could appear active without a usable reference and without clear UI feedback |
| Negative - no suitable order | PARTIAL / PLANNED | No false order/alert observed; 24h auto-stop + no-result lifecycle remains product work |
| Negative - Telegram failure | NOT EXECUTED | Deferred |
| Negative - database failure | NOT EXECUTED | Deferred |
| Negative - malformed Bybit response | NOT EXECUTED | Deferred |
| Exploratory | COMPLETED | Two active-search UX/state findings discovered; both selected findings were fixed |
| Endurance - V1 | PASS | ~12h35m, 9030 successful completed requests, 0 failed in final counters, retry recovery exercised |
| Endurance - V2 | ISSUE FOUND | BUG-001: one cycle took ~12h and was still saved; next cycle recovered |
| Research - BUG-001 | IN PROGRESS | Two manual network-loss scenarios did not reproduce the long cycle; diagnostic plan prepared |
| Retest | PASS | Selected fixed defects no longer reproduced |
| Targeted regression | PASS | Neighboring behavior around changed validation and active-monitor UI remained functional |
| Full regression suite | NOT CLAIMED | Only targeted regression was completed in this iteration |

## Smoke checks recorded as passed

- reference process / cache path;
- V1 runner;
- web UI;
- Telegram alert path;
- V2 runner;
- PostgreSQL snapshot save.

## Functional notes

The test plan contained more candidate checks than were individually executed and recorded.
The final report therefore distinguishes the **critical flow result** from unexecuted or skipped
standalone checks instead of converting every checklist item into a PASS.
