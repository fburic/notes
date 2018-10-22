

## Setting locale

Versions: 
* High Sierra 10.13.6

Mostly some Linux programs complain about locale not being set.
Add the following to `~/.profile`:

```
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8
```
