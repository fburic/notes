# Concatenating two DataFrames

**Note** Must have same number of rows

```python
df1 = df1.withColumn("id", monotonically_increasing_id())
df2 = df2.withColumn("id", monotonically_increasing_id())
df3 = df2.join(df1, "id", "outer").drop("id") 
```

A source: https://forums.databricks.com/questions/8180/how-to-merge-two-data-frames-column-wise-in-apache.html

- R: [`dplyr::bind_cols`](https://dplyr.tidyverse.org/reference/bind.html)
- Pandas: [`pandas.concat()`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.concat.html)
