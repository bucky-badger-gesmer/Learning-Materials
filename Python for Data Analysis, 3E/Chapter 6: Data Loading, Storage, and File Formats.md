# Chapter 6: Data Loading, Storage, and File Formats

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/accessing-data](https://wesmckinney.com/book/accessing-data)

---

## Table of Contents

- [6.1 Reading and Writing Data in Text Format](#61-reading-and-writing-data-in-text-format)
  - [Basic CSV Reading](#basic-csv-reading)
  - [Handling Missing Values](#handling-missing-values)
  - [Reading Text Files in Pieces](#reading-text-files-in-pieces)
  - [Writing Data to Text Format](#writing-data-to-text-format)
  - [Working with Other Delimited Formats (Python `csv` Module)](#working-with-other-delimited-formats-python-csv-module)
  - [JSON Data](#json-data)
  - [XML and HTML: Web Scraping](#xml-and-html-web-scraping)
- [6.2 Binary Data Formats](#62-binary-data-formats)
  - [Pickle](#pickle)
  - [Reading Microsoft Excel Files](#reading-microsoft-excel-files)
  - [Using HDF5 Format](#using-hdf5-format)
- [6.3 Interacting with Web APIs](#63-interacting-with-web-apis)
- [6.4 Interacting with Databases](#64-interacting-with-databases)
- [6.5 Conclusion](#65-conclusion)
- [Quick Reference: Choosing the Right Tool](#quick-reference-choosing-the-right-tool)

---

## Overview

Reading data and making it accessible (often called **data loading** or **parsing**) is the necessary first step for any data analysis workflow. This chapter covers data input and output using pandas across three main categories:

1. **Text files** and other efficient on-disk formats (CSV, JSON, XML, HTML, Excel, HDF5, Parquet)
2. **Databases** (SQL queries, SQLAlchemy)
3. **Network sources** (web APIs via HTTP/JSON)

---

## 6.1 Reading and Writing Data in Text Format

pandas provides a rich set of functions for reading tabular data into `DataFrame` objects. The most frequently used is `pd.read_csv`.

### Table: Text and Binary Data Loading Functions in pandas

| Function | Description |
|---|---|
| `read_csv` | Load delimited data from a file, URL, or file-like object; comma is the default delimiter |
| `read_fwf` | Read data in fixed-width column format (no delimiters) |
| `read_clipboard` | Variation of `read_csv` that reads from the clipboard; useful for tables from web pages |
| `read_excel` | Read tabular data from an Excel XLS or XLSX file |
| `read_hdf` | Read HDF5 files written by pandas |
| `read_html` | Read all tables found in the given HTML document |
| `read_json` | Read data from a JSON string, file, URL, or file-like object |
| `read_feather` | Read the Feather binary file format |
| `read_orc` | Read the Apache ORC binary file format |
| `read_parquet` | Read the Apache Parquet binary file format |
| `read_pickle` | Read an object stored by pandas using the Python pickle format |
| `read_sas` | Read a SAS dataset stored in one of the SAS system's custom storage formats |
| `read_spss` | Read a data file created by SPSS |
| `read_sql` | Read the results of a SQL query (using SQLAlchemy) |
| `read_sql_table` | Read a whole SQL table (using SQLAlchemy) |
| `read_stata` | Read a dataset from Stata file format |
| `read_xml` | Read a table of data from an XML file |

### Key Concepts

The optional arguments for these functions fall into several categories:

- **Indexing** — Treat one or more columns as the DataFrame index; control whether column names come from the file, user arguments, or are auto-generated.
- **Type inference and data conversion** — Automatic detection of numeric, integer, Boolean, and string types, plus user-defined value conversions and custom missing value markers.
- **Date and time parsing** — Combine date/time information spread across multiple columns into a single column.
- **Iterating** — Support for reading very large files in chunks.
- **Unclean data issues** — Skip rows or footers, handle comments, deal with thousands separators, etc.

> **Note:** `pandas.read_csv` has around 50 optional parameters. This is normal — real-world data is messy. The pandas documentation has many examples, so if you're struggling to read a particular file, look for a similar example.

> **Note on type inference:** Text-based formats (CSV, etc.) do not embed data type information, so pandas must infer it. Binary formats like HDF5, ORC, and Parquet have data type information embedded in the format.

---

### Basic CSV Reading

**Reading a standard CSV with a header row:**

```python
import pandas as pd

df = pd.read_csv("examples/ex1.csv")
#    a   b   c   d message
# 0  1   2   3   4   hello
# 1  5   6   7   8   world
# 2  9  10  11  12     foo
```

**Reading a CSV without a header row:**

```python
# Option 1: Let pandas assign default column names (0, 1, 2, ...)
pd.read_csv("examples/ex2.csv", header=None)

# Option 2: Specify column names explicitly
pd.read_csv("examples/ex2.csv", names=["a", "b", "c", "d", "message"])
```

**Setting a column as the index:**

```python
names = ["a", "b", "c", "d", "message"]
pd.read_csv("examples/ex2.csv", names=names, index_col="message")
#          a   b   c   d
# message               
# hello    1   2   3   4
# world    5   6   7   8
# foo      9  10  11  12
```

**Creating a hierarchical (MultiIndex) from multiple columns:**

```python
parsed = pd.read_csv("examples/csv_mindex.csv",
                     index_col=["key1", "key2"])
#            value1  value2
# key1 key2                
# one  a          1       2
#      b          3       4
# two  a          9      10
#      b         11      12
```

**Using a regex as a delimiter (for variable whitespace):**

```python
result = pd.read_csv("examples/ex3.txt", sep="\s+")
#            A         B         C
# aaa -0.264438 -1.026059 -0.619500
# bbb  0.927272  0.302904 -0.032399
```

> When there is one fewer column name than data columns, pandas infers that the first column should be the DataFrame's index.

**Skipping specific rows:**

```python
pd.read_csv("examples/ex4.csv", skiprows=[0, 2, 3])
```

---

### Handling Missing Values

Missing data is usually either not present (empty string) or marked by a **sentinel** (placeholder) value. By default, pandas recognizes common sentinels like `NA` and `NULL`.

```python
result = pd.read_csv("examples/ex5.csv")
#   something  a   b     c   d message
# 0       one  1   2   3.0   4     NaN
# 1       two  5   6   NaN   8   world
# 2     three  9  10  11.0  12     foo
```

**Adding custom NA sentinels:**

```python
result = pd.read_csv("examples/ex5.csv", na_values=["NULL"])
```

**Disabling default NA detection entirely:**

```python
result2 = pd.read_csv("examples/ex5.csv", keep_default_na=False)
# Now "NA" is read as the literal string "NA", not as NaN
```

**Combining `keep_default_na=False` with custom sentinels:**

```python
result3 = pd.read_csv("examples/ex5.csv", keep_default_na=False,
                      na_values=["NA"])
```

**Specifying different NA sentinels per column (using a dictionary):**

```python
sentinels = {"message": ["foo", "NA"], "something": ["two"]}
pd.read_csv("examples/ex5.csv", na_values=sentinels,
            keep_default_na=False)
```

---

### Table: Key `pandas.read_csv` Arguments

| Argument | Description |
|---|---|
| `path` | String indicating filesystem location, URL, or file-like object |
| `sep` / `delimiter` | Character sequence or regex to split fields in each row |
| `header` | Row number to use as column names; defaults to `0` (first row), use `None` if no header |
| `index_col` | Column numbers or names to use as the row index; pass a list for a hierarchical index |
| `names` | List of column names for the result |
| `skiprows` | Number of rows at the beginning to ignore, or a list of row numbers (0-indexed) to skip |
| `na_values` | Sequence of values to replace with NA; added to defaults unless `keep_default_na=False` |
| `keep_default_na` | Whether to use the default NA value list (`True` by default) |
| `comment` | Character(s) to split comments off the end of lines |
| `parse_dates` | Attempt to parse data to `datetime`; `False` by default. If `True`, tries all columns. Can specify a list of column numbers/names. If an element is a tuple/list, combines multiple columns into one date. |
| `keep_date_col` | If joining columns to parse date, keep the joined columns (`False` by default) |
| `converters` | Dict mapping column number/name to functions (e.g., `{"foo": f}` applies `f` to all `"foo"` values) |
| `dayfirst` | Treat ambiguous dates as international format (e.g., 7/6/2012 → June 7, 2012); `False` by default |
| `date_parser` | Function to use for parsing dates |
| `nrows` | Number of rows to read from the beginning (not counting the header) |
| `iterator` | Return a `TextFileReader` object for reading the file piecemeal |
| `chunksize` | For iteration, size of file chunks |
| `skip_footer` | Number of lines to ignore at the end of the file |
| `verbose` | Print parsing information (time spent, memory use) |
| `encoding` | Text encoding (e.g., `"utf-8"`); defaults to `"utf-8"` if `None` |
| `squeeze` | If parsed data has only one column, return a `Series` |
| `thousands` | Separator for thousands (e.g., `","` or `"."`); default is `None` |
| `decimal` | Decimal separator in numbers (e.g., `"."` or `","`); default is `"."` |
| `engine` | CSV parsing engine: `"c"` (default, fastest), `"python"` (slower, more features), or `"pyarrow"` (can be much faster for some files) |

---

### Reading Text Files in Pieces

When processing very large files, you can read only a portion or iterate through smaller chunks.

**Reading a limited number of rows:**

```python
pd.read_csv("examples/ex6.csv", nrows=5)
```

**Reading a file in chunks with `chunksize`:**

```python
chunker = pd.read_csv("examples/ex6.csv", chunksize=1000)
type(chunker)  # pandas.io.parsers.readers.TextFileReader
```

The `TextFileReader` object allows iteration over the file parts. A common pattern is to aggregate results across chunks:

```python
chunker = pd.read_csv("examples/ex6.csv", chunksize=1000)

tot = pd.Series([], dtype='int64')
for piece in chunker:
    tot = tot.add(piece["key"].value_counts(), fill_value=0)

tot = tot.sort_values(ascending=False)
```

`TextFileReader` also has a `get_chunk()` method to read pieces of arbitrary size.

---

### Writing Data to Text Format

Use the DataFrame's `to_csv()` method to export data:

```python
data = pd.read_csv("examples/ex5.csv")
data.to_csv("examples/out.csv")
```

**Using a different delimiter:**

```python
import sys
data.to_csv(sys.stdout, sep="|")
# |something|a|b|c|d|message
# 0|one|1|2|3.0|4|
```

**Customizing missing value representation:**

```python
data.to_csv(sys.stdout, na_rep="NULL")
```

**Omitting row and column labels:**

```python
data.to_csv(sys.stdout, index=False, header=False)
# one,1,2,3.0,4,
# two,5,6,,8,world
# three,9,10,11.0,12,foo
```

**Writing a subset of columns in a specific order:**

```python
data.to_csv(sys.stdout, index=False, columns=["a", "b", "c"])
```

---

### Working with Other Delimited Formats (Python `csv` Module)

For files with single-character delimiters, Python's built-in `csv` module is available:

```python
import csv

with open("examples/ex7.csv") as f:
    reader = csv.reader(f)
    for line in reader:
        print(line)
# ['a', 'b', 'c']
# ['1', '2', '3']
# ['1', '2', '3']
```

**Manual CSV parsing into a dictionary:**

```python
with open("examples/ex7.csv") as f:
    lines = list(csv.reader(f))

header, values = lines[0], lines[1:]
data_dict = {h: v for h, v in zip(header, zip(*values))}
# {'a': ('1', '1'), 'b': ('2', '2'), 'c': ('3', '3')}
```

> **Warning:** The `zip(*values)` transposition uses a lot of memory on large files.

**Defining a custom CSV dialect:**

```python
class my_dialect(csv.Dialect):
    lineterminator = "\n"
    delimiter = ";"
    quotechar = '"'
    quoting = csv.QUOTE_MINIMAL

reader = csv.reader(f, dialect=my_dialect)
```

Or pass parameters directly as keywords:

```python
reader = csv.reader(f, delimiter="|")
```

### Table: CSV `dialect` Options

| Argument | Description |
|---|---|
| `delimiter` | One-character string to separate fields; defaults to `","` |
| `lineterminator` | Line terminator for writing; defaults to `"\r\n"`. Reader ignores this and recognizes cross-platform terminators. |
| `quotechar` | Quote character for fields with special characters; default is `'"'` |
| `quoting` | Quoting convention: `csv.QUOTE_ALL`, `csv.QUOTE_MINIMAL`, `csv.QUOTE_NONNUMERIC`, or `csv.QUOTE_NONE`. Defaults to `QUOTE_MINIMAL`. |
| `skipinitialspace` | Ignore whitespace after each delimiter; default is `False` |
| `doublequote` | How to handle quoting character inside a field; if `True`, it is doubled |
| `escapechar` | String to escape the delimiter if `quoting` is `csv.QUOTE_NONE`; disabled by default |

> **Note:** For files with complicated or fixed multicharacter delimiters, you cannot use the `csv` module. Use string `split()` or `re.split()` instead. In practice, `pandas.read_csv` handles almost everything you need.

**Writing delimited files manually with `csv.writer`:**

```python
with open("mydata.csv", "w") as f:
    writer = csv.writer(f, dialect=my_dialect)
    writer.writerow(("one", "two", "three"))
    writer.writerow(("1", "2", "3"))
```

---

### JSON Data

JSON (JavaScript Object Notation) is a standard format for sending data via HTTP. It is more free-form than tabular text formats like CSV.

**JSON basics:**
- Objects → Python dictionaries
- Arrays → Python lists
- Strings, numbers, Booleans, and nulls
- All keys in an object must be strings
- JSON's `null` maps to Python's `None`

**Parsing JSON with the built-in `json` module:**

```python
import json

obj = """
{"name": "Wes",
 "cities_lived": ["Akron", "Nashville", "New York", "San Francisco"],
 "pet": null,
 "siblings": [{"name": "Scott", "age": 34, "hobbies": ["guitars", "soccer"]},
              {"name": "Katie", "age": 42, "hobbies": ["diving", "art"]}]
}
"""

# JSON string → Python object
result = json.loads(obj)

# Python object → JSON string
asjson = json.dumps(result)
```

**Converting JSON data to a DataFrame:**

```python
# Pass a list of dictionaries to the DataFrame constructor
siblings = pd.DataFrame(result["siblings"], columns=["name", "age"])
#     name  age
# 0  Scott   34
# 1  Katie   42
```

**Using `pd.read_json` for JSON files:**

```python
data = pd.read_json("examples/example.json")
#    a  b  c
# 0  1  2  3
# 1  4  5  6
# 2  7  8  9
```

**Exporting pandas data to JSON:**

```python
# Default orientation (column-oriented)
data.to_json(sys.stdout)
# {"a":{"0":1,"1":4,"2":7},"b":{"0":2,"1":5,"2":8},"c":{"0":3,"1":6,"2":9}}

# Row-oriented (records)
data.to_json(sys.stdout, orient="records")
# [{"a":1,"b":2,"c":3},{"a":4,"b":5,"c":6},{"a":7,"b":8,"c":9}]
```

---

### XML and HTML: Web Scraping

Python has many libraries for reading/writing HTML and XML: **lxml** (fastest), **Beautiful Soup** (handles malformed HTML well), and **html5lib**.

```bash
conda install lxml beautifulsoup4 html5lib
```

#### `pd.read_html` — Parsing HTML Tables

`pd.read_html` automatically parses all `<table>` tags in an HTML document and returns a **list of DataFrames**:

```python
tables = pd.read_html("examples/fdic_failed_bank_list.html")
len(tables)  # 1

failures = tables[0]
failures.head()
#                       Bank Name       City  ST   CERT  ...
# 0                   Allied Bank   Mulberry  AR     91  ...
# 1  The Woodbury Banking Company   Woodbury  GA  11297  ...
```

**Example analysis — bank failures by year:**

```python
close_timestamps = pd.to_datetime(failures["Closing Date"])
close_timestamps.dt.year.value_counts()
# Closing Date
# 2010    157
# 2009    140
# 2011     92
# 2012     51
# 2008     25
# ...
```

#### Parsing XML with `lxml.objectify`

For more general XML documents, `lxml.objectify` provides a programmatic way to parse XML:

```python
from lxml import objectify

path = "datasets/mta_perf/Performance_MNR.xml"

with open(path) as f:
    parsed = objectify.parse(f)

root = parsed.getroot()

# Iterate over each <INDICATOR> element and build a list of dicts
data = []
skip_fields = ["PARENT_SEQ", "INDICATOR_SEQ",
               "DESIRED_CHANGE", "DECIMAL_PLACES"]

for elt in root.INDICATOR:
    el_data = {}
    for child in elt.getchildren():
        if child.tag in skip_fields:
            continue
        el_data[child.tag] = child.pyval
    data.append(el_data)

# Convert to DataFrame
perf = pd.DataFrame(data)
```

**Simpler approach with `pd.read_xml`:**

```python
perf2 = pd.read_xml(path)
```

> For complex XML documents, refer to the `pd.read_xml` docstring for selections and filters to extract specific tables.

---

## 6.2 Binary Data Formats

### Pickle

Python's built-in `pickle` module provides a simple way to serialize data in binary format.

```python
# Write
frame.to_pickle("examples/frame_pickle")

# Read
pd.read_pickle("examples/frame_pickle")
```

> ⚠️ **Caution — Pickle stability:** `pickle` is recommended **only as a short-term storage format**. The format is not guaranteed to be stable over time; an object pickled today may not unpickle with a later version of a library. Pandas has tried to maintain backward compatibility, but the pickle format may break in the future.

### Other Binary Formats

pandas supports several open-source binary formats:

```python
# Parquet (requires pyarrow: conda install pyarrow)
fec = pd.read_parquet('datasets/fec/fec.parquet')

# Also available: read_feather, read_orc
```

---

### Reading Microsoft Excel Files

pandas supports Excel 2003+ files via `pd.ExcelFile` or `pd.read_excel`. These use `xlrd` (for old `.xls`) and `openpyxl` (for newer `.xlsx`) under the hood.

```bash
conda install openpyxl xlrd
```

**Using `ExcelFile` (faster for reading multiple sheets):**

```python
xlsx = pd.ExcelFile("examples/ex1.xlsx")
xlsx.sheet_names  # ['Sheet1']

# Parse a specific sheet
df = xlsx.parse(sheet_name="Sheet1")

# Handle an index column
df = xlsx.parse(sheet_name="Sheet1", index_col=0)
```

**Using `read_excel` directly (simpler for single sheets):**

```python
frame = pd.read_excel("examples/ex1.xlsx", sheet_name="Sheet1")
```

**Writing to Excel:**

```python
# Using ExcelWriter (required for multiple sheets)
writer = pd.ExcelWriter("examples/ex2.xlsx")
frame.to_excel(writer, "Sheet1")
writer.close()

# Simpler: pass file path directly
frame.to_excel("examples/ex2.xlsx")
```

---

### Using HDF5 Format

**HDF5** (Hierarchical Data Format version 5) is designed for storing large quantities of scientific array data. Key features:

- Each HDF5 file can store **multiple datasets** and supporting metadata
- Supports **on-the-fly compression** with various compression modes
- Good for datasets that **don't fit into memory** — you can efficiently read/write small sections of much larger arrays
- Available as a C library with interfaces in Java, Julia, MATLAB, and Python

```bash
conda install pytables
# or: pip install tables
```

**Using `HDFStore` (dictionary-like interface):**

```python
import numpy as np

frame = pd.DataFrame({"a": np.random.standard_normal(100)})

store = pd.HDFStore("examples/mydata.h5")
store["obj1"] = frame
store["obj1_col"] = frame["a"]

# View contents
store
# <class 'pandas.io.pytables.HDFStore'>
# File path: examples/mydata.h5

# Retrieve objects
store["obj1"]
```

**Two storage schemas: `"fixed"` vs `"table"`:**

| Schema | Speed | Query Support |
|---|---|---|
| `"fixed"` (default) | Faster | No |
| `"table"` | Slower | Yes — supports `where` queries |

```python
# Store with "table" format for query support
store.put("obj2", frame, format="table")

# Query using SQL-like syntax
store.select("obj2", where=["index >= 10 and index <= 15"])

store.close()
```

**Shortcut with `to_hdf` / `read_hdf`:**

```python
frame.to_hdf("examples/mydata.h5", "obj3", format="table")
pd.read_hdf("examples/mydata.h5", "obj3", where=["index < 5"])
```

> **Note:** For data stored on remote servers (Amazon S3, HDFS), consider **Apache Parquet** instead — it is designed for distributed storage.

> ⚠️ **Caution — HDF5 is not a database:** It is best suited for **write-once, read-many** datasets. While data can be added to a file at any time, if **multiple writers** write simultaneously, the file can become **corrupted**.

---

## 6.3 Interacting with Web APIs

Many websites provide public APIs delivering data via JSON. The recommended approach is the `requests` package.

```bash
conda install requests
```

### Basic Pattern for API Requests

```python
import requests

url = "https://api.github.com/repos/pandas-dev/pandas/issues"

# Make GET request
resp = requests.get(url)

# Always check for HTTP errors
resp.raise_for_status()

# Parse JSON response into Python objects (dict/list)
data = resp.json()

# Inspect
data[0]["title"]
```

### Converting API Response to DataFrame

```python
issues = pd.DataFrame(data, columns=["number", "title", "labels", "state"])
#     number                                              title  labels  state
# 0    52629  BUG: DataFrame.pivot mutates empty index.nam...     [...]   open
# 1    52628  DEPR: unused keywords in DTI/TDI construtors       []   open
# ...
```

> **Best practice:** Always call `resp.raise_for_status()` after `requests.get()` to check for HTTP errors.

> **Note:** API results are real-time data, so output will differ each time you run the code.

---

## 6.4 Interacting with Databases

SQL-based relational databases (SQL Server, PostgreSQL, MySQL) are widely used in business settings. pandas provides functions to simplify loading SQL query results into DataFrames.

### Using SQLite3 Directly

```python
import sqlite3

# Create a database and table
query = """
CREATE TABLE test
(a VARCHAR(20), b VARCHAR(20),
 c REAL,        d INTEGER
);"""

con = sqlite3.connect("mydata.sqlite")
con.execute(query)
con.commit()

# Insert data
data = [("Atlanta", "Georgia", 1.25, 6),
        ("Tallahassee", "Florida", 2.6, 3),
        ("Sacramento", "California", 1.7, 5)]

stmt = "INSERT INTO test VALUES(?, ?, ?, ?)"
con.executemany(stmt, data)
con.commit()

# Query data
cursor = con.execute("SELECT * FROM test")
rows = cursor.fetchall()
# [('Atlanta', 'Georgia', 1.25, 6),
#  ('Tallahassee', 'Florida', 2.6, 3),
#  ('Sacramento', 'California', 1.7, 5)]

# Get column names from cursor.description
cursor.description
# (('a', None, None, None, None, None, None),
#  ('b', None, None, None, None, None, None),
#  ('c', None, None, None, None, None, None),
#  ('d', None, None, None, None, None, None))

# Build DataFrame manually
pd.DataFrame(rows, columns=[x[0] for x in cursor.description])
#              a           b     c  d
# 0      Atlanta     Georgia  1.25  6
# 1  Tallahassee     Florida  2.60  3
# 2   Sacramento  California  1.70  5
```

### Using SQLAlchemy (Recommended)

SQLAlchemy abstracts away differences between SQL databases. pandas has a `read_sql` function that works with SQLAlchemy connections.

```bash
conda install sqlalchemy
```

```python
import sqlalchemy as sqla

# Create engine (SQLite in this example)
db = sqla.create_engine("sqlite:///mydata.sqlite")

# Read directly into a DataFrame — much simpler!
pd.read_sql("SELECT * FROM test", db)
#              a           b     c  d
# 0      Atlanta     Georgia  1.25  6
# 1  Tallahassee     Florida  2.60  3
# 2   Sacramento  California  1.70  5
```

> `pd.read_sql()` is the recommended approach for database interaction with pandas. It works with any SQLAlchemy-supported database (PostgreSQL, MySQL, SQL Server, etc.).

---

## 6.5 Conclusion

Getting access to data is the first step in any data analysis process. This chapter covered:

| Category | Key Tools |
|---|---|
| **Text files** | `read_csv`, `read_json`, `read_xml`, `read_html`, `to_csv`, `to_json` |
| **Binary formats** | Pickle, Excel (`read_excel`/`to_excel`), HDF5 (`HDFStore`/`read_hdf`), Parquet (`read_parquet`) |
| **Web APIs** | `requests` library + `json` parsing → `pd.DataFrame` |
| **Databases** | `sqlite3` (manual), SQLAlchemy + `pd.read_sql` (recommended) |

The upcoming chapters build on these foundations with deeper coverage of data wrangling, visualization, time series analysis, and more.

---

## Quick Reference: Choosing the Right Tool

| Scenario | Recommended Function |
|---|---|
| CSV or delimited text | `pd.read_csv()` |
| Fixed-width text | `pd.read_fwf()` |
| Excel file | `pd.read_excel()` |
| JSON file/URL | `pd.read_json()` or `json.loads()` + `pd.DataFrame()` |
| HTML tables | `pd.read_html()` |
| XML file | `pd.read_xml()` or `lxml.objectify` |
| HDF5 file | `pd.read_hdf()` or `pd.HDFStore` |
| Parquet file | `pd.read_parquet()` |
| Pickle file | `pd.read_pickle()` |
| SQL query | `pd.read_sql()` with SQLAlchemy |
| Web API (JSON) | `requests.get()` + `.json()` + `pd.DataFrame()` |
| Large files (memory constrained) | `pd.read_csv(chunksize=...)` |
