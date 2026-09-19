# Defect, UX Finding, and Requirement-Gap Register

## A. Open confirmed defect

### BUG-001 - V2 collection cycle can remain active for hours after a Bybit timeout and still save the snapshot
- **Status:** OPEN / RESEARCH REQUIRED
- **Severity:** Major
- **Priority:** High
- **Component:** V2 historical collector
- **Evidence:** cycle 28 saved snapshot 2361 after `collection=43424.645s`; next cycle recovered in ~30s.
- **Risk:** historical gap, temporally incoherent snapshot, stale reference context, contaminated downstream analysis.
- **Next step:** add stage-level timestamps and repeat endurance testing before implementing a targeted fix.

## B. Fixed and verified in this QA iteration

### Amount zero accepted
- **Pre-fix:** `amount=0` started monitoring.
- **Fix:** provided amount must be greater than zero; empty amount remains supported.
- **Retest:** PASS.
- **Targeted regression:** PASS.

### Active-search parameters remained editable
- **Pre-fix:** optional fields could be changed while monitoring was active, creating ambiguous state.
- **Fix:** active-monitor parameters are locked.
- **Retest / targeted regression:** PASS.

### START button did not represent active-monitor state
- **Pre-fix:** button still displayed `START` after monitoring began.
- **Fix:** active control reflects `SEARCHING... / STOP`, supports stopping, then returns to ready state.
- **Retest / targeted regression:** PASS.

### Target profit zero formatting / explicit plus
- **Pre-fix:** `-0` remained visible; zero formatting was inconsistent; explicit leading `+` was allowed.
- **Fix:** zero-equivalent values normalize to `0`; explicit leading `+` is blocked.
- **Retest / targeted regression:** PASS.

## C. Open product / UX findings

### Missing reference cache not surfaced clearly
- **Status:** OPEN.
- **Observed:** monitoring could look active while a usable reference was unavailable; UI did not clearly explain the condition.
- **Desired:** detect missing reference, show a clear status, avoid false result/alert, avoid indefinite silent waiting.

### Temporary network outage is not visible in UI
- **Status:** OPEN UX improvement.
- **Core behavior:** recovery worked; retries occurred; no false Telegram alert; valid results resumed.
- **UX finding:** user could not distinguish ordinary waiting from a connectivity/retry condition.

### No-result search lifetime
- **Status:** PLANNED PRODUCT LOGIC.
- **Desired product rule:** if no suitable order is found for 24 hours, stop the monitor, show a clear no-result message, and return the UI to a reusable state.

## D. Requirement gaps - not bugs until requirements are defined

### Maximum amount
- Extremely large amounts were accepted.
- No product maximum is defined.
- Define `MAX_AMOUNT` before assigning PASS/FAIL to upper-boundary cases.

### Target-profit numeric range
- Very large positive/negative values have no defined accepted range.
- Define MIN/MAX before formal boundary testing.

## E. Deferred test coverage - not defect claims
- Telegram transport failure injection: not executed.
- Database failure / rollback behavior: not executed.
- Malformed/missing Bybit payload fail-closed behavior: not executed in this QA session.
