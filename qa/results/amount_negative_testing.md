# P2P NOOB — Amount Negative Testing Results

Date: 2026-09-19

## Purpose

Check how the web UI and search flow handle unusual, invalid, boundary, and undefined `amount` values.

---

## Test 1 — Negative amount

**Input:** `-2`

**Expected:** negative amount should be rejected before search starts.

**Actual:** browser validation message:

`Value must be greater than or equal to 0`

**Result:** PASS

**Conclusion:** frontend blocks negative values correctly.

---

## Test 2 — Fractional amount

**Input:** `2.5`

**Expected:** fractional value should be rejected if RUB amount is expected to be an integer.

**Actual:** browser validation message:

`Please enter a valid value. The two nearest values are 2 and 3`

**Result:** PASS

**Conclusion:** frontend enforces integer step values.

---

## Test 3 — Empty amount

**Input:** empty field

**Expected:** empty amount is a valid product scenario and should start a search without amount-specific filtering.

**Actual:** search starts and looks for any suitable order.

**Result:** PASS

**Conclusion:** empty amount behaves as designed.

---

## Test 4 — Zero amount

**Input:** `0`

**Expected:** if a meaningful trade amount must be greater than zero, the value should be rejected and search should not start.

**Actual:** search starts and remains active.

**Result:** FAIL

**Finding:** validation currently allows zero.

**Suggested requirement:** `amount > 0` when the amount field is not empty.

---

## Test 5 — Extremely large amount

**Input tested:**

`15000000000000000551050151111111111111111111111`

**Expected:** not currently defined because the product has no specified maximum accepted amount.

**Actual:** search starts.

**Result:** REQUIREMENT GAP

**Conclusion:** the system has no documented upper boundary for `amount`. A maximum value should be defined explicitly before this case can have a clear PASS/FAIL result.

Possible product rule to define later:

`1 <= amount <= MAX_AMOUNT`

The exact value of `MAX_AMOUNT` should be a product decision rather than an arbitrary QA choice.

---

## Summary

| Amount case | Result |
|---|---|
| Negative (`-2`) | PASS |
| Fractional (`2.5`) | PASS |
| Empty | PASS |
| Zero (`0`) | FAIL |
| Extremely large number | REQUIREMENT GAP |

## Current findings

1. Negative values are blocked correctly.
2. Fractional values are blocked correctly.
3. Empty amount is intentionally supported.
4. Zero is accepted even though it has no meaningful trade value; this should likely be changed to require `amount > 0` when provided.
5. No maximum amount is currently defined. Negative testing exposed a requirement gap rather than a confirmed software bug.
