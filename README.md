# air-quality-data-audit
This project conducts an empirical audit of ambient air quality monitoring records from the UCI Air Quality dataset (April–May 2004)
# 📊 Environmental Data Audit & Reproducibility Analysis (Python & SQL)

## 📌 Project Overview

This project conducts an empirical audit of ambient air quality monitoring records from the UCI Air Quality dataset (April–May 2004)[cite: 2]. By evaluating two conflicting preliminary reporting drafts, it identifies critical data pipeline flaws—such as unstandardized units, unmasked missing value sentinel codes, duplicate rows, non-sequential revisions, and multi-day sensor dropouts[cite: 2]. 

Using Python (`pandas`, `numpy`, `matplotlib`), this study resolves pipeline inconsistencies, calculates robust descriptive statistics, tests aggregation sensitivity, and establishes whether nitrogen dioxide ($\text{NO}_2$) concentrations were lower in Period B (May) than in Period A (April)[cite: 2].

---

## 🎯 Objectives

- **Audit Data Quality & Pipeline Integrity:** Detect and resolve data export artifacts, including duplicate rows, revision replacement rules, and `-200` missing sentinel codes[cite: 2].
- **Standardize Measurement Units:** Detect and correct mixed unit scales ($\mu\text{g}/\text{m}^3$ vs. $\text{mg}/\text{m}^3$) across sensor channels[cite: 2].
- **Evaluate Reporting Drafts:** Replicate and explain how flawed data preprocessing led preliminary drafts to contradictory conclusions[cite: 2].
- **Conduct Sensitivity & Weighting Analysis:** Compare pooled hourly metrics against equally weighted daily aggregations across a standardized 75% coverage eligibility threshold[cite: 2].
- **Synthesize Business & Policy Guidance:** Deliver an evidence-backed statement on whether observed concentrations support an air quality improvement claim[cite: 2].

---

## 📂 Data Sources

- **`measurements.csv`:** Exported raw hourly observations containing sensor readings (`no2`, `temperature_c`), unit tags, line revisions, and UCI provenance row indices[cite: 2].
- **`expected_hours.csv`:** Full chronological schedule defining expected hourly intervals ($n = 720$ hours for Period A; $n = 744$ hours for Period B; total $1,464$ hours)[cite: 2].
- **`manifest.json`:** Provenance metadata and SHA-256 cryptographic checksums for raw file verification[cite: 2].

---

## 🛠️ Tools & Libraries Used

- **Python 3.10+**[cite: 2]
- **Pandas:** Data manipulation, revision handling, group aggregations, quantile interpolation[cite: 2]
- **NumPy:** Vectorized conditional masking (`np.where`), floating-point checks, array arithmetic
- **Matplotlib:** Multi-panel time-series visualisations, probability density histograms, horizontal bar charts[cite: 2]

---

# 📊 Key Analysis & Insights

---

## 🔹 1. Data Cleaning, Revision Auditing & Unit Standardisation

### Approach
- **Deduplication:** Dropped exact duplicated export lines[cite: 2].
- **Revision Handling:** Identified observation timestamps and retained the complete record corresponding to the highest revision number[cite: 2].
- **Sentinel Masking:** Identified `-200` codes as missing values in both $\text{NO}_2$ and temperature fields prior to numeric transformations[cite: 2].
- **Unit Conversion:** Converted valid non-negative $\text{mg}/\text{m}^3$ readings to $\mu\text{g}/\text{m}^3$ ($1\text{ mg}/\text{m}^3 = 1000\ \mu\text{g}/\text{m}^3$) while preserving valid native $\mu\text{g}/\text{m}^3$ values[cite: 2].
- **Variable-Specific Missingness:** Preserved valid $\text{NO}_2$ records even when ambient temperature was unobserved[cite: 2].

### Key Audit Findings
- **Draft A Error ($-54.62\,\mu\text{g}/\text{m}^3$ pseudo-reduction):** Failed to standardize units, averaging fractional $\text{mg}/\text{m}^3$ numbers (e.g., $0.059$) directly with $\mu\text{g}/\text{m}^3$ values, producing an artificial collapse in Period B[cite: 2].
- **Draft B Error ($+2.00\,\mu\text{g}/\text{m}^3$ pseudo-increase):** Discarded all $\text{mg}/\text{m}^3$ rows and dropped valid $\text{NO}_2$ rows where temperature was missing, discarding over 35% of legitimate observations[cite: 2].

📸 *Result Screenshot: Cleaned Time-Series & Mask Coverage (Figure F1)*  
`/screenshots/figure_f1_time_coverage.png`

---

## 🔹 2. Distributional Profiling (Parametric vs. Non-Parametric)

### Approach
- Calculated sample standard deviation with Bessel's correction ($ddof=1$)[cite: 2].
- Computed quartiles and interquartile range ($IQR = Q_3 - Q_1$) via linear interpolation[cite: 2].
- Evaluated central tendency using arithmetic means and medians across all valid retained hours[cite: 2].

### Key Insight
- Across both months, $\text{NO}_2$ distributions display pronounced positive right-skewness driven by episodic traffic spikes[cite: 2].
- Because sample means are inflated by episodic peak outliers, the **median** serves as the primary robust measure of typical exposure[cite: 2].
- The true median shift between Period A and Period B is minimal, disproving claims of meaningful pollution changes[cite: 2].

📸 *Result Screenshot: Density Distribution & Median Placement (Figure F2)*  
`/screenshots/figure_f2_distributions.png`

---

## 🔹 3. Controlled Sensitivity & Aggregation Analysis

### Approach
- Established calendar day eligibility based on having $\ge 18$ valid hourly readings ($75\%$ threshold)[cite: 2].
- Compared four controlled aggregation methods across periods:
  1. Pooled hourly mean across all valid records[cite: 2].
  2. Pooled hourly median across all valid records (primary contract metric)[cite: 2].
  3. Pooled hourly mean across eligible calendar days[cite: 2].
  4. Equally weighted mean of daily means across eligible calendar days[cite: 2].

### Sensitivity Summary Table

| Method | Period A Estimate | Period B Estimate | Difference ($B - A$) | Sample Size ($n_A, n_B$) |
|---|---|---|---|---|
| **1. Pooled Hourly Mean (All Valid)**[cite: 2] | $\sim 91.2\,\mu\text{g}/\text{m}^3$ | $\sim 97.9\,\mu\text{g}/\text{m}^3$ | $+6.6\,\mu\text{g}/\text{m}^3$ | $n_A=480, n_B=593$ hrs[cite: 2] |
| **2. Hourly Median (Primary Metric)**[cite: 2] | $\sim 91.6\,\mu\text{g}/\text{m}^3$ | $\sim 98.0\,\mu\text{g}/\text{m}^3$ | $+6.4\,\mu\text{g}/\text{m}^3$ | $n_A=480, n_B=593$ hrs[cite: 2] |
| **3. Pooled Mean (Eligible Days)**[cite: 2] | $\sim 92.4\,\mu\text{g}/\text{m}^3$ | $\sim 98.2\,\mu\text{g}/\text{m}^3$ | $+5.8\,\mu\text{g}/\text{m}^3$ | $n_A=421, n_B=510$ hrs |
| **4. Mean of Daily Means (Eligible Days)**[cite: 2] | $\sim 92.6\,\mu\text{g}/\text{m}^3$ | $\sim 98.3\,\mu\text{g}/\text{m}^3$ | $+5.7\,\mu\text{g}/\text{m}^3$ | $n_A=19, n_B=23$ days |

### Key Insight
- When data pipeline errors are corrected, concentration differences ($B - A$) remain stable within a narrow range across both hourly and daily weighting schemes[cite: 2].
- Significant multi-day data dropouts in both months constrain sample representativeness, meaning unobserved periods could alter baseline comparisons[cite: 2].

📸 *Result Screenshot: Method Sensitivity Comparison (Figure F3)*  
`/screenshots/figure_f3_sensitivity.png`

---

# 📈 Overall Business & Policy Value

- **Prevents Misleading Reporting:** Avoids publishing false air-quality conclusions stemming from unstandardized units or unhandled missing-data codes[cite: 2].
- **Audit-Proof Analytics:** Demonstrates how data quality auditing uncovers false positives in observational sensor data[cite: 2].
- **Reproducibility:** Enforces automated verification checks (V1 representation, V2 revision resolution, V3 weighting invariance) to guarantee deterministic outputs[cite: 2].

---

# 🧠 Key Takeaways

- Data pipelines must standardize measurement units and handle sentinel missing-value codes before performing statistical aggregations[cite: 2].
- Dropping records based on unrelated missing fields introduces severe selection bias[cite: 2].
- Right-skewed environmental data requires non-parametric evaluation (medians, IQR) alongside standard arithmetic metrics[cite: 2].
- Single-station observational records with multi-day gaps cannot support broad causal or geographic claims[cite: 2].

---

# 🚀 Future Improvements

- Automate ETL pipeline validation using `Great Expectations` or `Pydantic`.
- Incorporate wind speed and atmospheric pressure data to adjust for meteorological confounding.
- Implement automated unit-test suites within GitHub Actions CI/CD.

---

# 👤 Author

**An Truong**[cite: 2]  
Supply Chain & Data Analytics Enthusiast[cite: 3]  
Helsinki, Finland[cite: 3]
