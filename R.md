## Summarize and keep all columns

https://stackoverflow.com/questions/30024437/applying-group-by-and-summarise-on-data-while-keeping-all-the-columns-info


## Open file across SSH

```r
data <- read_csv( pipe( 'ssh <hostname> "cat path/to/file.csv"' ))
```

Further reading: https://cran.r-project.org/doc/manuals/R-data.html#Connections


## Locale errors on package install

Quit R, add the env vars below to `.bashrc`, then either source it or start a new terminal session.
A new session of R should not report the errors:

```bash
export LANG="en_US.UTF-8"
export LC_COLLATE="en_US.UTF-8"
export LC_CTYPE="en_US.UTF-8"
export LC_MESSAGES="en_US.UTF-8"
export LC_MONETARY="en_US.UTF-8"
export LC_NUMERIC="en_US.UTF-8"
export LC_TIME="en_US.UTF-8"
export LC_ALL="en_US.UTF-8"
```

https://stackoverflow.com/questions/27299420/how-to-get-rid-of-warning-messages-after-installing-r/28163310


## Conda chunk wrapper

```r
conda_engine <- function(options) {
    options$engine = "bash"
    options$engine.opts = "-i"
    options$code = paste(paste0("conda activate ", options$env),
                         options$code, sep = "\n")
    knitr::knit_engines$get("bash")(options)
}

knitr::knit_engines$set(conda = conda_engine)
```
 
```
```{conda, env="sequencing"}
tree
```
```
