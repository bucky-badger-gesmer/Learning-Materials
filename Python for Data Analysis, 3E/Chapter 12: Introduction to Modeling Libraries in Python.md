# Chapter 12: Introduction to Modeling Libraries in Python

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/modeling](https://wesmckinney.com/book/modeling)

---

## Table of Contents

- [12.1 Interfacing Between pandas and Model Code](#121-interfacing-between-pandas-and-model-code)
  - [Converting DataFrames to NumPy Arrays](#converting-dataframes-to-numpy-arrays)
  - [Selecting Specific Columns with `loc`](#selecting-specific-columns-with-loc)
  - [Creating Dummy Variables with `pd.get_dummies()`](#creating-dummy-variables-with-pdget_dummies)
  - [Joining Dummies Back to the Original DataFrame](#joining-dummies-back-to-the-original-dataframe)
- [12.2 Creating Model Descriptions with Patsy](#122-creating-model-descriptions-with-patsy)
  - [Basic Formula Syntax](#basic-formula-syntax)
  - [Intercept Handling](#intercept-handling)
  - [DesignMatrix Objects and `design_info`](#designmatrix-objects-and-design_info)
  - [Data Transformations in Formulas](#data-transformations-in-formulas)
  - [Stateful Transformations and Out-of-Sample Prediction](#stateful-transformations-and-out-of-sample-prediction)
  - [Categorical Data in Patsy](#categorical-data-in-patsy)
  - [Interaction Terms](#interaction-terms)
- [12.3 Introduction to statsmodels](#123-introduction-to-statsmodels)
  - [Array-Based API (`statsmodels.api`)](#array-based-api-statsmodelsapi)
  - [Formula-Based API (`statsmodels.formula.api`)](#formula-based-api-statsmodelsformulaapi)
  - [Estimating Linear Models](#estimating-linear-models)
  - [Model Summary and Diagnostics](#model-summary-and-diagnostics)
  - [Out-of-Sample Predictions](#out-of-sample-predictions)
  - [Estimating Time Series Processes](#estimating-time-series-processes)
- [12.4 Introduction to scikit-learn](#124-introduction-to-scikit-learn)
  - [Titanic Dataset: Full ML Workflow](#titanic-dataset-full-ml-workflow)
  - [Cross-Validation](#cross-validation)
  - [Built-in CV vs `cross_val_score`](#built-in-cv-vs-cross_val_score)
- [12.5 Conclusion](#125-conclusion)
- [Quick Reference](#quick-reference)
- [Key Differences: statsmodels vs scikit-learn](#key-differences-statsmodels-vs-scikit-learn)
- [Further Reading](#further-reading)

---

## Overview

Feature engineering — the process of building data representations suitable for machine learning algorithms — is the bridge between data wrangling and modeling. This chapter covers the essential Python libraries for statistical modeling and machine learning, focusing on how to prepare pandas data for model consumption and how to use the two most prominent modeling ecosystems: **statsmodels** for statistical inference and **scikit-learn** for general-purpose machine learning.

**Key capabilities:**
- Convert pandas DataFrames to NumPy arrays for model input (`to_numpy()`)
- Create dummy/one-hot encoded variables from categorical data (`pd.get_dummies()`)
- Describe statistical models using R-style formula syntax (Patsy)
- Fit linear models with detailed statistical diagnostics (statsmodels)
- Build autoregressive time series models (statsmodels)
- Train, evaluate, and cross-validate machine learning models (scikit-learn)
- Handle missing data and feature engineering for real-world datasets

```python
import numpy as np
import pandas as pd
```

---

## 12.1 Interfacing Between pandas and Model Code

The first step in any modeling workflow is converting your pandas data structures into formats that modeling libraries can consume. Most model libraries expect NumPy arrays as input.

### Converting DataFrames to NumPy Arrays

```python
data = pd.DataFrame({
    "x0": [1, 2, 3, 4, 5],
    "x1": [0.01, -0.01, 0.25, -4.1, 0.],
    "y": [-1.5, 0., 3.6, 1.3, -2.]
})

data.columns
# Index(['x0', 'x1', 'y'], dtype='object')

data.to_numpy()
# array([[ 1.  ,  0.01, -1.5 ],
#        [ 2.  , -0.01,  0.  ],
#        [ 3.  ,  0.25,  3.6 ],
#        [ 4.  , -4.1 ,  1.3 ],
#        [ 5.  ,  0.  , -2.  ]])
```

> ⚠️ **Homogeneous data requirement:** When a DataFrame contains columns of different dtypes, `to_numpy()` returns an **object array**, which is unsuitable for most numerical modeling libraries.

```python
df2 = pd.DataFrame({
    "x0": [1, 2, 3, 4, 5],
    "y": [-1.5, 0., 3.6, 1.3, -2.],
    "category": pd.Categorical(["a", "b", "a", "a", "b"])
})

df2.to_numpy()
# array([[1, -1.5, 'a'],
#        [2, 0.0, 'b'],
#        [3, 3.6, 'a'],
#        [4, 1.3, 'a'],
#        [5, -2.0, 'b']], dtype=object)
```

### Selecting Specific Columns with `loc`

Use `loc` to select only the numeric columns you need before conversion:

```python
data.loc[:, ["x0", "x1"]].to_numpy()
# array([[ 1.  ,  0.01],
#        [ 2.  , -0.01],
#        [ 3.  ,  0.25],
#        [ 4.  , -4.1 ],
#        [ 5.  ,  0.  ]])
```

### Creating Dummy Variables with `pd.get_dummies()`

Categorical columns must be converted to numerical representations. `pd.get_dummies()` creates one-hot encoded (dummy) variables:

```python
dummies = pd.get_dummies(df2[["category"]])
dummies
#    category_a  category_b
# 0           1           0
# 1           0           1
# 2           1           0
# 3           1           0
# 4           0           1
```

### Joining Dummies Back to the Original DataFrame

The standard pattern is to drop the original categorical column and join the dummies:

```python
data_with_dummies = df2.drop("category", axis=1).join(dummies)
data_with_dummies
#    x0    y  category_a  category_b
# 0   1 -1.5           1           0
# 1   2  0.0           0           1
# 2   3  3.6           1           0
# 3   4  1.3           1           0
# 4   5 -2.0           0           1

data_with_dummies.to_numpy()
# array([[ 1. , -1.5,  1. ,  0. ],
#        [ 2. ,  0. ,  0. ,  1. ],
#        [ 3. ,  3.6,  1. ,  0. ],
#        [ 4. ,  1.3,  1. ,  0. ],
#        [ 5. , -2. ,  0. ,  1. ]])
```

> 💡 **Feature engineering** — the process of building data representations suitable for machine learning algorithms — is often more important than the choice of model itself. The techniques in this section form the foundation of that process.

---

## 12.2 Creating Model Descriptions with Patsy

[Patsy](https://patsy.readthedocs.io/) is a Python library that provides a convenient way to describe statistical models using **R-style formula syntax**. It is installed automatically with statsmodels.

### Basic Formula Syntax

```python
import patsy

data = pd.DataFrame({
    "x0": [1, 2, 3, 4, 5],
    "x1": [0.01, -0.01, 0.25, -4.1, 0.],
    "y": [-1.5, 0., 3.6, 1.3, -2.]
})

y, X = patsy.dmatrices("y ~ x0 + x1", data)
```

> ⚠️ The `+` in formula syntax means **"include these terms in the design matrix"**, not arithmetic addition.

```python
y
# DesignMatrix with shape (5, 1)
#      y
#   -1.5
#    0.0
#    3.6
#    1.3
#   -2.0
#   Terms:
#     'y' (column 0)

X
# DesignMatrix with shape (5, 3)
#   Intercept  x0     x1
#           1  1.00  0.01
#           1  2.00 -0.01
#           1  3.00  0.25
#           1  4.00 -4.10
#           1  5.00  0.00
#   Terms:
#     'Intercept' (column 0)
#     'x0' (column 1)
#     'x1' (column 2)
```

### Intercept Handling

An **intercept column** (all 1s) is added by default. Suppress it with `+ 0`:

```python
patsy.dmatrices("y ~ x0 + x1 + 0", data)[1]
# DesignMatrix with shape (5, 2)
#   x0     x1
#   1.00  0.01
#   2.00 -0.01
#   3.00  0.25
#   4.00 -4.10
#   5.00  0.00
```

### DesignMatrix Objects and `design_info`

DesignMatrix objects are NumPy arrays with a `design_info` attribute containing metadata:

```python
np.asarray(y)
# array([[-1.5],
#        [ 0. ],
#        [ 3.6],
#        [ 1.3],
#        [-2. ]])

np.asarray(X)
# array([[ 1.  ,  1.  ,  0.01],
#        [ 1.  ,  2.  , -0.01],
#        [ 1.  ,  3.  ,  0.25],
#        [ 1.  ,  4.  , -4.1 ],
#        [ 1.  ,  5.  ,  0.  ]])

X.design_info.column_names
# ['Intercept', 'x0', 'x1']
```

You can pass DesignMatrix objects directly to NumPy linear algebra functions:

```python
# OLS regression via np.linalg.lstsq
coef, _, _, _ = np.linalg.lstsq(X, y, rcond=None)
coef
# array([[ 0.3129],
#        [-0.0791],
#        [-0.2655]])
```

### Data Transformations in Formulas

Patsy supports mixing Python code directly into formulas:

```python
y, X = patsy.dmatrices("y ~ x0 + np.log(np.abs(x1) + 1)", data)
X
# DesignMatrix with shape (5, 3)
#   Intercept  x0  np.log(np.abs(x1) + 1)
#           1  1.00                0.00995
#           1  2.00                0.00995
#           1  3.00                0.22314
#           1  4.00                1.62924
#           1  5.00                0.00000
```

**Built-in transformations** include `standardize()` and `center()`:

```python
y, X = patsy.dmatrices("y ~ standardize(x0) + center(x1)", data)
X
# DesignMatrix with shape (5, 3)
#   Intercept  standardize(x0)  center(x1)
#           1        -1.41421       -0.002
#           1        -0.70711       -0.022
#           1         0.00000        0.238
#           1         0.70711       -4.112
#           1         1.41421       -0.012
```

**Arithmetic in formulas** — use the `I()` function to perform arithmetic operations that would otherwise be interpreted as formula syntax:

```python
y, X = patsy.dmatrices("y ~ I(x0 + x1)", data)
X
# DesignMatrix with shape (5, 2)
#   Intercept  I(x0 + x1)
#           1        1.01
#           1        1.99
#           1        3.25
#           1       -0.10
#           1        5.00
```

### Stateful Transformations and Out-of-Sample Prediction

Stateful transformations like `standardize()` save parameters (mean, std) from the training data. To apply the **same transformation** to new data, use `patsy.build_design_matrices()`:

```python
new_data = pd.DataFrame({
    "x0": [6, 7, 8, 9],
    "x1": [3.1, -0.5, 0., 2.3],
    "y": [1, 2, 3, 4]
})

# Apply the same transformation as the training data
new_X = patsy.build_design_matrices([X.design_info], new_data)
new_X
# [DesignMatrix with shape (4, 3)
#   Intercept  standardize(x0)  center(x1)
#           1         2.12132        3.088
#           1         2.82843       -0.512
#           1         3.53553       -0.012
#           1         4.24264        2.288
#   Terms:
#     'Intercept' (column 0)
#     'standardize(x0)' (column 1)
#     'center(x1)' (column 2)]
```

> ⚠️ **Critical caveat:** For out-of-sample predictions, you **must** use the same transformation parameters (mean, std) from the training data. Using `build_design_matrices()` with the saved `design_info` ensures this consistency.

### Categorical Data in Patsy

Nonnumeric terms in formulas are **automatically converted to dummy variables**:

```python
data = pd.DataFrame({
    "key1": ["a", "a", "b", "b", "a", "a", "b", "b"],
    "key2": [0, 1, 0, 1, 0, 1, 0, 1],
    "v1": [1., 2., 3., 4., 5., 6., 7., 8.]
})

y, X = patsy.dmatrices("v1 ~ key1", data)
X
# DesignMatrix with shape (8, 2)
#   Intercept  key1[T.b]
#           1          0
#           1          0
#           1          1
#           1          1
#           1          0
#           1          0
#           1          1
#           1          1
#   Terms:
#     'Intercept' (column 0)
#     'key1' (column 1)
```

**With intercept:** One level is omitted to avoid perfect collinearity. The notation `key1[T.b]` means "the treatment contrast for level b relative to the reference level a."

**Without intercept:** All levels are included:

```python
y, X = patsy.dmatrices("v1 ~ key1 + 0", data)
X
# DesignMatrix with shape (8, 2)
#   key1[a]  key1[b]
#         1        0
#         1        0
#         0        1
#         0        1
#         1        0
#         1        0
#         0        1
#         0        1
```

**Treating numeric columns as categorical** — use the `C()` function:

```python
y, X = patsy.dmatrices("v1 ~ C(key2)", data)
X
# DesignMatrix with shape (8, 2)
#   Intercept  C(key2)[T.1]
#           1             0
#           1             1
#           1             0
#           1             1
#           1             0
#           1             1
#           1             0
#           1             1
```

### Interaction Terms

Interaction terms use the `:` operator for ANOVA-style models:

```python
y, X = patsy.dmatrices("v1 ~ key1:key2", data)
X
# DesignMatrix with shape (8, 3)
#   Intercept  key1[a]:key2  key1[b]:key2
#           1             0             0
#           1             1             0
#           1             0             0
#           1             0             1
#           1             0             0
#           1             1             0
#           1             0             0
#           1             0             1
```

For full factorial models (main effects + interactions), use `*`:

```python
y, X = patsy.dmatrices("v1 ~ key1 * key2", data)
# Equivalent to: v1 ~ key1 + key2 + key1:key2
```

---

## 12.3 Introduction to statsmodels

[statsmodels](https://www.statsmodels.org/) provides classes and functions for estimating many different statistical models, conducting statistical tests, and exploring statistical data. It focuses on **frequentist statistical methods** (Bayesian methods and machine learning are found in other libraries).

**Model types supported:**
- Linear models (OLS, GLS, WLS)
- Generalized linear models (GLM)
- Robust linear models
- Mixed effects models
- ANOVA
- Time series analysis (AR, ARIMA, VAR)
- Generalized method of moments (GMM)

statsmodels provides **two API modules:**
- `statsmodels.api as sm` — array-based API
- `statsmodels.formula.api as smf` — formula-based API (uses Patsy internally)

### Array-Based API (`statsmodels.api`)

```python
import statsmodels.api as sm

# Add intercept column manually
X_with_const = sm.add_constant(X)
```

### Formula-Based API (`statsmodels.formula.api`)

```python
import statsmodels.formula.api as smf

# Formula API automatically handles intercept and column names
model = smf.ols("y ~ x0 + x1 + x2", data=data)
```

### Estimating Linear Models

**Array-based approach:**

```python
import statsmodels.api as sm

# Simulate data
np.random.seed(12345)
x = np.random.standard_normal((100, 3))
y = x @ [0.5, -1.0, 0.25] + 0.5 * np.random.standard_normal(100)

# Add intercept
X = sm.add_constant(x)

# Fit OLS model
results = sm.OLS(y, X).fit()

# Access coefficients
results.params
# array([ 0.4967, -0.9833,  0.0859,  0.2348])
#          ^        ^        ^        ^
#      intercept   x0       x1       x2
```

**Formula-based approach:**

```python
import statsmodels.formula.api as smf

data = pd.DataFrame(x, columns=["col0", "col1", "col2"])
data["y"] = y

# Formula API — returns Series with meaningful column names
results = smf.ols("y ~ col0 + col1 + col2", data=data).fit()
results.params
# Intercept    0.496714
# col0        -0.983318
# col1         0.085867
# col2         0.234819
# dtype: float64
```

> 💡 **Preference:** The formula-based API is generally preferred for its readability and automatic handling of column names, intercepts, and categorical variables.

### Model Summary and Diagnostics

```python
results.summary()
#                             OLS Regression Results                            
# ==============================================================================
# Dep. Variable:                      y   R-squared:                       0.676
# Model:                            OLS   Adj. R-squared:                  0.666
# Method:                 Least Squares   F-statistic:                     67.24
# Date:                ...              Prob (F-statistic):           1.52e-23
# Time:                        ...      Log-Likelihood:                -85.048
# No. Observations:                 100   AIC:                             178.1
# Df Residuals:                      96   BIC:                             188.5
# Df Model:                           3                                         
# Covariance Type:            nonrobust                                         
# ==============================================================================
#                  coef    std err          t      P>|t|      [0.025      0.975]
# ------------------------------------------------------------------------------
# Intercept      0.4967      0.103      4.835      0.000       0.293       0.700
# col0          -0.9833      0.103     -9.574      0.000      -1.187      -0.779
# col1           0.0859      0.098      0.874      0.384      -0.109       0.281
# col2           0.2348      0.107      2.195      0.031       0.022       0.447
# ==============================================================================
```

Key diagnostics available on the results object:

```python
results.params      # Coefficient estimates
results.tvalues     # t-statistics
results.pvalues     # p-values
results.rsquared    # R-squared
results.fittedvalues  # Predicted values
results.resid       # Residuals
```

### Out-of-Sample Predictions

```python
# Generate new data
new_data = pd.DataFrame(np.random.standard_normal((10, 3)),
                        columns=["col0", "col1", "col2"])

# Predict using the fitted model
predictions = results.predict(new_data)
predictions
# 0    0.111855
# 1   -0.188040
# 2   -0.393520
# ...
# dtype: float64
```

### Estimating Time Series Processes

statsmodels includes tools for time series analysis, including autoregressive models:

```python
from statsmodels.tsa.ar_model import AutoReg

# Simulate an AR(2) process
np.random.seed(12345)
nobs = 200
xs = np.zeros(nobs)
xs[:2] = np.random.standard_normal(2)

# True parameters: x[t] = 0.5 * x[t-1] - 0.8 * x[t-2] + noise
for i in range(2, nobs):
    xs[i] = 0.5 * xs[i-1] - 0.8 * xs[i-2] + np.random.standard_normal()

# Fit AR model with MAXLAGS=2
results = AutoReg(xs, lags=2).fit()
results.params
# array([ 0.0297,  0.5097, -0.8111])
#          ^        ^        ^
#      intercept   lag1     lag2

# Fit with more lags than necessary — irrelevant lags show near-zero coefficients
results = AutoReg(xs, lags=12).fit()
results.params.round(2)
# array([ 0.03,  0.51, -0.81, -0.02,  0.06, -0.01,  0.03,
#         0.03, -0.1 ,  0.05,  0.04, -0.07,  0.04])
# Only lag1 (~0.5) and lag2 (~-0.8) have meaningful coefficients
```

> 💡 When fitting with more lags than the true order, the irrelevant lag coefficients will be near zero, helping you identify the correct model order.

---

## 12.4 Introduction to scikit-learn

[scikit-learn](https://scikit-learn.org/) is a general-purpose machine learning toolkit covering:
- Supervised learning (classification, regression)
- Unsupervised learning (clustering, dimensionality reduction)
- Model selection and evaluation
- Preprocessing and feature engineering

### Titanic Dataset: Full ML Workflow

This example demonstrates a complete machine learning pipeline from raw data to predictions:

```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import cross_val_score

# 1. Load data
train = pd.read_csv("datasets/titanic/train.csv")
test = pd.read_csv("datasets/titanic/test.csv")

# 2. Check for missing data
train.isna().sum()
# PassengerId      0
# Survived         0
# Pclass           0
# Name             0
# Sex              0
# Age            177     ← Needs imputation
# SibSp            0
# Parch            0
# Ticket           0
# Fare             0
# Cabin          687     ← Too many missing, drop
# Embarked         2     ← Minor, can drop or impute
# dtype: int64

# 3. Impute missing values (median imputation for Age)
impute_value = train["Age"].median()
train["Age"] = train["Age"].fillna(impute_value)
test["Age"] = test["Age"].fillna(impute_value)

# 4. Feature engineering: create binary IsFemale column
train["IsFemale"] = (train["Sex"] == "female").astype(int)
test["IsFemale"] = (test["Sex"] == "female").astype(int)

# 5. Select predictors and convert to NumPy arrays
predictors = ["Pclass", "IsFemale", "Age"]
X_train = train[predictors].to_numpy()
X_test = test[predictors].to_numpy()
y_train = train["Survived"].to_numpy()

# 6. Train logistic regression model
model = LogisticRegression()
model.fit(X_train, y_train)

# 7. Make predictions
y_predict = model.predict(X_test)

# 8. Evaluate accuracy
(y_train == model.predict(X_train)).mean()
# 0.789  (training accuracy)
```

> ⚠️ **Important caveats:**
> - **Missing data:** Most scikit-learn models do not handle missing values. You must impute or remove them before fitting.
> - **Training accuracy is not a reliable metric:** The model above shows ~79% training accuracy, but this doesn't tell us how it generalizes. Use cross-validation instead.
> - **Feature scaling:** Logistic regression benefits from standardized features. Use `StandardScaler` from `sklearn.preprocessing` for better results.

### Cross-Validation

Cross-validation provides a more reliable estimate of model performance by training and evaluating on different data splits.

**Built-in cross-validation with `LogisticRegressionCV`:**

```python
# LogisticRegressionCV performs built-in CV with grid search on C (regularization)
model_cv = LogisticRegressionCV(Cs=10)
model_cv.fit(X_train, y_train)

# Best C value selected through cross-validation
model_cv.C_
# array([0.2154])
```

**Manual cross-validation with `cross_val_score`:**

```python
from sklearn.model_selection import cross_val_score

model = LogisticRegression(C=10)
scores = cross_val_score(model, X_train, y_train, cv=4)
scores
# array([0.7965, 0.7477, 0.7703, 0.7883])

# Mean cross-validation accuracy
scores.mean()
# 0.7757
```

### Built-in CV vs `cross_val_score`

| Approach | Description | Pros | Cons |
|----------|-------------|------|------|
| `LogisticRegressionCV(Cs=10)` | Built-in CV with hyperparameter grid search | Faster, optimized for logistic regression, selects best C automatically | Only works for logistic regression |
| `cross_val_score(model, X, y, cv=4)` | Manual k-fold cross-validation | Works with any scikit-learn model, flexible | Slower, doesn't automatically tune hyperparameters |

> 💡 **When to use which:** Use `LogisticRegressionCV` when you want to simultaneously train and tune the regularization parameter for logistic regression. Use `cross_val_score` when you need to evaluate any model type or want more control over the cross-validation process.

---

## 12.5 Conclusion

This chapter covered the essential tools for interfacing pandas data with Python's modeling ecosystem. The workflow flows naturally from data preparation (converting DataFrames to arrays, creating dummy variables) through model specification (Patsy formulas) to estimation (statsmodels for statistical inference, scikit-learn for machine learning).

The key takeaway is that **feature engineering** — transforming raw data into representations suitable for modeling — is often the most impactful part of the modeling process. The choice between statsmodels and scikit-learn depends on your goals: statistical inference and detailed diagnostics favor statsmodels, while predictive modeling and production ML pipelines favor scikit-learn.

---

## Quick Reference

| Task | Code Pattern |
|------|-------------|
| Convert DataFrame to NumPy array | `df.to_numpy()` |
| Select specific columns for modeling | `df.loc[:, ["col1", "col2"]].to_numpy()` |
| Create dummy variables | `pd.get_dummies(df["category"])` |
| Join dummies to original data | `df.drop("category", axis=1).join(dummies)` |
| Create Patsy design matrices | `y, X = patsy.dmatrices("y ~ x0 + x1", data)` |
| Suppress intercept in Patsy | `"y ~ x0 + x1 + 0"` |
| Apply training transforms to new data | `patsy.build_design_matrices([X.design_info], new_data)` |
| Treat numeric column as categorical | `"y ~ C(numeric_col)"` |
| Interaction term in Patsy | `"y ~ key1 * key2"` (full factorial) |
| Add intercept to array | `sm.add_constant(X)` |
| Fit OLS (array API) | `sm.OLS(y, X).fit()` |
| Fit OLS (formula API) | `smf.ols("y ~ x0 + x1", data=data).fit()` |
| Get model summary | `results.summary()` |
| Out-of-sample predictions | `results.predict(new_data)` |
| Fit AR model | `AutoReg(series, lags=2).fit()` |
| Train scikit-learn model | `LogisticRegression().fit(X_train, y_train)` |
| Make predictions | `model.predict(X_test)` |
| Cross-validation (built-in) | `LogisticRegressionCV(Cs=10).fit(X, y)` |
| Cross-validation (manual) | `cross_val_score(model, X, y, cv=4)` |

---

## Key Differences: statsmodels vs scikit-learn

| Aspect | statsmodels | scikit-learn |
|--------|------------|--------------|
| **Primary focus** | Statistical inference, hypothesis testing | Prediction, model performance |
| **API style** | Array-based (`sm`) and formula-based (`smf`) | Estimator API (`fit`, `predict`, `transform`) |
| **Model output** | Detailed statistical summaries (p-values, confidence intervals, R²) | Predictions and scores |
| **Missing data** | Generally handles NaN values | Requires preprocessing (imputation) |
| **Formula support** | Built-in via Patsy | Not built-in (use `patsy` or `sklearn-pandas`) |
| **Cross-validation** | Limited | Comprehensive (`cross_val_score`, `GridSearchCV`, etc.) |
| **Model selection** | Manual comparison via diagnostics | Automated pipelines, grid search |
| **Best for** | Understanding relationships, testing hypotheses | Building predictive models, production ML |
| **Time series** | Rich support (ARIMA, VAR, state space) | Limited |
| **Regularization** | Limited (mainly in GLM) | Extensive (Ridge, Lasso, ElasticNet, etc.) |

---

## Further Reading

The following books are recommended for deeper study of machine learning and data science with Python:

- **Andreas Müller & Sarah Guido**, *Introduction to Machine Learning with Python* (O'Reilly) — Practical guide to scikit-learn with real-world examples
- **Aurélien Géron**, *Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow* (O'Reilly) — Comprehensive ML textbook covering theory and practice
- **Joel Grus**, *Data Science from Scratch* (O'Reilly) — Builds ML algorithms from first principles in Python
- **Sebastian Raschka**, *Python Machine Learning* (Packt) — In-depth coverage of ML algorithms and deep learning

> 💡 Always check the official documentation for the latest API changes, as these libraries evolve rapidly:
> - [statsmodels documentation](https://www.statsmodels.org/stable/)
> - [scikit-learn documentation](https://scikit-learn.org/stable/)
> - [Patsy documentation](https://patsy.readthedocs.io/)
