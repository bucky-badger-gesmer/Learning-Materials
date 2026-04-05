# Chapter 5: Getting Started with pandas

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/pandas-basics](https://wesmckinney.com/book/pandas-basics)

---

## Table of Contents

- [5.1 Introduction to pandas Data Structures](#51-introduction-to-pandas-data-structures)
  - [Series](#series)
  - [DataFrame](#dataframe)
  - [Index Objects](#index-objects)
- [5.2 Essential Functionality](#52-essential-functionality)
  - [Reindexing](#reindexing)
  - [Dropping Entries from an Axis](#dropping-entries-from-an-axis)
  - [Indexing, Selection, and Filtering](#indexing-selection-and-filtering)
  - [Arithmetic and Data Alignment](#arithmetic-and-data-alignment)
  - [Function Application and Mapping](#function-application-and-mapping)
  - [Sorting and Ranking](#sorting-and-ranking)
  - [Axis Indexes with Duplicate Labels](#axis-indexes-with-duplicate-labels)
- [5.3 Summarizing and Computing Descriptive Statistics](#53-summarizing-and-computing-descriptive-statistics)
- [5.4 Conclusion](#54-conclusion)

---

## Overview
pandas is a foundational library for data cleaning, manipulation, and analysis in Python. Unlike NumPy, which excels at homogeneous numerical arrays, pandas is designed for **tabular or heterogeneous data**. It adopts NumPy's array-based computing idioms but adds powerful labeling, alignment, and missing data handling features.

**Standard Import Convention:**
```python
import numpy as np
import pandas as pd
from pandas import Series, DataFrame
```

---

## 5.1 Introduction to pandas Data Structures

### Series
A **Series** is a one-dimensional array-like object containing a sequence of values and an associated array of data labels called its **index**.

#### Creation & Basic Usage
```python
# Default integer index (0 to N-1)
obj = pd.Series([4, 7, -5, 3])

# Custom index
obj2 = pd.Series([4, 7, -5, 3], index=["d", "b", "a", "c"])
```

#### Key Features
- **Label-based indexing:** `obj2["a"]`, `obj2[["c", "a", "d"]]`
- **NumPy operations preserve index:** Boolean filtering, scalar math, and ufuncs maintain the index-value link.
  ```python
  obj2[obj2 > 0]
  obj2 * 2
  np.exp(obj2)
  ```
- **Dictionary-like behavior:** Acts as a fixed-length, ordered dict.
  ```python
  "b" in obj2  # True
  ```
- **Creation from dict:** Keys become the index.
  ```python
  sdata = {"Ohio": 35000, "Texas": 71000, "Oregon": 16000, "Utah": 5000}
  obj3 = pd.Series(sdata)
  ```
- **Index overriding & Missing Data:** Passing a custom index introduces `NaN` for missing keys.
  ```python
  states = ["California", "Ohio", "Oregon", "Texas"]
  obj4 = pd.Series(sdata, index=states)  # California -> NaN, Utah dropped
  ```
- **Missing data detection:** `pd.isna(obj4)`, `pd.notna(obj4)`, or `obj4.isna()`
- **Automatic alignment:** Arithmetic operations align by index label.
  ```python
  obj3 + obj4  # Union of indexes; NaN where labels don't overlap
  ```
- **Naming:** `obj4.name = "population"`, `obj4.index.name = "state"`
- **Mutable index:** `obj.index = ["Bob", "Steve", "Jeff", "Ryan"]`

### DataFrame
A **DataFrame** represents a rectangular table of data with ordered, named columns. It can be thought of as a dictionary of Series sharing the same index.

#### Creation & Inspection
```python
data = {
    "state": ["Ohio", "Ohio", "Ohio", "Nevada", "Nevada", "Nevada"],
    "year": [2000, 2001, 2002, 2001, 2002, 2003],
    "pop": [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]
}
frame = pd.DataFrame(data)

frame.head()  # First 5 rows
frame.tail()  # Last 5 rows
```

#### Column & Row Access
- **Select columns:** `frame["state"]` or `frame.year` (dot notation only for valid identifiers)
- **Select rows:** `frame.loc[1]` (label-based) or `frame.iloc[2]` (position-based)
- **Column order:** Specify during creation: `pd.DataFrame(data, columns=["year", "state", "pop"])`
- **Missing columns:** `pd.DataFrame(data, columns=["year", "state", "pop", "debt"])` → `debt` filled with `NaN`

#### Modifying Data
```python
# Assign scalar
frame2["debt"] = 16.5

# Assign array (must match length)
frame2["debt"] = np.arange(6.)

# Assign Series (aligns by index, introduces NaN for mismatches)
val = pd.Series([-1.2, -1.5, -1.7], index=[2, 4, 5])
frame2["debt"] = val

# Computed columns
frame2["eastern"] = frame2["state"] == "Ohio"

# Delete columns
del frame2["eastern"]
```
> ⚠️ **Warning:** Columns returned from indexing are **views**, not copies. In-place modifications affect the original DataFrame. Use `.copy()` to avoid this.

#### Alternative Construction
- **Nested dicts:** Outer keys → columns, inner keys → row index.
  ```python
  populations = {"Ohio": {2000: 1.5, 2001: 1.7}, "Nevada": {2001: 2.4, 2002: 2.9}}
  frame3 = pd.DataFrame(populations)
  ```
- **Transpose:** `frame3.T` (swaps rows/columns; may lose dtype info)
- **Explicit index:** `pd.DataFrame(populations, index=[2001, 2002, 2003])`
- **To NumPy array:** `frame3.to_numpy()` (dtype becomes `object` if mixed types)

### Index Objects
`Index` objects hold axis labels and metadata. They are **immutable** and behave like fixed-size sets.

```python
index = obj.index
index[1:]  # Slicing works
# index[1] = "d"  # TypeError: immutable
```

#### Set-like Operations
```python
"Ohio" in frame3.columns   # True
2003 in frame3.index       # False
```

| Method/Property | Description |
|----------------|-------------|
| `append()` | Concatenate with additional Index objects |
| `difference()` | Compute set difference |
| `intersection()` | Compute set intersection |
| `union()` | Compute set union |
| `isin()` | Boolean array indicating membership |
| `delete()` / `drop()` | Remove elements |
| `insert()` | Insert element at position |
| `is_monotonic` / `is_unique` | Check ordering/uniqueness |
| `unique()` | Array of unique values |

---

## 5.2 Essential Functionality

### Reindexing
`reindex()` creates a new object with values rearranged to match a new index. Missing labels introduce `NaN`.

```python
obj = pd.Series([4.5, 7.2, -5.3, 3.6], index=["d", "b", "a", "c"])
obj2 = obj.reindex(["a", "b", "c", "d", "e"])  # e -> NaN
```

#### Interpolation & Filling
```python
obj3 = pd.Series(["blue", "purple", "yellow"], index=[0, 2, 4])
obj3.reindex(np.arange(6), method="ffill")  # Forward-fill
```

#### DataFrame Reindexing
```python
frame.reindex(index=["a", "b", "c", "d"])          # Reindex rows
frame.reindex(columns=["Texas", "Utah", "California"])  # Reindex columns
frame.reindex(states, axis="columns")               # Alternative syntax
```
> 💡 `reindex` arguments: `labels`, `index`, `columns`, `axis`, `method` (`ffill`/`bfill`), `fill_value`, `limit`, `tolerance`, `level`, `copy`.

### Dropping Entries from an Axis
`drop()` returns a new object with specified labels removed.

```python
# Series
obj.drop("c")
obj.drop(["d", "c"])

# DataFrame
data.drop(index=["Colorado", "Ohio"])      # Drop rows
data.drop(columns=["two"])                 # Drop columns
data.drop("two", axis=1)                   # Alternative
```

### Indexing, Selection, and Filtering

#### Series Indexing
```python
obj["b"]           # By label
obj[1]             # By position (⚠️ ambiguous if index contains integers)
obj.loc["b"]       # Explicit label-based (✅ preferred)
obj.iloc[1]        # Explicit position-based (✅ preferred)
obj[obj < 2]       # Boolean filtering
obj.loc["b":"c"]   # Label slicing (endpoint INCLUSIVE)
```

#### DataFrame Indexing
```python
data["two"]                    # Single column
data[["three", "one"]]         # Multiple columns
data[:2]                       # Row slice
data[data["three"] > 5]        # Boolean row filter
data[data < 5] = 0             # Boolean assignment
```

#### `loc` and `iloc` for DataFrame
| Syntax | Description |
|--------|-------------|
| `df.loc[rows]` | Select rows by label |
| `df.loc[:, cols]` | Select columns by label |
| `df.loc[rows, cols]` | Select both by label |
| `df.iloc[rows]` | Select rows by integer position |
| `df.iloc[:, cols]` | Select columns by integer position |
| `df.iloc[rows, cols]` | Select both by position |
| `df.at[row, col]` / `df.iat[row, col]` | Select single scalar value |

```python
data.loc["Colorado"]
data.loc["Colorado", ["two", "three"]]
data.iloc[2, [3, 0, 1]]
data.iloc[[1, 2], [3, 0, 1]]
```

#### ⚠️ Integer Indexing Pitfalls
When an index contains integers, `obj[0]` is **label-based**, not positional. This causes ambiguity. Always use `loc` (labels) or `iloc` (positions) to avoid surprises. Slicing with `obj[:2]` is always positional.

#### ⚠️ Chained Indexing Pitfalls
Chained assignments often modify a temporary copy, triggering `SettingWithCopyWarning` and leaving the original DataFrame unchanged.

```python
# ❌ BAD: Chained indexing
data.loc[data.three == 5]["three"] = 6

# ✅ GOOD: Single loc operation
data.loc[data.three == 5, "three"] = 6
```

### Arithmetic and Data Alignment
pandas automatically aligns data by index labels during arithmetic. Non-overlapping labels produce `NaN`.

```python
s1 + s2   # Union of indexes
df1 + df2 # Alignment on both rows AND columns
```

#### Arithmetic with Fill Values
Use `.add()`, `.sub()`, `.mul()`, `.div()` with `fill_value` to substitute missing data during operations.
```python
df1.add(df2, fill_value=0)  # Treat missing as 0
```

### Function Application and Mapping
| Method | Use Case |
|--------|----------|
| `df.apply(func)` | Apply function to each column/row |
| `df.applymap(func)` | Element-wise function on DataFrame |
| `series.map(func)` | Element-wise function on Series |

```python
frame.apply(np.abs)
frame.apply(lambda x: x.max() - x.min())
frame.applymap(lambda x: f"{x:.2f}")
obj.map({"a": "alpha", "b": "beta"})
```

### Sorting and Ranking
```python
obj.sort_index()               # Sort by index
frame.sort_index(axis=1)       # Sort columns alphabetically
obj.sort_values()              # Sort by values
frame.sort_values(by=["a", "b"])  # Sort by multiple columns
obj.rank()                     # Assign ranks (1-based, handles ties)
```

### Axis Indexes with Duplicate Labels
Indexes can contain duplicates. Selections return **all** matching occurrences. Some methods like `is_unique` help detect this.

---

## 5.3 Summarizing and Computing Descriptive Statistics

### Descriptive Statistics
Methods skip missing data by default (`skipna=True`).

```python
df.sum()            # Column sums
df.mean(axis=1)     # Row means
df.cumsum()         # Cumulative sum
df.describe()       # Summary stats (count, mean, std, min, quartiles, max)
df.idxmax()         # Index labels of max values
df.idxmin()         # Index labels of min values
```

### Correlation and Covariance
```python
df.corr()           # Pearson correlation matrix
df.cov()            # Covariance matrix
df.corrwith(other)  # Pairwise correlations with another Series/DataFrame
```

### Unique Values, Value Counts, and Membership
```python
obj.unique()            # Array of unique values
obj.value_counts()      # Frequency counts (sorted descending)
obj.isin(["a", "b"])    # Boolean membership test
pd.Index(["foo", "bar"]).isin(["foo"])  # Works on Index objects too
```

---

## 5.4 Conclusion
pandas provides a robust foundation for data manipulation through:
1. **Labeled data structures** (`Series`, `DataFrame`) that align automatically
2. **Explicit indexing** (`loc`, `iloc`) to avoid ambiguity
3. **Vectorized operations** that replace slow Python loops
4. **Flexible handling** of missing data, reindexing, and hierarchical indexing (covered in later chapters)

Mastering these fundamentals enables efficient data cleaning, transformation, and preparation for analysis or modeling.
