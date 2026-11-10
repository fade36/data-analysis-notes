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