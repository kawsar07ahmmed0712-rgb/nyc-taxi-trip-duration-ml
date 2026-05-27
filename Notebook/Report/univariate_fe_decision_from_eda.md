# Univariate EDA → Feature Engineering Decision Plan

## 1. Files Checked

| Source | Checked Item | Status |
|---|---|---|
| `Exploratory_Data_Analysis(3).ipynb` | Current notebook sections 1–6.3 | Checked |
| `Target_variable(1).md` | Target handling decision | Checked |
| `continous_numeric_analysis(1).md` | Coordinate univariate analysis | Checked |
| `contionous_numeric_univariate_fe_descision(1).md` | Coordinate FE decision | Checked |

---

## 2. Important QA Notes

1. `store_and_fwd_flag` vs `trip_duration` must not be analyzed in univariate EDA.  
   It belongs to bivariate EDA.

2. `vendor_id` vs `trip_duration` must not be analyzed in univariate EDA.  
   It belongs to bivariate EDA.

3. `dropoff_datetime` should not be used as a final model input because it creates target leakage.

4. Target IQR outliers should not be removed blindly.  
   Only clearly invalid target values should be removed:
   - `trip_duration <= 0`
   - `trip_duration > 24 hours`

5. Coordinate IQR outliers should not be removed blindly.  
   Only globally invalid, zero, or outside-wide-NYC coordinate records should be treated as remove candidates.

6. `vendor_id` Pareto plots need a small visual-order fix before final presentation.  
   The bar order and cumulative-line order should be forced with an explicit category order.  
   This does not change the FE decision.

7. Section 7 datetime analysis and Section 8 identifier check are still needed before the final Section 9 master univariate summary.

---

## 3. Master Feature Engineering Decision Table

| Feature | Raw Type | Univariate Finding | Cleaning Decision | Feature Engineering Decision | Final Model Role |
|---|---|---|---|---|---|
| `id` | Identifier | Unique row identifier; not a predictive category | Keep only for tracking/submission | No encoding; no model feature | Exclude from `X` |
| `vendor_id` | Nominal categorical | Clean, low-cardinality, categories `1` and `2`, no missing, train-test stable | Keep; no row removal | Convert to categorical/string; one-hot encode or create `vendor_id_is_2` | Include |
| `pickup_datetime` | Datetime | Needs Section 7 univariate analysis; safe datetime source | Parse to datetime; handle invalid parse if found | Extract time features: hour, day, weekday, month, weekend, cyclic encodings | Include engineered features only |
| `dropoff_datetime` | Datetime / leakage risk | Available after trip completion; related to target | Do not use for final model input | No FE for final model; use only for EDA validation if needed | Exclude from `X` |
| `passenger_count` | Discrete numeric | Valid but imbalanced; `1` dominates; `0` suspicious; `>6` rare anomaly | Keep; no row removal during EDA | Create `is_single_passenger`, `is_zero_passenger`, `is_high_passenger_count`, `passenger_count_group` | Include original + flags/groups |
| `pickup_longitude` | Continuous geospatial | No missing/zero/global invalid; extreme spatial tails exist | Use coordinate remove-candidate mask; do not IQR-remove | Source for distance, center-distance, airport, cluster, boundary flags | Include mainly via engineered features |
| `pickup_latitude` | Continuous geospatial | No missing/zero/global invalid; extreme spatial tails exist | Use coordinate remove-candidate mask; do not IQR-remove | Source for distance, center-distance, airport, cluster, boundary flags | Include mainly via engineered features |
| `dropoff_longitude` | Continuous geospatial | No missing/zero/global invalid; extreme spatial tails exist | Use coordinate remove-candidate mask; do not IQR-remove | Source for distance, center-distance, airport, cluster, boundary flags | Include mainly via engineered features |
| `dropoff_latitude` | Continuous geospatial | No missing/zero/global invalid; extreme spatial tails exist | Use coordinate remove-candidate mask; do not IQR-remove | Source for distance, center-distance, airport, cluster, boundary flags | Include mainly via engineered features |
| `store_and_fwd_flag` | Binary categorical | Clean binary feature; `N` ≈ 99.45%, `Y` ≈ 0.55%; highly rare minority class | Keep; no balancing; no row removal | Binary encode: `N=0`, `Y=1`; preserve rare `Y` class | Include |
| `trip_duration` | Target | No missing/zero/negative; highly right-skewed; extreme >24h values exist | Remove only invalid target records; do not blindly remove IQR outliers | Use as target; test raw target vs `log1p(trip_duration)` | Target `y`, not input |

---

## 4. Feature-Level FE Plan

### 4.1 `id`

**Decision**

- Keep for row tracking and submission.
- Do not use as a model feature.
- Do not encode.

**Future Code Idea**

```python
id_column = "id"
drop_from_model = ["id"]
```

---

### 4.2 `vendor_id`

**Decision**

- Keep as a nominal categorical feature.
- Do not treat `1` and `2` as numerical magnitude.
- No missing imputation needed.
- No rare-category grouping needed.
- No train-test unseen-category issue found.

**Recommended FE**

Option A: one-hot encoding

```python
vendor_id -> vendor_id_1, vendor_id_2
```

Option B: binary indicator

```python
vendor_id_is_2 = 1 if vendor_id == 2 else 0
```

**Preferred for clean ML pipeline**

```python
train["vendor_id"] = train["vendor_id"].astype(str)
test["vendor_id"] = test["vendor_id"].astype(str)
```

Use one-hot encoding with safe unknown handling.

---

### 4.3 `pickup_datetime`

**Decision**

- Use `pickup_datetime` as the safe datetime source.
- Do not use raw datetime directly in most models.
- Extract useful time features after completing Section 7 datetime univariate analysis.

**Recommended FE**

Basic time features:

```python
pickup_hour
pickup_day
pickup_dayofweek
pickup_month
pickup_weekofyear
is_weekend
```

Cyclic time features:

```python
pickup_hour_sin
pickup_hour_cos
pickup_dayofweek_sin
pickup_dayofweek_cos
pickup_month_sin
pickup_month_cos
```

Optional later features:

```python
is_rush_hour
is_late_night
time_of_day_group
```

**Need Before Final Lock**

Complete Section 7:

- datetime parsing check
- date range check
- train-test date coverage
- pickup hour distribution
- weekday/month distribution
- temporal gap check

---

### 4.4 `dropoff_datetime`

**Decision**

- Do not use as final model input.
- It is target-leakage risk because it is only known after trip completion.
- Test data does not contain it.

**Recommended FE**

No final model feature should be created from `dropoff_datetime`.

Allowed only for EDA validation:

```python
calculated_duration = dropoff_datetime - pickup_datetime
```

Do not include derived dropoff features in `X`.

---

### 4.5 `passenger_count`

**Decision**

- Keep as a discrete numeric feature.
- Do not treat as continuous distribution.
- Do not remove rows in EDA.
- `passenger_count = 0` is suspicious.
- `passenger_count > 6` is rare/anomalous.

**Recommended FE**

```python
is_single_passenger = passenger_count == 1
is_zero_passenger = passenger_count == 0
is_high_passenger_count = passenger_count > 6
```

Recommended grouping:

```python
passenger_count_group:
    zero
    single
    couple
    small_group_3_4
    large_group_5_6
    anomaly_7plus
```

**Model Role**

Use:

- original `passenger_count`
- binary flags
- grouped category if useful

Do not apply IQR/skewness-based numeric transformation.

---

### 4.6 Raw Coordinate Columns

Features:

```python
pickup_longitude
pickup_latitude
dropoff_longitude
dropoff_latitude
```

**Decision**

- Keep as source variables for feature engineering.
- Do not clip by IQR.
- Do not remove coordinate IQR outliers.
- Remove/review only clearly invalid coordinate records:
  - globally invalid coordinate
  - zero coordinate
  - outside wide NYC boundary

**Recommended FE**

Distance features:

```python
haversine_distance
manhattan_distance
```

Direction features:

```python
bearing
bearing_sin
bearing_cos
```

Centrality features:

```python
pickup_distance_to_center
dropoff_distance_to_center
```

Airport/geographic flags:

```python
pickup_near_airport
dropoff_near_airport
airport_trip_flag
```

Spatial grouping:

```python
pickup_cluster
dropoff_cluster
```

Diagnostic flags:

```python
pickup_outside_strict_nyc
dropoff_outside_strict_nyc
either_outside_strict_nyc
pickup_outside_wide_nyc
dropoff_outside_wide_nyc
either_outside_wide_nyc
```

**Important Rule**

Use raw coordinates mainly to create robust spatial features.  
Do not rely only on raw latitude/longitude as independent numeric columns.

---

### 4.7 `store_and_fwd_flag`

**Decision**

- Keep as binary categorical feature.
- Preserve rare `Y` class.
- Do not remove rows.
- Do not resample or balance because this is not the target.
- Check usefulness later in bivariate analysis/model feature importance.

**Recommended FE**

```python
store_and_fwd_flag_encoded = 1 if store_and_fwd_flag == "Y" else 0
```

Safe mapping:

```python
train["store_and_fwd_flag_encoded"] = train["store_and_fwd_flag"].map({"N": 0, "Y": 1})
test["store_and_fwd_flag_encoded"] = test["store_and_fwd_flag"].map({"N": 0, "Y": 1})
```

Add safety check:

```python
train["store_and_fwd_flag_encoded"].isna().sum()
test["store_and_fwd_flag_encoded"].isna().sum()
```

---

### 4.8 `trip_duration`

**Decision**

- Use only as prediction target.
- Do not use as input feature.
- Do not use `trip_duration_min` as input feature.
- Do not use `log_trip_duration` as input feature.
- Remove clearly unrealistic target values only.

**Cleaning Rule**

```python
target_clean_mask = (
    (train["trip_duration"] > 0) &
    (train["trip_duration"] <= 24 * 60 * 60)
)
```

**Target Experiment**

Run two modeling experiments:

```python
y_raw = trip_duration
y_log = np.log1p(trip_duration)
```

Final choice should be based on validation performance.

---

## 5. Final Preprocessing Order

### Step 1 — Preserve ID

```python
train_id = train["id"]
test_id = test["id"]
```

### Step 2 — Apply Target Cleaning on Train Only

```python
train = train[
    (train["trip_duration"] > 0) &
    (train["trip_duration"] <= 24 * 60 * 60)
].copy()
```

### Step 3 — Apply Coordinate Quality Rule

Create remove/review candidate flags first.  
Then decide whether to remove outside-wide-NYC records.

```python
# Remove candidates:
# globally invalid coordinate
# zero coordinate
# outside wide NYC
```

### Step 4 — Create Datetime Features from `pickup_datetime`

```python
pickup_hour
pickup_dayofweek
pickup_month
is_weekend
cyclic time features
```

### Step 5 — Create Passenger Features

```python
is_single_passenger
is_zero_passenger
is_high_passenger_count
passenger_count_group
```

### Step 6 — Create Spatial Features from Coordinates

```python
haversine_distance
manhattan_distance
bearing
center distances
airport flags
clusters
```

### Step 7 — Encode Categorical Features

```python
vendor_id one-hot / binary indicator
store_and_fwd_flag N=0, Y=1
```

### Step 8 — Drop Non-Model Columns

```python
drop_columns = [
    "id",
    "pickup_datetime",
    "dropoff_datetime",
    "trip_duration"
]
```

### Step 9 — Build Modeling Matrix

```python
X = engineered_features
y = trip_duration or log1p_trip_duration
```

---

## 6. Do Not Repeat in Future EDA

| Already Done | Do Not Repeat |
|---|---|
| Target distribution/outlier analysis | Do not redo hist/KDE/IQR for `trip_duration` |
| Coordinate univariate analysis | Do not redo coordinate IQR/percentile/boundary analysis |
| Passenger count univariate analysis | Do not redo count/dominance plots |
| Categorical overview | Do not redo missing/cardinality/category-consistency overview |
| `vendor_id` univariate analysis | Do not redo missing/cardinality/imbalance plots |
| `store_and_fwd_flag` univariate analysis | Do not redo rare-class/imbalance plots |

---

## 7. Still Needed Before Section 9

### 7.1 Datetime Univariate EDA

Add Section 7:

```text
7.1 Datetime Feature Overview and Quality Check
7.2 pickup_datetime Univariate Distribution
7.3 dropoff_datetime Leakage Decision
7.4 Datetime Feature Engineering Decision
```

### 7.2 Identifier Feature Check

Add Section 8:

```text
8.1 id Uniqueness and Duplicate Check
8.2 id Final Decision
```

### 7.3 Final Master Summary

Add Section 9:

```text
9. Final Univariate EDA Feature Engineering Decision Summary
```

---

## 8. Final One-Line Decision

Use univariate EDA to create a clean modeling dataset by:

```text
cleaning only clearly invalid target/coordinate records,
engineering time, spatial, passenger, and categorical features,
excluding leakage/id columns,
and preserving rare but valid input-feature categories.
```
