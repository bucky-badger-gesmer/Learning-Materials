# Chapter 7: Data Cleaning and Preparation

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/data-cleaning](https://wesmckinney.com/book/data-cleaning)

---

## Table of Contents

- [7.1 Handling Missing Data](#71-handling-missing-data)
  - [Filtering Out Missing Data](#filtering-out-missing-data)
  - [Filling In Missing Data](#filling-in-missing-data)
- [7.2 Data Transformation](#72-data-transformation)
  - [Removing Duplicates](#removing-duplicates)
  - [Transforming Data Using a Function or Mapping](#transforming-data-using-a-function-or-mapping)
  - [Replacing Values](#replacing-values)
  - [Renaming Axis Indexes](#renaming-axis-indexes)
  - [Discretization and Binning](#discretization-and-binning)
  - [Detecting and Filtering Outliers](#detecting-and-filtering-outliers)
  - [Permutation and Random Sampling](#permutation-and-random-sampling)
  - [Computing Indicator/Dummy Variables](#computing-indicatordummy-variables)
- [7.3 Extension Data Types](#73-extension-data-types)
  - [Converting Columns with `astype`](#converting-columns-with-astype)
- [7.4 String Manipulation](#74-string-manipulation)
  - [Python Built-In String Methods](#python-built-in-string-methods)
  - [Regular Expressions](#regular-expressions)
  - [String Functions in pandas](#string-functions-in-pandas)
- [7.5 Categorical Data](#75-categorical-data)
  - [Background and Motivation](#background-and-motivation)
  - [Categorical Extension Type](#categorical-extension-type)
  - [Computations with Categoricals](#computations-with-categoricals)
  - [Categorical Methods](#categorical-methods)
  - [Creating Dummy Variables for Modeling](#creating-dummy-variables-for-modeling)
- [7.6 Conclusion](#76-conclusion)

---

## Overview
Data preparation — loading, cleaning, transforming, and rearranging — often takes up 80% or more of an analyst's time. This chapter covers tools for handling missing data, duplicate data, string manipulation, extension data types, categorical data, and other analytical data transformations.

---

## 7.1 Handling Missing Data

pandas uses `NaN` (Not a Number) as a sentinel value for missing data in floating-point arrays, and adopts the R convention of referring to missing data as **NA** (*not available*). The built-in Python `None` is also treated as NA.

```python
float_data = pd.Series([1.2, -3.5, np.nan, 0])
float_data.isna()
# 0    False
# 1    False
# 2     True
# 3    False

# None is also treated as NA
string_data = pd.Series(["aardvark", np.nan, None, "avocado"])
string_data.isna()
# 0    False
# 1     True
# 2     True
# 3    False
```

### Table: NA Handling Methods

| Method | Description |
|--------|-------------|
| `dropna` | Filter axis labels based on missing data, with tolerance thresholds |
| `fillna` | Fill missing data with a value or interpolation (`ffill`/`bfill`) |
| `isna` | Return Boolean mask indicating missing/NA values |
| `notna` | Negation of `isna` |

### Filtering Out Missing Data

```python
# Series
data = pd.Series([1, np.nan, 3.5, np.nan, 7])
data.dropna()
# Equivalent:
data[data.notna()]

# DataFrame — drops any row containing a missing value by default
data.dropna()

# Drop only rows that are ALL NA
data.dropna(how="all")

# Drop columns
data.dropna(axis="columns", how="all")

# Keep rows with at most N missing values (thresh = minimum non-NA count)
df.dropna(thresh=2)  # Keep rows with at least 2 non-NA values
```

### Filling In Missing Data

```python
# Fill with a constant
df.fillna(0)

# Fill with different values per column (dict)
df.fillna({1: 0.5, 2: 0})

# Forward-fill / Backward-fill with interpolation
df.fillna(method="ffill")
df.fillna(method="ffill", limit=2)  # Max 2 consecutive fills

# Fill with computed statistics (e.g., mean)
data.fillna(data.mean())
```

### Table: `fillna` Arguments

| Argument | Description |
|----------|-------------|
| `value` | Scalar or dict-like object to fill missing values |
| `method` | Interpolation: `"bfill"` or `"ffill"` |
| `axis` | Axis to fill on (`"index"` or `"columns"`) |
| `limit` | Max consecutive periods to fill (for forward/backward fill) |

---

## 7.2 Data Transformation

### Removing Duplicates

```python
data = pd.DataFrame({"k1": ["one", "two"] * 3 + ["two"],
                     "k2": [1, 1, 2, 3, 3, 4, 4]})

# Boolean mask: True for duplicate rows
data.duplicated()

# Remove duplicates (keeps first occurrence by default)
data.drop_duplicates()

# Drop duplicates based on subset of columns
data.drop_duplicates(subset=["k1"])

# Keep last occurrence instead
data.drop_duplicates(["k1", "k2"], keep="last")
```

### Transforming Data Using a Function or Mapping

```python
data = pd.DataFrame({"food": ["bacon", "pulled pork", "pastrami", "corned beef"],
                     "ounces": [4, 3, 6, 7.5]})

meat_to_animal = {
    "bacon": "pig",
    "pulled pork": "pig",
    "pastrami": "cow",
    "corned beef": "cow",
}

# Map using a dictionary
data["animal"] = data["food"].map(meat_to_animal)

# Map using a function
def get_animal(x):
    return meat_to_animal[x]

data["food"].map(get_animal)
```

### Replacing Values

`replace` is more flexible than `map` for value substitution.

```python
data = pd.Series([1., -999., 2., -999., -1000., 3.])

# Replace single value
data.replace(-999, np.nan)

# Replace multiple values with same substitute
data.replace([-999, -1000], np.nan)

# Replace each value with a different substitute
data.replace([-999, -1000], [np.nan, 0])

# Dict mapping (cleanest approach)
data.replace({-999: np.nan, -1000: 0})
```

> ⚠️ `data.replace` is distinct from `data.str.replace` (element-wise string substitution).

### Renaming Axis Indexes

```python
data = pd.DataFrame(np.arange(12).reshape((3, 4)),
                    index=["Ohio", "Colorado", "New York"],
                    columns=["one", "two", "three", "four"])

# Transform index with a function
def transform(x):
    return x[:4].upper()

data.index.map(transform)

# Modify in place
data.index = data.index.map(transform)

# Create transformed copy without modifying original
data.rename(index=str.title, columns=str.upper)

# Rename specific labels with a dict
data.rename(index={"OHIO": "INDIANA"},
            columns={"three": "peekaboo"})
```

### Discretization and Binning

Continuous data is often separated into bins for analysis.

```python
ages = [20, 22, 25, 27, 21, 23, 37, 31, 61, 45, 41, 32]
bins = [18, 25, 35, 60, 100]

# pd.cut → equal-length bins
age_categories = pd.cut(ages, bins)
age_categories.codes      # Integer codes for each bin
age_categories.categories # IntervalIndex of bin edges
pd.value_counts(age_categories)  # Bin counts

# Change which side is closed (default: right-closed)
pd.cut(ages, bins, right=False)

# Custom labels instead of interval notation
group_names = ["Youth", "YoungAdult", "MiddleAged", "Senior"]
pd.cut(ages, bins, labels=group_names)

# Equal-length bins from integer count (based on min/max)
data = np.random.uniform(size=20)
pd.cut(data, 4, precision=2)

# pd.qcut → equal-size bins based on sample quantiles
data = np.random.standard_normal(1000)
quartiles = pd.qcut(data, 4, precision=2)
pd.value_counts(quartiles)  # Each bin has ~250 values

# Custom quantiles
pd.qcut(data, [0, 0.1, 0.5, 0.9, 1.]).value_counts()
```

### Detecting and Filtering Outliers

```python
data = pd.DataFrame(np.random.standard_normal((1000, 4)))

# Find values exceeding threshold in a column
col = data[2]
col[col.abs() > 3]

# Select all rows with any value exceeding threshold
data[(data.abs() > 3).any(axis="columns")]

# Cap values (winsorize) to a range
data[data.abs() > 3] = np.sign(data) * 3
```

### Permutation and Random Sampling

```python
df = pd.DataFrame(np.arange(5 * 7).reshape((5, 7)))

# Permute rows
sampler = np.random.permutation(5)
df.take(sampler)
df.iloc[sampler]  # Equivalent

# Permute columns
column_sampler = np.random.permutation(7)
df.take(column_sampler, axis="columns")

# Random sample without replacement
df.sample(n=3)

# Random sample WITH replacement
choices = pd.Series([5, 7, -1, 6, 4])
choices.sample(n=10, replace=True)
```

### Computing Indicator/Dummy Variables

Convert categorical variables into a matrix of 1s and 0s for statistical modeling.

```python
df = pd.DataFrame({"key": ["b", "b", "a", "c", "a", "b"],
                   "data1": range(6)})

pd.get_dummies(df["key"], dtype=float)
#      a    b    c
# 0  0.0  1.0  0.0
# 1  0.0  1.0  0.0
# 2  1.0  0.0  0.0
# ...

# Add prefix and merge
dummies = pd.get_dummies(df["key"], prefix="key", dtype=float)
df_with_dummy = df[["data1"]].join(dummies)
```

**Multiple membership** (e.g., movie genres separated by `|`):

```python
movies = pd.read_table("datasets/movielens/movies.dat", sep="::",
                       header=None, names=["movie_id", "title", "genres"])

# Split delimited string into dummy columns
dummies = movies["genres"].str.get_dummies("|")
movies_windic = movies.join(dummies.add_prefix("Genre_"))
```

**Combine `get_dummies` with `cut`** for binned indicator variables:

```python
values = np.random.uniform(size=10)
bins = [0, 0.2, 0.4, 0.6, 0.8, 1]
pd.get_dummies(pd.cut(values, bins))
```

---

## 7.3 Extension Data Types

pandas originally relied on NumPy, which had shortcomings:
- Missing data for integers/Booleans forced conversion to `float64` with `np.nan`
- String data was computationally expensive and memory-intensive
- Some types (time intervals, time zones) required expensive Python object arrays

**Extension types** solve these issues, allowing new data types alongside NumPy arrays.

```python
# Legacy behavior: integers with NA → float64
s = pd.Series([1, 2, 3, None])
s.dtype  # dtype('float64')

# Extension type: nullable integers
s = pd.Series([1, 2, 3, None], dtype=pd.Int64Dtype())
# or shorthand:
s = pd.Series([1, 2, 3, None], dtype="Int64")
# 0       1
# 1       2
# 2       3
# 3    <NA>
# dtype: Int64

s[3] is pd.NA  # True

# Extension type for strings (requires pyarrow)
s = pd.Series(['one', 'two', None, 'three'], dtype=pd.StringDtype())
```

### Converting Columns with `astype`

```python
df = pd.DataFrame({"A": [1, 2, None, 4],
                   "B": ["one", "two", "three", None],
                   "C": [False, None, False, True]})

df["A"] = df["A"].astype("Int64")
df["B"] = df["B"].astype("string")
df["C"] = df["C"].astype("boolean")
```

### Table: pandas Extension Data Types

| Extension Type | String Alias | Description |
|---------------|-------------|-------------|
| `BooleanDtype` | `"boolean"` | Nullable Boolean |
| `CategoricalDtype` | `"category"` | Categorical data |
| `DatetimeTZDtype` | — | Datetime with time zone |
| `Float32Dtype` | `"Float32"` | 32-bit nullable float |
| `Float64Dtype` | `"Float64"` | 64-bit nullable float |
| `Int8Dtype` – `Int64Dtype` | `"Int8"` – `"Int64"` | Nullable signed integers |
| `UInt8Dtype` – `UInt64Dtype` | `"UInt8"` – `"UInt64"` | Nullable unsigned integers |

> ⚠️ Capitalization matters: `"Int64"` (extension) vs `"int64"` (NumPy).

---

## 7.4 String Manipulation

### Python Built-In String Methods

```python
val = "a,b,  guido"

val.split(",")              # ['a', 'b', '  guido']
[x.strip() for x in val.split(",")]  # ['a', 'b', 'guido']
"::".join(pieces)           # 'a::b::guido'

"guido" in val              # True (substring detection)
val.index(",")              # 1
val.find(":")               # -1 (returns -1 if not found)
val.index(":")              # ValueError (raises if not found)
val.count(",")              # 2
val.replace(",", "::")      # 'a::b::  guido'
val.replace(",", "")        # 'ab  guido'
```

### Table: Python String Methods

| Method | Description |
|--------|-------------|
| `count` | Number of nonoverlapping occurrences |
| `endswith` / `startswith` | Check suffix/prefix |
| `join` | Concatenate sequence with delimiter |
| `index` / `find` / `rfind` | Locate substring |
| `replace` | Substitute pattern |
| `strip` / `rstrip` / `lstrip` | Trim whitespace |
| `split` | Break into substrings |
| `lower` / `upper` / `casefold` | Case conversion |
| `ljust` / `rjust` | Pad to minimum width |

### Regular Expressions

```python
import re

text = "foo    bar\t baz  \tqux"

# Split on whitespace
re.split(r"\s+", text)  # ['foo', 'bar', 'baz', 'qux']

# Compile for reuse (saves CPU cycles)
regex = re.compile(r"\s+")
regex.split(text)
regex.findall(text)  # All matches: ['    ', '\t ', '  \t']

# Email pattern
pattern = r"[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}"
regex = re.compile(pattern, flags=re.IGNORECASE)
regex.findall(text)  # List of all email addresses

# search → first match anywhere
m = regex.search(text)
text[m.start():m.end()]

# match → only at start of string
regex.match(text)  # None

# sub → replace all matches
print(regex.sub("REDACTED", text))

# Groups: segment pattern components
pattern = r"([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})"
regex = re.compile(pattern, flags=re.IGNORECASE)

m = regex.match("wesm@bright.net")
m.groups()  # ('wesm', 'bright', 'net')

regex.findall(text)
# [('dave', 'google', 'com'), ('steve', 'gmail', 'com'), ...]

# Reference groups in replacement
print(regex.sub(r"Username: \1, Domain: \2, Suffix: \3", text))
```

### Table: Regular Expression Methods

| Method | Description |
|--------|-------------|
| `findall` | All nonoverlapping matches as a list |
| `finditer` | Like `findall`, returns iterator |
| `match` | Match at start of string only |
| `search` | Match anywhere in string |
| `split` | Break string at each pattern occurrence |
| `sub` / `subn` | Replace all (`sub`) or first `n` (`subn`) occurrences |

### String Functions in pandas

Series has vectorized string methods via the `str` attribute that skip NA values.

```python
data = pd.Series({"Dave": "dave@google.com", "Steve": "steve@gmail.com",
                  "Rob": "rob@gmail.com", "Wes": np.nan})

# Vectorized string operations
data.str.contains("gmail")
# Dave     False
# Steve     True
# Rob       True
# Wes        NaN

# Use extension types for proper boolean output
data_as_string_ext = data.astype('string')
data_as_string_ext.str.contains("gmail")
# Wes → <NA> (boolean dtype) instead of NaN (object dtype)

# Regex with findall
pattern = r"([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})"
data.str.findall(pattern, flags=re.IGNORECASE)

# Vectorized element retrieval
matches = data.str.findall(pattern, flags=re.IGNORECASE).str[0]
matches.str.get(1)  # Extract domain

# Slicing
data.str[:5]

# Extract captured groups as DataFrame
data.str.extract(pattern, flags=re.IGNORECASE)
#            0       1    2
# Dave    dave  google  com
# Steve  steve   gmail  com
# Rob      rob   gmail  com
# Wes      NaN     NaN  NaN
```

### Table: Key Series String Methods (`str.*`)

| Method | Description |
|--------|-------------|
| `cat` | Concatenate with delimiter |
| `contains` | Boolean array if pattern found |
| `count` | Count pattern occurrences |
| `extract` | Extract regex groups → DataFrame |
| `endswith` / `startswith` | Check suffix/prefix |
| `findall` | List of all pattern matches |
| `get` | Index into each element |
| `join` | Join strings with separator |
| `len` | Length of each string |
| `lower` / `upper` | Case conversion |
| `match` | `re.match` on each element |
| `replace` | Replace pattern with string |
| `slice` | Slice each string |
| `split` | Split on delimiter/regex |
| `strip` / `rstrip` / `lstrip` | Trim whitespace |

---

## 7.5 Categorical Data

### Background and Motivation

Categorical (dictionary-encoded) representation stores distinct values once and references them with integer codes. This yields:
- **Significant memory savings** (especially for string data with many repeats)
- **Faster computation** (integer operations vs. string comparisons)
- **Efficient category transformations** (renaming, appending)

```python
# Dimension table pattern
values = pd.Series([0, 1, 0, 0] * 2)
dim = pd.Series(['apple', 'orange'])
dim.take(values)  # Restores original strings
```

### Categorical Extension Type

```python
fruits = ['apple', 'orange', 'apple', 'apple'] * 2
df = pd.DataFrame({'fruit': fruits, 'basket_id': np.arange(8),
                   'count': [11, 5, 12, 6, 5, 12, 10, 11],
                   'weight': [1.56, 1.33, 2.39, 0.75, 2.69, 3.77, 0.99, 3.80]})

# Convert to categorical
df['fruit'] = df['fruit'].astype('category')

# Access underlying Categorical object
c = df['fruit'].array
c.categories  # Index(['apple', 'orange'])
c.codes       # array([0, 1, 0, 0, 0, 1, 0, 0])

# Mapping between codes and categories
dict(enumerate(c.categories))  # {0: 'apple', 1: 'orange'}

# Create directly
my_categories = pd.Categorical(['foo', 'bar', 'baz', 'foo', 'bar'])

# From codes (e.g., from external source)
categories = ['foo', 'bar', 'baz']
codes = [0, 1, 2, 0, 0, 1]
my_cats_2 = pd.Categorical.from_codes(codes, categories)

# Ordered categories
ordered_cat = pd.Categorical.from_codes(codes, categories, ordered=True)
# ['foo' < 'bar' < 'baz']
my_cats_2.as_ordered()
```

### Computations with Categoricals

`pd.qcut` returns a `Categorical`:

```python
draws = rng.standard_normal(1000)
bins = pd.qcut(draws, 4, labels=['Q1', 'Q2', 'Q3', 'Q4'])
bins.codes[:10]  # array([0, 3, 0, 1, 1, ...])

# Extract summary statistics per quartile
bins = pd.Series(bins, name='quartile')
results = (pd.Series(draws)
           .groupby(bins)
           .agg(['count', 'min', 'max'])
           .reset_index())
```

#### Performance Benefits

```python
N = 10_000_000
labels = pd.Series(['foo', 'bar', 'baz', 'qux'] * (N // 4))
categories = labels.astype('category')

# Memory comparison
labels.memory_usage(deep=True)      # ~600 MB
categories.memory_usage(deep=True)  # ~10 MB

# value_counts speedup
labels.value_counts()      # ~331 ms
categories.value_counts()  # ~16 ms (20x faster)
```

### Categorical Methods

Accessed via the `cat` accessor:

```python
s = pd.Series(['a', 'b', 'c', 'd'] * 2)
cat_s = s.astype('category')

cat_s.cat.codes
cat_s.cat.categories

# Extend categories (data unchanged, but reflected in operations)
actual_categories = ['a', 'b', 'c', 'd', 'e']
cat_s2 = cat_s.cat.set_categories(actual_categories)
cat_s2.value_counts()  # Shows 'e' with count 0

# Remove unobserved categories after filtering
cat_s3 = cat_s[cat_s.isin(['a', 'b'])]
cat_s3.cat.remove_unused_categories()
```

### Table: Categorical Methods

| Method | Description |
|--------|-------------|
| `add_categories` | Append new (unused) categories |
| `as_ordered` / `as_unordered` | Toggle ordering |
| `remove_categories` | Remove categories (set values to null) |
| `remove_unused_categories` | Trim categories not present in data |
| `rename_categories` | Replace category names (same count) |
| `reorder_categories` | Reorder and optionally set ordering |
| `set_categories` | Replace categories entirely (add/remove) |

### Creating Dummy Variables for Modeling

```python
cat_s = pd.Series(['a', 'b', 'c', 'd'] * 2, dtype='category')
pd.get_dummies(cat_s, dtype=float)
```

---

## 7.6 Conclusion

Effective data preparation significantly improves productivity by enabling more time analyzing data and less time getting it ready. Key tools covered:

1. **Missing data**: `dropna`, `fillna`, `isna`/`notna`
2. **Duplicates**: `duplicated`, `drop_duplicates`
3. **Transformations**: `map`, `replace`, `rename`
4. **Binning**: `pd.cut` (equal-length), `pd.qcut` (equal-size)
5. **Outliers**: Boolean filtering, value capping with `np.sign`
6. **Sampling**: `sample`, `np.random.permutation`, `take`
7. **Dummy variables**: `pd.get_dummies`, `str.get_dummies`
8. **Extension types**: Nullable `Int64`, `string`, `boolean`
9. **String manipulation**: `str.*` vectorized methods, regex
10. **Categorical data**: Memory-efficient encoding with `category` dtype
