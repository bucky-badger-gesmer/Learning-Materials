# Chapter 9: Plotting and Visualization

> **Source:** *Python for Data Analysis, 3rd Edition* by Wes McKinney — [wesmckinney.com/book/plotting-and-visualization](https://wesmckinney.com/book/plotting-and-visualization)

---

## Table of Contents

- [9.1 A Brief matplotlib API Primer](#91-a-brief-matplotlib-api-primer)
  - [Figures and Subplots](#figures-and-subplots)
  - [Colors, Markers, and Line Styles](#colors-markers-and-line-styles)
  - [Ticks, Labels, and Legends](#ticks-labels-and-legends)
  - [Annotations and Drawing on a Subplot](#annotations-and-drawing-on-a-subplot)
  - [Case Study: Financial Crisis Annotation](#case-study-financial-crisis-annotation)
  - [Saving Plots to File](#saving-plots-to-file)
  - [matplotlib Configuration](#matplotlib-configuration)
- [9.2 Plotting with pandas and seaborn](#92-plotting-with-pandas-and-seaborn)
  - [Line Plots](#line-plots)
  - [Bar Plots](#bar-plots)
  - [Bar Plots with seaborn](#bar-plots-with-seaborn)
  - [Histograms and Density Plots](#histograms-and-density-plots)
  - [Scatter and Point Plots](#scatter-and-point-plots)
  - [Facet Grids and Categorical Data](#facet-grids-and-categorical-data)
- [9.3 Other Python Visualization Tools](#93-other-python-visualization-tools)
- [9.4 Conclusion](#94-conclusion)
- [Quick Reference: Plot Types and Code Patterns](#quick-reference-plot-types-and-code-patterns)
- [Important Notes and Caveats](#important-notes-and-caveats)
- [Key Takeaways](#key-takeaways)

---

## Overview

This chapter introduces the core visualization techniques in Python for exploratory data analysis and communication. It covers **matplotlib** as the foundational plotting library, **pandas** built-in plotting methods that simplify DataFrame/Series visualization, and **seaborn** for high-level statistical graphics with better defaults. The focus is on static plots; interactive alternatives (Bokeh, Altair, Plotly) are mentioned but not covered in depth.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

> **Jupyter Setup**: Run `%matplotlib inline` in a Jupyter notebook to display plots inline, or `%matplotlib notebook` for interactive plots.

> **Important Jupyter Nuance**: Plots are reset after each cell is evaluated. All plotting commands for a single figure must be placed in the **same notebook cell**.

---

## 9.1 A Brief matplotlib API Primer

Standard import convention:

```python
import matplotlib.pyplot as plt
```

### Figures and Subplots

Plots in matplotlib reside within a `Figure` object. You create a figure, then add one or more subplots to it.

#### Creating Figures and Axes

```python
# Create a blank figure
fig = plt.figure()

# Add subplots individually (2x2 grid, selecting subplot 1)
ax1 = fig.add_subplot(2, 2, 1)
ax2 = fig.add_subplot(2, 2, 2)
ax3 = fig.add_subplot(2, 2, 3)

# Plot on a specific subplot
ax3.plot(np.random.standard_normal(50).cumsum(), color="black",
         linestyle="dashed")

# Plot on other subplots
ax1.hist(np.random.standard_normal(100), bins=20, color="black", alpha=0.3)
ax2.scatter(np.arange(30), np.arange(30) + 3 * np.random.standard_normal(30))
```

#### Using `plt.subplots()` (Preferred)

The more convenient way to create a grid of subplots:

```python
fig, axes = plt.subplots(2, 3)  # Returns figure + 2D NumPy array of axes

# Index into the array
axes[0, 1].plot(data)

# Share axes across subplots
fig, axes = plt.subplots(2, 2, sharex=True, sharey=True)
for i in range(2):
    for j in range(2):
        axes[i, j].hist(np.random.standard_normal(500), bins=50,
                        color="black", alpha=0.5)
```

#### Adjusting Subplot Spacing

```python
fig.subplots_adjust(left=None, bottom=None, right=None, top=None,
                    wspace=None, hspace=None)
```

- **`wspace`** / **`hspace`**: Percent of figure width/height used as spacing between subplots
- Setting both to `0` removes all inter-subplot spacing (but axis labels may overlap)

### Table 9.1: `matplotlib.pyplot.subplots` Options

| Argument | Description |
|----------|-------------|
| `nrows` | Number of rows of subplots |
| `ncols` | Number of columns of subplots |
| `sharex` | All subplots use the same x-axis ticks (adjusting `xlim` affects all) |
| `sharey` | All subplots use the same y-axis ticks (adjusting `ylim` affects all) |
| `subplot_kw` | Dictionary of keywords passed to `add_subplot` for each subplot |
| `**fig_kw` | Additional keywords for figure creation, e.g. `figsize=(8, 6)` |

---

### Colors, Markers, and Line Styles

matplotlib's `plot` function accepts x/y coordinate arrays plus optional styling.

```python
# Basic styling with keyword arguments
ax.plot(x, y, linestyle="--", color="green")

# Hex color codes
ax.plot(x, y, color="#CECECE")

# Adding markers to highlight data points
ax.plot(np.random.standard_normal(30).cumsum(),
        color="black", linestyle="dashed", marker="o")

# Transparency with alpha
ax.hist(np.random.standard_normal(100), bins=20, color="black", alpha=0.3)
```

#### Drawstyle Options

By default, subsequent points are linearly interpolated. Use `drawstyle` to change this:

```python
fig = plt.figure()
ax = fig.add_subplot()
data = np.random.standard_normal(30).cumsum()

ax.plot(data, color="black", linestyle="dashed", label="Default")
ax.plot(data, color="black", linestyle="dashed",
        drawstyle="steps-post", label="steps-post")
ax.legend()  # Must call to display the legend
```

> **Note**: You must call `ax.legend()` to create the legend, whether or not you passed `label` arguments when plotting.

---

### Ticks, Labels, and Legends

Most plot decorations are accessed through methods on matplotlib axes objects. Each parameter has a getter (no args returns current value) and setter (with args sets the value).

#### Setting Title, Axis Labels, Ticks, and Tick Labels

```python
fig, ax = plt.subplots()
ax.plot(np.random.standard_normal(1000).cumsum())

# Set custom tick locations
ticks = ax.set_xticks([0, 250, 500, 750, 1000])

# Set custom tick labels with rotation and font size
labels = ax.set_xticklabels(["one", "two", "three", "four", "five"],
                            rotation=30, fontsize=8)

# Set axis label and title
ax.set_xlabel("Stages")
ax.set_title("My first matplotlib plot")

# Batch setting with ax.set()
ax.set(title="My first matplotlib plot", xlabel="Stages")
```

#### Setting Axis Limits

```python
ax.set_xlim([0, 10])   # Set x-axis range
ax.set_ylim([600, 1800])  # Set y-axis range
ax.set_xlim(["1/1/2007", "1/1/2011"])  # Works with dates too
```

#### Adding Legends

```python
fig, ax = plt.subplots()
ax.plot(np.random.randn(1000).cumsum(), color="black", label="one")
ax.plot(np.random.randn(1000).cumsum(), color="black",
        linestyle="dashed", label="two")
ax.plot(np.random.randn(1000).cumsum(), color="black",
        linestyle="dotted", label="three")
ax.legend()  # Auto-creates legend from labels

# Exclude elements from legend
ax.plot(data, label="_nolegend_")  # This line won't appear in legend
```

The `loc` argument controls legend placement. Default is `"best"`, which auto-selects the least obstructive position.

---

### Annotations and Drawing on a Subplot

#### Text Annotations

```python
ax.text(x, y, "Hello world!", family="monospace", fontsize=10)
```

#### Arrow Annotations

```python
ax.annotate(label,
            xy=(date, value),              # Point to annotate
            xytext=(date, value + 150),    # Text position
            arrowprops=dict(facecolor="black", headwidth=4,
                            width=2, headlength=4),
            horizontalalignment="left",
            verticalalignment="top")
```

#### Adding Shapes (Patches)

```python
from matplotlib.patches import Rectangle, Circle, Polygon

fig, ax = plt.subplots()

rect = plt.Rectangle((0.2, 0.75), 0.4, 0.15, color="black", alpha=0.3)
circ = plt.Circle((0.7, 0.2), 0.15, color="blue", alpha=0.3)
pgon = plt.Polygon([[0.15, 0.15], [0.35, 0.4], [0.2, 0.6]],
                   color="green", alpha=0.5)

ax.add_patch(rect)
ax.add_patch(circ)
ax.add_patch(pgon)
```

---

### Case Study: Financial Crisis Annotation

A practical example combining multiple annotation techniques to visualize the S&P 500 index with key events from the 2008-2009 financial crisis:

```python
from datetime import datetime

fig, ax = plt.subplots()

# Load and plot S&P 500 data
data = pd.read_csv("examples/spx.csv", index_col=0, parse_dates=True)
spx = data["SPX"]
spx.plot(ax=ax, color="black")

# Define crisis events
crisis_data = [
    (datetime(2007, 10, 11), "Peak of bull market"),
    (datetime(2008, 3, 12), "Bear Stearns Fails"),
    (datetime(2008, 9, 15), "Lehman Bankruptcy")
]

# Annotate each event with an arrow
for date, label in crisis_data:
    ax.annotate(label,
                xy=(date, spx.asof(date) + 75),
                xytext=(date, spx.asof(date) + 225),
                arrowprops=dict(facecolor="black", headwidth=4,
                                width=2, headlength=4),
                horizontalalignment="left", verticalalignment="top")

# Zoom into the crisis period
ax.set_xlim(["1/1/2007", "1/1/2011"])
ax.set_ylim([600, 1800])

ax.set_title("Important dates in the 2008-2009 financial crisis")
```

**Key techniques demonstrated:**
- `ax.annotate()` for labeled arrows pointing to specific data points
- `set_xlim()` / `set_ylim()` for manual axis range control
- `spx.asof(date)` for time-based value lookup
- Combining pandas `.plot()` with matplotlib axis methods

---

### Saving Plots to File

```python
fig.savefig("figpath.svg")               # Format inferred from extension
fig.savefig("figpath.png", dpi=400)      # High-resolution PNG
fig.savefig("figpath.pdf", facecolor="w")  # PDF with white background
```

### Table 9.2: `fig.savefig` Options

| Argument | Description |
|----------|-------------|
| `fname` | File path or Python file-like object. Format inferred from extension (`.pdf`, `.png`, `.svg`, etc.) |
| `dpi` | Figure resolution in dots per inch. Defaults to 100 (IPython) or 72 (Jupyter) |
| `facecolor`, `edgecolor` | Color of figure background outside subplots; `"w"` (white) by default |
| `format` | Explicit file format: `"png"`, `"pdf"`, `"svg"`, `"ps"`, `"eps"`, ... |

---

### matplotlib Configuration

#### Programmatic Configuration with `plt.rc()`

```python
# Set global default figure size
plt.rc("figure", figsize=(10, 10))

# Set font defaults using a dictionary
plt.rc("font", family="monospace", weight="bold", size=8)

# View all current settings
plt.rcParams

# Restore defaults
plt.rcdefaults()
```

#### Using Style Sheets

```python
plt.style.use("grayscale")   # Black-and-white publication style
plt.style.use("ggplot")      # R/ggplot2 style
```

#### Configuration File

Place a customized `matplotlibrc` file in `~/.matplotlibrc` to load settings automatically. Key customizable components: `"figure"`, `"axes"`, `"xtick"`, `"ytick"`, `"grid"`, `"legend"`, and more.

---

## 9.2 Plotting with pandas and seaborn

matplotlib is low-level: you assemble plots from base components. pandas and seaborn provide higher-level APIs built on top of matplotlib.

### Line Plots

#### Series Line Plots

```python
s = pd.Series(np.random.standard_normal(10).cumsum(),
              index=np.arange(0, 100, 10))
s.plot()  # Series index becomes x-axis
```

#### DataFrame Line Plots

```python
df = pd.DataFrame(np.random.standard_normal((10, 4)).cumsum(0),
                  columns=["A", "B", "C", "D"],
                  index=np.arange(0, 100, 10))
df.plot()  # Each column plotted as a separate line with auto-legend
```

> **Note**: `df.plot()` is equivalent to `df.plot.line()`. The `plot` attribute contains a family of methods for different plot types.

### Table 9.3: `Series.plot` Method Arguments

| Argument | Description |
|----------|-------------|
| `label` | Label for plot legend |
| `ax` | matplotlib subplot object to plot on; if none passed, uses active subplot |
| `style` | Style string (e.g., `"ko--"`) passed to matplotlib |
| `alpha` | Plot fill opacity (0 to 1) |
| `kind` | `"area"`, `"bar"`, `"barh"`, `"density"`, `"hist"`, `"kde"`, `"line"`, or `"pie"`; defaults to `"line"` |
| `figsize` | Size of the figure object to create |
| `logx` | `True` for logarithmic x-axis; `"sym"` for symmetric log (permits negatives) |
| `logy` | `True` for logarithmic y-axis; `"sym"` for symmetric log (permits negatives) |
| `title` | Title for the plot |
| `use_index` | Use the object index for tick labels |
| `rot` | Rotation of tick labels (0 through 360) |
| `xticks` | Values to use for x-axis ticks |
| `yticks` | Values to use for y-axis ticks |
| `xlim` | x-axis limits (e.g., `[0, 10]`) |
| `ylim` | y-axis limits |
| `grid` | Display axis grid (off by default) |

### Table 9.4: DataFrame-Specific Plot Arguments

| Argument | Description |
|----------|-------------|
| `subplots` | Plot each DataFrame column in a separate subplot |
| `layout` | 2-tuple `(rows, columns)` providing subplot layout |
| `sharex` | If `subplots=True`, share the same x-axis (linking ticks and limits) |
| `sharey` | If `subplots=True`, share the same y-axis |
| `legend` | Add a subplot legend (`True` by default) |
| `sort_columns` | Plot columns in alphabetical order; default uses existing column order |

---

### Bar Plots

#### Vertical and Horizontal Bar Plots

```python
# Series bar plots
fig, axes = plt.subplots(2, 1)
data = pd.Series(np.random.uniform(size=16), index=list("abcdefghijklmnop"))
data.plot.bar(ax=axes[0], color="black", alpha=0.7)      # Vertical
data.plot.barh(ax=axes[1], color="black", alpha=0.7)     # Horizontal

# DataFrame bar plots (columns grouped side-by-side per row)
df = pd.DataFrame(np.random.uniform(size=(6, 4)),
                  index=["one", "two", "three", "four", "five", "six"],
                  columns=pd.Index(["A", "B", "C", "D"], name="Genus"))
df.plot.bar()
```

#### Stacked Bar Plots

```python
df.plot.barh(stacked=True, alpha=0.5)
```

#### Frequency Bar Plots

A useful pattern for visualizing value distributions:

```python
s.value_counts().plot.bar()
```

#### Practical Example: Restaurant Tips by Party Size

Building a stacked bar plot showing the percentage of data points for each party size per day:

```python
tips = pd.read_csv("examples/tips.csv")

# Create cross-tabulation of day vs. party size
party_counts = pd.crosstab(tips["day"], tips["size"])
party_counts = party_counts.reindex(index=["Thur", "Fri", "Sat", "Sun"])
party_counts = party_counts.loc[:, 2:5]  # Remove 1- and 6-person parties

# Normalize so each row sums to 1
party_pcts = party_counts.div(party_counts.sum(axis="columns"), axis="index")

# Plot as stacked bar chart
party_pcts.plot.bar(stacked=True)
```

---

### Bar Plots with seaborn

seaborn simplifies plots that require aggregation or summarization:

```python
import seaborn as sns

tips["tip_pct"] = tips["tip"] / (tips["total_bill"] - tips["tip"])

# Basic bar plot with automatic aggregation and 95% CI error bars
sns.barplot(x="tip_pct", y="day", data=tips, orient="h")

# Split by an additional categorical variable
sns.barplot(x="tip_pct", y="day", hue="time", data=tips, orient="h")
```

Key seaborn features:
- **Automatic aggregation**: Bars show mean values when multiple observations exist per category
- **Error bars**: Black lines represent 95% confidence intervals (configurable)
- **`hue` parameter**: Splits bars by an additional categorical variable
- **Better defaults**: Improved color palette, background, and grid lines

#### seaborn Styling

```python
sns.set_style("whitegrid")       # Change plot appearance
sns.set_palette("Greys_r")       # Greyscale palette for B&W print
```

---

### Histograms and Density Plots

#### Histograms

```python
tips["tip_pct"].plot.hist(bins=50)
```

A histogram gives a discretized display of value frequency. Data points are split into evenly spaced bins and the count per bin is plotted.

#### Density Plots (KDE)

```python
tips["tip_pct"].plot.density()
```

A density plot computes an estimate of a continuous probability distribution that might have generated the observed data. It approximates the distribution as a mixture of kernels (typically normal distributions). Also called **Kernel Density Estimate (KDE)** plots.

> **Requirement**: Density plots require **SciPy**. Install with `conda install scipy` or `pip install scipy`.

#### Combined Histogram + Density with seaborn

```python
comp1 = np.random.standard_normal(200)
comp2 = 10 + 2 * np.random.standard_normal(200)
values = pd.Series(np.concatenate([comp1, comp2]))

sns.histplot(values, bins=100, color="black")  # Can show both histogram and KDE
```

---

### Scatter and Point Plots

#### Scatter Plot with Regression Line

```python
# Load macroeconomic data
macro = pd.read_csv("examples/macrodata.csv")
data = macro[["cpi", "m1", "tbilrate", "unemp"]]
trans_data = np.log(data).diff().dropna()  # Log differences

# Scatter plot with fitted regression line and confidence band
ax = sns.regplot(x="m1", y="unemp", data=trans_data)
ax.set_title("Changes in log(m1) versus log(unemp)")
```

#### Pair Plot (Scatter Matrix)

```python
sns.pairplot(trans_data, diag_kind="kde", plot_kws={"alpha": 0.2})
```

A pair plot shows scatter plots among all pairs of variables, with histograms or density estimates on the diagonal. Useful for exploratory data analysis.

- **`diag_kind`**: `"kde"` for density, `"hist"` for histogram on diagonal
- **`plot_kws`**: Dictionary of options passed to off-diagonal plotting calls
- **`hue`**: Color points by a categorical variable

---

### Facet Grids and Categorical Data

Facet grids are a two-dimensional layout of plots where data is split across plots based on distinct values of categorical variables.

#### Using `sns.catplot()`

```python
# Facet by column (columns of the grid)
sns.catplot(x="day", y="tip_pct", hue="time", col="smoker",
            kind="bar", data=tips[tips.tip_pct < 1])

# Facet by both row and column
sns.catplot(x="day", y="tip_pct", row="time", col="smoker",
            kind="bar", data=tips[tips.tip_pct < 1])

# Box plot variant
sns.catplot(x="tip_pct", y="day", kind="box",
            data=tips[tips.tip_pct < 0.5])
```

#### `catplot` `kind` Options

| `kind` | Description |
|--------|-------------|
| `"bar"` | Bar plot with mean and confidence intervals |
| `"box"` | Box plot showing median, quartiles, and outliers |
| `"point"` | Point plot with error bars |
| `"strip"` | Strip plot (scatter with categorical axis) |
| `"swarm"` | Swarm plot (non-overlapping strip plot) |
| `"violin"` | Violin plot (combines box plot with KDE) |
| `"count"` | Count of observations in each category |

#### Key `catplot` Parameters

- **`x`**, **`y`**: Column names for axes
- **`hue`**: Column to split by color within each facet
- **`col`**: Column to split into separate columns of facets
- **`row`**: Column to split into separate rows of facets
- **`kind`**: Type of plot to create
- **`data`**: pandas DataFrame

> **Note**: For more custom facet grids, use the `seaborn.FacetGrid` class directly. See the [seaborn documentation](https://seaborn.pydata.org/) for details.

---

## 9.3 Other Python Visualization Tools

For **static graphics** (print or web): use **matplotlib** + **pandas** + **seaborn**.

For **interactive web graphics**: consider these alternatives:

| Library | Description |
|---------|-------------|
| [Altair](https://altair-viz.github.io) | Declarative, statistical visualization based on Vega-Lite |
| [Bokeh](http://bokeh.pydata.org) | Interactive visualization for modern web browsers |
| [Plotly](https://plotly.com/python) | Interactive, publication-quality graphs |

> **Recommended Reading**: *Fundamentals of Data Visualization* by Claus O. Wilke (O'Reilly), available at [clauswilke.com/dataviz](https://clauswilke.com/dataviz).

---

## 9.4 Conclusion

This chapter covered the fundamentals of data visualization in Python using pandas, matplotlib, and seaborn. The next chapter covers data aggregation and group operations.

---

## Quick Reference: Plot Types and Code Patterns

| Plot Type | pandas Method | seaborn Function | When to Use |
|-----------|--------------|------------------|-------------|
| **Line plot** | `s.plot()` / `df.plot()` / `df.plot.line()` | N/A | Time series, trends over ordered data |
| **Bar plot (vertical)** | `s.plot.bar()` / `df.plot.bar()` | `sns.barplot(x, y, data)` | Comparing categorical values |
| **Bar plot (horizontal)** | `s.plot.barh()` / `df.plot.barh()` | `sns.barplot(x, y, data, orient="h")` | Long category labels |
| **Stacked bar** | `df.plot.bar(stacked=True)` | N/A | Part-to-whole comparison across categories |
| **Histogram** | `s.plot.hist(bins=n)` | `sns.histplot(data, bins=n)` | Distribution of a single variable |
| **Density (KDE)** | `s.plot.density()` | `sns.histplot(data, kde=True)` | Smooth distribution estimate |
| **Scatter plot** | N/A | `sns.regplot(x, y, data)` | Relationship between two continuous variables |
| **Scatter matrix** | N/A | `sns.pairplot(data)` | Exploring all pairwise relationships |
| **Box plot** | N/A | `sns.catplot(kind="box", ...)` | Distribution summary with outliers |
| **Faceted plot** | N/A | `sns.catplot(x, y, col, row, hue, kind)` | Multi-dimensional categorical comparison |

---

## Important Notes and Caveats

1. **Jupyter cell grouping**: All plotting commands for a single figure must be in the same notebook cell. Plots are reset between cells.

2. **KDE requires SciPy**: `plot.density()` and `sns.histplot(..., kde=True)` need SciPy installed.

3. **Object-oriented API preferred**: Use `ax.plot()` methods rather than top-level `plt.plot()` for complex or multi-subplot figures. This gives you explicit control over which subplot you're modifying.

4. **Suppress output**: matplotlib returns object references (e.g., `<matplotlib.lines.Line2D at ...>`). Suppress these with a trailing semicolon: `ax.plot(data);`

5. **Legend requires explicit call**: Passing `label` to `plot()` is not enough; you must call `ax.legend()` to render it.

6. **matplotlib doesn't check label overlap**: When setting `wspace=0, hspace=0`, axis labels may overlap and need manual adjustment via explicit tick locations.

7. **seaborn error bars are 95% CI by default**: The black lines on `sns.barplot` bars represent 95% confidence intervals, not standard deviation. This is appropriate for observational data but may be misleading if the data isn't a random sample.

8. **pandas `plot()` uses matplotlib under the hood**: Additional keyword arguments to pandas plotting methods are passed through to the underlying matplotlib function.

9. **`ax` parameter for flexible placement**: Most pandas plotting methods accept an `ax` parameter, allowing you to place plots in specific subplots of a grid layout.

10. **DataFrame column names become legend titles**: When plotting a DataFrame, the column index name (e.g., `"Genus"`) is used as the legend title.

---

## Key Takeaways

### Choosing the Right Visualization

| Goal | Recommended Tool |
|------|-----------------|
| Quick exploratory plots from pandas objects | `df.plot()` / `s.plot()` |
| Publication-quality static figures with full control | matplotlib (object-oriented API) |
| Statistical plots with automatic aggregation | seaborn (`barplot`, `regplot`, `catplot`) |
| Multi-variable exploration | `sns.pairplot()` |
| Multi-dimensional categorical comparison | `sns.catplot()` with `col`/`row`/`hue` |
| Interactive web graphics | Altair, Bokeh, or Plotly |

### Best Practices

- **Start simple, refine iteratively**: Begin with a basic plot, then add labels, annotations, and styling.
- **Use the object-oriented API** (`fig, ax = plt.subplots()`) for anything beyond the simplest plots.
- **Always label your axes and provide a title** — a plot without context is useless.
- **Use legends** when plotting multiple series; exclude irrelevant lines with `label="_nolegend_"`.
- **Leverage seaborn** for statistical plots — it handles aggregation, error bars, and faceting automatically.
- **Customize for your audience**: Use `plt.style.use()` or `sns.set_style()` / `sns.set_palette()` for consistent aesthetics.
- **Save at appropriate resolution**: Use `dpi=400` or higher for publication-quality figures.
- **Visualization is exploratory**: Use plots to identify outliers, suggest transformations, and generate model ideas — not just for final presentation.

### Datasets Referenced in This Chapter

| Dataset | File | Purpose |
|---------|------|---------|
| S&P 500 index | `examples/spx.csv` | Financial crisis annotation example |
| Restaurant tips | `examples/tips.csv` | Bar plots, histograms, facet grids |
| Macroeconomic data | `examples/macrodata.csv` | Scatter plots, pair plots |
