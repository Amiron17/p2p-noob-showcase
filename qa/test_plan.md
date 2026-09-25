# Test Plan

The test plan follows the current high-level architecture. Each architecture step is treated as a separate testable component. For each component, the plan defines its functionality, key risks and planned checks.

## Smoke Testing

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `smoke1` | Smoke | Reference Rate Flow: retrieve USD/RUB rate and update cached `.json` file. | A valid reference rate is received and the cache file is created or updated successfully. |
| `smoke2` | Smoke | Order Search Flow: submit valid parameters through Web UI and complete the search flow. | Market data is received, filters are applied, the best matching order is selected and the result is returned to the user. |
| `smoke3` | Smoke | Market History Flow: scan Bybit P2P market, collect a snapshot and store it in PostgreSQL. | A valid market snapshot is collected and stored successfully in PostgreSQL. |

# Block 1 — Reference Rate Flow

## Component: Retrieve USD/RUB Reference Rate

**Code:** `b1c1`

### Functionality

The component retrieves the current USD/RUB reference rate through the Twelve Data API. In the current build, the reference rate is updated once every 2 minutes.

### Key Risks

**r1)** The reference rate cannot be retrieved because the API key is invalid.

**r2)** The reference rate cannot be retrieved because Twelve Data or the network is unavailable.

**r3)** The last successfully retrieved rate becomes outdated because updates stop or are delayed.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b1c1t1` | Functional | Request the USD/RUB rate using a valid API key under normal network conditions. | A valid USD/RUB reference rate is received and accepted by the system. |
| `b1c1r1t1` | Negative | Use an invalid API key. | The system reports an API-key/authorization error and does not accept an invalid result. |
| `b1c1r2t1` | Negative | Interrupt the network connection or make Twelve Data unavailable. | The system reports the request failure and does not use an invalid response as a valid rate. |
| `b1c1r3t1` | Negative | Make the last successful reference-rate update older than the expected update interval. | The system identifies the cached rate as outdated instead of treating it as fresh. |
| `b1c1r3t2` | Endurance | Run the reference-rate component for an extended period under normal conditions. | The reference rate continues to update approximately once every 2 minutes without unexpected termination. |

---

## Component: Create or Update Cached Reference Rate File

**Code:** `b1c2`

### Functionality

The latest valid USD/RUB reference rate is stored in a cached `.json` file. During an update, a temporary file is created first and then replaces the main file. The required directory and cache file are created automatically if they do not already exist.

### Key Risks

**r1)** The cached file cannot be created or updated.

**r2)** Another component reads the cache while it is being updated and receives incomplete or corrupted data.

**r3)** The required directory or cache file does not exist on first launch.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b1c2t1` | Functional | Update an existing cache file with a newly retrieved valid rate. | The cached `.json` file contains the latest valid rate after the update. |
| `b1c2r1t1` | Negative | Make the cache location unavailable for writing. | The update failure is reported and an incomplete cache file is not treated as valid. |
| `b1c2r2t1` | Functional | Read the main cache while a new rate is being written through the temporary-file flow. | The reader receives the complete main file rather than a partially written file. |
| `b1c2r3t1` | Functional | Launch the component without an existing cache directory or cache file. | The required directory and file are created automatically and a valid rate is written successfully. |

---

# Block 2 — Order Search Flow

## Component: User Search Parameters Through Web UI

**Code:** `b2c1`

### Functionality

The Web UI accepts the implemented user search parameters and passes them into the order-search flow.

### Key Risks

**r1)** Invalid values are accepted as valid search parameters.

**r2)** Valid values are changed, lost or interpreted incorrectly when passed into the search flow.

**r3)** Boundary values are handled incorrectly or the supported boundary is not defined.

**r4)** The active-monitoring UI state is inconsistent or allows parameters to be changed while monitoring is already running.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b2c1t1` | Functional | Submit valid search parameters through the Web UI. | The entered parameters are accepted and passed to the search flow correctly. |
| `b2c1t2` | Functional | Leave the amount field empty. | Empty amount is accepted as the supported “any amount” option. |
| `b2c1t3` | Functional | Enter valid positive, negative and fractional target-profit values. | Valid target-profit values are accepted and passed to the search flow correctly. |
| `b2c1r1t1` | Negative | Enter text into a numeric amount field. | The invalid value is rejected or handled by validation and is not used as a valid amount. |
| `b2c1r1t2` | Negative | Enter zero as the amount and start monitoring. | Zero amount is rejected and monitoring does not start with the invalid value. |
| `b2c1r1t3` | Negative | Enter a negative amount. | The negative amount is rejected. |
| `b2c1r1t4` | Negative | Enter a fractional amount. | The fractional amount is rejected. |
| `b2c1r1t5` | Negative | Enter letters into the target-profit field. | Non-numeric target-profit input is blocked or rejected. |
| `b2c1r2t1` | Functional | Compare valid UI input with the parameters received by the search logic. | The search logic receives the same valid values entered by the user. |
| `b2c1r3t1` | Boundary / Requirement | Enter an extremely large amount. | The supported maximum amount and expected validation behavior must be defined before this check can be closed. |
| `b2c1r3t2` | Boundary | Enter `-0` as target profit. | The value is handled consistently according to the implemented target-profit validation and formatting rule. |
| `b2c1r3t3` | Boundary | Enter zero as target profit and inspect its formatting. | Zero target profit is represented consistently in the UI and search parameters. |
| `b2c1r3t4` | Boundary / Requirement | Enter an extremely large target-profit value. | The supported maximum target-profit value and expected validation behavior must be defined before this check can be closed. |
| `b2c1r4t1` | Exploratory / Functional | Start monitoring and attempt to edit optional search parameters. | Active search parameters remain locked until monitoring stops. |
| `b2c1r4t2` | Exploratory / Functional | Start monitoring and inspect the `START` control. | The control clearly represents the active monitoring state. |
| `b2c1r4t3` | Functional | Verify timer and refresh behavior while monitoring is active. | Timer and refresh continue to behave correctly in the active state. |
| `b2c1r4t4` | Functional | Verify the second-tab UI state while monitoring is active. | The second-tab state remains consistent with the active monitoring state. |
| `b2c1r4t5` | Functional | Stop monitoring and start it again. | Monitoring stops and restarts correctly without stale UI state. |

**Requirement note:** The historical notes do not retain a clear normalization rule for an explicit leading `+` in target-profit input. The maximum supported amount and target-profit value are also not defined, so those boundary checks remain open until requirements are specified.

---

## Component: Bybit P2P Market Monitoring

**Code:** `b2c2`

### Functionality

The component requests current USDT/RUB P2P market data from Bybit and provides valid market data to the live order-search flow.

### Key Risks

**r1)** Bybit or the network becomes unavailable during a request.

**r2)** Bybit returns an empty, invalid or incomplete response.

**r3)** Live monitoring stops unexpectedly during prolonged execution.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b2c2t1` | Functional | Request current Bybit P2P market data under normal network conditions. | Valid market data is received and passed to the next component. |
| `b2c2r1t1` | Negative | Interrupt the network connection during a market request. | The failure is handled without producing a false valid market result. |
| `b2c2r2t1` | Negative | Process an empty or invalid Bybit response. | Invalid response data is rejected and is not treated as valid market data. |
| `b2c2r3t1` | Endurance | Run live market monitoring for an extended period. | Market monitoring continues without unexpected termination and valid responses continue to be processed. |

---

## Component: Read Cached USD/RUB Reference Rate

**Code:** `b2c3`

### Functionality

The order-search flow reads the latest cached USD/RUB reference rate from the `.json` file so the selected market result can be compared with the external reference rate.

### Key Risks

**r1)** The cache file cannot be read.

**r2)** The cached data is missing or invalid.

**r3)** An outdated cached rate is used as a valid current reference.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b2c3t1` | Functional | Read a valid and current cached reference-rate file. | The expected USD/RUB reference rate is read successfully and passed to the order-search flow. |
| `b2c3r1t1` | Negative | Make the cached file unavailable for reading. | The read failure is reported and the search flow does not continue using a missing reference value. |
| `b2c3r2t1` | Negative | Provide malformed or incomplete cached data. | Invalid cache data is rejected and is not treated as a valid reference rate. |
| `b2c3r3t1` | Negative | Provide a cached rate older than the accepted update interval. | The stale rate is identified as outdated and is not treated as a fresh reference value. |

---

## Component: Apply Search and Quality Filters

**Code:** `b2c4`

### Functionality

The component applies the implemented user search criteria and merchant-quality requirements to current Bybit P2P orders. Only orders satisfying all mandatory conditions remain eligible for selection.

### Key Risks

**r1)** An order that does not match the user's search criteria remains eligible.

**r2)** An order that fails a mandatory merchant-quality condition remains eligible.

**r3)** A valid order is incorrectly excluded.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b2c4t1` | Functional | Process orders that satisfy all active search and quality filters. | Qualifying orders remain eligible for selection. |
| `b2c4r1t1` | Functional | Include an order whose limits do not include the requested amount. | The order is excluded. |
| `b2c4r1t2` | Functional | Include an order that does not match the selected payment criteria. | The order is excluded. |
| `b2c4r1t3` | Functional | Include an order that does not meet the selected target profit. | The order is excluded from the qualified result set. |
| `b2c4r1t4` | Functional | Include an order that meets the selected target profit and all other mandatory filters. | The order remains eligible for selection. |
| `b2c4r2t1` | Functional | Include an order that fails a mandatory merchant-quality threshold. | The order is excluded. |
| `b2c4r3t1` | Functional | Process a mixed set containing eligible and ineligible orders. | Eligible orders remain and all orders failing mandatory filters are excluded. |

---

## Component: Select the Best Matching Order

**Code:** `b2c5`

### Functionality

The component compares the qualified orders and selects the best matching order according to the current order-selection logic.

### Key Risks

**r1)** The selected order is not the best option among the qualified orders.

**r2)** An order that is not part of the qualified set is selected.

**r3)** The system attempts to select an order when the qualified set is empty.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b2c5t1` | Functional | Provide several qualified orders with different prices. | The best eligible order is selected according to the current selection logic. |
| `b2c5r1t1` | Functional | Change the ordering of prices in an otherwise equivalent qualified set. | The selected result changes accordingly and remains the best eligible order. |
| `b2c5r2t1` | Functional | Include a better-priced order that failed a mandatory filter. | The filtered-out order is not selected. |
| `b2c5r3t1` | Negative | Provide an empty qualified-order set. | No order is selected and the flow handles the absence of an eligible result correctly. |

---

## Component: Return Result to the User

**Code:** `b2c6`

### Functionality

The final order-search result is returned to the Web UI and displayed to the user with the implemented result data and UI state.

### Key Risks

**r1)** The displayed order differs from the order selected by the search logic.

**r2)** Result values are displayed incorrectly or incompletely.

**r3)** The UI shows a normal successful result when no eligible order exists or when the search fails.

**r4)** Temporary connectivity or retry state is not clearly surfaced to the user.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b2c6t1` | Functional | Complete a successful search and compare the selected order with the displayed result. | The UI displays the same order selected by the search logic. |
| `b2c6r1t1` | Functional | Compare key values of the selected internal order with the final UI result. | The displayed values match the selected order without unexpected changes. |
| `b2c6r2t1` | Functional | Verify the implemented fields in a successful result state. | Required result information is displayed correctly and completely. |
| `b2c6r3t1` | Negative | Complete a search with no eligible order. | The UI displays the appropriate no-result state instead of a false successful result. |
| `b2c6r3t2` | Negative | Trigger a handled failure in the search flow. | The UI does not display invalid data as a successful search result. |
| `b2c6r4t1` | Exploratory / UX | Interrupt connectivity during active monitoring and inspect the Web UI. | The UI clearly communicates the temporary connection or retry state to the user. |

---

# Block 3 — Market History Flow

## Component: Scan Bybit P2P Market and Collect Market Snapshot

**Code:** `b3c1`

### Functionality

The component scans the Bybit P2P USDT/RUB market and collects the available order data into a market snapshot for historical storage.

### Key Risks

**r1)** One or more required market requests fail and the collected snapshot becomes incomplete.

**r2)** Duplicate order IDs are included in the collected snapshot.

**r3)** Long-running collection becomes stuck or stops updating.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b3c1t1` | Functional | Run a normal market scan under stable network conditions. | A valid market snapshot is collected and prepared for the next stage. |
| `b3c1r1t1` | Negative | Interrupt the network during the market scan. | The failed collection is detected and incomplete data is not silently treated as a normal complete snapshot. |
| `b3c1r2t1` | Functional | Process market data containing duplicate order IDs. | Duplicate order IDs are removed or prevented according to the current collection logic. |
| `b3c1r3t1` | Endurance | Run repeated market collection for an extended period. | New collection cycles continue to complete without unexpected termination or prolonged inactivity. |
| `b3c1r3t2` | Research / Endurance | Attempt to reproduce the previously observed long-running collection anomaly. | Any recurrence is captured with timing and log evidence; otherwise the anomaly remains not reproduced. |

---

## Component: Read Cached USD/RUB Reference Rate

**Code:** `b3c2`

### Functionality

The market-history flow reads the cached USD/RUB reference rate so the collected historical market data can be stored together with the relevant external reference information.

### Key Risks

**r1)** The cached reference-rate file cannot be read during a collection cycle.

**r2)** Invalid cached data is associated with the market snapshot.

**r3)** An outdated reference rate is associated with newly collected market data.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b3c2t1` | Functional | Read a valid current cached rate during a normal market-history collection cycle. | The valid reference rate is available for the collected snapshot. |
| `b3c2r1t1` | Negative | Make the cache unavailable during collection. | The failure is detected and the collection cycle does not silently use a missing reference value. |
| `b3c2r2t1` | Negative | Provide malformed or invalid cached reference data. | Invalid reference data is rejected and is not associated with a normal valid snapshot. |
| `b3c2r3t1` | Negative | Provide an outdated cached reference rate. | The stale rate is identified and is not treated as a fresh reference value for the new snapshot. |

---

## Component: Store Market Data in PostgreSQL

**Code:** `b3c3`

### Functionality

The component stores the collected market snapshot and its associated data in PostgreSQL so it can later be used for historical market analysis.

### Key Risks

**r1)** A snapshot is stored but some expected market-order data is missing.

**r2)** Duplicate or conflicting records cause incorrect database data or an unhandled error.

**r3)** PostgreSQL becomes unavailable during persistence.

### Checklist

| ID | Test type | Check | Expected result |
|---|---|---|---|
| `b3c3t1` | Functional | Store a valid collected market snapshot in PostgreSQL. | The snapshot and its expected records are stored successfully and can be read back. |
| `b3c3r1t1` | Functional | Compare the collected unique-order count with the persisted records for the snapshot. | The expected number of unique records is stored for the snapshot. |
| `b3c3r1t2` | Functional | Compare persisted order contents with the collected snapshot data. | Stored order contents match the collected snapshot data. |
| `b3c3r1t3` | Functional | Verify that the reference-rate data associated with the snapshot is persisted. | The expected reference-rate data is stored with the historical snapshot. |
| `b3c3r2t1` | Negative | Attempt to persist data that conflicts with current database constraints. | The conflict is handled explicitly and does not silently create incorrect duplicate data. |
| `b3c3r3t1` | Negative | Make PostgreSQL unavailable during persistence. | The database failure is reported and the cycle is not reported as successfully stored. |

---
