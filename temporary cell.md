## 🎯 Target Variable Univariate Analysis Steps

### 1. 📌 Basic Summary Statistics
- Total count
- Data type
- Minimum value
- Maximum value
- Mean
- Median
- Mode
- Standard deviation
- Variance
- Range
- 25th percentile
- 50th percentile
- 75th percentile
- Unique value count

---

### 2. 🧹 Data Quality Check
- Missing values
- Missing percentage
- Zero values
- Negative values
- Duplicate values inside target column
- Extremely small duration values
- Extremely large duration values
- Invalid trip duration check

---

### 3. 📊 Distribution Analysis
- Histogram
- KDE plot
- Frequency distribution
- Log-transformed distribution
- Distribution shape checking
- Right-skewed / left-skewed / normal-like pattern

---

### 4. 📐 Skewness & Kurtosis Analysis
- Skewness value
- Positive skewness
- Negative skewness
- Highly skewed or moderately skewed
- Kurtosis value
- Heavy tail detection
- Extreme value indication

---

### 5. 📦 Outlier Analysis
- Boxplot
- IQR method
- Q1
- Q3
- IQR
- Lower bound
- Upper bound
- Outlier count
- Outlier percentage
- Extreme lower values
- Extreme upper values

---

### 6. 📍 Percentile / Quantile Analysis
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

---

### 7. ⏱️ Unit Conversion
- Convert seconds to minutes
- Convert seconds to hours
- Analyze human-readable duration
- Example:
  - `trip_duration_min = trip_duration / 60`
  - `trip_duration_hour = trip_duration / 3600`

---

### 8. 📈 Normality Check
- Histogram shape
- KDE curve
- QQ plot
- Shapiro-Wilk test
- Anderson-Darling test
- Visual normality check
- Check if target follows normal distribution or not

---

### 9. 🔄 Transformation Analysis
- `log1p(trip_duration)`
- Square root transformation
- Cube root transformation
- Box-Cox transformation
- Yeo-Johnson transformation
- Compare original vs transformed distribution
- Compare skewness before and after transformation

---

### 10. ✂️ Capping / Winsorization Check
- Cap using 1st and 99th percentile
- Cap using 0.5th and 99.5th percentile
- Cap using IQR bounds
- Compare before and after capping
- Decide whether extreme values should be capped or removed

---

### 11. 🧮 Value Grouping / Binning
- Very short trips
- Short trips
- Medium trips
- Long trips
- Very long trips

Example groups:
- `0–5 min`
- `5–15 min`
- `15–30 min`
- `30–60 min`
- `60+ min`

---

### 12. 📊 Important Target Plots
- Histogram
- KDE plot
- Boxplot
- Violin plot
- QQ plot
- ECDF plot
- Log histogram
- Boxen plot

---

### 13. 📝 Final Observation
- Distribution shape
- Skewness condition
- Outlier condition
- Mean vs median difference
- Most common trip duration range
- Transformation need
- Data cleaning decision