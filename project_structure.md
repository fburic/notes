# Computational Project Structure

Based on typical software engineering practices.
Example applied to compbio projects: [A Quick Guide to Organizing Computational Biology Projects](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1000424)


## General Principles

### Reproducibility and Tracking

A program or pipeline `Process` will behave like

`Process[version](data, parameters) -> results` 

So, assuming `data` is fixed, in order to recover any results, one needs to have access to:

1. The data  [stored and tracked (*) with some dedicated storage]
2. The given version of the process  [tracked with version control]
3. The parameters used during process run  [tracked with version control along with results]

#### Data

While generally assuming the input data to be fixed, corrections or new, related version may be appear.
This increases the need of also assigning a version number to the data, using some sort of dedicated storage.
Typical version control is inadequate since most systems are built for text.


#### Source code

Should be tracked with a version control system and any results must mention or otherwise record which version
of the code it ws produced with.

#### Parameters

Parameter values should be tracked with a version control system 
alongside (in sync) metainformation of the results (**not** the results themselves).
Here the assumption is that the results should be essentially similar with minor changes of the code base (weak coupling)
but parameters directly determine results (strong coupling).

One can of course consider cases where the code, parameters, and result metainformation are tracked as part of the same repository,
especially under development, when multiple algorithms are considerd, thus making the coupling to results stronger.
Tracking is also simpler in this case. 
However, the software is often made available separetely and is often reusable, so one may want to keep it independent after a certain point.

#### Results

The results need not be stored and should not if the size is problematic.
They should always be recoverable given the input data, process version, and parameters.



## Tests

TODO
