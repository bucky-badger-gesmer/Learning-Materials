# Chapter 4: Exploring Categorical Data

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/explore-categorical](https://openintro-ims.netlify.app/explore-categorical)

---

## Table of Contents

- [Overview](#overview)
- [Datasets Used](#datasets-used)
- [4.1 Contingency Tables and Bar Plots](#41-contingency-tables-and-bar-plots)
  - [4.1.1 Contingency Tables](#411-contingency-tables)
  - [4.1.2 Bar Plots for a Single Variable](#412-bar-plots-for-a-single-variable)
  - [4.1.3 R Code Patterns](#413-r-code-patterns)
- [4.2 Visualizing Two Categorical Variables](#42-visualizing-two-categorical-variables)
  - [4.2.1 Stacked Bar Plots](#421-stacked-bar-plots)
  - [4.2.2 Standardized (Filled) Bar Plots](#422-standardized-filled-bar-plots)
  - [4.2.3 Dodged Bar Plots](#423-dodged-bar-plots)
  - [4.2.4 Mosaic Plots](#424-mosaic-plots)
  - [4.2.5 Comparing Bar Plot Types](#425-comparing-bar-plot-types)
- [4.3 Row and Column Proportions](#43-row-and-column-proportions)
  - [4.3.1 Row Proportions](#431-row-proportions)
  - [4.3.2 Column Proportions](#432-column-proportions)
  - [4.3.3 Conditional Proportions](#433-conditional-proportions)
  - [4.3.4 Spam Email Example](#434-spam-email-example)
- [4.4 Pie Charts](#44-pie-charts)
- [4.5 Waffle Charts](#45-waffle-charts)
- [4.6 Comparing Numerical Data Across Groups](#46-comparing-numerical-data-across-groups)
  - [4.6.1 Overlaid Histograms](#461-overlaid-histograms)
  - [4.6.2 Side-by-Side Box Plots](#462-side-by-side-box-plots)
  - [4.6.3 Ridge Plots](#463-ridge-plots)
  - [4.6.4 Faceted Histograms](#464-faceted-histograms)
  - [4.6.5 Multi-Variable Ridge Plots](#465-multi-variable-ridge-plots)
  - [4.6.6 Causal Inference Warning](#466-causal-inference-warning)
- [4.7 Chapter Review](#47-chapter-review)
  - [4.7.1 Summary](#471-summary)
  - [4.7.2 Key Terms](#472-key-terms)
- [4.8 Exercises Overview](#48-exercises-overview)
- [Key Takeaways: When to Use Each Visualization](#key-takeaways-when-to-use-each-visualization)

---

## Overview

This chapter focuses on exploring **categorical** data using summary statistics and visualizations. The summaries and graphs are created using statistical software (R/ggplot2). Where possible, **multivariate plots** are presented — plots that visualize the relationship between multiple variables. Mastery of this content is crucial for understanding the methods and techniques introduced in the rest of the book.

---

## Datasets Used

| Dataset | Source | Description |
|---------|--------|-------------|
| **`loans`** | `openintro` R package (`loans_full_schema`, modified) | 10,000 Lending Club loans; variables: `homeownership` (rent/mortgage/own), `application_type` (joint/individual), `grade` (A–G), `loan_status` |
| **`email`** | `openintro` R package | 3,921 emails; variables: `spam` (spam/not spam), `format` (HTML/text) |
| **`county`** | `openintro` R package | 3,142 US counties; variables: `median_hh_income`, `pop_change`, `metro`, `median_edu` |

> **Note:** The `loans` dataset is a modified version of `loans_full_schema` where `homeownership` and `application_type` have been recoded. Loans from Lending Club are typically for small items or cash, **not** for homes — homeownership status is unrelated to the loan purpose.

---

## 4.1 Contingency Tables and Bar Plots

### 4.1.1 Contingency Tables

A **contingency table** summarizes data for two categorical variables. Each cell represents the number of times a particular combination of variable outcomes occurred. Row and column totals are included.

#### Table 4.1: Application Type × Homeownership (Counts)

| application_type | rent | mortgage | own | **Total** |
|------------------|------|----------|-----|-----------|
| **joint** | 362 | 950 | 183 | **1,495** |
| **individual** | 3,496 | 3,839 | 1,170 | **8,505** |
| **Total** | **3,858** | **4,789** | **1,353** | **10,000** |

#### Table 4.2: Homeownership Frequencies (Single Variable)

| homeownership | Count |
|---------------|-------|
| rent | 3,858 |
| mortgage | 4,789 |
| own | 1,353 |
| **Total** | **10,000** |

**Key observations:**
- The largest group of borrowers has a mortgage (47.9%)
- The next largest group rents (38.6%)
- The smallest group owns their home outright (13.5%)
- Individual applications vastly outnumber joint applications (85.1% vs 15.0%)

### 4.1.2 Bar Plots for a Single Variable

A **bar plot** is the standard way to display the distribution of a single categorical variable.

- **Count bar plot:** Height of each bar = raw count of observations in that category
- **Proportion bar plot:** Height of each bar = proportion (fraction) of total observations in that category

Both convey the same information; the proportion version makes it easier to compare relative sizes.

### 4.1.3 R Code Patterns

```r
# Load and prepare the loans dataset
library(openintro)
library(tidyverse)
library(janitor)

loans <- loans_full_schema |>
  mutate(application_type = as.character(application_type)) |>
  filter(application_type != "") |>
  mutate(
    homeownership = tolower(homeownership),
    homeownership = fct_relevel(homeownership, "rent", "mortgage", "own"),
    application_type = fct_relevel(application_type, "joint", "individual")
  )

# Contingency table
loans |>
  count(application_type, homeownership) |>
  pivot_wider(names_from = homeownership, values_from = n) |>
  select(application_type, rent, mortgage, own) |>
  adorn_totals(where = c("row", "col"))

# Bar plot — counts
ggplot(loans, aes(x = homeownership)) +
  geom_bar(fill = "steelblue") +
  labs(x = "Homeownership", y = "Count")

# Bar plot — proportions
loans |>
  count(homeownership) |>
  mutate(proportion = n / sum(n)) |>
  ggplot(aes(x = homeownership, y = proportion)) +
  geom_col(fill = "steelblue") +
  labs(x = "Homeownership", y = "Proportion")
```

---

## 4.2 Visualizing Two Categorical Variables

### 4.2.1 Stacked Bar Plots

**What it shows:** Bars for the primary variable (x-axis), with each bar subdivided (stacked) by the levels of a second variable (fill color). The total height of each bar represents the raw count for that primary category.

**When to use it:** When one variable can reasonably be treated as the **explanatory variable** and the other as the **response variable**. You are grouping by one variable first, then breaking it down by the other.

**Strengths:**
- Most clearly displays the total counts in each primary category
- Shows the composition of each group at a glance

**Limitations:**
- Difficult to compare proportions across groups because the bars have different total heights
- Hard to assess whether the two variables are associated

**R code:**
```r
ggplot(loans, aes(x = homeownership, fill = application_type)) +
  geom_bar() +
  labs(x = "Homeownership", y = "Count")
```

### 4.2.2 Standardized (Filled) Bar Plots

**What it shows:** Same as a stacked bar plot, but every bar is scaled to the same height (1.0 or 100%). Each segment within a bar shows the **proportion** of that sub-category within the primary category.

**When to use it:** When the primary variable is **relatively imbalanced** (some categories have far fewer observations than others), making a regular stacked bar plot less useful for checking association.

**Strengths:**
- Easy to compare proportions across groups — all bars are the same height
- Clear way to detect association: if the segment proportions differ across bars, the variables are associated
- Helpful when one category has only a fraction of the observations of another

**Limitations:**
- **Loses all sense of sample size** — you cannot tell how many cases each bar represents
- A bar representing 100 observations looks identical in height to one representing 5,000

**R code:**
```r
ggplot(loans, aes(x = homeownership, fill = application_type)) +
  geom_bar(position = "fill") +
  labs(x = "Homeownership", y = "Proportion")
```

### 4.2.3 Dodged Bar Plots

**What it shows:** Bars for each combination of the two variables are placed **side by side** (dodged) rather than stacked. Each group on the x-axis has multiple adjacent bars, one per level of the fill variable.

**When to use it:** When you want a display that is **agnostic** about which variable is explanatory vs. response, and when you need to see the actual counts in each of the six group combinations.

**Strengths:**
- Easy to read the exact count in each of the six combinations
- No assumption about explanatory/response relationship
- Direct comparison of sub-category sizes within each primary group

**Limitations:**
- Requires more horizontal space; can feel cramped with many categories
- When groups are very different in size (e.g., `own` vs `mortgage`), it is difficult to discern association
- Can become cluttered with more than 2–3 levels in the fill variable

**R code:**
```r
ggplot(loans, aes(x = homeownership, fill = application_type)) +
  geom_bar(position = "dodge") +
  labs(x = "Homeownership", y = "Count", fill = "Application type")
```

### 4.2.4 Mosaic Plots

**What it shows:** A visualization for contingency tables where **box areas** represent the number of cases in each category. The plot starts as a square, split into columns for each level of the first variable (column widths proportional to category sizes), then each column is subdivided vertically for the second variable.

**When to use it:** When you want the benefits of a standardized bar plot (easy comparison of proportions) **while still seeing the relative group sizes** of the primary variable.

**Strengths:**
- Shows both proportions (via vertical splits) **and** relative group sizes (via column widths)
- Association is visible: columns divided at different vertical locations indicate association
- The first split is typically the explanatory variable; the second split is the response

**Limitations:**
- Can be harder to read precisely than bar plots
- The order of splits matters — splitting by different variables first produces different-looking plots
- Less familiar to general audiences than bar plots

**R code:**
```r
# Split first by homeownership, then by application_type
library(ggmosaic)

ggplot(loans) +
  geom_mosaic(aes(x = product(homeownership), fill = application_type)) +
  labs(x = "Homeownership", y = "Application type")

# Alternative: split first by application_type, then by homeownership
ggplot(loans) +
  geom_mosaic(aes(x = product(application_type), fill = homeownership)) +
  labs(x = "Application type", y = "Homeownership")
```

### 4.2.5 Comparing Bar Plot Types

| Plot Type | Best For | Shows Counts? | Shows Proportions? | Space Efficient? |
|-----------|----------|:---:|:---:|:---:|
| **Stacked** | Explanatory → response relationship | ✅ | ❌ (hard to read) | ✅ |
| **Standardized/Filled** | Comparing proportions across imbalanced groups | ❌ | ✅ | ✅ |
| **Dodged** | Seeing exact counts in all combinations; agnostic display | ✅ | ❌ (requires mental math) | ❌ |
| **Mosaic** | Proportions + relative group sizes in one plot | ✅ (via area) | ✅ | ✅ |

---

## 4.3 Row and Column Proportions

### 4.3.1 Row Proportions

**Row proportions** are computed by dividing each cell count by its **row total**.

#### Table 4.3: Row Proportions for Application Type × Homeownership

| application_type | rent | mortgage | own | Total |
|------------------|------|----------|-----|-------|
| joint | 0.242 | 0.635 | 0.122 | 1 |
| individual | 0.411 | 0.451 | 0.138 | 1 |

**How to read:** Look **across** a row. Each value tells you the proportion of that application type falling into each homeownership category.

**Example interpretations:**
- **0.411** = 41.1% of **individual** applicants **rent** their home
- **0.451** = 45.1% of **individual** applicants have a **mortgage**
- **0.122** = 12.2% of **joint** applicants **own** their home outright

**How to check for association:** Look **down columns** — if the proportions differ substantially between rows, the variables may be associated.

### 4.3.2 Column Proportions

**Column proportions** are computed by dividing each cell count by its **column total**.

#### Table 4.4: Column Proportions for Application Type × Homeownership

| application_type | rent | mortgage | own |
|------------------|------|----------|-----|
| joint | 0.094 | 0.198 | 0.135 |
| individual | 0.906 | 0.802 | 0.865 |
| **Total** | **1.000** | **1.000** | **1.000** |

**How to read:** Look **down** a column. Each value tells you the proportion of that homeownership category falling into each application type.

**Example interpretations:**
- **0.906** = 90.6% of **renters** applied as **individuals**
- **0.802** = 80.2% of borrowers with a **mortgage** applied as **individuals**
- **0.135** = 13.5% of **homeowners** had a **joint** application

**How to check for association:** Compare the column proportions **across columns**. Since 90.6% ≠ 80.2% ≠ 86.5%, the rate of individual applications varies by homeownership status — evidence that the two variables are **associated**.

### 4.3.3 Conditional Proportions

Row and column proportions are also called **conditional proportions** because they tell us the proportion of observations in a given level of one variable, **conditional on** the level of another variable.

> **Key principle:** Row proportions and column proportions are **not equivalent**. Before deciding which to use, consider both to ensure you construct the most useful table.

### 4.3.4 Spam Email Example

A practical application of choosing between row and column proportions:

#### Table 4.5: Spam Status × Email Format

| spam | HTML | text | **Total** |
|------|------|------|-----------|
| not spam | 2,568 | 986 | **3,554** |
| spam | 158 | 209 | **367** |
| **Total** | **2,726** | **1,195** | **3,921** |

**Question:** Which proportions help a data scientist classify emails as spam?

**Answer: Column proportions** — because the data scientist wants to know how the proportion of spam changes **within each email format**:

- **Plain text emails:** 209 / 1,195 = **17.5% are spam**
- **HTML emails:** 158 / 2,726 = **5.8% are spam**

Plain text emails are roughly **3× more likely** to be spam than HTML emails. However, this single characteristic is insufficient for classification on its own — over 80% of plain text emails are still not spam. When combined with many other characteristics, accurate spam classification becomes feasible.

**General rule:** It is usually most useful to "condition" on the **explanatory variable**. In this case, email format is the potential explanatory variable for whether a message is spam.

> **Note:** When there is no clear explanatory-response relationship (as with the loan variables), neither row nor column proportions is obviously more useful.

---

## 4.4 Pie Charts

**What it shows:** A circular chart divided into slices, where each slice's angle represents the proportion of a category.

**When to use it:** Only for categorical variables with **very few levels**, especially when each level represents a **simple fraction** (one-half, one-quarter, etc.).

**Strengths:**
- Good for giving a high-level overview of how cases break down
- Intuitive for simple, familiar proportions

**Limitations and warnings:**
- ⚠️ **Difficult to compare values** — it is not immediately obvious that "mortgage" > "rent" in the homeownership pie chart, whereas this is very obvious in a bar plot
- ⚠️ **Hard to read with many categories** — the loan grade pie chart (A through G) is far harder to interpret than the equivalent bar plot
- ⚠️ Human eyes are better at comparing **lengths** (bar plots) than **angles/areas** (pie charts)

**Recommendation:** **Bar plots are generally preferred** over pie charts by data visualization experts.

**R code (pie chart):**
```r
loans |>
  mutate(homeownership = fct_infreq(homeownership)) |>
  count(homeownership) |>
  mutate(text_y = cumsum(n) - n / 2) |>
  ggplot(aes(x = "", fill = homeownership, y = n)) +
  geom_col(position = position_stack(reverse = TRUE)) +
  geom_text_repel(aes(x = 1, label = homeownership, y = text_y)) +
  coord_polar("y", start = 0) +
  theme_void()
```

**R code (equivalent bar plot):**
```r
loans |>
  mutate(homeownership = fct_infreq(homeownership)) |>
  ggplot(aes(x = homeownership, fill = homeownership)) +
  geom_bar() +
  labs(x = "Homeownership", y = "Count")
```

---

## 4.5 Waffle Charts

**What it shows:** A 10 × 10 grid (100 squares) where each square represents **1%** of the data. Squares are colored proportionally to the variable distribution.

**When to use it:** For categorical variables with a **low number of levels**. Like pie charts, they work best with few categories.

**Strengths:**
- Easier than pie charts for comparing **non-simple fractions** (e.g., 38.6% vs 47.9%)
- Intuitive: each square = 1%, so you can literally count
- Visually engaging for presentations and reports

**Limitations:**
- Works best with few levels (becomes cluttered with many categories)
- Less precise than bar plots for exact values
- Not a standard ggplot2 geom — requires the `waffle` package

**R code:**
```r
library(waffle)

# Waffle chart — homeownership
loans |>
  count(homeownership) |>
  ggplot(aes(fill = homeownership, values = n)) +
  geom_waffle(
    color = "white",
    flip = TRUE,
    make_proportional = TRUE,
    na.rm = TRUE
  ) +
  labs(fill = NULL, title = "Homeownership") +
  coord_equal() +
  theme_enhance_waffle()

# Waffle chart — loan status
loans |>
  count(loan_status) |>
  ggplot(aes(fill = loan_status, values = n)) +
  geom_waffle(
    color = "white",
    flip = TRUE,
    make_proportional = TRUE,
    na.rm = TRUE
  ) +
  labs(fill = NULL, title = "Loan status") +
  coord_equal() +
  theme_enhance_waffle()
```

---

## 4.6 Comparing Numerical Data Across Groups

Some of the most interesting investigations involve examining **numerical data across groups** defined by categorical variables. This section uses the `county` dataset (3,142 US counties) to compare median household income between counties that **gained** population (2010–2017) vs. those with **no gain**.

**Sample data:**

| State | County | Pop. change (%) | Gain/No gain | Median HH income |
|-------|--------|-----------------|--------------|-----------------|
| Arkansas | Izard County | 2.13 | gain | $39,135 |
| Georgia | Jackson County | 10.17 | gain | $57,999 |
| Oregon | Hood River County | 3.41 | gain | $57,269 |
| Texas | Montague County | 0.75 | gain | $46,592 |
| Kentucky | Ballard County | −2.62 | no gain | $42,988 |
| Kentucky | Letcher County | −5.13 | no gain | $30,293 |
| Texas | Jim Hogg County | −1.12 | no gain | $31,403 |
| Virginia | Richmond County | −0.19 | no gain | $47,341 |

Of the 3,139 counties with complete data: **1,541 gained** population, **1,598 had no gain**.

### 4.6.1 Overlaid Histograms

**What it shows:** Two histograms drawn on the same axes, with different colors and partial transparency (`alpha`), allowing visual comparison of the distributions.

**Strengths:**
- Shows distribution shape, skew, and modes for both groups
- Preserves sense of relative sample sizes (taller bars = more observations)
- Good for seeing the full distribution, not just summary statistics

**Limitations:**
- Can be hard to read when distributions overlap heavily
- Transparency can obscure details in overlapping regions

**R code:**
```r
county |>
  filter(!is.na(pop_change)) |>
  ggplot(aes(x = median_hh_income, fill = pop_change_2levels)) +
  geom_histogram(binwidth = 5000, alpha = 0.5) +
  labs(x = "Median household income", y = NULL)
```

**Key finding:** Counties with population gains tend to have higher income (median ~$45,000) vs. counties without a gain (median ~$40,000). Both distributions show slight right skew and are unimodal.

### 4.6.2 Side-by-Side Box Plots

**What it shows:** Two box plots placed next to each other on the same scale, one for each group.

**Strengths:**
- Excellent for comparing **centers** (medians) and **spreads** (IQRs)
- Clearly shows outliers
- Compact and easy to read

**Limitations:**
- Hides distribution shape (skew, modality)
- Does not show relative sample sizes
- Many points fall beyond whiskers with large datasets (> a few hundred), making outliers less informative

**R code:**
```r
county |>
  filter(!is.na(pop_change)) |>
  ggplot(aes(x = median_hh_income, y = pop_change_2levels, color = pop_change_2levels)) +
  geom_boxplot() +
  labs(x = "Median household income", y = NULL)
```

### 4.6.3 Ridge Plots

**What it shows:** Density plots for each group drawn on the same scale, stacked vertically with slight overlap (like mountain ridges).

**Strengths:**
- Excellent for seeing **distribution shape** and especially **modality**
- Clean visual comparison of centers and spreads
- More elegant than overlaid histograms for many groups

**Limitations:**
- Does not show individual data points or outliers
- Less intuitive for audiences unfamiliar with the visualization

**R code:**
```r
library(ggridges)

county |>
  filter(!is.na(pop_change)) |>
  ggplot(aes(x = median_hh_income, y = pop_change_2levels,
             fill = pop_change_2levels, color = pop_change_2levels)) +
  geom_density_ridges(alpha = 0.5) +
  labs(x = "Median household income", y = NULL)
```

### 4.6.4 Faceted Histograms

**What it shows:** Separate histogram panels (facets) for each group, all drawn on the **same scale** for easy comparison.

**Strengths:**
- Each group's distribution is clearly visible without overlap
- Extends to **two categorical variables** (2×2 grid, etc.), allowing display of relationships between **three variables**
- Same x and y axes across facets enable direct comparison

**Limitations:**
- Requires more space (multiple panels)
- Harder to compare exact values across panels than with overlaid plots

**R code — single variable faceting:**
```r
county |>
  filter(!is.na(pop_change)) |>
  ggplot(aes(x = median_hh_income, fill = pop_change)) +
  geom_histogram(binwidth = 7500) +
  facet_grid(pop_change ~ ., labeller = label_both) +
  labs(x = "Median household income", y = NULL)
```

**R code — two variable faceting (2×2 grid):**
```r
county |>
  filter(!is.na(pop_change) & !is.na(metro)) |>
  ggplot(aes(x = median_hh_income, fill = pop_change)) +
  geom_histogram(binwidth = 5000) +
  facet_grid(pop_change ~ metro, labeller = label_both) +
  labs(x = "Median household income", y = NULL)
```

**Key finding from 2×2 faceting:** Counties in metropolitan areas have household income distributions that are substantially higher than those not in metropolitan areas, regardless of population change.

### 4.6.5 Multi-Variable Ridge Plots

**What it shows:** Ridge plots faceted by two categorical variables, with a **third variable encoded by color/linetype**. In the chapter's example:
- Facets: population gain/no gain × metropolitan/non-metropolitan (2×2 grid)
- Color/linetype: median education level (HS diploma = solid, some college = dashed, Bachelor's = dotted)

**Key finding:** Regardless of location (metro or not) or population change, median household income increases with education level: counties where the median education is a Bachelor's degree have substantially higher income distributions than counties with some college or only a high school diploma.

**R code:**
```r
county |>
  filter(!is.na(pop_change) & !is.na(metro) &
         !is.na(median_edu) & median_edu != "below_hs") |>
  ggplot(aes(x = median_hh_income, y = median_edu,
             fill = median_edu, color = median_edu)) +
  geom_density_ridges(alpha = 0.5, aes(linetype = median_edu)) +
  scale_linetype_manual(values = c("solid", "dashed", "dotted")) +
  facet_grid(pop_change ~ metro, labeller = label_both) +
  labs(x = "Median household income", y = NULL)
```

### 4.6.6 Causal Inference Warning

> ⚠️ **Important:** These are **observational data**. While we might like to make a causal connection between income and population growth, such an interpretation would be, at best, **half-baked**. We can observe **associations** but **cannot establish causation** from these data alone.

---

## 4.7 Chapter Review

### 4.7.1 Summary

Fluently working with categorical variables is an important skill for data analysts. This chapter introduced different visualizations and numerical summaries applied to categorical variables. The graphical visualizations are even more descriptive when two variables are presented simultaneously. Key visualizations covered include:

- **Bar plots** (single variable, stacked, standardized/filled, dodged)
- **Mosaic plots**
- **Pie charts** (with caveats)
- **Waffle charts**
- **Conditional proportions** (row and column)
- **Comparing numerical data across groups** (overlaid histograms, side-by-side box plots, ridge plots, faceted plots)

### 4.7.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Contingency table** | A table summarizing counts for two categorical variables |
| **Row totals** | Total counts across each row of a contingency table |
| **Column totals** | Total counts down each column of a contingency table |
| **Row proportions** | Each cell divided by its row total |
| **Column proportions** | Each cell divided by its column total |
| **Conditional proportions** | Proportions of one variable conditional on the level of another (row or column proportions) |
| **Stacked bar plot** | Bars subdivided by a second variable; total height = raw count |
| **Standardized bar plot** | Stacked bar plot where all bars are scaled to 100% |
| **Filled bar plot** | Another name for standardized bar plot |
| **Dodged bar plot** | Bars for each combination placed side by side |
| **Mosaic plot** | Area-based visualization for contingency tables; column widths and segment heights both convey information |
| **Ridge plot** | Overlaid density plots for multiple groups, stacked vertically |
| **Side-by-side box plot** | Multiple box plots on the same scale for group comparison |
| **Faceted plot** | Data split across multiple plotting windows based on categorical variable levels |

---

## 4.8 Exercises Overview

The chapter includes **10 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **Antibiotic use in children** | Comparing bar plots vs pie charts; identifying features visible in each |
| 2 | **Views on immigration** | Computing percentages from contingency tables; assessing association; column proportions |
| 3 | **Black Lives Matter** | Reading stacked bar plots; assessing association; identifying confounding variables |
| 4 | **Raise taxes** | Interpreting stacked bar plots; association between political affiliation and policy views |
| 5 | **Heart transplant data** | Choosing between stacked vs standardized bar plots; what each reveals |
| 6 | **Shipping holiday gifts** | Selecting appropriate visualizations for different analytical questions |
| 7 | **Meat consumption & life expectancy** | Ridge plots; confounding variables; association vs causation |
| 8 | **Florence Nightingale** | Confounding variables (age, sex, disease); why breakdowns matter |
| 9 | **On-time arrivals (Simpson's paradox)** | Computing conditional proportions; understanding how aggregated data can reverse group-level trends |
| 10 | **US House of Representatives** | Simpson's paradox in political context; within-group vs overall trends |

> Answers to odd-numbered exercises can be found in Appendix A.4 of the textbook.

---

## Key Takeaways: When to Use Each Visualization

| Goal | Recommended Visualization | Why |
|------|--------------------------|-----|
| **Show distribution of one categorical variable** | Bar plot | Easy to read; compare counts/proportions at a glance |
| **Show composition of groups (explanatory → response)** | Stacked bar plot | Shows total counts and breakdown simultaneously |
| **Compare proportions across groups** | Standardized/filled bar plot | All bars at 100%; easy to spot differences |
| **Show exact counts for all combinations** | Dodged bar plot | Side-by-side bars; no stacking ambiguity |
| **Show proportions + relative group sizes** | Mosaic plot | Area encodes both dimensions of information |
| **High-level overview with few simple categories** | Pie chart or waffle chart | Intuitive for halves, quarters, etc. |
| **Compare proportions with non-simple fractions** | Waffle chart | Each square = 1%; easier than pie charts |
| **Compare numerical distributions across groups** | Overlaid histograms or ridge plots | Shows full distribution shape |
| **Compare centers and spreads across groups** | Side-by-side box plots | Median, IQR, outliers at a glance |
| **Display 3+ variables simultaneously** | Faceted plots or multi-variable ridge plots | Splits data by multiple categorical variables |

### Critical Warnings

1. ⚠️ **Pie charts are hard to read** with more than a few categories — prefer bar plots
2. ⚠️ **Association ≠ Causation** — observational data can only show associations
3. ⚠️ **Row and column proportions are not equivalent** — choose based on your analytical question
4. ⚠️ **Standardized bar plots lose sample size information** — a bar of 100 looks the same as a bar of 5,000
5. ⚠️ **Simpson's paradox** — aggregated data can show trends opposite to those within subgroups (Exercise 9)
6. ⚠️ **Confounding variables** can explain observed associations — always consider alternative explanations
