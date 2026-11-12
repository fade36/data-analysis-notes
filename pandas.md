# Pandas

```python
import pandas as pd
```

## Creating data

To create DataFrame:

```python
warehouse = pd.DataFrame({"Apples": [31, 22], "Bananas": [12, 32]}, index=[2020, 2025])
```

or

```python
pd.DataFrame([[31, 12], [22, 32]], columns=['Apples', 'Bananas'], index=[2020, 2025])
```

is the same.

To create Series:

```python
pd.Series([1, 2, 3, 4, 5], index=["one", "two", "three", "four", "five"], name="blablabla")
```

Order is relevant.

To read csv file:

```python
pd.read_csv(path)
```

To use the first column (at index 0) as the row index of the DataFrame instead of generating a new sequential one: `index_col=0` as argument of `pd.read_csv`.

To save dataframe to csv:

```python
warehouse.to_csv(path)
```

---

## Accessing data

To access the "X" property of "reviews" we can use:

1. `reviews.X`
2. `reviews["X"]`
3. `reviews.iloc[:, (index of X)]`
4. `reviews.loc[:, "X"]`

### iloc

Works only with indices.

`dataframe.iloc[rows, cols]`

- `dataframe.iloc[0]` will return Series of the first row with name = col_name
- `dataframe.iloc[row_ind, cols_ind]` will return value in row_ind and cols_ind
- You can use `[:, cols_ind]` / `[rows_ind, :]`. `:` means every row / col.
- In all cases return is Series.
- Using `dataframe.iloc[[1,2,3], :]` will return DataFrame 1,2,3 rows
- Using `[]` in (i)loc[] will return DataFrame
- `dataframe.iloc[0:5]` returns 0,1,2,3,4 rows

### loc

Works with labels = row_labels, col_labels.

- `dataframe.loc["X"]` returns Series of all vals in row with row_name=index="X" (if exists)
- `dataframe.loc[:, ['X', 'Y', 'Z']]` returns DataFrame with cols X,Y,Z
- `dataframe.loc[0:5]` returns labels with names = 0,1,2,3,4,5

### set_index()

`dataframe.set_index("field")` sets as index column, column "field"

---

## Conditional selection

`dataframe.field == val` returns Series of True/False; name="field", dtype=bool

Used in loc args:

- `dataframe.loc[dataframe.field == val]` returns DataFrame with only vals
- `dataframe.loc[(dataframe.field1 == val) & (dataframe.field2 == val)]` returns DataFrame with only field1 == val AND field2 == val

### isin

`dataframe.loc[dataframe.field.isin(['val1', 'val2'])]` returns DataFrame with field == val1 OR field == val2

### isnull / notnull

`dataframe.loc[dataframe.field.notnull()]` returns DataFrame without NULLs in 'field'

---

## Assigning data

`dataframe["field"] = val` will assign to every row value 'val'

If "field" does not exist -> will create and put to the end of all cols

---

## Summary functions and maps

### Summary functions

Shrink a column into a result.

- `df.col.describe()`: type-aware summary. For numbers: `count, mean, std, min, 25%, 50%, 75%, max`. For strings: `count, unique, top, freq`
- `df.col.mean()`: mean (skips NaN)
- `df.col.unique()`: array of distinct values
- `df.col.value_counts()`: how many times each value occurs (NaN is not counted by default)
- `df.col.max()` returns the **value**, `df.col.idxmax()` returns the **index label** of the row where the max is

### Maps

Transform every value into a new one. Length stays the same.

`Series.map(func)`: `func` gets **one value** and returns the new value.

```python
m = df.points.mean()
df.points.map(lambda p: p - m)
```

`p` is the parameter of the lambda. `map` passes every value of the column into it, one by one.

`DataFrame.apply(func, axis=...)`: `func` gets a **whole row or column**.

- `axis="columns"`: function gets one **row** at a time
- `axis="index"`: function gets one **column** at a time

```python
def remean_points(row):
    row.points = row.points - m
    return row

df.apply(remean_points, axis="columns")
```

`map` and `apply` return a **new** object, the original is not changed. To keep the result: `df["new_col"] = df.points.map(...)`.

### Vectorized operations (preferred)

Operators work on whole columns and are faster than `map` / `apply`:

```python
df.points - m                       # column minus a single number
df.country + " - " + df.region      # column + column (NaN if any part is NaN)
df.points / df.price                # element by element
```

`+ - * / > < ==` all work this way.

Use `map` / `apply` only when plain operators are not enough (e.g. `if / else` logic or several columns of one row):

```python
df.points.map(lambda p: "high" if p >= 90 else "low")
```

### Example: best bargain (highest points per price)

```python
ratio = reviews.points / reviews.price
ind = ratio.idxmax()
bargain_wine = reviews.loc[ind, "title"]
```

---

# Grouping and Sorting 

## Grouping

Group by syntax in pd:
```
dataframe.groupby('field1').field2.(any aggregate func)
```

for instance wine dataset:

```
reviews.groupby('winery').apply(lambda df: df.title.iloc[0])
```

`reviews.groupby('winery')` returns groups of wines group by winery
`apply(lambda df: df.title.iloc[0])` works with each group one by one, returning first title of each group

```reviews.groupby(['country']).price.agg([len, min, max])```
allows to run a bunch of aggregate funs in one time. Returns DataFrame

## Multi-indexes

if group by used with several parameters, we can get initial df by:

`countries_reviewed.reset_index()`

## Sorting 

`sort_values()` Like order by in SQL. by default - asc. accepts more then one field in []

```sort_values(by='field, ascending= True / False ')```

`sort_index()` - sort in index order

---

# Data types and missing values 

## Data types 

to check data type of the column:

```python
dataframe.field.dtype
```

to change data type, if its possible:

```python
dataframe.field.astype("float64" / "int64" ...)
```

## Missing values

to check column on NaN\`s:

```python
dataframe[pd.isnull(dataframe.field)]
```

to replace missing value:

```python
dataframe.field.fillna("It is missing")
```

### Replace a non-null value

```python
dataframe.field.replace("This", "On this")
```

---

# Renaming and Combining

## Renaming

Syntax:

Column renaming:

```python
dataframe.rename(columns={'old': 'new'})
```

Rows(index) renaming:
```python
dataframe.rename(index={0: 'firstEntry', 1: 'secondEntry'})
```

To rename not values but axis:

```python
dataframe.rename_axis("new_name_4_row", axis="index").rename_axis("new_name_4_col", axis="columns")
```

## Combining

### Three core methods:

#### concat():

- Joins dataframes along particular axis

Syntax:

```python
pd.concat([dataframe1, dataframe2, ...], axis=0/1, ignore_index=True/False)
```
axis=0 -> joins dataframes by rows
axis=1 -> joins dataframes by cols
ignore_index=True -> creates new index sequence
ignore_index=False -> keeps initial index sequence

#### join():

- Joins dataframes by index
- Left join by default

Syntax:

```python
first.join(second)
```

if there are equal col names in both of the dataframes, use fro example:

```python
first.join(second, lsuffix=_fromFirst, rsuffix=fromSecond) 
```

if inner join needed use:

```python
first.join(second, how="inner"") 
```

Example on Powerlifting datasets:

```python
powerlifting_meets.set_index("MeetID").join(powerlifting_competitors.set_index("MeetID"))
```

#### merge():

- Similar to joins in sql
- There are 4 types of merges: how=("inner", "outer", "left", "right")
- How to join ? 

	- If indicies are equal -> on="id"
	- Else -> left_on="id_1", right_on="id_2"

Syntax on some students datasets:

```python
pd.merge(students, grades, on="student_id", how="inner/outer/left/right")
```

With different indicies:

```python
pd.merge(students, grades, left_on="student_id", right_on="id")
```













 