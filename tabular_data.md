## Theta-joins aka Non-equi jons

R:

http://thug-r.life/post/2017-08-06-fuzzy-joining/
https://channel9.msdn.com/Events/useR-international-R-User-conference/useR2016/Efficient-in-memory-non-equi-joins-using-datatable

Spark:

http://kirillpavlov.com/blog/2016/04/23/beyond-traditional-join-with-apache-spark/

Theory:

https://en.wikipedia.org/wiki/Relational_algebra#%CE%B8-join_and_equijoin



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
