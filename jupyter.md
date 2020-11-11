## Display NumPy arrays as heatmaps

Sometimes I find myself wanting to quickly see heatmaps of matrices rather than their numerical values.
So I'd like to just evaluate a Jupyter cell like `In [1]: x` and have the heatmap displayed.
A quick hack for this is to dynamically modify (i.e. monkeypatch) the `numpy.ndarray` to support a `seaborn.heatmap` representation.

**Here's how it works:**

Jupyter uses IPython's [display](https://ipython.readthedocs.io/en/stable/api/generated/IPython.display.html#functions) functions to output the contents of objects using various formats (including HTML, Javascript, and images).

When you evaluate a cell like `In [1]: x`, IPython uses one of the class's `__repr__` or `_repr_*_` representation methods.
For `numpy.ndarray` this is just the nicely formatted numerical content of the array (e.g. it inserts newlines and ellipsis when the array is too large).

One is free to define whatever [representations]( https://ipython.readthedocs.io/en/stable/config/integrating.html) for one's own classes to pass to IPython.
However, one can't redefine `numpy.ndarray.__repr__` since numpy uses C data strucures, similar to Python's built-in classes and functions.

[Forbidden Fruit](https://github.com/clarete/forbiddenfruit) is a package that allows extending built-in types. To quote the doc: "If that's a good idea or not, you tell me."

**To make it work in a notebook:*

```python
import numpy as np
import seaborn as sns
from forbiddenfruit import curse, reverse

def repr_ndarray_as_heatmap(self):
    sns.heatmap(self, cmap='Blues')
    #print(x)   # uncomment to also show numerical values
    return ""   # gotta return a str

# Add the _repr_html_ method to np.ndarray for IPython to use for output
curse(np.ndarray, "_repr_html_", repr_ndarray_as_heatmap)
```

To undo this, run 
```python reverse(np.ndarray, "_repr_html_")```

**Note** Reimporting the numpy module doesn't seem to rever the change. Might be best to just restart the kernel in some cases.
This looks like a pretty irresopnsible approach so it's only recommended for notebooks and inspecting data.





## Install kernel for conda environment

```bash
source activate myenv
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

See:
* https://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments
* https://stackoverflow.com/questions/39604271/conda-environments-not-showing-up-in-jupyter-notebook
