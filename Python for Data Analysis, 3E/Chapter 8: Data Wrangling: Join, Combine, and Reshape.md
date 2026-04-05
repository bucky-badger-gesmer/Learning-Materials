# Chapter 8: Data Wrangling: Join, Combine, and Reshape

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/data-wrangling](https://wesmckinney.com/book/data-wrangling)

---

## Table of Contents

- [8.1 Hierarchical Indexing](#81-hierarchical-indexing)
  - [Creating MultiIndex](#creating-multiindex)
  - [Partial Indexing](#partial-indexing)
  - [Stack and Unstack](#stack-and-unstack)
  - [Hierarchical Indexes on Both Axes](#hierarchical-indexes-on-both-axes)
  - [Index Level Names](#index-level-names)
  - [Reordering and Sorting Levels](#reordering-and-sorting-levels)
  - [Summary Statistics by Level](#summary-statistics-by-level)
  - [set_index / reset_index](#set_index--reset_index)
- [8.2 Combining and Merging Datasets](#82-combining-and-merging-datasets)
  - [Database-Style DataFrame Joins (pd.merge)](#database-style-dataframe-joins-pdmerge)
  - [Join Types](#join-types)
  - [Many-to-Many Merges](#many-to-many-merges)
  - [Multiple Keys](#multiple-keys)
  - [Overlapping Column Names](#overlapping-column-names)
  - [Merging on Index](#merging-on-index)
  - [DataFrame.join](#dataframejoin)
  - [Concatenating Along an Axis (pd.concat)](#concatenating-along-an-axis-pdconcat)
  - [Combining Data with Overlap (combine_first)](#combining-data-with-overlap-combine_first)
- [8.3 Reshaping and Pivoting](#83-reshaping-and-pivoting)
  - [Reshaping with Hierarchical Indexing](#reshaping-with-hierarchical-indexing)
  - [Pivoting "Long" to "Wide" Format](#pivoting-long-to-wide-format)
  - [Pivoting "Wide" to "Long" Format](#pivoting-wide-to-long-format)
- [8.4 Conclusion](#84-conclusion)
- [Quick Reference: Common Data Wrangling Tasks](#quick-reference-common-data-wrangling-tasks)
- [Decision Guide: merge vs concat vs join](#decision-guide-merge-vs-concat-vs-join)

---

## Overview

In many applications, data may be spread across a number of files or databases, or be arranged in a form that is not convenient to analyze. This chapter focuses on tools to help combine, join, and rearrange data.

**Key capabilities:**
- Create and work with hierarchical (multi-level) indexes using `MultiIndex`
- Perform database-style joins with `pd.merge` (inner, left, right, outer)
- Concatenate objects along an axis with `pd.concat` and hierarchical keys
- Patch missing data with `combine_first`
- Reshape data between "long" and "wide" formats using `stack`, `unstack`, `pivot`, and `melt`
- Merge on indexes and handle hierarchical index joins
- Convert columns to indexes (and back) with `set_index` / `reset_index`

```python
import numpy as np
import pandas as pd
```

---

## 8.1 Hierarchical Indexing

*Hierarchical indexing* is an important feature of pandas that enables you to have multiple (two or more) index *levels* on an axis. It provides a way to work with higher dimensional data in a lower dimensional form.

### Creating MultiIndex

Create a Series with a list of lists (or arrays) as the index:

```python
data = pd.Series(np.random.uniform(size=9),
                 index=[["a", "a", "a", "b", "b", "c", "c", "d", "d"],
                        [1, 2, 3, 1, 3, 1, 2, 2, 3]])

data
# a  1    0.929616
#    2    0.316376
#    3    0.183919
# b  1    0.204560
#    3    0.567725
# c  1    0.595545
#    2    0.964515
# d  2    0.653177
#    3    0.748907
# dtype: float64
```

The "gaps" in the index display mean "use the label directly above." The underlying index is a `MultiIndex`:

```python
data.index
# MultiIndex([('a', 1),
#             ('a', 2),
#             ('a', 3),
#             ('b', 1),
#             ('b', 3),
#             ('c', 1),
#             ('c', 2),
#             ('d', 2),
#             ('d', 3)],
#            )
```

A `MultiIndex` can also be created standalone and reused:

```python
pd.MultiIndex.from_arrays([["Ohio", "Ohio", "Colorado"],
                           ["Green", "Red", "Green"]],
                          names=["state", "color"])
```

### Partial Indexing

With a hierarchically indexed object, *partial* indexing enables concise selection of data subsets:

```python
# Select by outer level label
data["b"]
# 1    0.204560
# 3    0.567725
# dtype: float64

# Slice range of outer labels
data["b":"c"]
# b  1    0.204560
#    3    0.567725
# c  1    0.595545
#    2    0.964515
# dtype: float64

# Select specific outer labels
data.loc[["b", "d"]]
# b  1    0.204560
#    3    0.567725
# d  2    0.653177
#    3    0.748907
# dtype: float64

# Select from an inner level (all rows where inner level == 2)
data.loc[:, 2]
# a    0.316376
# c    0.964515
# d    0.653177
# dtype: float64
```

### Stack and Unstack

Hierarchical indexing plays an important role in reshaping data. You can rearrange a Series with a `MultiIndex` into a DataFrame using `unstack`:

```python
data.unstack()
#           1         2         3
# a  0.929616  0.316376  0.183919
# b  0.204560       NaN  0.567725
# c  0.595545  0.964515       NaN
# d       NaN  0.653177  0.748907
```

The inverse operation is `stack`:

```python
data.unstack().stack()
# a  1    0.929616
#    2    0.316376
#    3    0.183919
# b  1    0.204560
#    3    0.567725
# c  1    0.595545
#    2    0.964515
# d  2    0.653177
#    3    0.748907
# dtype: float64
```

> ⚠️ `unstack` may introduce missing data (NaN) if not all values in the inner level are present for every outer level label.

### Hierarchical Indexes on Both Axes

With a DataFrame, either axis can have a hierarchical index:

```python
frame = pd.DataFrame(np.arange(12).reshape((4, 3)),
                     index=[["a", "a", "b", "b"], [1, 2, 1, 2]],
                     columns=[["Ohio", "Ohio", "Colorado"],
                              ["Green", "Red", "Green"]])

frame
#      Ohio     Colorado
#     Green Red    Green
# a 1     0   1        2
#   2     3   4        5
# b 1     6   7        8
#   2     9  10       11
```

Partial column indexing works similarly:

```python
frame["Ohio"]
# color      Green  Red
# key1 key2            
# a    1         0    1
#      2         3    4
# b    1         6    7
#      2         9   10
```

### Index Level Names

Hierarchical levels can have names (strings or any Python objects):

```python
frame.index.names = ["key1", "key2"]
frame.columns.names = ["state", "color"]

frame
# state      Ohio     Colorado
# color     Green Red    Green
# key1 key2                   
# a    1        0   1        2
#      2        3   4        5
# b    1        6   7        8
#      2        9  10       11
```

> ⚠️ Index names like `"state"` and `"color"` are **not** part of the row labels (`frame.index` values). They are metadata on the column index.

Check the number of levels with `nlevels`:

```python
frame.index.nlevels  # 2
```

### Reordering and Sorting Levels

**`swaplevel`** — interchanges two level numbers or names (data is otherwise unaltered):

```python
frame.swaplevel("key1", "key2")
# state      Ohio     Colorado
# color     Green Red    Green
# key2 key1                   
# 1    a        0   1        2
# 2    a        3   4        5
# 1    b        6   7        8
# 2    b        9  10       11
```

**`sort_index`** — sorts lexicographically using all index levels by default, or a subset via `level`:

```python
frame.sort_index(level=1)
# state      Ohio     Colorado
# color     Green Red    Green
# key1 key2                   
# a    1        0   1        2
# b    1        6   7        8
# a    2        3   4        5
# b    2        9  10       11

# Combine swaplevel + sort_index for full lexicographic order
frame.swaplevel(0, 1).sort_index(level=0)
# state      Ohio     Colorado
# color     Green Red    Green
# key2 key1                   
# 1    a        0   1        2
#      b        6   7        8
# 2    a        3   4        5
#      b        9  10       11
```

> 💡 **Performance tip:** Data selection is much faster on hierarchically indexed objects when the index is lexicographically sorted starting with the outermost level — i.e., the result of calling `sort_index(level=0)` or `sort_index()`.

### Summary Statistics by Level

Many descriptive and summary statistics on DataFrame and Series have a `level` option to specify which level to aggregate by:

```python
# Aggregate rows by level "key2"
frame.groupby(level="key2").sum()
# state  Ohio     Colorado
# color Green Red    Green
# key2                    
# 1         6   8       10
# 2        12  14       16

# Aggregate columns by level "color"
frame.groupby(level="color", axis="columns").sum()
# color      Green  Red
# key1 key2            
# a    1         2    1
#      2         8    4
# b    1        14    7
#      2        20   10
```

### set_index / reset_index

**`set_index`** — creates a new DataFrame using one or more columns as the index:

```python
frame = pd.DataFrame({"a": range(7), "b": range(7, 0, -1),
                      "c": ["one", "one", "one", "two", "two",
                            "two", "two"],
                      "d": [0, 1, 2, 0, 1, 2, 3]})

frame
#    a  b    c  d
# 0  0  7  one  0
# 1  1  6  one  1
# 2  2  5  one  2
# 3  3  4  two  0
# 4  4  3  two  1
# 5  5  2  two  2
# 6  6  1  two  3

frame2 = frame.set_index(["c", "d"])
frame2
#        a  b
# c   d      
# one 0  0  7
#     1  1  6
#     2  2  5
# two 0  3  4
#     1  4  3
#     2  5  2
#     3  6  1
```

By default, columns are removed from the DataFrame. Keep them with `drop=False`:

```python
frame.set_index(["c", "d"], drop=False)
#        a  b    c  d
# c   d              
# one 0  0  7  one  0
#     1  1  6  one  1
#     2  2  5  one  2
# two 0  3  4  two  0
#     1  4  3  two  1
#     2  5  2  two  2
#     3  6  1  two  3
```

**`reset_index`** — does the opposite; moves hierarchical index levels into columns:

```python
frame2.reset_index()
#      c  d  a  b
# 0  one  0  0  7
# 1  one  1  1  6
# 2  one  2  2  5
# 3  two  0  3  4
# 4  two  1  4  3
# 5  two  2  5  2
# 6  two  3  6  1
```

---

## 8.2 Combining and Merging Datasets

Data contained in pandas objects can be combined in three main ways:

| Operation | Function | Purpose |
|-----------|----------|---------|
| **Merge/Join** | `pd.merge` | Connect rows based on one or more keys (database-style joins) |
| **Concatenate** | `pd.concat` | Stack objects together along an axis |
| **Combine** | `combine_first` | Fill missing values in one object with values from another |

### Database-Style DataFrame Joins (pd.merge)

*Merge* or *join* operations combine datasets by linking rows using one or more *keys*. The `pd.merge` function is the main entry point, implementing database join operations familiar to SQL users.

**Many-to-one join** — multiple rows with the same key in one DataFrame, single row per key in the other:

```python
df1 = pd.DataFrame({"key": ["b", "b", "a", "c", "a", "a", "b"],
                    "data1": pd.Series(range(7), dtype="Int64")})

df2 = pd.DataFrame({"key": ["a", "b", "d"],
                    "data2": pd.Series(range(3), dtype="Int64")})

# Default: inner join on overlapping column names
pd.merge(df1, df2)
#   key  data1  data2
# 0   b      0      1
# 1   b      1      1
# 2   b      6      1
# 3   a      2      0
# 4   a      4      0
# 5   a      5      0

# Best practice: specify the join column explicitly
pd.merge(df1, df2, on="key")
```

**Different column names** — use `left_on` and `right_on`:

```python
df3 = pd.DataFrame({"lkey": ["b", "b", "a", "c", "a", "a", "b"],
                    "data1": pd.Series(range(7), dtype="Int64")})

df4 = pd.DataFrame({"rkey": ["a", "b", "d"],
                    "data2": pd.Series(range(3), dtype="Int64")})

pd.merge(df3, df4, left_on="lkey", right_on="rkey")
#   lkey  data1 rkey  data2
# 0    b      0    b      1
# 1    b      1    b      1
# 2    b      6    b      1
# 3    a      2    a      0
# 4    a      4    a      0
# 5    a      5    a      0
```

> ⚠️ When joining columns on columns, the **indexes on the passed DataFrame objects are discarded**. Use `reset_index()` first if you need to preserve index values.

### Join Types

Table 8.1: Different join types with the `how` argument

| Option | Behavior |
|--------|----------|
| `how="inner"` | Use only the key combinations observed in **both** tables (intersection) |
| `how="left"` | Use **all** key combinations found in the left table |
| `how="right"` | Use **all** key combinations found in the right table |
| `how="outer"` | Use **all** key combinations observed in both tables together (union) |

```python
# Outer join — includes keys from both sides, with NaN for non-matching
pd.merge(df1, df2, how="outer")
#   key  data1  data2
# 0   b      0      1
# 1   b      1      1
# 2   b      6      1
# 3   a      2      0
# 4   a      4      0
# 5   a      5      0
# 6   c      3   <NA>    ← only in df1
# 7   d   <NA>      2    ← only in df2

# Left join — keeps all rows from df1
pd.merge(df3, df4, left_on="lkey", right_on="rkey", how="left")

# Right join — keeps all rows from df2
pd.merge(df3, df4, left_on="lkey", right_on="rkey", how="right")
```

### Many-to-Many Merges

Many-to-many merges form the **Cartesian product** of matching keys:

```python
df1 = pd.DataFrame({"key": ["b", "b", "a", "c", "a", "b"],
                    "data1": pd.Series(range(6), dtype="Int64")})

df2 = pd.DataFrame({"key": ["a", "b", "a", "b", "d"],
                    "data2": pd.Series(range(5), dtype="Int64")})

pd.merge(df1, df2, on="key", how="left")
#    key  data1  data2
# 0    b      0      1    ← first "b" in df1 × first "b" in df2
# 1    b      0      3    ← first "b" in df1 × second "b" in df2
# 2    b      1      1    ← second "b" in df1 × first "b" in df2
# 3    b      1      3    ← second "b" in df1 × second "b" in df2
# 4    a      2      0
# 5    a      2      2
# 6    c      3   <NA>
# 7    a      4      0
# 8    a      4      2
# 9    b      5      1
# 10   b      5      3
```

> ⚠️ With 3 `"b"` rows in the left and 2 in the right, there are 3 × 2 = 6 `"b"` rows in the result. The `how` argument affects only which distinct key values appear, not the Cartesian product behavior.

### Multiple Keys

Pass a list of column names to merge on multiple keys:

```python
left = pd.DataFrame({"key1": ["foo", "foo", "bar"],
                     "key2": ["one", "two", "one"],
                     "lval": pd.Series([1, 2, 3], dtype='Int64')})

right = pd.DataFrame({"key1": ["foo", "foo", "bar", "bar"],
                      "key2": ["one", "one", "one", "two"],
                      "rval": pd.Series([4, 5, 6, 7], dtype='Int64')})

pd.merge(left, right, on=["key1", "key2"], how="outer")
#   key1 key2  lval  rval
# 0  foo  one     1     4
# 1  foo  one     1     5
# 2  foo  two     2  <NA>
# 3  bar  one     3     6
# 4  bar  two  <NA>     7
```

Think of multiple keys as forming an array of tuples used as a single join key.

### Overlapping Column Names

When columns overlap (other than the join key), pandas automatically appends suffixes:

```python
pd.merge(left, right, on="key1")
#   key1 key2_x  lval key2_y  rval
# 0  foo    one     1    one     4
# 1  foo    one     1    one     5
# 2  foo    two     2    one     4
# 3  foo    two     2    one     5
# 4  bar    one     3    one     6
# 5  bar    one     3    two     7
```

Customize suffixes with `suffixes`:

```python
pd.merge(left, right, on="key1", suffixes=("_left", "_right"))
#   key1 key2_left  lval key2_right  rval
# 0  foo       one     1        one     4
# 1  foo       one     1        one     5
# 2  foo       two     2        one     4
# 3  foo       two     2        one     5
# 4  bar       one     3        one     6
# 5  bar       one     3        two     7
```

**`validate`** and **`indicator`** parameters for merge verification:

```python
# Verify merge type (raises error if not one-to-many)
pd.merge(df1, df2, on="key", validate="one_to_many")

# Add _merge column showing source of each row
pd.merge(df1, df2, on="key", how="outer", indicator=True)
#   key  data1  data2      _merge
# 0   b      0      1        both
# 1   b      1      1        both
# 2   b      6      1        both
# 3   a      2      0        both
# 4   a      4      0        both
# 5   a      5      0        both
# 6   c      3   <NA>   left_only
# 7   d   <NA>      2  right_only
```

Table 8.2: `pd.merge` function arguments

| Argument | Description |
|----------|-------------|
| `left` | DataFrame to be merged on the left side |
| `right` | DataFrame to be merged on the right side |
| `how` | Type of join: `"inner"`, `"outer"`, `"left"`, or `"right"` (default: `"inner"`) |
| `on` | Column names to join on; must be in both DataFrames. If not specified, uses intersection of column names |
| `left_on` | Columns in left DataFrame to use as join keys |
| `right_on` | Columns in right DataFrame to use as join keys |
| `left_index` | Use row index in left as join key (or keys, if `MultiIndex`) |
| `right_index` | Use row index in right as join key |
| `sort` | Sort merged data lexicographically by join keys (default: `False`) |
| `suffixes` | Tuple of strings to append to overlapping column names (default: `("_x", "_y")`) |
| `copy` | If `False`, avoid copying data in some exceptional cases (default: always copies) |
| `validate` | Verifies merge type: one-to-one, one-to-many, many-to-one, or many-to-many |
| `indicator` | Adds `_merge` column showing source: `"left_only"`, `"right_only"`, or `"both"` |

### Merging on Index

When merge keys are found in the index rather than columns, use `left_index=True` or `right_index=True`:

```python
left1 = pd.DataFrame({"key": ["a", "b", "a", "a", "b", "c"],
                      "value": pd.Series(range(6), dtype="Int64")})

right1 = pd.DataFrame({"group_val": [3.5, 7]}, index=["a", "b"])

# Merge left column with right index
pd.merge(left1, right1, left_on="key", right_index=True)
#   key  value  group_val
# 0   a      0        3.5
# 2   a      2        3.5
# 3   a      3        3.5
# 1   b      1        7.0
# 4   b      4        7.0
```

> 💡 Note that the index values from `left1` are preserved here (unlike column-on-column merges), because the right index is unique and this is a many-to-one merge.

**Outer join on index:**

```python
pd.merge(left1, right1, left_on="key", right_index=True, how="outer")
#   key  value  group_val
# 0   a      0        3.5
# 2   a      2        3.5
# 3   a      3        3.5
# 1   b      1        7.0
# 4   b      4        7.0
# 5   c      5        NaN    ← "c" has no match in right1
```

**Hierarchical index merging** — equivalent to a multiple-key merge:

```python
lefth = pd.DataFrame({"key1": ["Ohio", "Ohio", "Ohio",
                               "Nevada", "Nevada"],
                      "key2": [2000, 2001, 2002, 2001, 2002],
                      "data": pd.Series(range(5), dtype="Int64")})

righth_index = pd.MultiIndex.from_arrays([
    ["Nevada", "Nevada", "Ohio", "Ohio", "Ohio", "Ohio"],
    [2001, 2000, 2000, 2000, 2001, 2002]
])

righth = pd.DataFrame({"event1": pd.Series([0, 2, 4, 6, 8, 10], dtype="Int64",
                                           index=righth_index),
                       "event2": pd.Series([1, 3, 5, 7, 9, 11], dtype="Int64",
                                           index=righth_index)})

# Multiple columns on left match MultiIndex on right
pd.merge(lefth, righth, left_on=["key1", "key2"], right_index=True)
#      key1  key2  data  event1  event2
# 0    Ohio  2000     0       4       5
# 0    Ohio  2000     0       6       7   ← duplicate index in righth
# 1    Ohio  2001     1       8       9
# 2    Ohio  2002     2      10      11
# 3  Nevada  2001     3       0       1
```

**Index-on-index merge:**

```python
left2 = pd.DataFrame([[1., 2.], [3., 4.], [5., 6.]],
                     index=["a", "c", "e"],
                     columns=["Ohio", "Nevada"]).astype("Int64")

right2 = pd.DataFrame([[7., 8.], [9., 10.], [11., 12.], [13, 14]],
                      index=["b", "c", "d", "e"],
                      columns=["Missouri", "Alabama"]).astype("Int64")

pd.merge(left2, right2, how="outer", left_index=True, right_index=True)
#    Ohio  Nevada  Missouri  Alabama
# a     1       2      <NA>     <NA>
# b  <NA>    <NA>         7        8
# c     3       4         9       10
# d  <NA>    <NA>        11       12
# e     5       6        13       14
```

### DataFrame.join

`DataFrame.join` is a convenience method that simplifies merging by index. It defaults to a **left join** and supports joining multiple DataFrames:

```python
# Simpler syntax for index-on-index merge
left2.join(right2, how="outer")
# Same result as pd.merge above

# Join index of right onto a column of left
left1.join(right1, on="key")
#   key  value  group_val
# 0   a      0        3.5
# 1   b      1        7.0
# 2   a      2        3.5
# 3   a      3        3.5
# 4   b      4        7.0
# 5   c      5        NaN

# Join multiple DataFrames at once
another = pd.DataFrame([[7., 8.], [9., 10.], [11., 12.], [16., 17.]],
                       index=["a", "c", "e", "f"],
                       columns=["New York", "Oregon"])

left2.join([right2, another])
#    Ohio  Nevada  Missouri  Alabama  New York  Oregon
# a     1       2      <NA>     <NA>       7.0     8.0
# c     3       4         9       10       9.0    10.0
# e     5       6        13       14      11.0    12.0

left2.join([right2, another], how="outer")
#    Ohio  Nevada  Missouri  Alabama  New York  Oregon
# a     1       2      <NA>     <NA>       7.0     8.0
# c     3       4         9       10       9.0    10.0
# e     5       6        13       14      11.0    12.0
# b  <NA>    <NA>         7        8       NaN     NaN
# d  <NA>    <NA>        11       12       NaN     NaN
# f  <NA>    <NA>      <NA>     <NA>      16.0    17.0
```

> 💡 Think of `join` as joining data "into" the object whose `join` method was called.

### Concatenating Along an Axis (pd.concat)

The `pd.concat` function concatenates ("stacks") objects along an axis. It generalizes NumPy's `concatenate` for labeled pandas objects.

**Basic concatenation** (along rows, the default):

```python
s1 = pd.Series([0, 1], index=["a", "b"], dtype="Int64")
s2 = pd.Series([2, 3, 4], index=["c", "d", "e"], dtype="Int64")
s3 = pd.Series([5, 6], index=["f", "g"], dtype="Int64")

pd.concat([s1, s2, s3])
# a    0
# b    1
# c    2
# d    3
# e    4
# f    5
# g    6
# dtype: Int64
```

**Concatenate along columns:**

```python
pd.concat([s1, s2, s3], axis="columns")
#       0     1     2
# a     0  <NA>  <NA>
# b     1  <NA>  <NA>
# c  <NA>     2  <NA>
# d  <NA>     3  <NA>
# e  <NA>     4  <NA>
# f  <NA>  <NA>     5
# g  <NA>  <NA>     6
```

**`join="inner"`** — intersection of indexes on the other axis:

```python
s4 = pd.concat([s1, s3])

pd.concat([s1, s4], axis="columns", join="inner")
#    0  1
# a  0  0
# b  1  1
# ← "f" and "g" excluded (not in s1)
```

**`keys` argument** — creates a hierarchical index identifying source objects:

```python
result = pd.concat([s1, s1, s3], keys=["one", "two", "three"])
result
# one    a    0
#        b    1
# two    a    0
#        b    1
# three  f    5
#        g    6
# dtype: Int64

result.unstack()
#           a     b     f     g
# one       0     1  <NA>  <NA>
# two       0     1  <NA>  <NA>
# three  <NA>  <NA>     5     6
```

**Keys with column concatenation** — keys become column headers:

```python
pd.concat([s1, s2, s3], axis="columns", keys=["one", "two", "three"])
#     one   two  three
# a     0  <NA>   <NA>
# b     1  <NA>   <NA>
# c  <NA>     2   <NA>
# d  <NA>     3   <NA>
# e  <NA>     4   <NA>
# f  <NA>  <NA>      5
# g  <NA>  <NA>      6
```

**DataFrame concatenation with keys:**

```python
df1 = pd.DataFrame(np.arange(6).reshape(3, 2), index=["a", "b", "c"],
                   columns=["one", "two"])

df2 = pd.DataFrame(5 + np.arange(4).reshape(2, 2), index=["a", "c"],
                   columns=["three", "four"])

pd.concat([df1, df2], axis="columns", keys=["level1", "level2"])
#   level1     level2     
#      one two  three four
# a      0   1    5.0  6.0
# b      2   3    NaN  NaN
# c      4   5    7.0  8.0
```

**Dictionary input** — dict keys used as `keys` automatically:

```python
pd.concat({"level1": df1, "level2": df2}, axis="columns")
# Same result as above
```

**Named hierarchical levels:**

```python
pd.concat([df1, df2], axis="columns", keys=["level1", "level2"],
          names=["upper", "lower"])
# upper level1     level2     
# lower    one two  three four
# a          0   1    5.0  6.0
# b          2   3    NaN  NaN
# c          4   5    7.0  8.0
```

**`ignore_index=True`** — discard original indexes, assign new range index:

```python
df1 = pd.DataFrame(np.random.standard_normal((3, 4)),
                   columns=["a", "b", "c", "d"])

df2 = pd.DataFrame(np.random.standard_normal((2, 3)),
                   columns=["b", "d", "a"])

pd.concat([df1, df2], ignore_index=True)
#           a         b         c         d
# 0  1.248804  0.774191 -0.319657 -0.624964
# 1  1.078814  0.544647  0.855588  1.343268
# 2 -0.267175  1.793095 -0.652929 -1.886837
# 3 -0.007799  1.059626       NaN  0.644448
# 4  0.667226 -0.449204       NaN  2.448963
```

Table 8.3: `pd.concat` function arguments

| Argument | Description |
|----------|-------------|
| `objs` | List or dictionary of pandas objects to concatenate (required) |
| `axis` | Axis to concatenate along; default `"index"` (rows) |
| `join` | `"outer"` (union, default) or `"inner"` (intersection) of indexes on other axes |
| `keys` | Values to associate with objects, forming hierarchical index on concatenation axis |
| `levels` | Specific indexes to use as hierarchical level(s) if `keys` passed |
| `names` | Names for created hierarchical levels if `keys` and/or `levels` passed |
| `verify_integrity` | Check for duplicates on new axis and raise exception if found (default: `False`) |
| `ignore_index` | Discard original indexes, produce new `range(total_length)` index |

### Combining Data with Overlap (combine_first)

`combine_first` fills NaN values in the calling object with values from another object, aligning by index. Think of it as **"patching"** missing data.

**Series example:**

```python
a = pd.Series([np.nan, 2.5, 0.0, 3.5, 4.5, np.nan],
              index=["f", "e", "d", "c", "b", "a"])

b = pd.Series([0., np.nan, 2., np.nan, np.nan, 5.],
              index=["a", "b", "c", "d", "e", "f"])

# Equivalent to np.where(pd.isna(a), b, a) but with index alignment
a.combine_first(b)
# a    0.0
# b    4.5
# c    3.5
# d    0.0
# e    2.5
# f    5.0
# dtype: float64
```

> ⚠️ `np.where(pd.isna(a), b, a)` does not check index alignment and doesn't require objects to be the same length. Use `combine_first` when index alignment matters.

**DataFrame example** — patches column by column:

```python
df1 = pd.DataFrame({"a": [1., np.nan, 5., np.nan],
                    "b": [np.nan, 2., np.nan, 6.],
                    "c": range(2, 18, 4)})

df2 = pd.DataFrame({"a": [5., 4., np.nan, 3., 7.],
                    "b": [np.nan, 3., 4., 6., 8.]})

df1.combine_first(df2)
#      a    b     c
# 0  1.0  NaN   2.0    ← df1.a[0]=1.0 kept (not NaN)
# 1  4.0  2.0   6.0    ← df1.a[1]=NaN → patched with df2.a[1]=4.0
# 2  5.0  4.0  10.0    ← df1.a[2]=5.0 kept; df1.b[2]=NaN → patched
# 3  3.0  6.0  14.0    ← df1.a[3]=NaN → patched with df2.a[3]=3.0
# 4  7.0  8.0   NaN    ← new row from df2; df1 has no row 4
```

> 💡 The output of `combine_first` with DataFrames has the **union of all column names** from both objects.

---

## 8.3 Reshaping and Pivoting

### Reshaping with Hierarchical Indexing

Two primary actions for rearranging tabular data:

| Method | Direction | Result |
|--------|-----------|--------|
| `stack()` | Columns → Rows | DataFrame → Series (or DataFrame with fewer columns) |
| `unstack()` | Rows → Columns | Series → DataFrame (or DataFrame with more columns) |

**Basic stack/unstack:**

```python
data = pd.DataFrame(np.arange(6).reshape((2, 3)),
                    index=pd.Index(["Ohio", "Colorado"], name="state"),
                    columns=pd.Index(["one", "two", "three"], name="number"))

data
# number    one  two  three
# state                    
# Ohio        0    1      2
# Colorado    3    4      5

result = data.stack()
result
# state     number
# Ohio      one       0
#           two       1
#           three     2
# Colorado  one       3
#           two       4
#           three     5
# dtype: int64

result.unstack()  # Returns to original DataFrame
```

**Unstack a different level** — by number or name:

```python
result.unstack(level=0)
# state   Ohio  Colorado
# number                
# one        0         3
# two        1         4
# three      2         5

result.unstack(level="state")  # Same result
```

**Unstacking with missing data:**

```python
s1 = pd.Series([0, 1, 2, 3], index=["a", "b", "c", "d"], dtype="Int64")
s2 = pd.Series([4, 5, 6], index=["c", "d", "e"], dtype="Int64")
data2 = pd.concat([s1, s2], keys=["one", "two"])

data2.unstack()
#         a     b  c  d     e
# one     0     1  2  3  <NA>
# two  <NA>  <NA>  4  5     6

# stack() filters out missing data by default (easily invertible)
data2.unstack().stack()
# one  a    0
#      b    1
#      c    2
#      d    3
# two  c    4
#      d    5
#      e    6
# dtype: Int64

# Keep missing values with dropna=False
data2.unstack().stack(dropna=False)
# one  a       0
#      b       1
#      c       2
#      d       3
#      e    <NA>
# two  a    <NA>
#      b    <NA>
#      c       4
#      d       5
#      e       6
# dtype: Int64
```

**DataFrame with MultiIndex on both axes:**

```python
df = pd.DataFrame({"left": result, "right": result + 5},
                  columns=pd.Index(["left", "right"], name="side"))

df
# side             left  right
# state    number             
# Ohio     one        0      5
#          two        1      6
#          three      2      7
# Colorado one        3      8
#          two        4      9
#          three      5     10

df.unstack(level="state")
# side   left          right         
# state  Ohio Colorado  Ohio Colorado
# number                             
# one       0        3     5        8
# two       1        4     6        9
# three     2        5     7       10

# Stack by named axis
df.unstack(level="state").stack(level="side")
# state         Colorado  Ohio
# number side                 
# one    left          3     0
#        right         8     5
# two    left          4     1
#        right         9     6
# three  left          5     2
#        right        10     7
```

> 💡 When you unstack in a DataFrame, the level unstacked becomes the **lowest level** in the result.

### Pivoting "Long" to "Wide" Format

**Long format** (stacked): each row is a single observation. Common in SQL databases.
**Wide format**: multiple values per row, one column per distinct item.

Example workflow — converting wide-format macroeconomic data to long format, then pivoting back:

```python
# Start with wide-format data
data = pd.read_csv("examples/macrodata.csv")
data = data.loc[:, ["year", "quarter", "realgdp", "infl", "unemp"]]

# Create a PeriodIndex from year and quarter
periods = pd.PeriodIndex(year=data.pop("year"),
                         quarter=data.pop("quarter"),
                         name="date")
data.index = periods.to_timestamp("D")
data.columns.name = "item"

data.head()
# item         realgdp  infl  unemp
# date                             
# 1959-01-01  2710.349  0.00    5.8
# 1959-04-01  2778.801  2.34    5.1
# 1959-07-01  2775.488  2.74    5.3
```

**Convert wide → long using stack + reset_index:**

```python
long_data = (data.stack()
             .reset_index()
             .rename(columns={0: "value"}))

long_data[:10]
#         date     item     value
# 0 1959-01-01  realgdp  2710.349
# 1 1959-01-01     infl     0.000
# 2 1959-01-01    unemp     5.800
# 3 1959-04-01  realgdp  2778.801
# 4 1959-04-01     infl     2.340
# 5 1959-04-01    unemp     5.100
```

**Convert long → wide using pivot:**

```python
pivoted = long_data.pivot(index="date", columns="item", values="value")

pivoted.head()
# item        infl   realgdp  unemp
# date                             
# 1959-01-01  0.00  2710.349    5.8
# 1959-04-01  2.34  2778.801    5.1
# 1959-07-01  2.74  2775.488    5.3
```

**Multiple value columns** → hierarchical column index:

```python
long_data["value2"] = np.random.standard_normal(len(long_data))

pivoted = long_data.pivot(index="date", columns="item")

pivoted.head()
#            value                    value2                    
# item        infl   realgdp unemp      infl   realgdp     unemp
# date                                                          
# 1959-01-01  0.00  2710.349   5.8  0.575721  0.802926  1.381918
# 1959-04-01  2.34  2778.801   5.1 -0.143492  0.000992 -0.206282

# Access a single value column
pivoted["value"].head()
# item        infl   realgdp  unemp
# date                             
# 1959-01-01  0.00  2710.349    5.8
```

> 💡 `pivot` is equivalent to `set_index` followed by `unstack`:
> ```python
> long_data.set_index(["date", "item"]).unstack("item")
> ```

### Pivoting "Wide" to "Long" Format

`pd.melt` is the inverse of `pivot`. It merges multiple columns into one, producing a longer DataFrame.

```python
df = pd.DataFrame({"key": ["foo", "bar", "baz"],
                   "A": [1, 2, 3],
                   "B": [4, 5, 6],
                   "C": [7, 8, 9]})

df
#    key  A  B  C
# 0  foo  1  4  7
# 1  bar  2  5  8
# 2  baz  3  6  9
```

**Melt with identifier columns:**

```python
melted = pd.melt(df, id_vars="key")
melted
#    key variable  value
# 0  foo        A      1
# 1  bar        A      2
# 2  baz        A      3
# 3  foo        B      4
# 4  bar        B      5
# 5  baz        B      6
# 6  foo        C      7
# 7  bar        C      8
# 8  baz        C      9
```

**Reshape back with pivot:**

```python
reshaped = melted.pivot(index="key", columns="variable", values="value")
reshaped
# variable  A  B  C
# key              
# bar       2  5  8
# baz       3  6  9
# foo       1  4  7

# Move index back to a column
reshaped.reset_index()
# variable  key  A  B  C
# 0         bar  2  5  8
# 1         baz  3  6  9
# 2         foo  1  4  7
```

**Subset of value columns:**

```python
pd.melt(df, id_vars="key", value_vars=["A", "B"])
#    key variable  value
# 0  foo        A      1
# 1  bar        A      2
# 2  baz        A      3
# 3  foo        B      4
# 4  bar        B      5
# 5  baz        B      6
```

**No identifier columns:**

```python
pd.melt(df, value_vars=["A", "B", "C"])
#   variable  value
# 0        A      1
# 1        A      2
# 2        A      3
# 3        B      4
# 4        B      5
# 5        B      6
# 6        C      7
# 7        C      8
# 8        C      9
```

**Custom column names:**

```python
pd.melt(df, id_vars="key", var_name="measurement", value_name="reading")
#    key measurement  reading
# 0  foo           A        1
# 1  bar           A        2
# 2  baz           A        3
# 3  foo           B        4
# ...
```

---

## 8.4 Conclusion

This chapter covered the essential tools for combining, joining, and rearranging data in pandas:

- **Hierarchical indexing** (`MultiIndex`) enables working with higher dimensional data in lower dimensional forms, with powerful partial indexing and level-based aggregation
- **`pd.merge`** implements database-style joins (inner, left, right, outer) with support for column and index-based keys, multiple keys, and merge validation
- **`pd.concat`** provides flexible concatenation along any axis with hierarchical key tracking
- **`combine_first`** offers a clean way to patch missing data from a secondary source
- **`stack`/`unstack`**, **`pivot`**, and **`melt`** enable bidirectional reshaping between long and wide data formats

These tools form the foundation for data reorganization tasks that appear throughout the rest of the book. Applied usage of these techniques is demonstrated extensively in [Chapter 13: Data Analysis Examples](https://wesmckinney.com/book/data-analysis-examples).

---

## Quick Reference: Common Data Wrangling Tasks

| Task | Code Pattern |
|------|-------------|
| Create MultiIndex from arrays | `pd.MultiIndex.from_arrays([arr1, arr2], names=["a", "b"])` |
| Select outer level of MultiIndex | `data["label"]` or `data.loc[["a", "b"]]` |
| Select inner level of MultiIndex | `data.loc[:, inner_value]` |
| Convert Series with MultiIndex to DataFrame | `series.unstack()` |
| Convert DataFrame columns to rows | `df.stack()` |
| Name index levels | `df.index.names = ["key1", "key2"]` |
| Swap index levels | `df.swaplevel("key1", "key2")` |
| Sort by specific index level | `df.sort_index(level=1)` |
| Aggregate by index level | `df.groupby(level="key2").sum()` |
| Columns → Index | `df.set_index(["col1", "col2"])` |
| Index → Columns | `df.reset_index()` |
| Inner join on column | `pd.merge(left, right, on="key")` |
| Left join on different column names | `pd.merge(left, right, left_on="a", right_on="b", how="left")` |
| Join on index | `pd.merge(left, right, left_index=True, right_index=True)` |
| Join index to column | `left_df.join(right_df, on="key")` |
| Join multiple DataFrames by index | `df1.join([df2, df3], how="outer")` |
| Stack Series vertically | `pd.concat([s1, s2, s3])` |
| Stack DataFrames side by side | `pd.concat([df1, df2], axis="columns")` |
| Concat with source labels | `pd.concat([s1, s2], keys=["first", "second"])` |
| Concat ignoring original indexes | `pd.concat([df1, df2], ignore_index=True)` |
| Patch NaN values from another object | `df1.combine_first(df2)` |
| Long → Wide (pivot) | `df.pivot(index="date", columns="item", values="value")` |
| Wide → Long (melt) | `pd.melt(df, id_vars=["key"], var_name="item", value_name="value")` |
| Pivot equivalent | `df.set_index(["date", "item"]).unstack("item")` |

---

## Decision Guide: merge vs concat vs join

### When to use each operation:

**Use `pd.merge` when:**
- You need database-style joins (inner, left, right, outer)
- Joining on one or more column values
- You need fine-grained control over join behavior (`validate`, `indicator`, `suffixes`)
- Keys have different names in each DataFrame (`left_on` / `right_on`)
- You need to handle many-to-many relationships with Cartesian products

**Use `pd.concat` when:**
- Stacking objects vertically (adding rows) or horizontally (adding columns)
- Combining objects with the same or similar structure
- You want to track the source of each piece with `keys`
- You need to combine more than two objects at once
- Objects may have non-overlapping indexes (use `join="outer"` or `join="inner"`)

**Use `DataFrame.join` when:**
- Joining primarily on index values (simpler syntax than `pd.merge`)
- Joining multiple DataFrames with compatible indexes
- You want a convenient left-join-by-default interface
- You think of it as joining data "into" the calling DataFrame

**Use `combine_first` when:**
- You have two datasets with overlapping indexes
- You want to fill NaN values in one object with values from another
- You need index-aligned "patching" rather than row-matching joins

### Key caveats:

1. **Indexes are discarded** when joining columns on columns with `pd.merge`. Use `reset_index()` first if you need to preserve them.
2. **Duplicate keys** in many-to-many merges produce Cartesian products, which can explode your data size unexpectedly.
3. **Performance with MultiIndex**: Always sort your hierarchical index lexicographically (`sort_index()`) for optimal selection performance.
4. **`unstack` introduces NaN** when not all inner-level values exist for every outer-level group.
5. **`stack` drops NaN by default** — use `stack(dropna=False)` to preserve missing values.
6. **`pd.concat` defaults to outer join** on non-concatenation axes — use `join="inner"` to keep only overlapping labels.
