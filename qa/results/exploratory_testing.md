# P2P NOOB — Exploratory Testing Summary

Date: 2026-09-19

## Charter

Explore the web interface during an active search and look for confusing, inconsistent, or broken states.

---

## Observation 1 — Optional fields remain editable during active search

**Observation:**  
After the search starts, optional parameters can still be changed.

**Concern:**  
It becomes unclear whether edited values affect the currently active search or only the next search.

**Suggested behavior:**  
Search parameters should be locked while monitoring is active, or changing them should explicitly restart/update the active search.

**Result:** UX / state-consistency issue

---

## Observation 2 — START button does not reflect active search state

**Observation:**  
After monitoring starts, the button still remains `START`.

**Suggested behavior:**  
The control should clearly show that monitoring is active, for example:

`Searching... / Stop`

or another explicit active-state design.

**Result:** UX issue

---

## Observation 3 — Search timer

**Observation:**  
The timer continues to count search duration correctly while monitoring is active.

**Result:** PASS

---

## Observation 4 — Matching orders

**Observation:**  
Matching orders are displayed correctly during the active search.

**Result:** PASS

---

## Observation 5 — Page refresh during active search

**Observation:**  
Refreshing the page does not break the active search.

The running search state is restored after refresh.

**Result:** PASS

---

## Session conclusion

The exploratory session found:

- 2 UX / state-consistency issues:
  - optional fields remain editable during active monitoring;
  - START button does not reflect active search state;
- 3 positive observations:
  - timer works correctly;
  - matching orders are displayed correctly;
  - page refresh preserves the active search.

The editable optional fields are the most important finding because they create uncertainty about which parameters are currently controlling the active search.
