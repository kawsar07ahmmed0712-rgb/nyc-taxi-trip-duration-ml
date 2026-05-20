# 5. 🔢 Numeric Feature Analysis

## 5.1 📏 Continuous Numeric Feature Analysis — Univariate Only

In this section, we will analyze continuous numeric features individually.  
We will not compare them with the target variable or with other features in this step.

---

## 📌 Continuous Numeric Features in This Dataset

| Feature Name | Feature Type | Meaning |
|---|---|---|
| `pickup_longitude` | Continuous / Geospatial | Pickup location longitude |
| `pickup_latitude` | Continuous / Geospatial | Pickup location latitude |
| `dropoff_longitude` | Continuous / Geospatial | Dropoff location longitude |
| `dropoff_latitude` | Continuous / Geospatial | Dropoff location latitude |

---

## ✅ 5.1 Analysis Plan for Continuous Numeric Features

### 1. 📌 Basic Information

For each continuous numeric feature, we will check:

- Data type
- Total count
- Missing count
- Missing percentage
- Unique values
- Minimum value
- Maximum value
- Range
- Mean
- Median
- Mode
- Standard deviation
- Variance
- Skewness
- Kurtosis

---

### 2. 🧹 Data Quality Check

For each continuous feature, we will check:

- Missing values
- Zero values
- Negative values
- Duplicate values inside the column
- Infinite values
- Invalid values
- Extremely small values
- Extremely large values

---

### 3. 📍 Geospatial Boundary Check

Since these features are latitude and longitude, we need to check realistic location boundaries.

#### Longitude Features
- `pickup_longitude`
- `dropoff_longitude`

Check:
- Longitude should not be `0`
- Longitude should not be positive for NYC
- Very low or very high longitude values may indicate invalid GPS records
- Values far outside NYC area should be investigated

#### Latitude Features
- `pickup_latitude`
- `dropoff_latitude`

Check:
- Latitude should not be `0`
- Latitude should be positive for NYC
- Very low or very high latitude values may indicate invalid GPS records
- Values far outside NYC area should be investigated

---

### 4. 📊 Distribution Analysis

For each continuous feature, we will analyze:

- Histogram
- KDE plot
- Distribution shape
- Value concentration
- Long tail behavior
- Normal-like or non-normal pattern
- Single peak or multiple peaks

Useful plots:
- Histogram
- KDE plot
- Boxplot
- Violin plot
- Boxen plot

---

### 5. 📐 Skewness & Kurtosis Check

For each continuous feature, we will check:

- Skewness value
- Positive skewness
- Negative skewness
- Approximately symmetric shape
- Kurtosis value
- Heavy tail behavior
- Extreme value indication

---

### 6. 📍 Percentile / Quantile Analysis

For each continuous feature, we will check:

- 1st percentile
- 5th percentile
- 10th percentile
- 25th percentile
- 50th percentile
- 75th percentile
- 90th percentile
- 95th percentile
- 99th percentile
- 99.5th percentile
- 99.9th percentile

This helps us understand the normal value range and extreme tail values.

---

### 7. 📦 Outlier Analysis

For each continuous feature, we will detect outliers using:

- IQR method
- Q1
- Q3
- IQR
- Lower bound
- Upper bound
- Lower outlier count
- Upper outlier count
- Total outlier count
- Outlier percentage

Useful plots:
- Boxplot
- Boxen plot
- Jittered box plot
- Histogram with boundary lines

---

### 8. 🚫 Invalid Coordinate Check

For location features, we will especially check:

- Latitude = `0`
- Longitude = `0`
- Pickup and dropoff coordinates outside expected range
- Coordinates too far from NYC
- Same pickup/dropoff coordinate pattern
- Suspicious GPS values

---

### 9. 🔁 Duplicate / Repeated Value Check

For each continuous feature, we will check:

- Duplicate value count
- Duplicate percentage
- Most repeated values
- Unusual repeated coordinate values
- Possible default GPS values

---

### 10. 🔎 Decimal Precision Check

For latitude and longitude features, decimal precision is important.

We will check:

- Number of decimal places
- Too many rounded values
- Values ending with `.0`
- Low precision GPS values
- Repeated coordinate precision pattern

---

### 11. 📈 Transformation Check

For normal continuous numeric features, we can check transformations such as:

- Log transformation
- Square root transformation
- Cube root transformation
- Box-Cox transformation
- Yeo-Johnson transformation

However, for latitude and longitude features, transformation is usually not very useful.  
For these geospatial features, boundary cleaning and distance feature extraction are more important.

---

### 12. 🧠 Final Feature Decision

For each continuous feature, we will decide:

- Keep as original feature
- Clean invalid coordinate values
- Remove unrealistic GPS records
- Use for distance-based feature engineering
- Use for cluster-based location features
- Drop only if the feature is clearly invalid or harmful

---

## 📌 Feature-Wise Analysis Focus

| Feature | Main Focus |
|---|---|
| `pickup_longitude` | Boundary check, zero values, invalid NYC longitude, outliers |
| `pickup_latitude` | Boundary check, zero values, invalid NYC latitude, outliers |
| `dropoff_longitude` | Boundary check, zero values, invalid NYC longitude, outliers |
| `dropoff_latitude` | Boundary check, zero values, invalid NYC latitude, outliers |

---

## ✅ Best Order for 5.1 Continuous Numeric Analysis

1. Basic summary statistics  
2. Data quality check  
3. Distribution analysis  
4. Skewness and kurtosis check  
5. Percentile / quantile analysis  
6. Outlier analysis  
7. Geospatial boundary check  
8. Invalid coordinate check  
9. Duplicate / repeated value check  
10. Final continuous feature decision  

---

## 📝 Important Note

These continuous features are mainly geospatial coordinates.  
So, normal numeric analysis is useful, but the most important part is checking whether the pickup and dropoff coordinates are valid for NYC.  
Later, these coordinates can be used to create stronger features such as distance, direction, pickup area, dropoff area, and location clusters.