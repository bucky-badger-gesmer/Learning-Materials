# Chapter 5: Exploring Numerical Data

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/explore-numerical](https://openintro-ims.netlify.app/explore-numerical)

---

## Table of Contents

- [Overview](#overview)
- [Datasets Used](#datasets-used)
- [5.1 Scatterplots for Paired Data](#51-scatterplots-for-paired-data)
- [5.2 Dot Plots and the Mean](#52-dot-plots-and-the-mean)
  - [5.2.1 The Mean](#521-the-mean)
  - [5.2.2 Weighted Means](#522-weighted-means)
- [5.3 Histograms and Shape](#53-histograms-and-shape)
  - [5.3.1 Histograms and Data Density](#531-histograms-and-data-density)
  - [5.3.2 Density Plots](#532-density-plots)
  - [5.3.3 Skew and Symmetry](#533-skew-and-symmetry)
  - [5.3.4 Modes](#534-modes)
- [5.4 Variance and Standard Deviation](#54-variance-and-standard-deviation)
  - [5.4.1 Variance](#541-variance)
  - [5.4.2 Standard Deviation](#542-standard-deviation)
  - [5.4.3 The Empirical Rule](#543-the-empirical-rule)
- [5.5 Box Plots, Quartiles, and the Median](#55-box-plots-quartiles-and-the-median)
  - [5.5.1 The Median](#551-the-median)
  - [5.5.2 Quartiles and IQR](#552-quartiles-and-iqr)
  - [5.5.3 Outliers](#553-outliers)
- [5.6 Robust Statistics](#56-robust-statistics)
- [5.7 Transforming Data](#57-transforming-data)
- [5.8 Mapping Data](#58-mapping-data)
- [5.9 Chapter Review](#59-chapter-review)
  - [5.9.1 Summary](#591-summary)
  - [5.9.2 Key Terms](#592-key-terms)
- [5.10 Exercises Overview](#510-exercises-overview)
- [Key Takeaways: Choosing the Right Summary](#key-takeaways-choosing-the-right-summary)

---

## Overview

This chapter focuses on exploring **numerical** data using summary statistics and visualizations. Mastery of these methods is crucial for understanding the techniques introduced in the rest of the book. Numerical variables allow us to sensibly discuss numerical differences (e.g., loan amounts), unlike categorical variables such as area codes or zip codes.

---

## Datasets Used

| Dataset | Source | Description |
|---------|--------|-------------|
| **`loan50`** | `openintro` R package | 50 randomly sampled loans from Lending Club; variables include `loan_amount`, `interest_rate`, `total_income`, `term`, `grade`, `state`, `homeownership` |
| **`county`** | `usdata` R package | 3,142 US counties; variables include `pop2010`, `pop2017`, `pop_change`, `poverty`, `unemployment_rate`, `homeownership`, `median_hh_income`, `median_edu`, `metro`, `per_capita_income` |

---

## 5.1 Scatterplots for Paired Data

A **scatterplot** provides a case-by-case view of data for two numerical variables. Each point represents a single case.

**Key uses:**
- Examining relationships between two numerical variables
- Identifying linear or nonlinear trends
- Detecting outliers or unusual patterns

**Example — `loan50`:** A scatterplot of `loan_amount` vs. `total_income` reveals many borrowers with income below $100,000, with a handful above $250,000. The relationship is moderately positive.

**Example — `county`:** A scatterplot of `median_hh_income` vs. `poverty` rate shows a **nonlinear (curvilinear)** relationship, different from the mostly linear trends seen in earlier examples.

> **What scatterplots reveal:** They show the relationship between two numerical variables on a case-by-case basis, making it possible to identify trends (positive, negative, or none), curvature, and unusual observations.

---

## 5.2 Dot Plots and the Mean

A **dot plot** is a one-variable scatterplot — the most basic display for the distribution of a single numerical variable.

- Shows the exact value of each observation
- Useful for small datasets; becomes hard to read with larger samples
- The mean is often overlaid as a triangle, representing the balancing point of the distribution

### 5.2.1 The Mean

The **mean** (or **average**) is a common measure of the center of a distribution.

**Sample mean:**

$$\bar{x} = \frac{x_1 + x_2 + \cdots + x_n}{n}$$

| Symbol | Meaning |
|--------|---------|
| $\bar{x}$ | Sample mean |
| $\mu$ | Population mean (Greek letter *mu*) |
| $x_i$ | The $i$-th observation |
| $n$ | Number of observations |

The sample mean $\bar{x}$ serves as a **point estimate** of the population mean $\mu$. Larger samples tend to produce more accurate estimates.

**Why the mean is useful:** It allows us to rescale or standardize a metric into something more interpretable and comparable. For example, comparing average asthma attacks per patient across treatment groups of different sizes, or calculating an average hourly wage from total earnings and hours worked.

### 5.2.2 Weighted Means

When observations represent groups of different sizes, a **weighted mean** is more appropriate than a simple mean.

**Example:** Computing average income per person in the US using county-level per capita income data. A simple mean of county per capita incomes would treat a county of 5,000 residents equally with a county of 5,000,000 residents. Instead, we should compute total income for each county, sum across all counties, and divide by the total population.

- **Weighted mean result:** $30,861 per capita income for the US
- **Simple mean of counties:** $26,093 (misleading)

---

## 5.3 Histograms and Shape

### 5.3.1 Histograms and Data Density

A **histogram** bins observations into intervals and plots the count (or proportion) in each bin as bars. Unlike dot plots, histograms do not show individual observation values.

**Key features:**
- Higher bars = more common values (higher **data density**)
- Boundary observations are allocated to the lower bin
- Resembles a heavily binned version of a stacked dot plot

### 5.3.2 Density Plots

A **density plot** is a smoothed-out histogram. The shape, scale, and spread of observations are displayed similarly, but the technical details of smoothing are beyond the scope of this text.

### 5.3.3 Skew and Symmetry

| Shape | Description | Visual Cue |
|-------|------------|------------|
| **Right skewed** | Long tail to the right; most data clustered on the left | Bulk of data on left, trail off to right |
| **Left skewed** | Long tail to the left; most data clustered on the right | Bulk of data on right, trail off to left |
| **Symmetric** | Roughly equal trailing off in both directions | Mirror-image halves |

### 5.3.4 Modes

A **mode** is represented by a **prominent peak** in the distribution.

| Type | Number of Prominent Peaks |
|------|--------------------------|
| **Unimodal** | 1 |
| **Bimodal** | 2 |
| **Multimodal** | More than 2 |

> **Note:** The definition of "prominent" is not rigorously defined. Looking for modes is about better understanding your data, not finding a single correct answer. Less prominent peaks that differ from neighbors by only a few observations are not counted.

---

## 5.4 Variance and Standard Deviation

### 5.4.1 Variance

The **variance** measures the average squared distance of observations from the mean.

**Sample variance:**

$$s^2 = \frac{\sum_{i=1}^n (x_i - \bar{x})^2}{n - 1}$$

- We divide by $n - 1$ (not $n$) to make the statistic slightly more reliable and useful
- Squaring deviations makes large values relatively much larger and eliminates negative signs
- The variance is the average squared distance from the mean

### 5.4.2 Standard Deviation

The **standard deviation** is the square root of the variance:

$$s = \sqrt{s^2} = \sqrt{\frac{\sum_{i=1}^n (x_i - \bar{x})^2}{n - 1}}$$

| Symbol | Meaning |
|--------|---------|
| $s^2$ | Sample variance |
| $s$ | Sample standard deviation |
| $\sigma^2$ | Population variance |
| $\sigma$ | Population standard deviation (Greek letter *sigma*) |

**Interpretation:** The standard deviation represents the typical deviation of observations from the mean. It is useful for understanding how far data are distributed from the center.

### 5.4.3 The Empirical Rule

As a rough guideline:
- About **68%** of data fall within **1 standard deviation** of the mean
- About **95%** of data fall within **2 standard deviations** of the mean

> ⚠️ **Warning:** These percentages are not strict rules. They work well for unimodal, bell-shaped (symmetric) distributions but are poor approximations for skewed or multimodal distributions.

**Example:** For the interest rate variable (right skewed), 68% fell within 1 SD and 96% within 2 SD — close to the rule, but not guaranteed.

---

## 5.5 Box Plots, Quartiles, and the Median

A **box plot** summarizes a dataset using five statistics and identifies unusual observations.

### 5.5.1 The Median

The **median** splits the data in half — 50% of observations fall below and 50% above.

- **Odd number of observations:** The middle value is the median
- **Even number of observations:** The average of the two middle values is the median

### 5.5.2 Quartiles and IQR

| Statistic | Definition |
|-----------|-----------|
| **First quartile ($Q_1$)** | The 25th percentile — 25% of data fall below this value |
| **Third quartile ($Q_3$)** | The 75th percentile — 75% of data fall below this value |
| **Interquartile Range (IQR)** | $IQR = Q_3 - Q_1$ — the length of the box; measures variability of the middle 50% |
| **Percentile** | A value with $\alpha$% of observations below and $(100-\alpha)$% above |

The box in a box plot represents the middle 50% of the data, bounded by $Q_1$ and $Q_3$. The median is shown as a dark line inside the box.

### 5.5.3 Outliers

**Whiskers** extend from the box toward the minimum and maximum values, unless there are observations considered unusually high or low.

- **Outlier:** An observation that appears extreme relative to the rest of the data
- **Common rule:** Any observation beyond $1.5 \times IQR$ from $Q_1$ or $Q_3$ is flagged as an outlier
- Outliers are labeled as individual dots beyond the whiskers

**Why examine outliers:**
- Identify strong skew in the distribution
- Detect possible data collection or entry errors
- Reveal interesting properties of the data

> ⚠️ **Note:** Some datasets naturally have long skews — outlying points do not always represent a problem.

---

## 5.6 Robust Statistics

How are summary statistics affected by extreme observations?

| Statistic | Affected by Extremes? | Robust? |
|-----------|:---:|:---:|
| **Median** | ❌ No | ✅ Yes |
| **IQR** | ❌ No | ✅ Yes |
| **Mean** | ✅ Yes | ❌ No |
| **Standard Deviation** | ✅ Yes | ❌ No |

**Robust statistics** (median, IQR) are resistant to extreme observations because they only depend on values near the center of the distribution. The mean and standard deviation are heavily influenced by extreme values.

**Choosing between mean and median depends on context:**

| Scenario | Better Measure | Why |
|----------|---------------|-----|
| Overall company profit | Mean | Captures total profit margin across all customers |
| Typical customer profit | Median | Represents the experience of an individual customer |
| App launch time — overall waste | Mean | Total time wasted across all users |
| App launch time — typical experience | Median | What a typical user experiences |
| App launch time — worst cases | Upper percentile (e.g., 95th) | Median hides the fact that 10% of users wait >10 seconds |

> **Key principle:** For right-skewed distributions (like loan amounts), the **median** better represents the "typical" value, while the **mean** is pulled upward by extreme values.

Regardless of which centrality measure is chosen, always consider additional statistics (percentiles, IQR, SD) and visualize the data for a complete picture.

---

## 5.7 Transforming Data

When data are very strongly skewed, **transformations** can make them easier to model and visualize.

A **transformation** is a rescaling of the data using a function.

| Transformation | Formula | Common Use |
|---------------|---------|------------|
| **Logarithm (base 10)** | $\log_{10}(x)$ | Strongly right-skewed positive data |
| **Square root** | $\sqrt{x}$ | Moderately right-skewed positive data |
| **Inverse** | $1/x$ | Reducing extreme skew |

**Benefits of transforming data:**
- Reduce skew
- Make distributions more symmetric
- Reign in outliers
- Make patterns visible in scatterplots that were obscured by extreme skew
- Assist in building statistical models

**Example — `county` population:** The raw population distribution is extremely right-skewed, with nearly all data in the left-most bin. After a $\log_{10}$ transformation, the distribution becomes reasonably symmetric and bell-shaped.

**Example — scatterplots:** Applying a $\log_{10}$ transformation to a heavily skewed variable in a scatterplot can reveal associations (e.g., a positive linear relationship between $\log_{10}$ population and population change) that were invisible in the raw data.

---

## 5.8 Mapping Data

When data are geographic, standard plots (dot plots, scatterplots, box plots) can miss the true nature of the data. An **intensity map** (or heatmap) uses colors to show higher and lower values of a variable across geographic regions.

**Intensity maps of US counties reveal:**
- **Poverty rate:** Higher in the Deep South, Arizona, New Mexico, Mississippi flood plains, and Kentucky
- **Unemployment rate:** Follows similar trends to poverty; correspondence between the two is evident
- **Homeownership rate:** Lowest on the West Coast
- **Median household income:** Highest in the Bay Area and along the New England coast

> **Key insight:** The poverty rate is much higher than the unemployment rate in many areas, meaning many people are working but not earning enough to break out of poverty.

Intensity maps are not useful for getting precise values in any given county, but they are excellent for seeing geographic trends and generating research questions.

---

## 5.9 Chapter Review

### 5.9.1 Summary

Fluently working with numerical variables is an important skill for data analysts. This chapter introduced visualizations and numerical summaries for numeric variables:

- **Visualizations:** Scatterplots, dot plots, histograms, density plots, box plots, intensity maps
- **Numerical summaries:** Mean, median, quartiles, IQR, variance, standard deviation
- **Concepts:** Skew, modality, robust statistics, data transformations, geographic mapping

Graphical visualizations become even more descriptive when two variables are presented simultaneously.

### 5.9.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Average** | Another name for the mean |
| **Bimodal** | A distribution with two prominent peaks |
| **Box plot** | A plot summarizing data using median, quartiles, and whiskers; identifies outliers |
| **Data density** | How concentrated observations are in a region; shown by bar height in histograms |
| **Density plot** | A smoothed-out histogram showing distribution shape |
| **IQR** | Interquartile range; $Q_3 - Q_1$; measures spread of middle 50% |
| **Left skewed** | Long tail to the left |
| **Mean** | Sum of observations divided by count; $\bar{x}$ for sample, $\mu$ for population |
| **Median** | The middle value when data are ordered; splits data in half |
| **Multimodal** | A distribution with more than two prominent peaks |
| **Standard deviation** | Square root of variance; typical distance of observations from the mean |
| **Symmetric** | Roughly equal trailing off in both directions |
| **Tail** | The trailing-off portion of a distribution; long tails indicate skew |
| **Third quartile ($Q_3$)** | The 75th percentile |
| **Transformation** | Rescaling data using a function (e.g., log, square root, inverse) to reduce skew or reveal patterns |
| **Unimodal** | A distribution with one prominent peak |

---

## 5.10 Exercises Overview

The chapter includes **20 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **Migraine & acupuncture** | Dot plots; comparing distributions; choosing between mean and median |
| 2 | **Heart transplant mortality** | Dot plots; center and spread comparison; interpreting differences |
| 3 | **Air quality (PM10)** | Histograms; skew identification; effect of transformations |
| 4 | **Distribution shapes** | Matching histograms to summary statistics; skew and modality |
| 5 | **Stats final scores** | Histogram interpretation; estimating center and spread; skew |
| 6 | **Baby weights** | Box plots; estimating median, quartiles, IQR; identifying skew |
| 7 | **Ozone pollution** | Box plots; comparing distributions across groups; outliers |
| 8 | **Prison isolation experiment** | Box plots; comparing treatment groups; assessing intervention effectiveness |
| 9 | **Central Limit Theorem (simulation)** | Sampling distributions; how sample size affects variability of the mean |
| 10 | **Sampling distributions (simulation)** | Shape, center, and spread of sampling distributions at different sample sizes |
| 11 | **Chile earthquake** | Intensity maps; geographic patterns; generating research questions |
| 12 | **Global unemployment** | Intensity maps; global patterns; cross-country comparisons |
| 13 | **Robust statistics** | Identifying which statistics are affected by extreme observations |
| 14 | **Transforming data** | When and why to apply transformations; interpreting transformed axes |
| 15 | **Scatterplots & association** | Describing relationships between two numerical variables |
| 16 | **Means vs. medians in context** | Choosing the appropriate measure of center for real-world scenarios |
| 17 | **Histograms & skew** | Identifying skew direction and its implications for summary statistics |
| 18 | **Box plot construction** | Computing five-number summary; identifying outliers using IQR rule |
| 19 | **Comparing visualizations** | Strengths and limitations of dot plots, histograms, and box plots |
| 20 | **Weighted means** | When a simple mean is misleading; computing and interpreting weighted means |

> Answers to odd-numbered exercises can be found in Appendix A.5 of the textbook.

---

## Key Takeaways: Choosing the Right Summary

### Visualizations

| Goal | Recommended Visualization | Why |
|------|--------------------------|-----|
| **Relationship between two numerical variables** | Scatterplot | Shows case-by-case association, trend, and curvature |
| **Distribution of a single variable (small dataset)** | Dot plot | Shows exact values; intuitive |
| **Distribution shape (any dataset size)** | Histogram | Shows density, skew, and modality clearly |
| **Smoothed distribution shape** | Density plot | Cleaner version of histogram; good for overlaying groups |
| **Five-number summary + outliers** | Box plot | Compact; median, IQR, and extreme values at a glance |
| **Geographic patterns** | Intensity map | Reveals regional trends invisible in other plots |

### Numerical Summaries

| Goal | Recommended Statistic | Why |
|------|----------------------|-----|
| **Center of symmetric distribution** | Mean | Uses all data; efficient estimator |
| **Center of skewed distribution** | Median | Robust to extreme values |
| **Spread of symmetric distribution** | Standard deviation | Pairs naturally with the mean |
| **Spread of skewed distribution** | IQR | Robust to extreme values |
| **Overall impact (total effect)** | Mean | Captures contribution of all values including extremes |
| **Typical experience** | Median | Represents the middle observation |
| **Extreme cases** | Upper/lower percentiles (e.g., 95th) | Reveals tail behavior that median hides |

### Critical Warnings

1. ⚠️ **The empirical rule is not a rule** — ~68%/95% within 1/2 SDs only holds approximately for unimodal, symmetric distributions
2. ⚠️ **Mean vs. median is context-dependent** — neither is universally "better"; they answer different questions
3. ⚠️ **Skew pulls the mean toward the tail** — in right-skewed data, the mean > median; in left-skewed data, the mean < median
4. ⚠️ **Simple means can mislead with grouped data** — use weighted means when observations represent groups of different sizes
5. ⚠️ **Transformations change interpretation** — after a log transformation, values represent powers of the base (e.g., $\log_{10}(x) = 5$ means $x = 10^5 = 100{,}000$)
6. ⚠️ **Outliers are not always errors** — they may reflect natural variation, strong skew, or genuinely interesting phenomena
7. ⚠️ **Mode counting is subjective** — the goal is understanding your data, not finding a single correct number of modes
