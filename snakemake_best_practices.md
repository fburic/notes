# Snakemake Best Practices

## Connect rules explicitly

Specify dependencies by reference, not by actual target names:

```make
rule C:
  input:
        rules.A.output, rules.B.output,
```

**Constraint:** Input rules must be *defined before* they're referenced.

**Exception:** 
The dummy `all` rule can't be used like this since it must come first in the file
and setting up some extra dummy rule is probably not worth it, plus wildcards can't be given
as targets.


## Keep Snakefiles on the meta level

Don't write processing Python code in the rules themselves unless it's a few one-liners.

Why:
* The purpose of a Snakefile is to specify the overall build workflow (the "**what**") and it should not be
concerned with **how** steps execute, merely their input and output
* Modularization: The processing programs may change, but as long as their I/O is the same, 
it makes not sense to change the Snakefile and it makes the pipeline less robust
* Less headache: processes with complicated input-output mappings can be encapsulated in a wrapper that focuses on the end results

**Example**

Instead of wrrestling an awkward tool that generates many files per input inside the Snakefile
write a (maybe simple) wrapper for it in a Python module.

That way: the Snakemake rule becomes 1:1, is easier to control, is cleaner (easier to understand and change), 
and changing the wrapper behaviour is isolated from the Snakefile as long as you still output the relevant file.

```make
import wrappers

rule complicated_prog_execution:
  """
  FileGerenator creates 1000 files in the given output dir.
  Using a wrapper to run it and fetch the interesting output file.
  """
  ...
  run:
      wrapper.run_file_generator(input, output)
```   


## Use flag files for commands with unknown  / nondeterministic output

```make
rule boom:
    input:
        rules.A.output, rules.B.output
    output:
        touch('boom.DONE')
```

Note that one can still later refer to `rules.boom.output`


## Document cryptic / surprising rules

Like Python functions, rules accept a doc string.
While one should always try to use meaningful variable names etc.,
it's sometimes difficult to avoid complicated code, 
especially when dealing with other people's crappy software

```make
rule wtf:
    """
    I was forced to do this since there's no other way
    """
```

Note that the `messsage` clause defines a description to be displayed / logged during execution.
It's not entirely appropariate for documentation.
