# Experiments and Findings

The project was developed through small experiments rather than by assuming how the Bybit market or external APIs behave.

Below are several of the most useful experiments that changed the design.

## 1. Amount-independent market snapshots

### Question

Can historical Bybit P2P snapshots be collected without binding them to one user-entered RUB amount?

### Test

Compared a generic request using:

```text
amount=""
```

with an amount-specific request using:

```text
amount=100000
```

For the first 100 generic results:

```text
Generic orders:                         100
Locally suitable for 100000 RUB:        35
Orders returned by Bybit for 100000:   100
Common orders:                           35
Only generic/local:                       0
Only amount-specific:                    65
```

### Finding

A generic first page does not fully reproduce the result set returned by an amount-specific Bybit query.

### Design decision

The two use cases were separated:

- **V1** keeps the amount in the live user request;
- **V2** stores broader market snapshots without tying the database schema to a user amount.

This keeps historical data reusable while preserving Bybit's own amount-specific behavior in live searches.

---

## 2. Bybit request latency

### Question

Would requesting fewer P2P ads make V1 significantly faster?

### Test

Compared different result sizes while measuring request time.

### Finding

Reducing the page size did not materially reduce the response time.

Local normalization, filtering and sorting were fast compared with the external HTTP request.

### Conclusion

The main bottleneck was the Bybit endpoint itself rather than Python processing or response size.

### Design decision

Kept the larger result set for better coverage instead of sacrificing data for little or no latency improvement.

---

## 3. Overlapping live requests

### Problem

A single Bybit request can take longer than the desired monitoring interval.

Waiting for every request to finish before starting the next one would reduce market freshness.

### Experiment

Changed V1 so a new request can be launched every 5 seconds while allowing a small number of requests to remain in flight.

Current configuration:

```text
request interval: 5 seconds
max in-flight requests: 3
```

### New issue discovered

Concurrent requests can finish out of order.

An older request may complete after a newer one.

### Design decision

V1 keeps track of request IDs and processes the freshest completed result while older responses are ignored.

This improved result freshness without rewriting the project around asynchronous networking.

---

## 4. External FX reference isolation

### Problem

V1 originally depended directly on an external USD/RUB request.

If the FX provider was slow or temporarily unavailable, the live market loop could be delayed.

### Change

Moved USD/RUB updating into a separate process.

The updater:

- requests the latest reference rate;
- writes it into a shared JSON cache;
- uses atomic file replacement;
- keeps the previous good cache if an update fails.

V1 and V2 only read the cached value.

### Result

Live Bybit monitoring no longer depends on Twelve Data response latency during normal processing.

---

## 5. V2 collection cadence

### Goal

Collect useful historical market data without starting the next cycle based on how long the previous one happened to take.

### Test

Measured multi-page snapshot collection over repeated cycles.

Typical observations in that profiling run:

```text
pages per snapshot: ~4
orders per snapshot: ~340–360
collection time: ~35–42 seconds
target cadence: 60 seconds start-to-start
```

The later multi-day PostgreSQL history contains a wider range of order counts and collection durations; see [`analysis/README.md`](analysis/README.md) for the full public summary.

### Design decision

V2 uses a one-minute **start-to-start** cadence:

```text
sleep = max(0, 60 seconds - cycle duration)
```

Pages remain sequential because the market is changing during collection and predictable page order is preferred over speculative parallelization.

---

## 6. Failure and logging review

Longer test runs were used to inspect:

- request failures;
- retries;
- scheduler stalls;
- stale/out-of-order responses;
- Telegram delivery;
- reference API failures;
- snapshot timing.

After the profiling phase, high-volume logs were removed and only useful operational events were kept.

One important finding was that authenticated request URLs could expose API credentials in tracebacks.

### Design decision

Error messages were changed to log only safe information such as:

```text
Twelve Data HTTP <status>
Telegram request failed: <exception type>
```

The affected API key was also rotated.

---


## 7. Data-driven V1 quality gate

### Question

The initial V1 merchant gate used:

```text
recent orders >= 500
completion rate >= 98%
```

Those values were a reasonable starting heuristic, but they were not derived from observed market data.

Could the gate be improved without sacrificing candidate coverage or admitting an unnecessarily weak merchant segment?

### Test

After V2 had accumulated enough history, multiple combinations of `recent_order_num` and `recent_execute_rate` were compared.

The main dense comparison set used:

```text
237 market snapshots
24,699 relevant observations
10,000 RUB user amount
bank transfer
baseline gate: 500 / 98
```

The analysis included:

- market distributions of recent order count and completion rate;
- coverage under different thresholds;
- a coarse grid of 40 threshold combinations;
- a bank-transfer relevance check;
- observed merchant persistence/stability by activity band;
- a finer comparison across the 300–500 recent-order range.

### Finding

The original `500 / 98` rule was driven mostly by the `500 recent orders` boundary. Adding `98% completion` after that threshold removed almost no additional observations.

The strongest practical result was the comparison between the original baseline and `400 / 99`.

Across the dense sample:

```text
500 / 98 average best price: 86.2162 RUB/USDT
400 / 99 average best price: 84.9991 RUB/USDT
average price difference:   approximately -1.409%
candidate coverage:          100%
```

Thresholds below 400 did not produce an additional best-price improvement in the tested sample, while stricter order-count thresholds increasingly reduced useful market coverage or worsened the available best price.

A `100% completion` requirement also reduced the candidate set substantially without a corresponding price benefit.

### Design decision

The V1 product gate was changed to:

```text
recent orders >= 400
completion rate >= 99%
```

The important point is not the exact numbers by themselves, but how they were chosen:

```text
initial heuristic
→ collect historical market data
→ compare threshold combinations
→ measure price / coverage trade-offs
→ update the product rule
```

### Limitation

This result is an **empirical market filter**, not proof that a merchant is safe.

The historical dataset contains listings and merchant statistics, but not verified transaction outcomes, complaints, or fraud labels. The `400 / 99` rule should therefore be interpreted as a data-supported quality gate for the observed RUB → USDT market window, not as a universal merchant-risk guarantee.

---

## Summary

The main development pattern throughout the project has been:

```text
question
→ experiment
→ measurement
→ design change
→ longer verification run
```

The goal is to keep the implementation simple and only add complexity when a real limitation has been observed.
