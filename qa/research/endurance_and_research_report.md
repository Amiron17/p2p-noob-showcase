# Endurance and Research Report

## 1. V1 long-running monitor

The final observed V1 heartbeat was recorded after **12h 34m 56.6s** of uptime.

| Metric | Value |
|---|---:|
| Requests launched | 9031 |
| Requests completed | 9030 |
| Requests succeeded | 9030 |
| Requests failed | 0 |
| Results processed | 9025 |
| Stale responses dropped | 1 |
| Telegram notifications sent | 100 |
| Bybit retry events in raw log | 2 |

### Findings
- V1 continued running through the observed Bybit retry events.
- One delayed response was discarded as stale after a fresher request had already won.
- The final heartbeat reported zero failed completed requests.
- Telegram notifications continued to be sent during the long run.

### Interpretation
The endurance run exercised the concurrency, retry, stale-response and notification paths under
real external latency. It does not prove absence of all failure modes, but it provides useful
stability evidence for the current monitoring architecture.

## 2. V2 baseline before anomaly

Cycles 1-27 provide a useful normal baseline:

| Metric | Observed baseline |
|---|---:|
| Pages per snapshot | 4 |
| Collection time | 35.191s - 40.957s |
| Mean collection time | 37.830s |
| Median collection time | 37.415s |
| Orders per snapshot | 295 - 329 |
| Mean orders | 314.5 |

## 3. BUG-001 evidence

Cycle 28 was a severe outlier:
- page 4 recorded a Bybit retry;
- snapshot `2361` was still saved;
- `orders=299`;
- `collection=43424.645s`;
- `cycle_total=43424.824s`;
- `CYCLE_OVERRUN` was approximately 12 hours;
- the delayed cycle created a risk that the reference context was stale relative to later-collected orders;
- cycle 29 recovered automatically and completed in `29.762s`.

This creates a data-quality problem even though the process eventually recovered.

## 4. Research testing performed after discovery

### Experiment 1 - network loss through retry exhaustion
Result: **NOT REPRODUCED**.

Observed behavior:
- pagination began normally;
- requests failed and retry attempts were exhausted;
- the collector did not remain stuck for hours;
- the next scheduled cycle started.

Conclusion: a simple temporary disconnect with retry exhaustion is not sufficient by itself to
reproduce BUG-001.

### Experiment 2 - connectivity restored before request timeout
Result: **NOT REPRODUCED**.

Observed behavior:
- connectivity was interrupted during collection;
- connection was restored before timeout;
- no retry/error was logged;
- collection continued.

Conclusion: a short interruption resolved before timeout is also not sufficient to reproduce BUG-001.

## 5. Deferred diagnostic plan

Random network toggling was stopped because it was no longer narrowing the problem efficiently.

The next research step is to add stage-level timestamps without changing V2 behavior:

```text
REQUEST_START / REQUEST_END
NORMALIZE_START / NORMALIZE_END
REFERENCE_START / REFERENCE_END
DB_SAVE_START / DB_SAVE_END
```

Optional:
```text
CYCLE_START / CYCLE_END
RETRY_START / RETRY_END
```

A future endurance run can then identify the exact stage containing the long gap before a targeted fix is attempted.
