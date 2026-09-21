# QA Test Results

## Summary

The main live-monitoring and historical-collection flows passed smoke and functional testing. Several validation and UI-state issues were found, fixed, and successfully retested. Targeted regression around the affected areas did not reveal additional regressions.

One major historical-collection defect remains open and under continued endurance testing: **BUG-001**.

## Evidence and reporting scope

This report summarizes previously performed manual checks. This documentation review did not execute new tests or verify a new build. PASS and FIXED / RETESTED labels below preserve the original recorded outcomes; they are not new certification results.

The original notes do not retain an exact build/commit, browser and OS versions, or detailed inputs for every check. Grouped PASS summaries describe the exercised scope, not exhaustive coverage. The 529-cycle follow-up is recorded in the original report; its complete per-cycle log is not included in this showcase.

See [defects.md](defects.md) for structured incident and historical defect records, including evidence limitations.

## 1. Smoke and functional checks

<table>
<tr>

<td width="28%" valign="top">

<h3>Live monitoring flow</h3>

<pre>
External reference
      ↓
Live monitor
      ↓
Web UI
      ↓
Telegram alert

Smoke: PASS
</pre>

</td>

<td width="28%" valign="top">

<h3>Historical collection flow</h3>

<pre>
External reference
      ↓
Bybit market data
      ↓
Historical collection
      ↓
PostgreSQL snapshot save

Smoke: PASS
</pre>

</td>

<td width="44%" valign="top">

<h3>Functional testing: PASS</h3>

Functional testing covered normal implemented behavior across both flows, including:

- order filtering and best-order selection;
- amount and payment-method handling;
- reference-rate usage and edge calculation;
- Telegram notification delivery;
- snapshot persistence in PostgreSQL;
- saved-order count consistency;
- reference persistence;
- stored order contents.

</td>

</tr>
</table>

## 2. Negative and exploratory testing

### Amount input

<table width="100%">
<tr>
  <th width="33%">Case</th>
  <th width="33%">Pre-fix result</th>
  <th width="33%">Current status</th>
</tr>
<tr>
  <td>Negative amount</td>
  <td>PASS — rejected</td>
  <td>unchanged</td>
</tr>
<tr>
  <td>Fractional amount</td>
  <td>PASS — rejected</td>
  <td>unchanged</td>
</tr>
<tr>
  <td>Empty amount</td>
  <td>PASS — supported as “any amount”</td>
  <td>unchanged</td>
</tr>
<tr>
  <td>Zero amount</td>
  <td>FAIL — monitoring started</td>
  <td>🟢 <strong>FIXED / RETESTED</strong></td>
</tr>
<tr>
  <td>Extremely large amount</td>
  <td>REQUIREMENT GAP</td>
  <td>🔴 <strong>UNDEFINED</strong></td>
</tr>
</table>

### Target profit

<table width="100%">
<tr>
  <th width="33%">Case</th>
  <th width="33%">Pre-fix result</th>
  <th width="33%">Current status</th>
</tr>
<tr>
  <td>Fractional values</td>
  <td>PASS</td>
  <td>unchanged</td>
</tr>
<tr>
  <td>Letters</td>
  <td>PASS — blocked</td>
  <td>unchanged</td>
</tr>
<tr>
  <td><code>-0</code></td>
  <td>Reported as FAIL; the original note does not specify the exact incorrect display or behavior</td>
  <td>🟢 <strong>FIXED / RETESTED</strong></td>
</tr>
<tr>
  <td>Zero formatting</td>
  <td>inconsistent</td>
  <td>🟢 <strong>FIXED / RETESTED</strong></td>
</tr>
<tr>
  <td>Explicit leading <code>+</code></td>
  <td>Accepted a leading +; the original note does not specify the required normalization rule, so acceptance alone does not establish a defect</td>
  <td>Change recorded as FIXED / RETESTED; original expected behavior not retained</td>
</tr>
<tr>
  <td>Very large values</td>
  <td>REQUIREMENT GAP</td>
  <td>🔴 <strong>UNDEFINED</strong></td>
</tr>
</table>

### Exploratory UI findings

<table width="100%">
<tr>
  <th width="33%">Case</th>
  <th width="33%">Pre-fix result</th>
  <th width="33%">Current status</th>
</tr>
<tr>
  <td>Optional parameters menu for active monitoring</td>
  <td>FAIL — remained editable</td>
  <td>🟢 <strong>FIXED / RETESTED</strong></td>
</tr>
<tr>
  <td>The <code>START</code> control button</td>
  <td>FAIL — did not clearly represent the active state</td>
  <td>🟢 <strong>FIXED / RETESTED</strong></td>
</tr>
</table>

### Network interruption

<table width="100%">
<tr>
  <th width="33%">Case</th>
  <th width="33%">Pre-fix result</th>
  <th width="33%">Current status</th>
</tr>
<tr>
  <td>Temporary network loss during live monitoring</td>
  <td>PASS — process remained alive, retry/error handling activated, no false Telegram alert was observed, and valid results resumed after connectivity returned</td>
  <td>unchanged</td>
</tr>
<tr>
  <td>User-facing connection status</td>
  <td>UX GAP — temporary connectivity/retry state was not clearly surfaced in the web interface</td>
  <td>🔴 <strong>OPEN</strong></td>
</tr>
</table>

## 3. Endurance testing

### BUG-001 — historical collection cycle can remain active for hours and still save the delayed snapshot

**Status:** 🔴 OPEN / RESEARCH REQUIRED  
**Severity:** Major  
**Priority:** High  
**Component:** Historical market collector  
**Detected by:** Endurance testing  
**Description:** A collection cycle started normally but encountered a Bybit request timeout during pagination. The recorded wall-clock interval between collection start and finish was roughly **12 hours**, after which the snapshot was saved. The available logs do not identify where the delay occurred or establish that the process was executing continuously during this interval. The next collection cycle started normally, so the collector recovered without a manual restart.  
**Observed incident data:** Snapshot - `2361`; Orders saved - `299`; Cycle duration from the exported database timestamps - `43424.645s` (~12h 4m). The earlier report quoted `43424.824s`; the small timing difference does not affect the finding.

<p align="center">
  <img src="evidence/long_collection_cycle_terminal.jpg" alt="BUG-001 terminal evidence" width="620">
</p>

<p align="center">
  <em>The defect matters because a delayed snapshot can combine market data collected across a very large time interval and later be treated as an ordinary historical snapshot.</em>
</p>


**Reproduction attempts:** The issue has not been reproduced reliably.

- network loss until retry exhaustion — **NOT REPRODUCED**;
- connectivity restored before timeout — **NOT REPRODUCED**;
- later endurance run of 529 consecutive collection cycles — **NOT REPRODUCED**.

**Cause:** Unconfirmed. The timeout precedes the saved snapshot in the terminal output, but the evidence does not establish that the request itself consumed the full interval.

**Next diagnostic steps (planned, not executed in this documentation review):** Add timestamped start/end logs for requests, retry waits, reference retrieval, database writes and cycle boundaries; record a monotonic elapsed-time measurement alongside wall-clock timestamps; inspect host sleep/resume events; capture the build and environment. This should distinguish a blocked stage from host suspension or another timing issue.

## 4. Fix verification

All issues marked **FIXED / RETESTED** above were verified after the changes were applied.

A targeted regression check focused on the validation and UI-state areas affected by the fixes. No regressions were found in the tested flows. Checked areas included:

- valid amount values and normal monitoring start;
- negative/fractional amount validation;
- timer and refresh with active state;
- second-tab state;
- stop/restart behavior;
- valid positive, negative, and fractional target-profit values;
- critical monitor → result → Telegram path.
