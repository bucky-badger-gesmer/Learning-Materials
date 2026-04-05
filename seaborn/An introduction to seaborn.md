# An Introduction to Seaborn

> **Source:** seaborn — [seaborn.pydata.org/tutorial/introduction](https://seaborn.pydata.org/tutorial/introduction.html)

---

## Table of Contents

- [What is Seaborn?](#what-is-seaborn)
- [The Declarative API](#the-declarative-api)
- [Getting Started](#getting-started)
- [Key Concepts](#key-concepts)
- [A High-Level API for Statistical Graphics](#a-high-level-api-for-statistical-graphics)
- [Multivariate Views on Complex Datasets](#multivariate-views-on-complex-datasets)
- [Opinionated Defaults and Flexible Customization](#opinionated-defaults-and-flexible-customization)
- [Relationship to Matplotlib](#relationship-to-matplotlib)
- [Function Quick Reference](#function-quick-reference)
- [Best Practices](#best-practices)
- [Next Steps](#next-steps)

---

## What is Seaborn?

Seaborn is a Python library for creating **statistical graphics**. It sits on top of [matplotlib](https://matplotlib.org/) and integrates closely with [pandas](https://pandas.pydata.org/) data structures.

### Core Philosophy

Seaborn is designed to help you **explore and understand your data**. Its plotting functions operate on entire dataframes or arrays and internally perform:

- **Semantic mapping** — translating data values into visual properties (color, size, shape)
- **Statistical aggregation** — computing summaries like means, confidence intervals, and distributions

The key insight: you specify **what the different elements of your plots mean**, rather than the low-level details of how to draw them.

### Standard Import Convention

```python
import seaborn as sns
```

By convention, seaborn is always imported with the shorthand `sns`.

---

## The Declarative API

Seaborn uses a **dataset-oriented, declarative API**. This is the most important concept to understand when learning seaborn.

### Imperative vs. Declarative

| Approach | You Specify | Example |
|----------|-------------|---------|
| **Imperative (matplotlib)** | How to draw each element | "Draw a circle at pixel (150, 200) with radius 5 and color `#1f77b4`" |
| **Declarative (seaborn)** | What data maps to what role | "Map the `species` column to color, `bill_length_mm` to x-axis" |

### How It Works

When you call a seaborn function, you provide:

1. **A dataframe** — the source of all your data
2. **Variable names** — column names assigned to visual roles (`x`, `y`, `hue`, `size`, `style`)
3. **The function** — determines the type of visualization

Behind the scenes, seaborn:
- Looks up the column values in the dataframe
- Decides appropriate visual encodings (colors, markers, line styles)
- Calls matplotlib with the correct low-level arguments
- Adds axis labels and legends automatically

```python
# Declarative: you say WHAT, seaborn figures out HOW
sns.relplot(
    data=tips,
    x="total_bill", y="tip", col="time",
    hue="smoker", style="smoker", size="size",
)
```

This single call encodes **five variables** in one figure — without specifying a single color value or marker code.

---

## Getting Started

### Installation

```bash
pip install seaborn
```

### Basic Setup

```python
import seaborn as sns

# Apply the default theme (affects all matplotlib plots)
sns.set_theme()

# Load an example dataset (returns a pandas DataFrame)
tips = sns.load_dataset("tips")

# Create your first visualization
sns.relplot(data=tips, x="total_bill", y="tip")
```

### `sns.set_theme()`

This function configures matplotlib's `rcParams` system to apply seaborn's default styling. Key points:

- **Affects all matplotlib plots** — even those not created with seaborn
- **Applies globally** — call it once at the start of your session
- **Optional** — you can skip it and still use seaborn plotting functions
- **Customizable** — accepts `style`, `palette`, `font`, `font_scale`, and other parameters

```python
# Apply a specific theme with larger fonts
sns.set_theme(style="ticks", font_scale=1.25)
```

### `sns.load_dataset()`

Convenience function to load example datasets bundled with seaborn:

```python
tips = sns.load_dataset("tips")       # Restaurant bills and tips
penguins = sns.load_dataset("penguins")  # Penguin measurements
fmri = sns.load_dataset("fmri")       # fMRI experiment data
dots = sns.load_dataset("dots")       # Neural firing rate data
```

These are standard **pandas DataFrames**. You could load them with `pandas.read_csv()` or build them by hand — `load_dataset()` is just for quick examples.

---

## Key Concepts

### Semantic Mappings

Semantic mappings connect **data variables** to **visual properties** of plot elements. Seaborn standardizes these across all plot types:

| Mapping | Visual Property (Scatter) | Visual Property (Line) | Best For |
|---------|--------------------------|----------------------|----------|
| `hue` | Color of markers | Color of lines | Categorical or numeric grouping |
| `size` | Area of markers | Width of lines | Numeric variables (magnitude) |
| `style` | Shape of markers | Dashing pattern of lines | Categorical grouping |

**Important**: The same parameter affects different plot types differently. `size` changes marker area in scatter plots but line width in line plots. You don't need to remember these details — seaborn handles it automatically.

```python
# Categorical hue → distinct colors (blue, orange)
sns.relplot(data=penguins, x="bill_length_mm", y="bill_depth_mm", hue="species")

# Numeric hue → continuous gradient
sns.relplot(data=penguins, x="bill_length_mm", y="bill_depth_mm", hue="body_mass_g")
```

### Statistical Estimation

Many seaborn functions **automatically perform statistical computations**:

- **Aggregation**: Computing means, medians, or other summaries across grouped data
- **Bootstrapping**: Resampling data to estimate uncertainty
- **Confidence intervals**: Drawing error bars that represent estimation uncertainty

```python
# seaborn automatically aggregates repeated measurements and draws confidence intervals
sns.relplot(
    data=fmri, kind="line",
    x="timepoint", y="signal", col="region",
    hue="event", style="event",
)
```

In the line plot above, if multiple observations exist for each `timepoint`, seaborn:
1. Computes the mean signal at each timepoint
2. Bootstraps to estimate the confidence interval
3. Draws the mean as a line and the CI as a shaded band

### Facet Grids

A **facet grid** (also called a "trellis plot" or "small multiples") splits your data into subsets and draws the same plot type for each subset. This lets you see how relationships change across categories.

```python
# Creates separate subplots for each value of "time"
sns.relplot(data=tips, x="total_bill", y="tip", col="time")
```

Faceting is controlled by:
- `col` — creates columns of subplots
- `row` — creates rows of subplots
- `hue` — overlays groups within the same subplot (not faceting, but complementary)

---

## A High-Level API for Statistical Graphics

Seaborn provides figure-level functions that serve as entry points for different families of plots. Each function has a `kind` parameter to switch between specific plot types.

### `relplot()` — Statistical Relationships

**What it does**: Visualizes relationships between variables. The go-to function for understanding how one variable changes with another.

**Key parameters**:

| Parameter | Description |
|-----------|-------------|
| `data` | Input DataFrame |
| `x`, `y` | Variable names for axes |
| `kind` | `"scatter"` (default) or `"line"` |
| `hue` | Grouping variable mapped to color |
| `size` | Variable mapped to marker size / line width |
| `style` | Grouping variable mapped to marker shape / line dashing |
| `col`, `row` | Variables to facet on |
| `col_wrap` | Maximum number of columns before wrapping |

**When to use it**:
- Exploring correlations between two numeric variables → `kind="scatter"`
- Showing trends over time or ordered categories → `kind="line"`
- Comparing relationships across subgroups → use `col`/`row` + `hue`

**Code examples**:

```python
# Scatter plot: relationship between bill total and tip amount
sns.relplot(data=tips, x="total_bill", y="tip")
```

```python
# Scatter plot with multiple semantic mappings (5 variables in one plot)
sns.relplot(
    data=tips,
    x="total_bill", y="tip", col="time",
    hue="smoker", style="smoker", size="size",
)
```

```python
# Line plot: firing rate over time, grouped by experimental conditions
dots = sns.load_dataset("dots")
sns.relplot(
    data=dots, kind="line",
    x="time", y="firing_rate", col="align",
    hue="choice", size="coherence", style="choice",
    facet_kws=dict(sharex=False),
)
```

```python
# Line plot with automatic statistical estimation (mean + CI bands)
fmri = sns.load_dataset("fmri")
sns.relplot(
    data=fmri, kind="line",
    x="timepoint", y="signal", col="region",
    hue="event", style="event",
)
```

### `lmplot()` — Linear Regression Models

**What it does**: Enhances scatter plots by fitting and displaying a linear regression model with its uncertainty band.

**Key parameters**:

| Parameter | Description |
|-----------|-------------|
| `data` | Input DataFrame |
| `x`, `y` | Variable names for axes |
| `hue` | Grouping variable (fits separate regression per group) |
| `col`, `row` | Variables to facet on |
| `ci` | Confidence interval size (default 95) |
| `scatter` | Whether to show scatter points (default True) |

**When to use it**:
- You want to see the best-fit line through a scatter plot
- You want to compare regression slopes across groups
- You want to assess the strength and direction of a linear relationship

**Code example**:

```python
# Scatter plot with regression lines, faceted by time and colored by smoker status
sns.lmplot(data=tips, x="total_bill", y="tip", col="time", hue="smoker")
```

### `displot()` — Distribution Plots

**What it does**: Visualizes the distribution of one or more variables. Supports multiple approaches to understanding how data is spread.

**Key parameters**:

| Parameter | Description |
|-----------|-------------|
| `data` | Input DataFrame |
| `x` | Variable to plot |
| `kind` | `"hist"` (default), `"kde"`, `"ecdf"`, or `"rug"` |
| `hue` | Grouping variable for overlaid distributions |
| `col`, `row` | Variables to facet on |
| `kde` | Add kernel density estimate overlay to histogram (True/False) |
| `rug` | Add rug plot (small ticks at each observation) |
| `bins` | Number of histogram bins |
| `multiple` | How to stack multiple distributions: `"layer"`, `"stack"`, `"fill"`, `"dodge"` |

**When to use it**:
- Understanding the shape of a single variable's distribution → `kind="hist"` or `kind="kde"`
- Comparing distributions across groups → use `hue`
- Checking cumulative proportions → `kind="ecdf"`
- Seeing exact data point density → `rug=True`

**Code examples**:

```python
# Histogram with KDE overlay, faceted by meal time
sns.displot(data=tips, x="total_bill", col="time", kde=True)
```

```python
# Empirical Cumulative Distribution Function (ECDF)
sns.displot(
    data=tips, kind="ecdf",
    x="total_bill", col="time",
    hue="smoker", rug=True,
)
```

```python
# Kernel density estimation only
sns.displot(data=tips, kind="kde", x="total_bill", hue="time")
```

### `catplot()` — Categorical Data Plots

**What it does**: Visualizes relationships where at least one variable is categorical. Offers multiple granularities of representation.

**Key parameters**:

| Parameter | Description |
|-----------|-------------|
| `data` | Input DataFrame |
| `x` | Categorical variable |
| `y` | Numeric variable |
| `kind` | `"strip"`, `"swarm"`, `"box"`, `"violin"`, `"boxen"`, `"point"`, `"bar"`, `"count"` |
| `hue` | Secondary grouping variable |
| `col`, `row` | Variables to facet on |
| `split` | For violin plots: split each violin to compare two groups side-by-side |
| `orient` | Plot orientation: `"v"` (vertical) or `"h"` (horizontal) |

**When to use it**:
- Seeing every individual observation → `kind="swarm"` or `kind="strip"`
- Understanding the underlying distribution shape → `kind="violin"`
- Comparing aggregate statistics (mean ± CI) → `kind="bar"` or `kind="point"`
- Counting observations per category → `kind="count"`

**Code examples**:

```python
# Swarm plot: every observation, positions adjusted to avoid overlap
sns.catplot(data=tips, kind="swarm", x="day", y="total_bill", hue="smoker")
```

```python
# Violin plot: KDE representation of the distribution, split to compare groups
sns.catplot(
    data=tips, kind="violin",
    x="day", y="total_bill",
    hue="smoker", split=True,
)
```

```python
# Bar plot: mean value with confidence intervals
sns.catplot(data=tips, kind="bar", x="day", y="total_bill", hue="smoker")
```

```python
# Box plot: quartiles and outliers
sns.catplot(data=tips, kind="box", x="day", y="total_bill", hue="smoker")
```

```python
# Point plot: mean with error bars, connected by lines (good for trends)
sns.catplot(data=tips, kind="point", x="day", y="total_bill", hue="smoker")
```

---

## Multivariate Views on Complex Datasets

These functions combine multiple plot types to give informative summaries of entire datasets at a glance.

### `jointplot()` — Joint + Marginal Distributions

**What it does**: Shows the joint distribution between two variables in the center, with each variable's marginal distribution on the top and right edges.

**Key parameters**:

| Parameter | Description |
|-----------|-------------|
| `data` | Input DataFrame |
| `x`, `y` | Variables for the joint plot |
| `kind` | `"scatter"` (default), `"kde"`, `"hist"`, `"hex"`, `"reg"`, `"resid"` |
| `hue` | Grouping variable |
| `height` | Size of the figure (square) |
| `marginal_ticks` | Show ticks on marginal axes |

**When to use it**:
- You want to see both the relationship AND the individual distributions of two variables
- Exploring correlation structure at a glance
- Comparing joint vs. marginal behavior

**Code examples**:

```python
penguins = sns.load_dataset("penguins")
sns.jointplot(
    data=penguins,
    x="flipper_length_mm", y="bill_length_mm",
    hue="species",
)
```

```python
# With regression fit
sns.jointplot(
    data=penguins,
    x="flipper_length_mm", y="bill_length_mm",
    kind="reg", hue="species",
)
```

### `pairplot()` — All Pairwise Relationships

**What it does**: Creates a grid of axes showing the joint distribution for every pair of variables, with marginal distributions on the diagonal.

**Key parameters**:

| Parameter | Description |
|-----------|-------------|
| `data` | Input DataFrame |
| `hue` | Grouping variable (applied across all subplots) |
| `vars` | Subset of columns to include |
| `x_vars`, `y_vars` | Explicit control of axes |
| `kind` | Plot type for off-diagonal: `"scatter"` (default) or `"kde"` |
| `diag_kind` | Plot type for diagonal: `"hist"` (default) or `"kde"` |
| `corner` | Show only lower triangle (`True`/`False`) |
| `plot_kws` | Dict of kwargs passed to off-diagonal plotting function |
| `diag_kws` | Dict of kwargs passed to diagonal plotting function |

**When to use it**:
- Initial exploratory data analysis (EDA) on a new dataset
- Looking for correlations, clusters, or outliers across all variable pairs
- Understanding multivariate structure before building models

**Code examples**:

```python
# Full pairplot colored by species
sns.pairplot(data=penguins, hue="species")
```

```python
# Subset of variables with KDE on diagonal
sns.pairplot(
    data=penguins, hue="species",
    vars=["bill_length_mm", "bill_depth_mm", "flipper_length_mm"],
    diag_kind="kde",
)
```

### `PairGrid` — Custom Multi-Plot Figures

**What it does**: A lower-level, more flexible tool for building custom multi-plot grids. Unlike `pairplot()` (which uses the same plot type everywhere), `PairGrid` lets you specify different plot types for different regions of the grid.

**Key methods**:

| Method | Description |
|--------|-------------|
| `map(func, **kwargs)` | Apply `func` to every subplot (upper, lower, and diagonal) |
| `map_upper(func, **kwargs)` | Apply to upper triangle only |
| `map_lower(func, **kwargs)` | Apply to lower triangle only |
| `map_diag(func, **kwargs)` | Apply to diagonal only |
| `add_legend(**kwargs)` | Add a legend to the figure |

**When to use it**:
- `pairplot()` doesn't offer the combination of plot types you need
- You want different visualizations in different grid positions
- You need fine-grained control over each region of the grid

**Code example**:

```python
# Custom PairGrid: KDE contours below, scatter points, histograms on diagonal
g = sns.PairGrid(penguins, hue="species", corner=True)
g.map_lower(sns.kdeplot, hue=None, levels=5, color=".2")
g.map_lower(sns.scatterplot, marker="+")
g.map_diag(sns.histplot, element="step", linewidth=0, kde=True)
g.add_legend(frameon=True)
g.legend.set_bbox_to_anchor((.61, .6))
```

This example demonstrates:
- `corner=True` — only the lower triangle (no redundant upper triangle)
- Different plot types in different regions (KDE contours + scatter points below, histograms on diagonal)
- Custom legend positioning

---

## Opinionated Defaults and Flexible Customization

### What "Opinionated Defaults" Means

Seaborn makes intelligent choices so you get a good-looking, informative plot with minimal code:

- **Automatic axis labels** — uses column names from the dataframe
- **Automatic legends** — explains every semantic mapping (`hue`, `size`, `style`)
- **Smart color selection** — distinct hues for categorical variables, continuous gradients for numeric
- **Appropriate plot types** — chooses encodings that match the data type

### Customization Workflow: From Defaults to Fine-Grained Control

Seaborn offers **four levels** of customization, from broad to specific:

#### Level 1: Global Themes

Apply styling that affects all plots in your session:

```python
sns.set_theme(style="ticks", font_scale=1.25)
```

Available styles: `"darkgrid"`, `"whitegrid"`, `"dark"`, `"white"`, `"ticks"`

#### Level 2: Standardized Semantic Parameters

Control how data maps to visual properties on a per-plot basis:

```python
sns.relplot(
    data=penguins,
    x="bill_length_mm", y="bill_depth_mm", hue="body_mass_g",
    palette="crest",    # Choose a specific color palette
    marker="x",         # Override default marker
    s=100,              # Override default marker size
)
```

#### Level 3: Additional Keyword Arguments

Pass extra kwargs through to the underlying matplotlib artists:

```python
# These kwargs go directly to matplotlib's scatter/line functions
sns.relplot(data=penguins, x="bill_length_mm", y="bill_depth_mm",
            alpha=0.7, edgecolor="black", linewidth=0.5)
```

#### Level 4: Post-Creation Modification

Modify the figure after it's been created, using both seaborn and matplotlib APIs:

```python
g = sns.relplot(
    data=penguins,
    x="bill_length_mm", y="bill_depth_mm", hue="body_mass_g",
    palette="crest", marker="x", s=100,
)

# Seaborn-level modifications
g.set_axis_labels("Bill length (mm)", "Bill depth (mm)", labelpad=10)
g.legend.set_title("Body mass (g)")
g.despine(trim=True)

# Matplotlib-level modifications
g.figure.set_size_inches(6.5, 4.5)
g.ax.margins(.15)
```

### Complete Customization Example

This example chains all four levels together:

```python
# Level 1: Global theme
sns.set_theme(style="ticks", font_scale=1.25)

# Level 2 + 3: Plot creation with semantic params + matplotlib kwargs
g = sns.relplot(
    data=penguins,
    x="bill_length_mm", y="bill_depth_mm", hue="body_mass_g",
    palette="crest", marker="x", s=100,
)

# Level 4: Post-creation tweaks
g.set_axis_labels("Bill length (mm)", "Bill depth (mm)", labelpad=10)
g.legend.set_title("Body mass (g)")
g.figure.set_size_inches(6.5, 4.5)
g.ax.margins(.15)
g.despine(trim=True)
```

---

## Relationship to Matplotlib

### How They Work Together

```
┌─────────────────────────────────────────┐
│              Your Code                   │
│    (seaborn function calls)              │
├─────────────────────────────────────────┤
│           Seaborn Layer                  │
│  - Semantic mapping                      │
│  - Statistical estimation                │
│  - Layout management                     │
│  - Default styling                       │
├─────────────────────────────────────────┤
│          Matplotlib Layer                │
│  - Actual drawing/rendering              │
│  - Figure/axes management                │
│  - Output format (PNG, SVG, PDF, etc.)  │
└─────────────────────────────────────────┘
```

### Key Points

- **Seaborn uses matplotlib internally** — every seaborn plot is a matplotlib figure
- **Works in all matplotlib environments**: Jupyter notebooks, GUI windows, scripts, web servers
- **Supports all matplotlib output formats**: PNG, SVG, PDF, EPS, and more
- **Full matplotlib knowledge transfers** — anything you know about matplotlib applies

### When to Drop Down to Matplotlib

| Scenario | Use Seaborn | Use Matplotlib |
|----------|:-----------:|:--------------:|
| Quick EDA plots | ✅ | |
| Statistical relationships | ✅ | |
| Distribution visualization | ✅ | |
| Categorical comparisons | ✅ | |
| Multi-plot grids | ✅ | |
| Custom axis tick formatting | | ✅ |
| Complex annotations (arrows, text boxes) | | ✅ |
| Non-standard plot types | | ✅ |
| Fine-grained layout control | | ✅ |
| Animation | | ✅ |

### Practical Guidance

> "While you can be productive using only seaborn functions, full customization of your graphics will require some knowledge of matplotlib's concepts and API."

The ideal workflow:
1. **Start with seaborn** — get your data visualized quickly with good defaults
2. **Refine with seaborn** — adjust themes, palettes, semantic mappings
3. **Drop to matplotlib** — for pixel-perfect tweaks, custom annotations, or non-standard elements

```python
# Step 1: Create with seaborn
g = sns.relplot(data=penguins, x="bill_length_mm", y="bill_depth_mm", hue="species")

# Step 2: Refine with seaborn
g.set_axis_labels("Bill Length", "Bill Depth")
g.despine()

# Step 3: Fine-tune with matplotlib
g.ax.annotate("Adelie outlier", xy=(35, 21), xytext=(40, 22),
              arrowprops=dict(arrowstyle="->", color="red"))
g.figure.savefig("penguins.pdf", bbox_inches="tight", dpi=300)
```

---

## Function Quick Reference

| Function | Purpose | `kind` Options | Returns |
|----------|---------|----------------|---------|
| **`relplot()`** | Statistical relationships between variables | `"scatter"`, `"line"` | `FacetGrid` |
| **`lmplot()`** | Scatter plot + linear regression model | N/A (always regression) | `FacetGrid` |
| **`displot()`** | Distribution visualization | `"hist"`, `"kde"`, `"ecdf"` | `FacetGrid` |
| **`catplot()`** | Categorical data visualization | `"strip"`, `"swarm"`, `"box"`, `"violin"`, `"boxen"`, `"point"`, `"bar"`, `"count"` | `FacetGrid` |
| **`jointplot()`** | Joint + marginal distributions for two variables | `"scatter"`, `"kde"`, `"hist"`, `"hex"`, `"reg"`, `"resid"` | `JointGrid` |
| **`pairplot()`** | All pairwise joint + marginal distributions | `"scatter"`, `"kde"` (off-diag) | `PairGrid` |
| **`PairGrid`** | Custom multi-plot grid builder | N/A (manual mapping) | `PairGrid` object |

### Figure-Level vs. Axes-Level Functions

The functions above (`relplot`, `displot`, `catplot`, `lmplot`, `jointplot`, `pairplot`) are **figure-level functions**. They:

- Create and manage their own figure
- Support faceting (`col`, `row`)
- Return a grid object (`FacetGrid`, `JointGrid`, `PairGrid`)
- Are the recommended entry points

Seaborn also has **axes-level functions** (`scatterplot`, `lineplot`, `histplot`, `kdeplot`, `boxplot`, `violinplot`, etc.) that:

- Draw onto an existing matplotlib axes
- Do NOT support faceting
- Return the matplotlib `Axes` object
- Are useful when building custom multi-panel figures

---

## Best Practices

### 1. Start with Figure-Level Functions

Prefer `relplot()`, `displot()`, and `catplot()` over their axes-level counterparts. They offer more flexibility (faceting) and consistent returns.

```python
# Good: figure-level function
sns.relplot(data=df, x="a", y="b")

# Only use axes-level when you need to embed in a custom figure
fig, ax = plt.subplots()
sns.scatterplot(data=df, x="a", y="b", ax=ax)
```

### 2. Call `sns.set_theme()` Early

Set your theme at the top of your script or notebook to ensure consistent styling:

```python
import seaborn as sns
sns.set_theme()  # Do this once, before any plotting
```

### 3. Use Semantic Mappings Thoughtfully

- **`hue`**: Best for categorical grouping or highlighting a third variable
- **`size`**: Best for numeric variables where magnitude matters
- **`style`**: Best for additional categorical grouping (especially for black-and-white printing)
- **Avoid over-mapping**: More than 3-4 semantic mappings can make a plot hard to read

### 4. Leverage Faceting for Comparisons

When comparing across categories, faceting is often clearer than overplotting:

```python
# Better for comparison: separate subplots
sns.relplot(data=df, x="x", y="y", col="category")

# Can get messy: all groups overlaid
sns.relplot(data=df, x="x", y="y", hue="category")
```

### 5. Use the Right `kind` for Your Data

| Data Type | Recommended `kind` |
|-----------|-------------------|
| Two continuous variables | `"scatter"` |
| Time series / ordered data | `"line"` |
| Single variable distribution | `"hist"` or `"kde"` |
| Comparing distributions | `"ecdf"` |
| Categorical x, continuous y | `"swarm"`, `"violin"`, or `"bar"` |
| Categorical counts | `"count"` |

### 6. Customize for Publication

When preparing figures for publication:

```python
# Larger fonts for readability
sns.set_theme(font_scale=1.25)

# Tight, clean style
g = sns.relplot(..., style="ticks")

# Remove unnecessary ink
g.despine(trim=True)

# Set explicit figure size
g.figure.set_size_inches(width, height)

# Save in vector format
g.figure.savefig("output.pdf", bbox_inches="tight")
```

### 7. Know When to Use Matplotlib

Don't try to force seaborn to do everything. Drop to matplotlib for:
- Custom annotations
- Complex layouts (inset axes, broken axes)
- Non-standard plot types
- Pixel-level adjustments

---

## Next Steps

After mastering the introduction material, continue your seaborn journey:

### Official Resources

| Resource | URL | What You'll Learn |
|----------|-----|-------------------|
| **Installation Guide** | [seaborn.pydata.org/installing.html](https://seaborn.pydata.org/installing.html) | How to install seaborn and its dependencies |
| **Example Gallery** | [seaborn.pydata.org/examples](https://seaborn.pydata.org/examples/index.html) | Browse what seaborn can produce |
| **Full Tutorial** | [seaborn.pydata.org/tutorial.html](https://seaborn.pydata.org/tutorial.html) | Deep dive into each plot type |
| **API Reference** | [seaborn.pydata.org/api.html](https://seaborn.pydata.org/api.html) | Complete function documentation with parameters |

### Recommended Learning Path

1. **Function Overview** — Understand the distinction between figure-level and axes-level functions
2. **Data Structures** — Learn what data formats seaborn accepts
3. **Relational Plots** — Deep dive into `relplot()` with scatter and line plots
4. **Distribution Plots** — Master `displot()` and its various `kind` options
5. **Categorical Plots** — Explore all `catplot()` kinds and when to use each
6. **Error Bars** — Understand statistical estimation and confidence intervals
7. **Regression Plots** — Learn `lmplot()` and regression visualization
8. **Axis Grids** — Build custom multi-plot figures with `FacetGrid`, `JointGrid`, `PairGrid`
9. **Aesthetics** — Control themes, styles, and figure appearance
10. **Color Palettes** — Choose and customize color mappings

### The `seaborn.objects` Interface

Seaborn 0.12+ introduced a new `seaborn.objects` interface that provides a grammar-of-graphics approach (similar to ggplot2). This is covered in a separate tutorial section and represents the future direction of the library.

---

## Summary

Seaborn is a **high-level statistical visualization library** that:

- **Simplifies** complex plots into single function calls
- **Automates** statistical estimation and semantic mapping
- **Integrates** seamlessly with pandas DataFrames
- **Builds on** matplotlib for rendering and deep customization
- **Encourages** exploratory data analysis through easy switching between plot types

The declarative API means you think about **what your data means**, not **how to draw pixels**. This lets you focus on understanding your data rather than wrestling with plotting code.
