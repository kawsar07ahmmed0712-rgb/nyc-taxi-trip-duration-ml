# Continuous Numeric Univariate FE Decision

**Notebook section checked:** `5.1 Continuous Numeric Feature Analysis`  
**Feature group:** Raw pickup/dropoff coordinates  
**Decision type:** Cleaning + feature-engineering preparation

---

## 1. Final Decision Snapshot

| Item | Decision |
|---|---|
| Use raw coordinates directly? | No, use mainly for FE |
| Remove IQR outliers directly? | No |
| Remove strict NYC boundary violations? | No |
| Remove wide NYC boundary violations? | Yes, flag as remove candidates |
| Clip latitude/longitude? | No |
| Keep diagnostic flags? | Yes |
| Next step | Move to `passenger_count` univariate analysis |

---

## 2. Cleaning Rule

### Remove candidate mask

```python
coordinate_remove_candidate_mask = (
    coordinate_df["any_globally_invalid_coordinate"] |
    coordinate_df["any_zero_coordinate"] |
    coordinate_df["either_outside_wide_nyc"]
)
```

### Keep candidate mask

```python
coordinate_keep_mask = ~coordinate_remove_candidate_mask
```

### Expected result

| Rule | Count | Percentage |
|---|---:|---:|
| Total records | 1,458,644 | 100.0000 |
| Records kept by coordinate rule | 1,458,589 | 99.9962 |
| Coordinate remove candidates | 55 | 0.0038 |

---

## 3. Decision Matrix

| Condition | Action | Reason |
|---|---|---|
| Globally invalid coordinate | Remove candidate | Invalid latitude/longitude range |
| Zero coordinate | Remove candidate | Not valid NYC trip coordinate |
| Outside wide NYC | Strong review / remove candidate | Clearly far from NYC operating area |
| Outside strict but inside wide NYC | Keep but review | Possible airport / nearby-area trip |
| IQR outlier inside wide NYC | Keep | Geographically valid, statistically unusual |
| Clean coordinate range | Keep | Normal NYC coordinate pattern |

---

## 4. Why IQR Is Not Used for Removal

| Metric | Value |
|---|---:|
| Any IQR coordinate outlier | 190,862 |
| IQR outlier percentage | 13.0849% |
| IQR outlier inside wide NYC | 190,807 |
| Outside wide NYC | 55 |
| Outside wide NYC but not IQR outlier | 0 |

**Decision:** IQR flags are diagnostic only. Removing all IQR outliers would remove many valid trips.

---

## 5. Severity-based Action

| Severity | Count | Percentage | Action |
|---|---:|---:|---|
| Clean - normal coordinate range | 1,267,782 | 86.9151 | Keep |
| Low - IQR outlier inside NYC | 189,970 | 13.0237 | Keep |
| Medium - outside strict + IQR outlier | 837 | 0.0574 | Keep but review |
| High - outside wide NYC | 55 | 0.0038 | Strong review / remove candidate |

---

## 6. Boundary Decision

| Boundary Type | Use Case | Cleaning Role |
|---|---|---|
| Strict NYC boundary | Dense NYC operating area | Diagnostic only |
| Wide NYC boundary | Conservative valid-area boundary | Removal-candidate rule |

**Decision:** Strict boundary is too restrictive. Wide boundary is safer.

---

## 7. Feature Engineering Plan

| FE Feature | Source Columns | Purpose | Priority |
|---|---|---|---|
| `haversine_distance` | pickup/dropoff lat-lon | Great-circle trip distance | High |
| `manhattan_distance` | pickup/dropoff lat-lon | Grid-like city distance proxy | High |
| `bearing` | pickup/dropoff lat-lon | Trip direction | Medium |
| `distance_to_center_pickup` | pickup lat-lon | Pickup centrality | Medium |
| `distance_to_center_dropoff` | dropoff lat-lon | Dropoff centrality | Medium |
| `airport_flag` | pickup/dropoff lat-lon | Airport-related trip signal | High |
| `pickup_cluster` | pickup lat-lon | Location segmentation | Medium |
| `dropoff_cluster` | dropoff lat-lon | Destination segmentation | Medium |
| `coordinate_outside_wide_flag` | boundary flags | Rare suspicious indicator | Low / diagnostic |

---

## 8. Do / Do Not

| Do | Do Not |
|---|---|
| Use wide-boundary rule for conservative cleaning | Drop all IQR outliers |
| Keep strict-boundary rows if inside wide NYC | Clip raw coordinates to IQR bounds |
| Convert coordinates into distance/location features | Treat raw coordinates as fully independent numeric features |
| Review 55 high-risk records | Remove airport/outer-area trips blindly |
| Keep coordinate diagnostic flags for audit | Use min/max alone for cleaning |

---

## 9. Final Coordinate Preprocessing Plan

```python
# 1. Build coordinate diagnostic flags
# 2. Remove only globally invalid, zero, or outside-wide-NYC coordinates
# 3. Keep IQR and strict-boundary flags as diagnostics
# 4. Engineer distance, bearing, airport, and cluster features
# 5. Avoid clipping raw latitude/longitude values
```

---

## 10. Final FE Decision

| Feature Group | Final Status |
|---|---|
| Raw coordinate columns | Usable for feature engineering |
| Direct raw coordinate use | Not recommended as main model signal |
| Cleaning strategy | Conservative wide-boundary based |
| Removal candidates | 55 rows |
| Ready for next EDA block | Yes |

