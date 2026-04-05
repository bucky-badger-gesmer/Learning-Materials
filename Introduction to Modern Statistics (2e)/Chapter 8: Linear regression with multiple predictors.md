# Chapter 8: Linear Regression with Multiple Predictors

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/model-mlr](https://openintro-ims.netlify.app/model-mlr)

---

## Table of Contents

- [Overview](#overview)
- [Datasets Used](#datasets-used)
- [8.1 Indicator and Categorical Predictors](#81-indicator-and-categorical-predictors)
  - [8.1.1 Two-Level Categorical Predictors](#811-two-level-categorical-predictors)
  - [8.1.2 Multi-Level Categorical Predictors](#812-multi-level-categorical-predictors)
  - [8.1.3 The Reference Level](#813-the-reference-level)
- [8.2 Many Predictors in a Model](#82-many-predictors-in-a-model)
  - [8.2.1 The Multiple Regression Model](#821-the-multiple-regression-model)
  - [8.2.2 Interpreting Coefficients](#822-interpreting-coefficients)
  - [8.2.3 Collinearity and Multicollinearity](#823-collinearity-and-multicollinearity)
- [8.3 Adjusted R-squared](#83-adjusted-r-squared)
- [8.4 Model Selection](#84-model-selection)
  - [8.4.1 Stepwise Selection](#841-stepwise-selection)
  - [8.4.2 Other Model Selection Strategies](#842-other-model-selection-strategies)
- [8.5 Chapter Review](#85-chapter-review)
  - [8.5.1 Summary](#851-summary)
  - [8.5.2 Key Terms](#852-key-terms)
- [8.6 Exercises Overview](#86-exercises-overview)
- [Key Takeaways: Building Multiple Regression Models](#key-takeaways-building-multiple-regression-models)

---

## Overview

Multiple regression extends single-predictor linear regression to cases with two or more predictor variables. By considering how different explanatory variables interact, we can uncover complicated relationships between predictors and the response variable. One challenge is knowing which variables are most important to include in the model. This chapter covers indicator and categorical predictors, fitting models with many predictors, adjusted $R^2$, and model selection strategies including backward elimination and forward selection.

---

## Datasets Used

| Dataset | Source | Description |
|---------|--------|-------------|
| **`loans`** | `openintro` R package (modified from `loans_full_schema`) | 10,000 Lending Club loans; variables include `interest_rate`, `verified_income` (3 levels), `debt_to_income`, `credit_util`, `bankruptcy` (indicator), `term`, `credit_checks`, `issue_month` (3 levels) |
| **`births14`** | `openintro` R package | 1,000 births from 2014 US CDC data; variables include `weight` (lbs), `weeks` (gestation), `mage` (mother's age), `sex`, `visits`, `habit` (smoker/nonsmoker), `mature` |
| **`penguins`** | `palmerpenguins` R package | 344 Antarctic penguins (3 species, 3 islands); variables include `bill_length_mm`, `bill_depth_mm`, `flipper_length_mm`, `body_mass_g`, `species`, `sex` |

---

## 8.1 Indicator and Categorical Predictors

### 8.1.1 Two-Level Categorical Predictors

Indicator variables (0/1) can be used directly as predictors in regression. For example, a single-predictor model for interest rate based on bankruptcy history:

$$\widehat{\texttt{interest\_rate}} = 12.34 + 0.74 \times \texttt{bankruptcy}$$

| term | estimate | std.error | statistic | p.value |
|------|----------|-----------|-----------|---------|
| (Intercept) | 12.34 | 0.05 | 231.49 | <0.0001 |
| bankruptcy1 | 0.74 | 0.15 | 4.82 | <0.0001 |

**Interpretation:** The model predicts a 0.74% higher interest rate for borrowers with a bankruptcy in their record, compared to those without.

### 8.1.2 Multi-Level Categorical Predictors

When a categorical variable has $k > 2$ levels, software produces $k - 1$ coefficient rows. One level is omitted — this is the **reference level**.

**Example — `verified_income` (3 levels: Not Verified, Source Verified, Verified):**

| term | estimate | std.error | statistic | p.value |
|------|----------|-----------|-----------|---------|
| (Intercept) | 11.10 | 0.08 | 137.2 | <0.0001 |
| verified_incomeSource Verified | 1.42 | 0.11 | 12.8 | <0.0001 |
| verified_incomeVerified | 3.25 | 0.13 | 25.1 | <0.0001 |

The model equation uses indicator variables for each non-reference level:

$$\begin{aligned} \widehat{\texttt{interest\_rate}} &= 11.10 \\ &+ 1.42 \times \texttt{verified\_income}_{\texttt{Source Verified}} \\ &+ 3.25 \times \texttt{verified\_income}_{\texttt{Verified}} \end{aligned}$$

**Predictions for each level:**

| Income Verification | Indicator Values | Predicted Interest Rate |
|---------------------|-----------------|------------------------|
| Not Verified (reference) | Both indicators = 0 | 11.10% |
| Source Verified | Source Verified = 1, Verified = 0 | 12.52% |
| Verified | Source Verified = 0, Verified = 1 | 14.35% |

### 8.1.3 The Reference Level

| Concept | Explanation |
|---------|-------------|
| **Reference level** | The omitted category; all other level coefficients are measured relative to it |
| **Intercept** | The predicted outcome for the reference level (when all other predictors are 0) |
| **Coefficient for a level** | The average difference in outcome between that level and the reference level, holding other predictors constant |

**Counter-intuitive finding:** Borrowers with verified income have *higher* interest rates. This may be explained by confounding — lenders may require income verification for borrowers with poor credit, making verification a signal of risk rather than reassurance.

---

## 8.2 Many Predictors in a Model

### 8.2.1 The Multiple Regression Model

**Multiple regression** simultaneously accounts for all predictor variables in the model.

For the `loans` dataset with 7 variables (9 effective predictors due to multi-level categorical variables):

$$\begin{aligned} \widehat{\texttt{interest\_rate}} &= b_0 \\ &+ b_1 \times \texttt{verified\_income}_{\texttt{Source Verified}} + b_2 \times \texttt{verified\_income}_{\texttt{Verified}} \\ &+ b_3 \times \texttt{debt\_to\_income} + b_4 \times \texttt{credit\_util} \\ &+ b_5 \times \texttt{bankruptcy} + b_6 \times \texttt{term} \\ &+ b_7 \times \texttt{credit\_checks} + b_8 \times \texttt{issue\_month}_{\texttt{Jan-2018}} \\ &+ b_9 \times \texttt{issue\_month}_{\texttt{Mar-2018}} \end{aligned}$$

Coefficients are estimated by minimizing the sum of squared residuals:

$$SSE = \sum_{i=1}^{n} e_i^2 = \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$$

**Full model output:**

| term | estimate | std.error | statistic | p.value |
|------|----------|-----------|-----------|---------|
| (Intercept) | 1.89 | 0.21 | 9.01 | <0.0001 |
| verified_incomeSource Verified | 1.00 | 0.10 | 10.06 | <0.0001 |
| verified_incomeVerified | 2.56 | 0.12 | 21.87 | <0.0001 |
| debt_to_income | 0.02 | 0.00 | 7.43 | <0.0001 |
| credit_util | 4.90 | 0.16 | 30.25 | <0.0001 |
| bankruptcy1 | 0.39 | 0.13 | 2.96 | 0.0031 |
| term | 0.15 | 0.00 | 38.89 | <0.0001 |
| credit_checks | 0.23 | 0.02 | 12.52 | <0.0001 |
| issue_monthJan-2018 | 0.05 | 0.11 | 0.42 | 0.6736 |
| issue_monthMar-2018 | -0.04 | 0.11 | -0.39 | 0.696 |

### 8.2.2 Interpreting Coefficients

**General interpretation:** Each coefficient represents the predicted change in the outcome for a one-unit increase in that predictor, **given that all other predictors in the model are held constant**.

**Example — `credit_checks` ($b = 0.23$):** All else held constant, for each additional credit check in the last 12 months, the interest rate is expected to be 0.23 percentage points higher on average.

**Why coefficients change between single and multiple predictor models:**

| Model | `bankruptcy` Coefficient |
|-------|------------------------|
| Single predictor only | 0.74 |
| Full model (all predictors) | 0.39 |

The difference arises because predictors are correlated. In the single-predictor model, the `bankruptcy` coefficient absorbed the effect of other unmeasured variables (like income verification, debt-to-income ratio). When all variables are included, this underlying bias is reduced or eliminated.

### 8.2.3 Collinearity and Multicollinearity

| Term | Definition |
|------|-----------|
| **Collinear** | Two predictor variables are correlated with each other |
| **Multicollinearity** | The presence of correlation among multiple predictor variables; complicates model estimation |

Multicollinearity is impossible to prevent in observational data but can be avoided in experimental design. It makes it difficult to isolate the individual effect of each predictor.

**The intercept in multiple regression:** The intercept represents the predicted outcome when all predictors equal zero. This interpretation is only reasonable if $x = 0$ is observed and meaningful for all variables. In the loans model, `term` (loan length in months) never equals zero, making the intercept interpretation uninformative.

---

## 8.3 Adjusted R-squared

Regular $R^2$ measures the proportion of outcome variation explained by the model:

$$R^2 = 1 - \frac{\text{variability in residuals}}{\text{variability in the outcome}} = 1 - \frac{Var(e_i)}{Var(y_i)}$$

However, $R^2$ is a **biased (overly optimistic)** estimate when applied to models with more than one predictor. The **adjusted $R^2$** corrects this bias.

$$R_{adj}^2 = 1 - \frac{s_{\text{residuals}}^2}{s_{\text{outcome}}^2} \times \frac{n-1}{n-k-1}$$

| Symbol | Meaning |
|--------|---------|
| $n$ | Number of observations |
| $k$ | Number of predictor variables (a categorical variable with $p$ levels contributes $p - 1$ to $k$) |
| $n - k - 1$ | Degrees of freedom |

**Key properties:**

| Property | Explanation |
|----------|-------------|
| $R_{adj}^2 \leq R^2$ | Because $k$ is never negative, adjusted $R^2$ is always smaller (often just slightly) |
| Penalizes unnecessary predictors | Adding a predictor that doesn't reduce residual variance will **decrease** adjusted $R^2$ |
| Better for model comparison | More accurate estimate of how well the model will predict new data |
| Nearly identical to $R^2$ when $k = 1$ | The adjustment is negligible for single-predictor models |

**Example — loans full model:**
- Variance of residuals: 18.53
- Variance of outcome: 25.01
- $R^2 = 1 - \frac{18.53}{25.01} = 0.2591$
- $R_{adj}^2 = 1 - \frac{18.53}{25.01} \times \frac{9999}{9990} = 0.2584$

---

## 8.4 Model Selection

The best model is not always the most complicated. Including unimportant predictors can reduce prediction accuracy. Models that have undergone predictor pruning are called **parsimonious**.

The **full model** includes all available predictors. If it isn't the best model, we want to identify a smaller model that is preferable.

### 8.4.1 Stepwise Selection

Two common strategies for adding or removing predictors:

| Strategy | Starting Point | Process |
|----------|---------------|---------|
| **Backward elimination** | Full model (all predictors) | Remove one predictor at a time until no further improvement in adjusted $R^2$ |
| **Forward selection** | No predictors | Add one predictor at a time until no further improvement in adjusted $R^2$ |

**Backward elimination example — loans data:**

| Step | Action | Adjusted $R^2$ |
|------|--------|---------------|
| Baseline | Full model (all 7 variables) | 0.2597 |
| Step 1 | Drop `issue_month` | **0.2598** (highest) |
| Step 2 | Try dropping each remaining predictor | None improve beyond 0.2598 |
| **Final model** | All predictors except `issue_month` | 0.2598 |

**Forward selection example — loans data:**

| Step | Predictor Added | Adjusted $R^2$ |
|------|----------------|---------------|
| Baseline | No predictors | 0.0000 |
| Step 1 | `term` | 0.12855 |
| Step 2 | `credit_util` | 0.20046 |
| Step 3 | `verified_income` | 0.24183 |
| Step 4 | `debt_to_income` | — |
| Step 5 | `credit_checks` | — |
| Step 6 | `bankruptcy` | 0.25854 |
| Step 7 | `issue_month` considered | 0.25843 (worse — not added) |
| **Final model** | All predictors except `issue_month` | 0.25854 |

Both strategies arrived at the same final model in this example, but this is not guaranteed — the decision for whether to include a predictor depends on which other predictors are already in the model.

**Important considerations:**
- Decide on a **meaningful threshold** for adjusted $R^2$ improvement before beginning model selection (e.g., 1% or 5%)
- Use the threshold consistently to decide if one model is "better" than another
- Backward elimination and forward selection can produce different final models when predictors are correlated

### 8.4.2 Other Model Selection Strategies

| Strategy | Decision Criterion | Notes |
|----------|-------------------|-------|
| **Stepwise with p-values** | Statistical significance of predictors | Covered in [Chapter 24](/inf-model-slr) onward |
| **Stepwise with AIC/BIC** | Akaike or Bayesian Information Criterion | Covered in more advanced courses |
| **Expert opinion** | Domain knowledge and research focus | Many statisticians advocate for this approach |
| **Thoughtful combination** | Research focus + data features | Preferred over stepwise selection alone |

> ⚠️ **Warning:** Many statisticians discourage the use of stepwise regression *alone* for model selection. A more thoughtful approach that carefully considers the research focus and features of the data is preferred.

---

## 8.5 Chapter Review

### 8.5.1 Summary

Multiple linear regression describes how multiple variables can be modeled together. Each coefficient represents how the model predicts the outcome might change with a one-unit increase of that predictor, **given the rest of the predictor variables in the model**. Working with and interpreting multivariable models can be tricky, especially when predictors show multicollinearity. There is often no perfect or "right" final model, but adjusted $R^2$ is one way to identify important predictor variables. Later chapters will generalize multiple linear regression models to a larger population.

### 8.5.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Adjusted R-squared** | $R_{adj}^2$; bias-corrected version of $R^2$ for comparing models with different numbers of predictors |
| **Backward elimination** | Stepwise strategy starting with the full model and removing predictors one at a time |
| **Collinear** | Two predictor variables that are correlated with each other |
| **Degrees of freedom** | $n - k - 1$ in multiple regression; used in the adjusted $R^2$ formula |
| **Forward selection** | Stepwise strategy starting with no predictors and adding them one at a time |
| **Full model** | The model that includes all available predictor variables |
| **Multicollinearity** | Correlation among multiple predictor variables; complicates model estimation |
| **Multiple regression** | A linear model with many predictors |
| **Parsimonious** | A model that has been pruned of unimportant predictors; simpler is better |
| **Reference level** | The omitted category in a multi-level categorical predictor; other levels are measured relative to it |
| **Stepwise selection** | Adding or removing predictors one at a time based on a decision criterion (e.g., adjusted $R^2$) |

---

## 8.6 Exercises Overview

The chapter includes **16 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **High correlation, good or bad?** | Distinguishing predictor-outcome vs. predictor-predictor correlation; understanding multicollinearity |
| 2 | **Dealing with categorical predictors** | Whether correlation can be computed with recoded binary predictors; alternatives for assessing association |
| 3 | **Meat consumption & life expectancy** | Describing relationships; confounding by income status; stratified analysis |
| 4 | **Arrival delays** | Simpson's paradox-like scenario; combined vs. stratified plots; when to stratify |
| 5 | **Training for a 5K** | Identifying redundant/perfectly collinear predictors (`days_since_start` vs. `days_till_race`) |
| 6 | **Multiple regression fact checking** | True/false statements about collinearity, coefficient interpretation, and causation |
| 7 | **Baby weights & smoking** | Writing regression equation; interpreting slope for binary predictor; computing predictions |
| 8 | **Baby weights & mature moms** | Writing regression equation; interpreting slope; computing predictions for each category |
| 9 | **Movie returns, prediction** | Identifying highest-ROI genre from coefficients; evaluating whether to add a predictor based on adjusted $R^2$ change |
| 10 | **Movie returns by genre** | Interpreting predicted vs. actual plots by genre; evaluating model fit quality across categories |
| 11 | **Predicting baby weights (multiple)** | Writing full regression equation; interpreting slopes; explaining coefficient differences between small and large models; computing residuals |
| 12 | **Palmer penguins, predicting body mass** | Writing full regression equation; interpreting all slopes; computing and interpreting residuals; interpreting $R^2$ |
| 13 | **Baby weights, backward elimination** | Applying backward elimination using adjusted $R^2$; deciding which variable to remove |
| 14 | **Palmer penguins, backward elimination** | Applying backward elimination; deciding which variable to remove first |
| 15 | **Baby weights, forward selection** | Applying forward selection; deciding which variable to add first based on adjusted $R^2$ |
| 16 | **Palmer penguins, forward selection** | Applying forward selection; deciding which variable to add first |

> Answers to odd-numbered exercises can be found in Appendix A.8 of the textbook.

---

## Key Takeaways: Building Multiple Regression Models

### Model Structure

| Concept | Key Point |
|---------|-----------|
| **Multiple regression equation** | $\hat{y} = b_0 + b_1 x_1 + b_2 x_2 + \cdots + b_k x_k$ |
| **Categorical variable with $p$ levels** | Contributes $p - 1$ terms to the model (one reference level is omitted) |
| **Coefficient interpretation** | Change in $y$ per one-unit increase in $x$, **holding all other predictors constant** |
| **Intercept interpretation** | Predicted $y$ when all $x = 0$ — only meaningful if $x = 0$ is observed and relevant for all predictors |

### Model Assessment and Selection

| Tool | Purpose |
|------|---------|
| **Adjusted $R^2$** | Compare models with different numbers of predictors; penalizes unnecessary complexity |
| **Backward elimination** | Start full, remove predictors that don't improve adjusted $R^2$ |
| **Forward selection** | Start empty, add predictors that most improve adjusted $R^2$ |
| **Expert opinion** | Use domain knowledge to select predictors; preferred over stepwise alone |

### Critical Warnings

1. ⚠️ **Coefficients change with model composition** — a predictor's coefficient in a single-predictor model differs from its coefficient in a multiple regression model due to confounding variables
2. ⚠️ **Multicollinearity complicates interpretation** — correlated predictors make it difficult to isolate individual effects
3. ⚠️ **$R_{adj}^2$ can decrease when adding predictors** — if a new predictor doesn't reduce residual variance enough, adjusted $R^2$ goes down
4. ⚠️ **Stepwise strategies can disagree** — backward elimination and forward selection may produce different final models
5. ⚠️ **Set a threshold before model selection** — decide what constitutes a "meaningful" improvement in adjusted $R^2$ (e.g., 1% or 5%) before beginning
6. ⚠️ **Don't include redundant predictors** — variables that are perfectly or nearly perfectly correlated (e.g., `days_since_start` and `days_till_race`) should not both be in the model
7. ⚠️ **The "best" model depends on context** — there is rarely a single correct model; the choice depends on the research question and the intended use
8. ⚠️ **Stepwise selection alone is discouraged** — many statisticians recommend combining stepwise methods with thoughtful consideration of the research focus and data features
9. ⚠️ **Counter-intuitive coefficients may signal confounding** — if a coefficient seems surprising (e.g., verified income → higher interest rates), look for unmeasured variables that may explain the relationship
10. ⚠️ **Categorical predictors need a reference level** — software omits one level; all other coefficients are relative to this baseline
