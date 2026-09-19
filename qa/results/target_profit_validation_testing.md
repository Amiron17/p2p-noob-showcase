# P2P NOOB — Target Profit Validation / Negative Testing

Date: 2026-09-19

## Purpose

Check how the web UI handles unusual, invalid, boundary, and formatting-related `Target profit` values.

---

## Test 1 — Input `+0`

**Input:** `+0`

**Expected:** value is normalized and displayed simply as `0`.

**Actual:** value changes to `0,0`.

**Result:** FAIL / formatting issue

**Conclusion:** mathematically the value is valid, but the UI formatting is unnecessarily inconsistent. Zero should have one canonical representation.

---

## Test 2 — Input `-0`

**Input:** `-0`

**Expected:** value is normalized to `0`.

**Actual:** UI keeps `-0`.

**Result:** FAIL

**Conclusion:** `-0` has no separate product meaning for Target profit and should be normalized to `0`.

---

## Test 3 — Explicit `+` sign

**Input examples:** `+1`, `+5`, `+0.5`

**Expected:** positive values should be entered without an explicit `+` sign.

Allowed examples:
- `5`
- `0.5`
- `-2`

**Actual:** `+` can be entered.

**Result:** FAIL if this is accepted as the product input rule.

**Conclusion:** the UI should allow either a plain positive number or a negative number with `-`, but not require/support an explicit leading `+`.

---

## Test 4 — Extremely large value

**Input:** very large numeric value

**Expected:** Target profit should have a defined valid range.

**Actual:** very large values can be entered.

**Result:** REQUIREMENT GAP

**Conclusion:** upper and possibly lower limits for Target profit are not yet defined. QA should not invent them. Product requirements should define acceptable MIN/MAX values first.

After that, boundary tests should include:
- MIN
- MIN - small step
- MAX
- MAX + small step
- extremely large positive value
- extremely large negative value

---

## Test 5 — Fractional value

**Input examples:** `0.5`, `-0.5`

**Expected:** fractional values are accepted.

**Actual:** fractional values are accepted.

**Result:** PASS

**Conclusion:** fractional Target profit values work as intended.

---

## Test 6 — Letters

**Input:** `abc`

**Expected:** non-numeric letters are not accepted.

**Actual:** letters are not accepted.

**Result:** PASS

**Conclusion:** basic numeric input restriction works correctly.

---

## Proposed normalization rule

Any zero-equivalent input should be normalized to one canonical display value:

`0`

Examples that should end up displayed as `0`:
- `0`
- `+0`
- `-0`
- `0.0`
- `-0.0`

---

## Summary

| Case | Result |
|---|---|
| `+0` | FAIL / formatting issue |
| `-0` | FAIL |
| Explicit `+` sign | FAIL if product rule is adopted |
| Extremely large value | REQUIREMENT GAP |
| Fractional values | PASS |
| Letters | PASS |

## Current findings

1. Fractional values are supported correctly.
2. Letters are blocked correctly.
3. Zero formatting is inconsistent and should be normalized.
4. `-0` should not remain visible as a distinct value.
5. Explicit `+` input should be disallowed if the chosen product rule is `n` or `-n`.
6. Target profit currently has no defined numeric range, so MIN/MAX remain a requirement gap.
