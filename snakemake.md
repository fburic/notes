## Dynamic, matched input and output

**Scenario**: 
- The rule has many dynamic (unknown a priori) input files. (E.g. It should run only on a subset of `input/*.csv`, with the size determined programmatically)
- The rule should 1:1 match output files with input (E.g. create  `output/NAME.out` for each `input/NAME.csv`.

**Method**:  Create a wildcard association between dynamic input and output: https://stackoverflow.com/a/44590076/10725218


## Sidestepping cyclical dependene situations

It normally makes no sense to have cyclical relations and if this happens it nearly always
points to an error in the specification structure.

However, there may be legitimate reasons behind what Snakemake detects as cyclical.
One way to sidestep the issues is to use the `params` clause instead of `input`.
Obviously, consider carefully if this is necessary.


## Draw dependency graph as ASCII

Useful for debugging over ssh:

```shell
snakemake --forceall -s Snakefile --dag |  graph-easy --from=dot --as_ascii
```

Needs `graph-easy` installed: https://metacpan.org/pod/Graph::Easy
