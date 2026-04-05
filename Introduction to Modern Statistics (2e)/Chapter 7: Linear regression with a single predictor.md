# Chapter 7: Linear Regression with a Single Predictor

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/model-slr](https://openintro-ims.netlify.app/model-slr)

---

## Table of Contents

- [Overview](#overview)
- [Datasets Used](#datasets-used)
- [7.1 Fitting a Line, Residuals, and Correlation](#71-fitting-a-line-residuals-and-correlation)
  - [7.1.1 Fitting a Line to Data](#711-fitting-a-line-to-data)
  - [7.1.2 Using Linear Regression to Predict Possum Head Lengths](#712-using-linear-regression-to-predict-possum-head-lengths)
  - [7.1.3 Residuals](#713-residuals)
  - [7.1.4 Describing Linear Relationships with Correlation](#714-describing-linear-relationships-with-correlation)
- [7.2 Least Squares Regression](#72-least-squares-regression)
  - [7.2.1 Gift Aid for First-Year Students at Elmhurst College](#721-gift-aid-for-first-year-students-at-elmhurst-college)
  - [7.2.2 An Objective Measure for Finding the Best Line](#722-an-objective-measure-for-finding-the-best-line)
  - [7.2.3 Finding and Interpreting the Least Squares Line](#723-finding-and-interpreting-the-least-squares-line)
  - [7.2.4 Extrapolation is Treacherous](#724-extrapolation-is-treacherous)
  - [7.2.5 Describing the Strength of a Fit](#725-describing-the-strength-of-a-fit)
  - [7.2.6 Categorical Predictors with Two Levels](#726-categorical-predictors-with-two-levels)
- [7.3 Outliers in Linear Regression](#73-outliers-in-linear-regression)
- [7.4 Chapter Review](#74-chapter-review)
  - [7.4.1 Summary](#741-summary)
  - [7.4.2 Key Terms](#742-key-terms)
- [7.5 Exercises Overview](#75-exercises-overview)
- [Key Takeaways: Building and Interpreting Linear Models](#key-takeaways-building-and-interpreting-linear-models)

---

## Overview

Linear regression is a powerful statistical technique for fitting a line to data where the relationship between two variables can be modeled by a straight line with some error. Linear models can be used for prediction or to describe the relationship between two numerical variables, assuming there is a linear relationship between them. This chapter covers the form of a linear model, criteria for what makes a good fit, the correlation statistic, least squares regression, and the role of outliers.

---

## Datasets Used

| Dataset | Source | Description |
|---------|--------|-------------|
| **`possum`** | `openintro` R package | 104 brushtail possums from Australia; variables include `head_l` (head length in mm), `total_l` (total length in cm), `sex`, `age` |
| **`elmhurst`** | `openintro` R package | 50 first-year students at Elmhurst College (2011-2012); variables include `family_income` ($1,000s), `gift_aid` ($1,000s), `price_paid` ($1,000s) |
| **`mariokart`** | `openintro` R package | eBay auctions for Mario Kart (Nintendo Wii); variables include `price` (total auction price), `condnew` (indicator: 1 = new, 0 = used); limited to 141 games sold below $100 |
| **`bdims`** | `openintro` R package | 507 physically active individuals; variables include weight (kg/lbs) and height (cm/inches) |

---

## 7.1 Fitting a Line, Residuals, and Correlation

### 7.1.1 Fitting a Line to Data

A perfect linear relationship means we know the exact value of $y$ just by knowing $x$. This is unrealistic in almost any natural process. For example, family income provides useful but imperfect information about how much financial aid a college may offer.

**Linear regression model:**

$$y = \beta_0 + \beta_1 x + e$$

| Symbol | Meaning |
|--------|---------|
| $\beta_0$ | Population intercept (parameter) |
| $\beta_1$ | Population slope (parameter) |
| $b_0$ | Sample estimate of the intercept (statistic) |
| $b_1$ | Sample estimate of the slope (statistic) |
| $e$ | Error term |
| $x$ | Predictor variable |
| $y$ | Outcome variable |

The error term $e$ is often dropped when writing the model since the main focus is on predicting the average outcome.

**When linear regression is not appropriate:** If the relationship between variables is clearly nonlinear (e.g., quadratic), fitting a straight line is not helpful — the best-fitting line may be flat even though there is a strong relationship.

### 7.1.2 Using Linear Regression to Predict Possum Head Lengths

Researchers measured 104 brushtail possums in Australia. A scatterplot of head length (mm) vs. total length (cm) shows a moderately strong positive linear association: possums with above-average total length tend to have above-average head lengths.

A linear model fit by eye:

$$\hat{y} = 41 + 0.59x$$

**Interpretation:**
- A possum with total length 80 cm is predicted to have a head length of $41 + 0.59 \times 80 = 88.2$ mm
- This is an **estimate of the average** — possums with a total length of 80 cm will have an *average* head length of 88.2 mm

**Additional variables:** Male possums tend to be larger in both total length and head length than female possums. Age does not appear to change the relationship noticeably. Multiple predictors will be covered in [Chapter 8](/model-mlr).

### 7.1.3 Residuals

**Residuals** are the leftover variation in the data after accounting for the model fit:

$$\text{Data} = \text{Fit} + \text{Residual}$$

$$e_i = y_i - \hat{y}_i$$

| Residual Sign | Meaning |
|:---:|---|
| **Positive** | Model underestimated the observation (actual > predicted) |
| **Negative** | Model overestimated the observation (actual < predicted) |

One goal in picking the right linear model is for residuals to be as small as possible.

**Residual plots:** Residuals are plotted with predicted values on the horizontal axis and residual values on the vertical axis. This is like tipping the scatterplot over so the regression line is horizontal.

**Reading residual plots:**

| Pattern in Residuals | Interpretation |
|---------------------|---------------|
| Random scatter around 0 | Linear model is appropriate |
| Curved pattern | Relationship is nonlinear; a straight line is not appropriate |
| No obvious trend, scattered around 0 | Linear model is reasonable, but the slope may not be significantly different from zero |

### 7.1.4 Describing Linear Relationships with Correlation

**Correlation ($r$)** quantifies the strength and direction of the **linear** relationship between two variables. It always takes values between -1 and +1.

| Correlation Value | Interpretation |
|:---:|---|
| $r = +1$ | Perfect positive linear relationship |
| $r$ near +1 | Strong positive linear relationship |
| $r$ near 0 | No apparent linear relationship |
| $r$ near -1 | Strong negative linear relationship |
| $r = -1$ | Perfect negative linear relationship |

**Key properties of correlation:**
- **Unitless:** Not affected by changing units (e.g., kg to lbs, cm to inches)
- **Only measures linear relationships:** Strong nonlinear relationships (quadratic, cyclic) can produce correlations near zero
- **Formula:**

$$r = \frac{1}{n-1} \sum_{i=1}^{n} \frac{x_i-\bar{x}}{s_x}\frac{y_i-\bar{y}}{s_y}$$

where $\bar{x}$, $\bar{y}$, $s_x$, and $s_y$ are the sample means and standard deviations.

---

## 7.2 Least Squares Regression

Fitting lines by eye is subjective. **Least squares regression** provides a rigorous, objective approach.

### 7.2.1 Gift Aid for First-Year Students at Elmhurst College

Data from 50 first-year students at Elmhurst College show a negative trend: students with higher family incomes tended to receive lower gift aid (financial aid that does not need to be paid back). The correlation is $r = -0.499$.

### 7.2.2 An Objective Measure for Finding the Best Line

Two common criteria for fitting a line:

| Criterion | Formula | Notes |
|-----------|---------|-------|
| **Minimize absolute residuals** | $\sum \|e_i\|$ | Reasonable fit, but less common |
| **Minimize squared residuals (least squares)** | $\sum e_i^2$ | Most common; standard in statistical software |

**Four reasons to prefer least squares:**
1. Most commonly used method (tradition and convenience)
2. Widely supported in statistical software
3. A residual twice as large is **more than** twice as bad — squaring accounts for this
4. Analyses linking the model to population inference are most straightforward with least squares

### 7.2.3 Finding and Interpreting the Least Squares Line

For the Elmhurst data, the least squares regression equation is:

$$\widehat{\texttt{aid}} = \beta_0 + \beta_1 \times \texttt{family\_income}$$

**R output:**

| term | estimate | std.error | statistic | p.value |
|------|----------|-----------|-----------|---------|
| (Intercept) | 24.32 | 1.29 | 18.83 | <0.0001 |
| family_income | -0.04 | 0.01 | -3.98 | 2e-04 |

**Interpreting the slope ($b_1 = -0.043$):** For each additional $1,000 of family income, a student is expected to receive $43.10 less in gift aid on average. The negative coefficient means higher income corresponds to less aid.

**Interpreting the intercept ($b_0 = 24.319$):** The average aid for a student whose family has no income is $24,319. This is meaningful here because some students do have $0 family income. In other applications, the intercept may have little practical value if $x = 0$ is not observed or relevant.

> ⚠️ **Caution:** These are observational data — we cannot interpret a causal connection between variables.

**Manual calculation from summary statistics:**

The least squares line has two key properties:
1. The slope can be estimated by $b_1 = \frac{s_y}{s_x} r$
2. The point $(\bar{x}, \bar{y})$ falls on the least squares line

| | Family income ($x$) | Gift aid ($y$) |
|---|---|---|
| **mean** | 102 | 19.9 |
| **sd** | 63.2 | 5.46 |
| **$r$** | \multicolumn{2}{c|}{-0.499} |

$$b_1 = \frac{5.46}{63.2}(-0.499) = -0.0431$$

Using the point-slope equation with $(\bar{x}, \bar{y}) = (102, 19.9)$:

$$\widehat{\texttt{aid}} = 24.3 - 0.0431 \times \texttt{family\_income}$$

> **Important:** The final least squares equation should always include a "hat" on the predicted variable.

### 7.2.4 Extrapolation is Treacherous

**Extrapolation** is applying a model estimate to values outside the realm of the original data.

**Example:** Using the Elmhurst model to predict aid for a family with $1 million income:

$$24.3 - 0.0431 \times 1000 = -18.8$$

The model predicts -$18,800 in aid, which is nonsensical. Linear models are approximations of real relationships, and extrapolating makes an unreliable bet that the linear relationship holds where it has not been analyzed.

### 7.2.5 Describing the Strength of a Fit

**$R^2$ (R-squared)**, also called the **coefficient of determination**, describes the proportion of variation in the outcome variable explained by the least squares line.

$$R^2 = r^2 = \frac{SST - SSE}{SST} = 1 - \frac{SSE}{SST}$$

| Term | Formula | Meaning |
|------|---------|---------|
| **SST (Total Sum of Squares)** | $\sum (y_i - \bar{y})^2$ | Total variability in $y$ |
| **SSE (Sum of Squared Errors)** | $\sum (y_i - \hat{y}_i)^2 = \sum e_i^2$ | Variability remaining after the model |
| **SSR (Regression Sum of Squares)** | $\sum (\hat{y}_i - \bar{y})^2$ | Variation in $y$ accounted for by the model |

**Example — Elmhurst data:**
- $R^2 = (-0.499)^2 = 0.25$
- 25% of the variation in gift aid is explained by family income using the linear model
- The remaining 75% of variation is unexplained by this model

$R^2$ always falls between 0 and 1 (since $r$ is between -1 and 1).

### 7.2.6 Categorical Predictors with Two Levels

Categorical variables with two levels can be used as predictors by converting them into an **indicator variable** (0 for one category, 1 for the other).

**Example — Mario Kart eBay auctions:**

$$\widehat{\texttt{price}} = 42.87 + 10.9 \times \texttt{condnew}$$

| term | estimate |
|------|----------|
| (Intercept) | 42.9 |
| condnew | 10.9 |

**Interpretation:**
- **Intercept ($b_0 = 42.9$):** The average selling price of a **used** game (`condnew` = 0) is $42.90
- **Slope ($b_1 = 10.9$):** On average, **new** games sell for $10.90 more than used games

**General rule for categorical predictors with two levels:**
- The intercept is the average outcome for the reference category (indicator = 0)
- The slope is the average difference in the outcome between the two categories

---

## 7.3 Outliers in Linear Regression

Outliers in regression are observations that fall far from the cloud of points. They can have a strong influence on the least squares line.

| Term | Definition |
|------|-----------|
| **Outlier** | A point (or group of points) that does not appear to belong with the vast majority of other points |
| **Leverage point** | An outlier that falls horizontally far from the center of the cloud; these points "pull harder" on the line |
| **High leverage** | Points with high leverage have the potential to strongly influence the slope |
| **Influential point** | A high leverage point that actually does invoke its influence on the slope; if the line were fitted without it, the point would be unusually far from the line |

**Key distinctions:**
- Being outlying in a univariate sense (in $x$ or $y$) does not necessarily mean being outlying from the **bivariate model**
- If points are in-line with the bivariate model, they will not influence the regression line even if they are extreme in $x$ or $y$

**Types of outlier influence:**

| Scenario | Influence on Line |
|----------|-------------------|
| Outlier in $y$ direction only | Slight influence on the line |
| Outlier in $x$ direction but close to the line | Not influential |
| Outlier in both $x$ and $y$, far from the trend | Pulls the line toward it; influential |
| Secondary cloud of outliers | Can drag the line, making it fit poorly almost everywhere |
| Single outlier with no trend in main cloud | Can create a false linear trend where none exists |
| Outlier far in both directions but in-line with the trend | Not influential |

**Best practice:** Produce two analyses — one with and one without the outlying observations. Present both and discuss the role of the outliers for a more holistic understanding.

> ⚠️ **Warning:** Don't remove outliers without a very good reason. Models that ignore exceptional cases often perform poorly. For example, a financial firm that ignored the largest market swings would soon go bankrupt.

---

## 7.4 Chapter Review

### 7.4.1 Summary

This chapter covered the nuances of the linear model:
- Creating linear models with numerical predictors (e.g., possum total length) and categorical predictors with two levels (e.g., game condition: new/used)
- Residuals as an important metric for understanding model fit
- How high leverage points, influential points, and other types of outliers impact the fit
- Correlation as a measure of the strength and direction of a linear relationship, without specifying which variable is explanatory and which is outcome
- Future chapters will focus on generalizing the linear model from sample data to claims about the population

### 7.4.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Coefficient of determination** | $R^2$; proportion of variation in the outcome explained by the linear model |
| **Correlation** | $r$; measures strength and direction of linear relationship between two variables (-1 to +1) |
| **Extrapolation** | Applying a model to values outside the range of the original data |
| **High leverage** | Points that fall horizontally far from the center of the data cloud |
| **Indicator variable** | A binary variable (0 or 1) used to represent a categorical predictor with two levels |
| **Influential point** | An outlier that strongly affects the slope of the least squares line |
| **Least squares line** | The line that minimizes the sum of squared residuals |
| **Leverage point** | An outlier far from the center of the cloud in the horizontal direction |
| **Outcome** | The variable being predicted ($y$) |
| **Outlier** | A point that does not appear to belong with the rest of the data |
| **Predictor** | The variable used to make predictions ($x$) |
| **R-squared** | $R^2$; same as coefficient of determination |
| **Regression sum of squares** | $SSR = SST - SSE$; variation in $y$ accounted for by the model |
| **Residuals** | $e_i = y_i - \hat{y}_i$; the difference between observed and predicted values |
| **Sum of squared errors** | $SSE = \sum e_i^2$; total squared distance of observations from the fitted line |
| **Total sum of squares** | $SST = \sum (y_i - \bar{y})^2$; total variability in the outcome variable |

---

## 7.5 Exercises Overview

The chapter includes **32 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1–2 | **Visualizing residuals** | Constructing and interpreting residual plots; identifying patterns |
| 3–4 | **Identifying relationships** | Assessing strength of linear relationships and appropriateness of linear models from scatterplots |
| 5 | **Midterms and final** | Comparing correlations; interpreting scatterplots |
| 6 | **Meat consumption & life expectancy** | Describing relationships; comparing correlations; unit invariance of $r$ |
| 7–8 | **Matching correlations** | Matching correlation values to scatterplots |
| 9 | **Body measurements (correlation)** | Describing relationships; effect of unit changes on correlation |
| 10 | **Comparing correlations** | Correlation is unaffected by unit changes |
| 11–12 | **Coast Starlight & crawling babies (correlation)** | Describing relationships; unit invariance of $r$ |
| 13–14 | **Perfect correlations** | Correlation under perfect linear transformations ($r = \pm 1$) |
| 15 | **Units of regression** | Identifying units of correlation (unitless), intercept, and slope |
| 16 | **Uncertainty in slope** | Relationship between scatter and slope estimate precision |
| 17–18 | **Over/under estimation** | Interpreting residual signs (positive = underestimate, negative = overestimate) |
| 19–20 | **Starbucks menu items** | Describing relationships; predictor/outcome identification; residual plot interpretation |
| 21 | **Coast Starlight (regression)** | Computing regression equation from summary stats; interpreting slope, intercept, $R^2$; calculating residuals; identifying extrapolation |
| 22 | **Body measurements (regression)** | Writing regression equation; interpreting parameters; prediction; residual calculation; extrapolation |
| 23 | **Poverty & unemployment** | Reading regression output; writing model; interpreting intercept, slope, $R^2$; computing $r$ |
| 24 | **Cat weights** | Reading regression output; interpreting parameters; $R^2$ interpretation; computing $r$ |
| 25–26 | **Identifying outliers** | Classifying outliers as leverage points, influential points, or non-influential |
| 27 | **Urban homeowners** | Describing relationship; identifying outlier type (leverage/influential) |
| 28 | **Crawling babies (outliers)** | Assessing leverage and influence of a potential outlier |
| 29 | **True/False** | Testing understanding of correlation properties |
| 30 | **Cherry trees** | Comparing predictor variables; choosing the better predictor based on scatterplot patterns |
| 31 | **Matching correlations III** | Matching correlation values to scatterplots |
| 32 | **Helmets & lunches** | Computing correlation from $R^2$; calculating slope and intercept from summary statistics; interpreting residuals |

> Answers to odd-numbered exercises can be found in Appendix A.7 of the textbook.

---

## Key Takeaways: Building and Interpreting Linear Models

### Model Form and Fitting

| Concept | Key Point |
|---------|-----------|
| **Linear model equation** | $\hat{y} = b_0 + b_1 x$ — always use a "hat" on the predicted variable |
| **Least squares criterion** | Minimizes $\sum e_i^2$; the standard approach for fitting a line |
| **Slope from summary stats** | $b_1 = \frac{s_y}{s_x} r$ — slope combines relative spread and correlation |
| **Intercept from summary stats** | $b_0 = \bar{y} - b_1 \bar{x}$ — the line always passes through $(\bar{x}, \bar{y})$ |
| **Categorical predictor (2 levels)** | Use an indicator variable; intercept = reference group mean, slope = difference between groups |

### Parameter Interpretation

| Parameter | Interpretation |
|-----------|---------------|
| **Slope ($b_1$)** | The estimated average change in $y$ for each one-unit increase in $x$ |
| **Intercept ($b_0$)** | The estimated average value of $y$ when $x = 0$ (only meaningful if $x = 0$ is observed/relevant) |

### Assessing Model Quality

| Tool | What It Reveals |
|------|----------------|
| **Scatterplot** | Direction, strength, linearity, and outliers |
| **Residual plot** | Whether a linear model is appropriate (random scatter = good; patterns = bad) |
| **Correlation ($r$)** | Strength and direction of linear relationship only |
| **$R^2$** | Proportion of outcome variation explained by the model |

### Critical Warnings

1. ⚠️ **Correlation only measures linear relationships** — strong nonlinear patterns can produce $r \approx 0$
2. ⚠️ **Correlation is unitless** — changing units (kg to lbs, cm to inches) does not change $r$
3. ⚠️ **Extrapolation is unreliable** — linear models are approximations; do not trust predictions outside the observed data range
4. ⚠️ **$R^2$ is always $r^2$** — for simple linear regression, squaring the correlation gives $R^2$
5. ⚠️ **Don't remove outliers without justification** — they may represent real phenomena; always present analyses with and without them
6. ⚠️ **Observational data cannot establish causation** — even strong regression fits do not imply causal relationships
7. ⚠️ **High leverage ≠ influential** — a point can have high leverage without actually changing the slope; influence requires both leverage and deviation from the trend
8. ⚠️ **The intercept may be meaningless** — if $x = 0$ is outside the observed range or nonsensical in context, the intercept has no practical interpretation
9. ⚠️ **Residual sign matters** — positive residual means the model underestimated; negative residual means the model overestimated
