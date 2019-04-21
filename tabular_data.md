
## Diff CSVs on field and column level


### With Git

```
git diff --color-words="[^[:space:],]+" x.csv y.csv
```

### With daff 

https://github.com/paulfitz/daff

```
pip install daff
daff x.csv y.csv
```

## Compare Pandas and Spark dataframes

* package: https://pypi.org/project/datacompy/
* by hand: https://stackoverflow.com/questions/17095101/outputting-difference-in-two-pandas-dataframes-side-by-side-highlighting-the-d
