
## `which` should not be used

It looks like `which` may not reflect actual files and paths and is a 
"broken heritage from the C-Shell and is better left alone in Bourne-like shells."
https://unix.stackexchange.com/questions/85249/why-not-use-which-what-to-use-then


## Recursive FTP download with file pattern filtering

The ftp address does not support wildcards. One must give an "accept list" to `wget`

```
wget -A FILE_PATTERN -r -np ftp://DIRECTORY
```

* `-np` == "no parent" Apparently the recursive behaviour is also to go to parent directories.
  This prevents that.
* `-r` - recursive

### Example
`wget -A "example*.XML" -r -nv -np -nc ftp://ftp.example.com/data/`

### Other useful options
* `-nv` == "non-verbose" (good for when saving log)
* `-nc` - Does not check if target files already exist (overwrite)
