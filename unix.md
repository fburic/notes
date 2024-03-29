
## The command `which` should not be used

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

## Gnuplot 

Pattern: `<DATA COLUMNS> | gnuplot -e "set term dumb; plot '-' with linespoints pt 'O' notitle"`

Examples

```bash
# Ox
tail -n+2 training.log | cut -d ',' -f 3 | gnuplot -e "set term dumb; plot '-' with linespoints pt 'o' notitle"

# Vera
ml load iccifort/2018.3.222-GCC-7.3.0-2.30 impi/2018.3.222 gnuplot/5.2.2
cat -n <(grep "|-Score" slurm-1547670.out | cut -d ' ' -f 1,3) | gnuplot -e "set term dumb; plot '-' with linespoints  pt 'o' notitle"
```

References:

* https://www.datafix.com.au/BASHing/2019-06-21.html



## X11 forwarding

* `X11 connection rejected because of wrong authentication.` Fix: https://unix.stackexchange.com/questions/215558/why-am-i-getting-this-message-from-xauth-timeout-in-locking-authority-file-ho


## Security

### Reuse `ssh-agent` from previous session

Setup: from your home directory, start an agent and record relevant env vars.

```bash
eval $(ssh-agent -s | tee agent.env)
chmod go-rwx agent.env

ssh-add ~/.ssh/id_rsa
```

Add the following to `.bsahrc` to bind any new terminal session to the running agent:

```bash
# Get env vars for recorded ssh-agent
source agent.env

if $(ps -p $SSH_AGENT_PID > /dev/null 2>&1)
then
    echo "Using existing ssh-agent instance"
    source agent.env
else
    echo "Starting new ssh-agent"
    eval $(ssh-agent -s | tee agent.env)
fi
```

References:
* https://unix.stackexchange.com/questions/439254/use-my-already-running-ssh-agent-process
* http://rabexc.org/posts/using-ssh-agent


## rsync with jump

```bash
rsync -azv -e 'ssh -A -J USER@PROXYHOST:PORT' foo/ dest:./foo/
```

`-J (ProxyJump)` 

if your version of ssh is new enough (OpenSSH >= v7.3)

"Note that I'm using -A (agent forwarding) but it should also work with password authentication if you don't use keys, and, of course, you can replace proxy with B and dest with C in your example."

Source: https://superuser.com/a/1115998


## Useful aliases

```bash
alias lastfile="ls -t1 | head -n 1"
alias timestamp='date "+%Y%m%d_%H%M%S"'
alias e='emacs -nw'
```


## Send message to another user

```
echo "Some text goes here" | write <user>
```
