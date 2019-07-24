
## `which` should not be used

Use `type` and/or `command -v` instead. But see post below for caveats.

It looks like `which` may not reflect actual files and paths and is a 
"broken heritage from the C-Shell and is better left alone in Bourne-like shells."
https://unix.stackexchange.com/questions/85249/why-not-use-which-what-to-use-then


## Recursive FTP download with file pattern filtering

The ftp address does not support wildcards. One must give an "accept list" to `wget`

```bash
wget -A FILE_PATTERN -r -np ftp://DIRECTORY
```

* `-np` == "no parent" Apparently the recursive behaviour is also to go to parent directories.
  This prevents that.
* `-r` - recursive

### Example
```bash
wget -A "example*.XML" -r -nv -np -nc ftp://ftp.example.com/data/
```

### Other useful options
* `-nv` == "non-verbose" (good for when saving log)
* `-nc` - Does not check if target files already exist (overwrite)

## Simple path bookmarking 

Keeps track of paths by writing them to a file called `bookmark` in your home directory.

**NOTE !** Make sure you don't have a file with that name 
or change the `BOOKMARK_FILE` env variable below.

* `bookmark` moves to the last bookmarked directory (via `pushd` so one can go back easily with `popd`).
* `bookmark N` moves to bookmarked directory number `N`. 
* `listbookmarks` prints the numbered list of currrent bookmarks
* `setbookmark` appends the current directory to the list of bookmakrs. This will become the default bookmark.
* `rmbookmark` removes the last bookmarked directory.
* `rmbookmark N` removes the bookmarked directory number `N`.

Add to `.bashrc` or `.profile`:

```bash
export BOOKMARK_FILE=~/bookmark
function bookmark {
    if [[ -n "$1" ]]; then
        pushd $(sed "$1q;d" ${BOOKMARK_FILE})
    else
        pushd $(tail -n 1 ${BOOKMARK_FILE})
    fi
}
function rmbookmark {
    if [[ -n "$1" ]]; then
	sed -i "${1}d" ${BOOKMARK_FILE}
    else
	sed -i "$ d" ${BOOKMARK_FILE}
    fi
}
alias setbookmark='pwd >> ${BOOKMARK_FILE}'
alias listbookmarks='nl ${BOOKMARK_FILE}'

```

## X11 forwarding

* `X11 connection rejected because of wrong authentication.` Fix: https://unix.stackexchange.com/questions/215558/why-am-i-getting-this-message-from-xauth-timeout-in-locking-authority-file-ho



## Useful aliases

```bash
alias lastfile="ls -t1 | head -n 1"
alias timestamp='date "+%Y%m%d-%H%M%S"'
```


## Send message to another user

```
echo "Some text goes here" | write <user>
```
