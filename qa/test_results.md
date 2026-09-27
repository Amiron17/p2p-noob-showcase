# Test Results

**Test status:** 🟢&nbsp;PASS · 🟢&nbsp;FAIL&nbsp;/&nbsp;FIXED · 🟡&nbsp;OPEN · 🔴&nbsp;FAIL · 🔴&nbsp;NOT&nbsp;RUN

**Retest status:** 🟢&nbsp;PASS · 🔴&nbsp;FAIL · 🔴&nbsp;NOT&nbsp;RUN · TBD (to be determined) · — (not required)

| ID | Test&nbsp;status | Retest&nbsp;status | Actual&nbsp;result |
|---|---|---|---|
| `smoke1` | 🟢&nbsp;PASS | — | Same as expected. |
| `smoke2` | 🟢&nbsp;PASS | — | Same as expected. |
| `smoke3` | 🟢&nbsp;PASS | — | Same as expected. |
| `b1c1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c1r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c1r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c1r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c1r3t2` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c2r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c2r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b1c2r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c1t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1t2` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1t3` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c1r1t2` | 🟢&nbsp;FAIL&nbsp;/&nbsp;FIXED | 🟢&nbsp;PASS | Zero amount initially started monitoring; validation was fixed and retested. |
| `b2c1r1t3` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1r1t4` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1r1t5` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c1r3t1` | 🟡&nbsp;OPEN | — | Maximum supported amount remains undefined, so the boundary requirement is not yet testable. |
| `b2c1r3t2` | 🟢&nbsp;FAIL&nbsp;/&nbsp;FIXED | 🟢&nbsp;PASS | The historical notes record the `-0` case as failed, then fixed and retested; the exact original incorrect behavior was not retained. |
| `b2c1r3t3` | 🟢&nbsp;FAIL&nbsp;/&nbsp;FIXED | 🟢&nbsp;PASS | Zero formatting was inconsistent, then fixed and retested. |
| `b2c1r3t4` | 🟡&nbsp;OPEN | — | Maximum supported target-profit value remains undefined, so the boundary requirement is not yet testable. |
| `b2c1r4t1` | 🟢&nbsp;FAIL&nbsp;/&nbsp;FIXED | 🟢&nbsp;PASS | Optional search parameters remained editable during active monitoring; the UI state was fixed and retested. |
| `b2c1r4t2` | 🟢&nbsp;FAIL&nbsp;/&nbsp;FIXED | 🟢&nbsp;PASS | The `START` control did not clearly represent the active state; the UI state was fixed and retested. |
| `b2c1r4t3` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1r4t4` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c1r4t5` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c2t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c2r1t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c2r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c2r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c3t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c3r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c3r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c3r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c4t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c4r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c4r1t2` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c4r1t3` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c4r1t4` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c4r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c4r3t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c5t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b2c5r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c5r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c5r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c6t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c6r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c6r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c6r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c6r3t2` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b2c6r4t1` | 🔴&nbsp;FAIL | TBD | Temporary connectivity/retry state was not clearly surfaced in the Web UI. |
| `b3c1t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c1r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b3c1r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b3c1r3t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c1r3t2` | 🟡&nbsp;OPEN | — | BUG-001 was not reproduced during network-failure attempts or the 529-cycle endurance run. The original trigger remains unknown, so the defect is still open. |
| `b3c2t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c2r1t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b3c2r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b3c2r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b3c3t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c3r1t1` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c3r1t2` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c3r1t3` | 🟢&nbsp;PASS | — | Same as expected. |
| `b3c3r2t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |
| `b3c3r3t1` | 🔴&nbsp;NOT&nbsp;RUN | TBD |  |

## Bug Summary

**Bug Status:** 🟡&nbsp;OPEN · 🟢&nbsp;CLOSED

**Retest Status:** 🟢&nbsp;PASS · 🔴&nbsp;FAIL · 🔴&nbsp;NOT&nbsp;RUN

| Bug&nbsp;ID | Status | Retest | Severity | Priority | Source | Related test | Notes |
|---|---|---|---|---|---|---|---|
| <nobr>`BUG-001`</nobr> | 🟡&nbsp;OPEN | 🔴&nbsp;NOT&nbsp;RUN | Major | High | Endurance testing | `b3c1r3t2` | Delayed snapshot may be saved after a multi-hour collection cycle. |

## Regression Runs

Targeted regression runs are added as needed when new fixes or affected risk areas require regression testing.

**Regression Status:** 🟢&nbsp;PASS · 🔴&nbsp;FAIL · 🔴&nbsp;NOT&nbsp;RUN

| ID | Type | Status | Related changes | Executed tests | Notes |
|---|---|---|---|---|---|
| `REG1` | Targeted | 🟢&nbsp;PASS | Zero-amount validation fix (`b2c1r1t2`) | `b2c1t1`, `b2c1t2`, `b2c1r1t3`, `b2c1r1t4` | Related amount-input behavior remained valid after the fix. |
| `REG2` | Targeted | 🟢&nbsp;PASS | Target-profit handling fixes (`b2c1r3t2`, `b2c1r3t3`) | `b2c1t3`, `b2c1r1t5` | Related target-profit input behavior remained valid after the fixes. |
| `REG3` | Targeted | 🟢&nbsp;PASS | Active-monitoring UI-state fixes (`b2c1r4t1`, `b2c1r4t2`) | `b2c1r4t3`, `b2c1r4t4`, `b2c1r4t5`, `smoke2` | Timer, second-tab state, restart behavior and the main Order Search flow remained valid after the UI fixes. |
| `REG4` | Full | 🔴&nbsp;NOT&nbsp;RUN | Current `v0.1.0` build after accumulated fixes | Full Regression Suite | Waiting for Full Regression entry criteria to be met. |