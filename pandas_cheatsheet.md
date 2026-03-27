# Pandas Cheatsheet 🐼
> Based on your [pandas_basics.ipynb](file:///c:/Users/Vir%20Bhadra/Desktop/Coding/Machine%20Learning/Pandas/pandas_basics.ipynb) notebook using IPL [matches.csv](file:///c:/Users/Vir%20Bhadra/Desktop/Coding/Machine%20Learning/Pandas/matches.csv) and [deliveries.csv](file:///c:/Users/Vir%20Bhadra/Desktop/Coding/Machine%20Learning/Pandas/deliveries.csv)

---

## What is Pandas?
Pandas is a Python library for **data manipulation and analysis**. It offers two key data structures:
- **DataFrame** — tabular data (rows + columns), like an Excel sheet
- **Series** — a single column/row from a DataFrame

```python
import pandas as pd
import numpy as np
```

---

## 1. `pd.read_csv()` — Load Data

| Parameter | Description |
|---|---|
| `filepath` | Path to the CSV file |
| `sep` | Delimiter (default: `,`) |
| `header` | Row to use as column names (default: 0) |
| `index_col` | Column to use as row index |
| `nrows` | Number of rows to read |

```python
data = pd.read_csv('matches.csv')   # loads the file into a DataFrame
delivery = pd.read_csv('deliveries.csv')
```

---

## 2. `df.head()` / `df.tail()` — Preview Data

| Function | What it does |
|---|---|
| `df.head(n)` | Returns **top n rows** (default: 5) |
| `df.tail(n)` | Returns **last n rows** (default: 5) |

```python
data.head()       # top 5 rows
data.head(10)     # top 10 rows
data.tail()       # last 5 rows
```

---

## 3. `df.shape` — DataFrame Dimensions (Attribute, not a function)

Returns a tuple **(rows, columns)**

```python
data.shape        # (636, 18) → 636 rows, 18 columns
data.shape[0]     # 636 → number of rows
data.shape[1]     # 18  → number of columns
```

---

## 4. `df.info()` — Detailed Metadata

Shows **column names, data types, non-null counts, and memory usage**.

```python
data.info()
# Output:
# Column       Non-Null Count  Dtype
# city         629 non-null    object
# winner       633 non-null    object
# umpire3      0 non-null      float64
```

---

## 5. `df.describe()` — Statistical Summary

Works **only on numerical columns** by default. Shows count, mean, std, min, max, percentiles.

```python
data.describe()
```

---

## 6. Selecting Columns

```python
data['winner']                          # single column → returns Series
data[['team1', 'team2', 'winner']]      # multiple columns → returns DataFrame
```

> **Note:** Single `[]` = Series, Double `[[]]` = DataFrame

---

## 7. `df.iloc[]` — Integer-Based Indexing (by position)

| Usage | What it does |
|---|---|
| `df.iloc[0]` | First row |
| `df.iloc[1:10:2]` | Rows 1–9, every 2nd row (slicing) |
| `df.iloc[:, [4,5,10]]` | All rows, columns at positions 4, 5, 10 |

```python
data.iloc[0]             # row at index 0
data.iloc[1:10:2]        # rows 1,3,5,7,9
data.iloc[:, [4,5,10]]   # fancy column indexing (team1, team2, winner)
```

---

## 8. Boolean Masking / Filtering

Filter rows using a condition that returns `True`/`False` for each row.

```python
mask = data['city'] == 'Hyderabad'
data[mask]                              # only Hyderabad matches

# Multiple conditions: use & (AND), | (OR)
mask1 = data['city'] == 'Hyderabad'
mask2 = data['date'] > '2010-01-01'
data[mask1 & mask2]                     # Hyderabad matches after 2010
```

> **Tip:** Don't use `and`/`or` — use `&`/`|` with boolean masks!

---

## 9. `Series.value_counts()` — Count Unique Values

Returns how many times each unique value appears, sorted **descending**.

```python
data['winner'].value_counts()
# Mumbai Indians              92
# Chennai Super Kings         79
# ...
```

---

## 10. `df.plot()` / `Series.plot()` — Quick Plotting

| Parameter | Description |
|---|---|
| `kind` | Plot type: `'bar'`, `'barh'`, `'line'`, `'pie'`, `'hist'` |
| `title` | Plot title |
| `figsize` | Tuple `(width, height)` |

```python
data['winner'].value_counts().head().plot(kind='barh')
# Horizontal bar chart of top 5 winners
```

---

## 11. `df.sort_values()` — Sort Rows

| Parameter | Description |
|---|---|
| `by` | Column name(s) to sort by |
| `ascending` | `True` (default) = A→Z / 0→9; `False` = Z→A / 9→0 |
| `inplace` | Modify the original DataFrame |

```python
runs['batsman_runs'].sum().sort_values(ascending=False).head()
# Top 5 scorers in descending order
```

---

## 12. `df.groupby()` — Group and Aggregate

Groups rows by a column value, then apply aggregate functions.

```python
runs = delivery.groupby('batsman')     # group by batsman name
runs.get_group('V Kohli').shape        # get all rows for V Kohli

# Sum of runs per batsman
runs['batsman_runs'].sum().sort_values(ascending=False).head()
# SK Raina    4548
# V Kohli     4423

# Count of fours per batsman
new_delivery.groupby('batsman')['batsman_runs'].count().sort_values(ascending=False).head(5)
```

> **Common aggregations:** `.sum()`, `.mean()`, `.count()`, `.max()`, `.min()`

---

## 13. `Series.isin()` — Check Membership

Returns a boolean mask: `True` if the value is **in the list**.

```python
batsman_list = ['V Kohli', 'MS Dhoni', 'SK Raina']
final = delivery[delivery['batsman'].isin(batsman_list)]
```

---

## 14. `df.drop_duplicates()` — Remove Duplicate Rows

| Parameter | Description |
|---|---|
| `subset` | Column(s) to consider for duplicates |
| `keep` | `'first'` (default), `'last'`, or `False` (drop all) |
| `inplace` | Modify original DataFrame |

```python
data.drop_duplicates(subset=['city']).shape
# Returns 31 rows → 31 unique cities

data.drop_duplicates(subset=['city', 'season']).shape
# No duplicate (city + season) combo

data.drop_duplicates('season', keep='last')[['season', 'winner']].sort_values('season')
# IPL winner per season (last match of each season wins)
```

---

## 15. `pd.merge()` — Join Two DataFrames

Merges two DataFrames on a common column (like SQL JOIN).

| Parameter | Description |
|---|---|
| `left`, `right` | The two DataFrames |
| `on` | Column name to join on |
| `how` | `'inner'`(default), `'left'`, `'right'`, `'outer'` |

```python
pd.merge(df1, df2, on='season', how='left')
```

---

## 16. `df.corr()` — Correlation Matrix

Shows the **correlation coefficient** between numerical columns.
- Value close to **+1** → strong positive correlation
- Value close to **–1** → strong negative correlation
- Value close to **0** → no correlation

```python
data.corr(numeric_only=True)

import seaborn as sns
sns.heatmap(data.corr(numeric_only=True))  # visualize as heatmap
```

---

## 17. `df.rename()` — Rename Columns or Index

| Parameter | Description |
|---|---|
| `columns` | Dict mapping `{old_name: new_name}` |
| `index` | Dict mapping index labels |
| `inplace` | Modify the original DataFrame |

```python
data.rename(columns={'city': 'place', 'id': 'CID'}).head(1)
# Renames 'city' → 'place' and 'id' → 'CID'
```

---

## 18. `df.set_index()` / `df.reset_index()` — Manage Index

```python
data.set_index('id', inplace=True)    # Use 'id' column as the index
data.reset_index(inplace=True)        # Restore default integer index
```

---

## 19. `df.dropna()` — Remove Missing (NaN) Values

| Parameter | Description |
|---|---|
| `axis` | `0` = drop rows (default), `1` = drop columns |
| `how` | `'any'` (default) = drop if any NaN; `'all'` = drop only if ALL are NaN |
| `subset` | Only consider specific columns |
| `inplace` | Modify the original DataFrame |

```python
data.dropna(how='all').shape
# Drops rows where EVERY value is NaN
# Returns (636, 18) → no all-NaN rows in this dataset
```

---

## 20. `df.fillna()` — Fill Missing Values

| Parameter | Description |
|---|---|
| `value` | Value to fill NaN with (scalar, dict, or Series) |
| `method` | `'ffill'` (forward fill) or `'bfill'` (backward fill) |
| `inplace` | Modify the original DataFrame |

```python
data.fillna(0)          # replace all NaN with 0
data['city'].fillna('Unknown')  # fill NaN in 'city' only
```

---

## Quick Reference Table

| Function / Method | Type | Purpose |
|---|---|---|
| `pd.read_csv()` | Function | Load CSV file |
| `df.head(n)` | Method | First n rows |
| `df.tail(n)` | Method | Last n rows |
| `df.shape` | Attribute | (rows, columns) |
| `df.info()` | Method | Column info, dtypes, nulls |
| `df.describe()` | Method | Stats for numeric columns |
| `df['col']` | Indexing | Select one column (Series) |
| `df[['c1','c2']]` | Indexing | Select multiple columns (DataFrame) |
| `df.iloc[r, c]` | Indexing | Select by integer position |
| `df[mask]` | Filtering | Filter rows by condition |
| `s.value_counts()` | Method | Count occurrences of each unique value |
| `df.plot(kind=...)` | Method | Quick visualization |
| `df.sort_values(by=...)` | Method | Sort rows |
| `df.groupby()` | Method | Group and aggregate |
| `s.isin(list)` | Method | Membership check |
| `df.drop_duplicates()` | Method | Remove duplicate rows |
| `pd.merge()` | Function | Join two DataFrames |
| `df.corr()` | Method | Correlation matrix |
| `df.rename(columns=...)` | Method | Rename column headers |
| `df.set_index()` | Method | Change the index column |
| `df.reset_index()` | Method | Restore default index |
| `df.dropna()` | Method | Remove NaN rows/columns |
| `df.fillna()` | Method | Fill NaN with a value |

---

> 💡 **Pro Tips from your notebook:**
> - Use `Shift + Tab` inside a function in Jupyter to see its docs
> - A single column selection returns a **Series**; use `[[]]` to keep it as a **DataFrame**
> - Combine masks with `&` (AND) or `|` (OR), never `and`/`or`
> - `groupby` + `.sum()` / `.count()` / `.mean()` is the power combo for analysis
