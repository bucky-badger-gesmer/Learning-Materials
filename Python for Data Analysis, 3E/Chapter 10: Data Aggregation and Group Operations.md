# Chapter 10: Data Aggregation and Group Operations

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/data-aggregation](https://wesmckinney.com/book/data-aggregation)

---

## Table of Contents

- [10.1 How to Think About Group Operations](#101-how-to-think-about-group-operations)
  - [Basic GroupBy Usage](#basic-groupby-usage)
  - [Group Size and Count](#group-size-and-count)
  - [Iterating Over Groups](#iterating-over-groups)
  - [Grouping on Columns (axis="columns")](#grouping-on-columns-axiscolumns)
  - [Selecting Columns for Aggregation](#selecting-columns-for-aggregation)
  - [Grouping with Dictionaries and Series](#grouping-with-dictionaries-and-series)
  - [Grouping with Functions](#grouping-with-functions)
  - [Grouping by Index Levels](#grouping-by-index-levels)
- [10.2 Data Aggregation](#102-data-aggregation)
  - [Custom Aggregation Functions](#custom-aggregation-functions)
  - [Column-Wise and Multiple Function Application](#column-wise-and-multiple-function-application)
  - [Returning Aggregated Data Without Row Indexes](#returning-aggregated-data-without-row-indexes)
- [10.3 Apply: General split-apply-combine](#103-apply-general-split-apply-combine)
  - [Basic Usage](#basic-usage)
  - [Suppressing the Group Keys](#suppressing-the-group-keys)
  - [Quantile and Bucket Analysis](#quantile-and-bucket-analysis)
  - [Example: Filling Missing Values with Group-Specific Values](#example-filling-missing-values-with-group-specific-values)
  - [Example: Random Sampling and Permutation](#example-random-sampling-and-permutation)
  - [Example: Group Weighted Average and Correlation](#example-group-weighted-average-and-correlation)
  - [Example: Yearly Correlations with SPX](#example-yearly-correlations-with-spx)
  - [Example: Group-Wise Linear Regression](#example-group-wise-linear-regression)
- [10.4 Group Transforms and "Unwrapped" GroupBys](#104-group-transforms-and-unwrapped-groupbys)
  - [Basic Transform](#basic-transform)
  - [Normalization with Transform vs Apply](#normalization-with-transform-vs-apply)
  - ["Unwrapped" Group Operations](#unwrapped-group-operations)
- [10.5 Pivot Tables and Cross-Tabulation](#105-pivot-tables-and-cross-tabulation)
  - [Basic Pivot Table](#basic-pivot-table)
  - [Multi-Dimensional Pivot Tables](#multi-dimensional-pivot-tables)
  - [Custom Aggregation Functions](#custom-aggregation-functions-1)
  - [Cross-Tabulations: `crosstab`](#cross-tabulations-crosstab)
- [10.6 Conclusion](#106-conclusion)

---

## Overview
Categorizing a dataset and applying a function to each group — whether an aggregation or transformation — is a critical component of data analysis workflows. pandas provides a versatile `groupby` interface that enables you to slice, dice, and summarize datasets in a natural way, going beyond the limitations of SQL-like query languages.

**Key capabilities:**
- Split a pandas object into pieces using one or more keys (functions, arrays, or DataFrame column names)
- Calculate group summary statistics (count, mean, standard deviation, or user-defined functions)
- Apply within-group transformations (normalization, linear regression, rank, subset selection)
- Compute pivot tables and cross-tabulations
- Perform quantile analysis and other statistical group analyses

```python
import numpy as np
import pandas as pd
```

---

## 10.1 How to Think About Group Operations

Hadley Wickham coined the term **split-apply-combine** for describing group operations:

1. **Split**: Data is split into groups based on one or more keys
2. **Apply**: A function is applied to each group, producing a new value
3. **Combine**: Results are combined into a result object

### Grouping Keys Can Be:
- A list or array of values (same length as the axis being grouped)
- A column name in a DataFrame
- A dictionary or Series giving correspondence between axis values and group names
- A function invoked on the axis index or individual labels

### Basic GroupBy Usage

```python
df = pd.DataFrame({"key1": ["a", "a", None, "b", "b", "a", None],
                   "key2": pd.Series([1, 2, 1, 2, 1, None, 1], dtype="Int64"),
                   "data1": np.random.standard_normal(7),
                   "data2": np.random.standard_normal(7)})

# Group a single column by a key column
grouped = df["data1"].groupby(df["key1"])
grouped.mean()
# key1
# a    0.555881
# b    0.705025
# Name: data1, dtype: float64

# Group by multiple keys → hierarchical index
means = df["data1"].groupby([df["key1"], df["key2"]]).mean()
means.unstack()

# Group by external arrays
states = np.array(["OH", "CA", "CA", "OH", "OH", "CA", "OH"])
years = [2005, 2005, 2006, 2005, 2006, 2005, 2006]
df["data1"].groupby([states, years]).mean()

# Group by column names directly
df.groupby("key1").mean()
df.groupby(["key1", "key2"]).mean()
```

### Group Size and Count

```python
df.groupby(["key1", "key2"]).size()  # Group sizes

# Missing values in group keys are excluded by default
df.groupby("key1", dropna=False).size()  # Include NaN groups
```

### Iterating Over Groups

```python
# Single key
for name, group in df.groupby("key1"):
    print(name)
    print(group)

# Multiple keys → tuple of key values
for (k1, k2), group in df.groupby(["key1", "key2"]):
    print((k1, k2))
    print(group)

# Build a dictionary of groups
pieces = {name: group for name, group in df.groupby("key1")}
```

### Grouping on Columns (axis="columns")

```python
grouped = df.groupby({"key1": "key", "key2": "key",
                      "data1": "data", "data2": "data"}, axis="columns")
for group_key, group_values in grouped:
    print(group_key)
    print(group_values)
```

### Selecting Columns for Aggregation

```python
# These are equivalent:
df.groupby("key1")["data1"]
df["data1"].groupby(df["key1"])

# Aggregate only specific columns (more efficient for large datasets)
df.groupby(["key1", "key2"])[["data2"]].mean()
```

### Grouping with Dictionaries and Series

```python
people = pd.DataFrame(np.random.standard_normal((5, 5)),
                      columns=["a", "b", "c", "d", "e"],
                      index=["Joe", "Steve", "Wanda", "Jill", "Trey"])

mapping = {"a": "red", "b": "red", "c": "blue",
           "d": "blue", "e": "red", "f": "orange"}

by_column = people.groupby(mapping, axis="columns")
by_column.sum()

# Same works with Series
map_series = pd.Series(mapping)
people.groupby(map_series, axis="columns").count()
```

### Grouping with Functions

Any function passed as a group key is called once per index value; return values become group names.

```python
people.groupby(len).sum()  # Group by name length

# Mix functions with arrays/dicts/Series
key_list = ["one", "one", "one", "two", "two"]
people.groupby([len, key_list]).min()
```

### Grouping by Index Levels

```python
columns = pd.MultiIndex.from_arrays([["US", "US", "US", "JP", "JP"],
                                     [1, 3, 5, 1, 3]],
                                    names=["cty", "tenor"])
hier_df = pd.DataFrame(np.random.standard_normal((4, 5)), columns=columns)

hier_df.groupby(level="cty", axis="columns").count()
```

---

## 10.2 Data Aggregation

Aggregations produce scalar values from arrays. Many common aggregations have optimized implementations.

### Table: Optimized `groupby` Methods

| Function | Description |
|----------|-------------|
| `any`, `all` | Return `True` if any/all non-NA values are truthy |
| `count` | Number of non-NA values |
| `cummin`, `cummax` | Cumulative min/max |
| `cumsum`, `cumprod` | Cumulative sum/product |
| `first`, `last` | First/last non-NA values |
| `mean`, `median` | Mean/median |
| `min`, `max` | Minimum/maximum |
| `nth` | Value at position `n` in sorted order |
| `ohlc` | Open-high-low-close for time series |
| `prod` | Product |
| `quantile` | Sample quantile |
| `rank` | Ordinal ranks |
| `size` | Group sizes |
| `sum` | Sum |
| `std`, `var` | Standard deviation/variance |

### Custom Aggregation Functions

```python
def peak_to_peak(arr):
    return arr.max() - arr.min()

grouped.agg(peak_to_peak)

# Non-optimized methods also work (e.g., nsmallest)
grouped["data1"].nsmallest(2)
```

> ⚠️ Custom aggregation functions are slower than optimized methods due to overhead in constructing intermediate group data chunks.

### Column-Wise and Multiple Function Application

```python
tips = pd.read_csv("examples/tips.csv")
tips["tip_pct"] = tips["tip"] / tips["total_bill"]
grouped = tips.groupby(["day", "smoker"])

# Single function (string alias)
grouped_pct = grouped["tip_pct"]
grouped_pct.agg("mean")

# Multiple functions → DataFrame with function names as columns
grouped_pct.agg(["mean", "std", peak_to_peak])

# Custom column names via (name, function) tuples
grouped_pct.agg([("average", "mean"), ("stdev", np.std)])

# Multiple functions on multiple columns → hierarchical columns
functions = ["count", "mean", "max"]
result = grouped[["tip_pct", "total_bill"]].agg(functions)

# Different functions per column (dict mapping)
grouped.agg({"tip": np.max, "size": "sum"})
grouped.agg({"tip_pct": ["min", "max", "mean", "std"],
             "size": "sum"})
```

### Returning Aggregated Data Without Row Indexes

```python
# Disable group key as index
grouped = tips.groupby(["day", "smoker"], as_index=False)
grouped.mean(numeric_only=True)

# Alternatively, call reset_index() on the result
```

---

## 10.3 Apply: General split-apply-combine

`apply` is the most general-purpose GroupBy method. It splits the object, invokes the passed function on each piece, and concatenates the results.

### Basic Usage

```python
def top(df, n=5, column="tip_pct"):
    return df.sort_values(column, ascending=False)[:n]

# Apply to each group
tips.groupby("smoker").apply(top)

# Pass additional arguments
tips.groupby(["smoker", "day"]).apply(top, n=1, column="total_bill")
```

The function must return a pandas object or a scalar value. Results are glued together with `pandas.concat`, labeling pieces with group names.

### Suppressing the Group Keys

```python
# Disable hierarchical index from group keys
tips.groupby("smoker", group_keys=False).apply(top)
```

### Quantile and Bucket Analysis

Combine `pd.cut` (equal-length buckets) or `pd.qcut` (equal-size buckets by quantiles) with `groupby`:

```python
frame = pd.DataFrame({"data1": np.random.standard_normal(1000),
                      "data2": np.random.standard_normal(1000)})

# Equal-length buckets
quartiles = pd.cut(frame["data1"], 4)
grouped = frame.groupby(quartiles)
grouped.agg(["min", "max", "count", "mean"])

# Equal-size buckets (sample quartiles)
quartiles_samp = pd.qcut(frame["data1"], 4, labels=False)
grouped = frame.groupby(quartiles_samp)
grouped.apply(get_stats)
```

### Example: Filling Missing Values with Group-Specific Values

```python
states = ["Ohio", "New York", "Vermont", "Florida",
          "Oregon", "Nevada", "California", "Idaho"]
group_key = ["East", "East", "East", "East",
             "West", "West", "West", "West"]
data = pd.Series(np.random.standard_normal(8), index=states)
data[["Vermont", "Nevada", "Idaho"]] = np.nan

# Fill with group mean
def fill_mean(group):
    return group.fillna(group.mean())

data.groupby(group_key).apply(fill_mean)

# Fill with predefined values using group.name
fill_values = {"East": 0.5, "West": -1}
def fill_func(group):
    return group.fillna(fill_values[group.name])

data.groupby(group_key).apply(fill_func)
```

### Example: Random Sampling and Permutation

```python
# Build a deck of cards
suits = ["H", "S", "C", "D"]
card_val = (list(range(1, 11)) + [10] * 3) * 4
base_names = ["A"] + list(range(2, 11)) + ["J", "K", "Q"]
cards = []
for suit in suits:
    cards.extend(str(num) + suit for num in base_names)

deck = pd.Series(card_val, index=cards)

def draw(deck, n=5):
    return deck.sample(n)

# Draw 2 random cards from each suit
def get_suit(card):
    return card[-1]

deck.groupby(get_suit).apply(draw, n=2)
deck.groupby(get_suit, group_keys=False).apply(draw, n=2)
```

### Example: Group Weighted Average and Correlation

```python
df = pd.DataFrame({"category": ["a", "a", "a", "a", "b", "b", "b", "b"],
                   "data": np.random.standard_normal(8),
                   "weights": np.random.uniform(size=8)})

grouped = df.groupby("category")
def get_wavg(group):
    return np.average(group["data"], weights=group["weights"])

grouped.apply(get_wavg)
```

### Example: Yearly Correlations with SPX

```python
close_px = pd.read_csv("examples/stock_px.csv", parse_dates=True, index_col=0)
rets = close_px.pct_change().dropna()

def spx_corr(group):
    return group.corrwith(group["SPX"])

def get_year(x):
    return x.year

by_year = rets.groupby(get_year)
by_year.apply(spx_corr)

# Intercolumn correlation
def corr_aapl_msft(group):
    return group["AAPL"].corr(group["MSFT"])

by_year.apply(corr_aapl_msft)
```

### Example: Group-Wise Linear Regression

```python
import statsmodels.api as sm

def regress(data, yvar=None, xvars=None):
    Y = data[yvar]
    X = data[xvars]
    X["intercept"] = 1.
    result = sm.OLS(Y, X).fit()
    return result.params

by_year.apply(regress, yvar="AAPL", xvars=["SPX"])
```

---

## 10.4 Group Transforms and "Unwrapped" GroupBys

`transform` is similar to `apply` but imposes constraints:
- Can produce a scalar value broadcast to the group shape
- Can produce an object of the same shape as the input group
- Must not mutate its input

### Basic Transform

```python
df = pd.DataFrame({'key': ['a', 'b', 'c'] * 4,
                   'value': np.arange(12.)})

g = df.groupby('key')['value']

# Replace values with group means (same shape as input)
g.transform('mean')

# Multiply each group by 2
def times_two(group):
    return group * 2

g.transform(times_two)

# Compute ranks within each group
def get_ranks(group):
    return group.rank(ascending=False)

g.transform(get_ranks)
```

### Normalization with Transform vs Apply

```python
def normalize(x):
    return (x - x.mean()) / x.std()

# Both produce equivalent results, but transform keeps original index
g.transform(normalize)
g.apply(normalize)  # Adds group keys to index
```

### "Unwrapped" Group Operations

Using built-in aggregation functions with `transform` is faster than `apply`:

```python
# Unwrapped: vectorized arithmetic between transform outputs
normalized = (df['value'] - g.transform('mean')) / g.transform('std')
```

> 💡 While unwrapped operations may involve multiple group aggregations, the benefit of vectorized operations often outweighs this cost.

---

## 10.5 Pivot Tables and Cross-Tabulation

A **pivot table** aggregates data by one or more keys, arranging results with some keys along rows and others along columns. It's powered by `groupby` combined with hierarchical indexing reshape operations.

### Basic Pivot Table

```python
tips.pivot_table(index=["day", "smoker"],
                 values=["size", "tip", "tip_pct", "total_bill"])

# Same as: tips.groupby(["day", "smoker"]).mean()
```

### Multi-Dimensional Pivot Tables

```python
# Rows: time, day | Columns: smoker
tips.pivot_table(index=["time", "day"], columns="smoker",
                 values=["tip_pct", "size"])

# Add partial totals (margins)
tips.pivot_table(index=["time", "day"], columns="smoker",
                 values=["tip_pct", "size"], margins=True)
```

### Custom Aggregation Functions

```python
# Use count/len instead of mean
tips.pivot_table(index=["time", "smoker"], columns="day",
                 values="tip_pct", aggfunc=len, margins=True)

# Fill empty combinations
tips.pivot_table(index=["time", "size", "smoker"], columns="day",
                 values="tip_pct", fill_value=0)
```

### Table: `pivot_table` Options

| Argument | Description |
|----------|-------------|
| `values` | Column(s) to aggregate; default: all numeric columns |
| `index` | Column(s) to group on rows |
| `columns` | Column(s) to group on columns |
| `aggfunc` | Aggregation function(s); default: `"mean"` |
| `fill_value` | Replace missing values in result |
| `dropna` | Exclude columns with all NA entries |
| `margins` | Add row/column subtotals and grand total |
| `margins_name` | Name for margin labels; default: `"All"` |
| `observed` | With Categorical keys, show only observed categories |

### Cross-Tabulations: `crosstab`

A cross-tabulation is a special case of a pivot table that computes group frequencies.

```python
data = pd.read_table(StringIO(data), sep="\s+")

pd.crosstab(data["Nationality"], data["Handedness"], margins=True)
# Handedness   Left-handed  Right-handed  All
# Nationality                                
# Japan                  2             3    5
# USA                    1             4    5
# All                    3             7   10

# Multiple grouping keys
pd.crosstab([tips["time"], tips["day"]], tips["smoker"], margins=True)
```

---

## 10.6 Conclusion

Mastering pandas's data grouping tools enables efficient data cleaning, modeling, and statistical analysis. The `groupby` facility — combined with `apply`, `transform`, `agg`, and `pivot_table` — provides a powerful toolkit for split-apply-combine workflows that goes far beyond what traditional SQL queries can express.
