# Bug Reports

## BUG-001 — V2 collection cycle can remain active for hours after a Bybit timeout and still save the snapshot

**Status:** Open  
**Severity:** Major  
**Priority:** High  
**Component:** V2 historical collector  
**Environment:** Windows, Python, PostgreSQL, Bybit P2P external endpoint  
**Detected by:** Endurance / exploratory testing

### Preconditions
- V2 historical collector is running continuously.
- Snapshot collection uses sequential pagination.
- Bybit request timeout/retry logic is enabled.

### Steps to Reproduce
The issue was found during a long-running endurance test and is not yet reliably reproducible on demand.

Observed sequence:
1. Start `v2_runner.py`.
2. Allow snapshot collection to run continuously.
3. During cycle 28, pages 1–3 complete normally.
4. A request for page 4 hits a Bybit read timeout.
5. The retry/cycle remains active for several hours.
6. The cycle eventually resumes and saves the snapshot.

### Expected Result
A single external API timeout should be handled within the configured retry policy.

If the request cannot be completed within a reasonable bounded time:
- the current collection cycle should fail cleanly;
- no temporally inconsistent snapshot should be saved;
- the next scheduled cycle should be allowed to continue.

### Actual Result
Cycle 28 remained active for approximately 12 hours before completing.

The snapshot was still saved after the long delay:
- `snapshot_id=2361`
- `orders=299`
- `collection=43424.645s`
- `cycle_total=43424.824s`

The cycle also generated a `CYCLE_OVERRUN` of approximately 12 hours.

The following cycle recovered automatically and completed normally.

### Impact
- Historical collection stops producing fresh snapshots while the cycle is stuck.
- The saved snapshot combines data collected over a very large time interval and may not represent one coherent market state.
- The external reference rate stored with the snapshot can be stale relative to the final collected orders.
- Downstream historical analysis may treat this snapshot as normal unless explicitly filtered.

### Notes
- The system recovered without a manual restart.
- The terminal displayed the underlying `Read timed out` exception.
- The file log recorded `BYBIT_RETRY`, but did not preserve the full exception message.
- Root cause has not yet been confirmed.

### Verification After Fix
After a fix, repeat:
1. Normal V2 collection.
2. Forced or simulated request timeout.
3. Retry handling.
4. Snapshot-save validation.
5. Next-cycle recovery.
6. Multi-hour endurance test.

Confirm that:
- a single cycle has a bounded maximum duration;
- an incomplete/stale cycle is not saved as a normal snapshot;
- subsequent cycles continue;
- retry/error details are sufficient for diagnosis.
