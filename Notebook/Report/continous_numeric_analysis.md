# Continuous Numeric Univariate Analysis

**Notebook section checked:** `5.1 Continuous Numeric Feature Analysis`  
**Features:** `pickup_longitude`, `pickup_latitude`, `dropoff_longitude`, `dropoff_latitude`  
**Rows:** `1,458,644`

---

## 1. Section Coverage

| Section | Topic | Status |
|---|---|---|
| 5.1.1 | Basic summary statistics | Done |
| 5.1.2 | Data quality check | Done |
| 5.1.3 | Geospatial boundary check | Done |
| 5.1.4 | Distribution analysis | Done |
| 5.1.5 | Skewness & kurtosis | Done |
| 5.1.6 | Percentile / quantile analysis | Done |
| 5.1.7 | IQR-based outlier analysis | Done |
| 5.1.8 | Spatial coordinate review | Done |
| 5.1.9 | Coordinate cleaning candidate summary | Done |

---

## 2. Feature Overview

| Feature | Type | Role |
|---|---|---|
| `pickup_longitude` | Continuous geospatial | Pickup x-coordinate |
| `pickup_latitude` | Continuous geospatial | Pickup y-coordinate |
| `dropoff_longitude` | Continuous geospatial | Dropoff x-coordinate |
| `dropoff_latitude` | Continuous geospatial | Dropoff y-coordinate |

---

## 3. Basic Statistics

| Feature | Missing | Unique | Min | Max | Mean | Median | Std | Skewness | Kurtosis |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| `pickup_longitude` | 0 | 23,047 | -121.9333 | -61.3355 | -73.9735 | -73.9817 | 0.0709 | -418.1204 | 288156.5204 |
| `pickup_latitude` | 0 | 45,245 | 34.3597 | 51.8811 | 40.7509 | 40.7541 | 0.0329 | 5.4891 | 12950.2415 |
| `dropoff_longitude` | 0 | 33,821 | -121.9333 | -61.3355 | -73.9734 | -73.9798 | 0.0706 | -425.3317 | 292526.0218 |
| `dropoff_latitude` | 0 | 62,519 | 32.1811 | 43.9210 | 40.7518 | 40.7545 | 0.0359 | -20.6712 | 4259.5426 |

**Signal:** Central values are NYC-consistent; min/max contain extreme coordinates.

---

## 4. Data Quality

| Feature | Missing | Zero | Infinite | Basic Invalid Coordinate | Wrong Sign |
|---|---:|---:|---:|---:|---:|
| `pickup_longitude` | 0 | 0 | 0 | 0 | 0 |
| `pickup_latitude` | 0 | 0 | 0 | 0 | 0 |
| `dropoff_longitude` | 0 | 0 | 0 | 0 | 0 |
| `dropoff_latitude` | 0 | 0 | 0 | 0 | 0 |

**Signal:** No basic data-quality failure in raw coordinate columns.

---

## 5. NYC Boundary Check

### Feature-level boundary counts

| Feature | Outside Strict NYC | Outside Strict % | Outside Wide NYC | Outside Wide % |
|---|---:|---:|---:|---:|
| `pickup_longitude` | 160 | 0.0110 | 25 | 0.0017 |
| `pickup_latitude` | 143 | 0.0098 | 33 | 0.0023 |
| `dropoff_longitude` | 564 | 0.0387 | 36 | 0.0025 |
| `dropoff_latitude` | 424 | 0.0291 | 43 | 0.0029 |

### Row-level boundary counts

| Check | Count | Percentage |
|---|---:|---:|
| Pickup outside strict NYC | 247 | 0.0169 |
| Dropoff outside strict NYC | 879 | 0.0603 |
| Either pickup/dropoff outside strict NYC | 892 | 0.0612 |
| Pickup outside wide NYC | 36 | 0.0025 |
| Dropoff outside wide NYC | 51 | 0.0035 |
| Either pickup/dropoff outside wide NYC | 55 | 0.0038 |

**Signal:** Strict-boundary violations are rare; wide-boundary violations are extremely rare and higher-risk.

---

## 6. Percentile / Quantile Summary

| Feature | P0.1 | P1 | P25 | P50 | P75 | P99 | P99.9 |
|---|---:|---:|---:|---:|---:|---:|---:|
| `pickup_longitude` | -74.0172 | -74.0143 | -73.9919 | -73.9817 | -73.9673 | -73.7822 | -73.7767 |
| `pickup_latitude` | 40.6415 | 40.6448 | 40.7373 | 40.7541 | 40.7684 | 40.8066 | 40.8425 |
| `dropoff_longitude` | -74.1776 | -74.0153 | -73.9913 | -73.9798 | -73.9630 | -73.7905 | -73.7398 |
| `dropoff_latitude` | 40.6042 | 40.6453 | 40.7359 | 40.7545 | 40.7698 | 40.8368 | 40.8896 |

### Tail concentration

| Feature | IQR | Central 98% Range | Extreme 99.8% Range | Extreme-to-IQR Ratio | Dominant Tail |
|---|---:|---:|---:|---:|---|
| `dropoff_longitude` | 0.0283 | 0.2248 | 0.4379 | 15.4650 | Lower |
| `pickup_longitude` | 0.0245 | 0.2321 | 0.2405 | 9.8032 | Upper |
| `dropoff_latitude` | 0.0339 | 0.1915 | 0.2853 | 8.4104 | Upper |
| `pickup_latitude` | 0.0310 | 0.1618 | 0.2010 | 6.4803 | Upper |

**Signal:** Middle distribution is compact; extreme tails drive skewness/kurtosis.

---

## 7. IQR Outlier Summary

| Feature | Q1 | Q3 | IQR | Lower Bound | Upper Bound | Lower Outliers | Upper Outliers | Total | % |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| `pickup_longitude` | -73.9919 | -73.9673 | 0.0245 | -74.0287 | -73.9305 | 669 | 83,653 | 84,322 | 5.7808 |
| `pickup_latitude` | 40.7373 | 40.7684 | 0.0310 | 40.6908 | 40.8149 | 45,092 | 7,651 | 52,743 | 3.6159 |
| `dropoff_longitude` | -73.9913 | -73.9630 | 0.0283 | -74.0338 | -73.9205 | 4,504 | 73,465 | 77,969 | 5.3453 |
| `dropoff_latitude` | 40.7359 | 40.7698 | 0.0339 | 40.6850 | 40.8207 | 47,409 | 24,581 | 71,990 | 4.9354 |

### IQR vs boundary

| Condition | Count | Percentage |
|---|---:|---:|
| Any coordinate IQR outlier | 190,862 | 13.0849 |
| Any coordinate outside wide NYC | 55 | 0.0038 |
| Both IQR outlier and outside wide NYC | 55 | 0.0038 |
| IQR outlier but inside wide NYC | 190,807 | 13.0811 |
| Outside wide NYC but not IQR outlier | 0 | 0.0000 |

**Signal:** IQR is too aggressive for direct coordinate cleaning.

---

## 8. Spatial Review

| Spatial Status | Pickup Count | Pickup % | Dropoff Count | Dropoff % |
|---|---:|---:|---:|---:|
| Inside strict NYC | 1,458,397 | 99.9831 | 1,457,765 | 99.9397 |
| Outside strict but inside wide NYC | 211 | 0.0145 | 828 | 0.0568 |
| Outside wide NYC | 36 | 0.0025 | 51 | 0.0035 |

### Trip duration by spatial group

| Spatial Group | Count | Mean | Median | Min | Max | P90 | P95 | P99 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Inside wide NYC | 1,458,589 | 959.43 | 662.00 | 1 | 3,526,282 | 1,634.00 | 2,104.00 | 3,439.00 |
| Outside wide NYC | 55 | 2,503.85 | 782.00 | 19 | 23,440 | 5,937.00 | 7,983.70 | 20,158.42 |

**Signal:** Outside-wide-NYC records are rare and duration behavior is more extreme.

---

## 9. Cleaning Candidate Summary

| Cleaning Severity | Record Count | Percentage |
|---|---:|---:|
| Clean - normal coordinate range | 1,267,782 | 86.9151 |
| Low - IQR outlier inside NYC | 189,970 | 13.0237 |
| Medium - outside strict + IQR outlier | 837 | 0.0574 |
| High - outside wide NYC | 55 | 0.0038 |

| Recommended Action | Record Count | Percentage |
|---|---:|---:|
| Keep | 1,457,752 | 99.9388 |
| Keep but review | 837 | 0.0574 |
| Strong review / remove candidate | 55 | 0.0038 |

---

## 10. Final Analysis Status

| Item | Status |
|---|---|
| Continuous coordinate univariate analysis | Complete |
| Coordinate quality baseline | Clean |
| Statistical outlier review | Complete |
| Spatial outlier review | Complete |
| Coordinate cleaning rule | Defined |
| Ready for next feature group | Yes |

