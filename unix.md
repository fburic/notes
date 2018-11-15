
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

## Simple path bookmarking 

Keeps track of paths by writing them to a file called `bookmark` in your home directory.

**NOTE!** Make sure you don't have a file with that name or change the commands below.

**TODO** Make bookmark file name a variable instead of hardcoded.

* `bookmark` will move to the last bookmarked directory (via `pushd` so one can go back easily with `popd`).
* `bookmark N` will move to bookmarked directory number `N`. 
* `listbookmarks` prints the numbered list of currrent bookmarks
* `setbookmark` will append the current directory to the list of bookmakrs. This will become the defautl bookmark.
* **TODO**: Ability to remove bookmark

Add to `.bashrc` or `.profile`:

```
function bookmark {
     if [[ -n "$1" ]]; then
          pushd $(sed "$1q;d" ~/bookmark)
     else
          pushd $(tail -n 1 ~/bookmark)
     fi
}
alias setbookmark='pwd >> ~/bookmark'
alias listbookmarks='nl ~/bookmark'
```
