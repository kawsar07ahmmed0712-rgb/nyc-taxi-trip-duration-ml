## 3.6 🎯 Final Target Variable Decision for Feature Engineering

After analyzing the target variable `trip_duration`, we need to decide how it will be handled during feature engineering and modeling.

---

### ✅ What We Understood from Target Analysis

- `trip_duration` has **no missing values**.
- There are **no zero or negative duration values**.
- The raw target is **highly right-skewed** because of extremely long trips.
- Some trips have **very small duration**, such as less than 1 minute.
- Some trips have **extremely large duration**, including unrealistic values above 24 hours.
- IQR method detected many upper outliers, but removing all IQR outliers may be too aggressive.
- After IQR-based outlier handling, the distribution became much cleaner.
- Log transformation helped the raw target distribution, but after strong outlier handling it over-corrected the distribution.
- Percentile analysis showed that most trips are short to medium duration trips.

---

### 🎯 Target Column Handling Decision

| Decision Area | Final Decision |
|---|---|
| Main target column | Keep `trip_duration` as the target variable |
| Use target as input feature? | ❌ No |
| Use `trip_duration_min` as feature? | ❌ No, only for EDA explanation |
| Use `log_trip_duration` as feature? | ❌ No, only as optional transformed target |
| Remove missing target rows? | Not needed |
| Remove zero / negative duration? | Not needed, none found |
| Remove duration above 24 hours? | ✅ Yes, clearly unrealistic |
| Remove all IQR outliers? | ❌ Not directly, may remove valid long trips |
| Use IQR outlier handling? | ✅ For EDA understanding, not blindly for final modeling |
| Test log target? | ✅ Optional modeling experiment |

---

### 🧠 Important Leakage Decision

`trip_duration` must not be used directly or indirectly as an input feature.

Also, `dropoff_datetime` should be handled carefully because:

- `dropoff_datetime` is calculated from pickup time plus trip duration.
- In real prediction, we usually do not know dropoff time before the trip ends.
- Using `dropoff_datetime` can create **data leakage**.

So, for feature engineering:

✅ Use `pickup_datetime`  
❌ Do not use `dropoff_datetime` for final model features  

---

### 🧹 Target Cleaning Decision

For final modeling dataset, we will remove only clearly invalid target records:

- `trip_duration <= 0`
- `trip_duration > 24 hours`

We will not remove all trips above the IQR upper bound because long trips can be valid in real taxi data.

Examples of valid long trips:

- Airport trips  
- Heavy traffic trips  
- Long-distance city trips  
- Peak-hour trips  

---

### 🔄 Target Transformation Decision

We will keep two possible target versions for modeling experiments:

| Target Version | Purpose |
|---|---|
| `trip_duration` | Main raw target |
| `log1p(trip_duration)` | Optional transformed target for comparison |

Final target choice will be based on model performance.

---

### ✅ Final Feature Engineering Plan for Target

- Separate `trip_duration` as the output variable.
- Do not include `trip_duration` in input features.
- Do not use target-derived columns as model features.
- Remove clearly unrealistic target records only.
- Use `pickup_datetime` to create time-based features.
- Avoid `dropoff_datetime` to prevent data leakage.
- Try both raw target and log-transformed target during modeling.
- Select the final target format based on validation performance.

---

### 📌 Final Decision

For feature engineering, `trip_duration` will be treated only as the prediction target.  
It will not be used as an input feature.  
We will clean only clearly unrealistic target values and keep log transformation as an optional modeling experiment.