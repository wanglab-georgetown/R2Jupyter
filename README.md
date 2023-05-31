# R in Jupyter Notebook

## Overview
`R` is a programming language for statistics and data analysis. It is popular among data scientists and bioinformaticians. On the other hand, Jupyter Notebook is a web-based interactive computing platform that is widely used by Python programmers. There are use cases where a Python programmer may want to use some dedicated `R` functions in a Python Jupyter Notebook. We review three ways to use `R` in Jupyter Notebook:

- Run `R` natively in Jupyter Notebook by installing an `R` kernel.
- Import and call `R` function in Python.
- Transfer data from Python to `R`, run `R` function, and transfer the results back to Python.

We show the details of each method below.

## Install `R`

Download R from https://cran.r-project.org/ and install.

## Add `R` to Jupyter Notebook

### `R`

Open RStudio or type `R` in command line. Run the following:

`install.packages(c('repr', 'IRdisplay', 'evaluate', 'crayon', 'pbdZMQ', 'devtools', 'uuid', 'digest'))`

`devtools::install_github('IRkernel/IRkernel')`

if the previous line does not work, try this instead

`devtools::install_github('IRkernel/IRkernel', force=T)`

Install IRKernel for the current user

`IRkernel::installspec()`

Or install IRKernel for all users (or the previous line fails)

`IRkernel::installspec(user = FALSE)`

### Python

1. Open a new terminal

2. Create new conda environment (*Optional*): You can create a new conda environment:

`conda create -n rkernel`

and activate the environment

`conda activate rkernel`

3. Install Jupyter: If jupyter has not been installed, run

`conda install jupyter`

4. Install r-irkernel: Run the following command and type `y` if prompted

`conda install -c r r-irkernel`


5. Run Juptyer Notebook: In terminal, type

`jupyter notebook`


6. To create a new Notebook for R:

click on `New` on the top right-hand-side of your screen  
select `R` from the drop-down list  
You can then code in `R` and click on `Run` to run the code.


## Import and call `R` function in Python with rpy2

In some cases, one mainly uses Python and may only occasionally need to call some `R` function that is not available in Python. `rpy2` provides an interface that allows one to run `R` functions in Python.

To install `rpy2`, in terminal run

`pip install rpy2`

or in Jupyter Notebook run

`!pip install rpy2`

You can then import rpy2 packages

`import rpy2`

To install and load R packages, we can use `rpy2.robjects.packages.importr()`. For example, to install and import the `stats` package in `R`, we can do

`from rpy2.robjects.packages import importr`  
`utils = importr('utils')`  
`utils.install_packages('stats')`  
`stats = importr('stats')`

A detailed example of using `importr` to compare `R` `p.adjust` with its equivalent Python implementation can be found in  [`R2Jupyter_example.ipynb`](https://github.com/wanglab-georgetown/R2Jupyter/blob/main/R2Jupyter_example.ipynb).

Sometimes the `R` function name may change after being imported into Python. If you cannot find it, call the following lines to find it:  
`methods = list(dir(stats))`  
`[s for s in methods if "adjust" in s]`

## Transfer data from Python to `R` and transfer the results back to Python

In some cases, one may want to reuse some existing `R` code rather than rewrite the whole `R` code in Python. In other cases, some `R` packages may be hard to use by importing into Python through `rpy2.robjects.packages.importr()`. It is then preferred to transfer data from Python to `R`, run `R` code natively, and then transfer the results back to Python.

To start, run the following in a cell

`%load_ext rpy2.ipython`

In another cell, run `R Magic`

`%%R -i input -o output`  
`some R code uses input and gets output as results`

where input and output are Python variables.

An example of comparing using `R Magic` to call `R` `p.adjust` with importing it to Python can be found in [`R2Jupyter_example.ipynb`](https://github.com/wanglab-georgetown/R2Jupyter/blob/main/R2Jupyter_example.ipynb).

To pass a special data structure such as a dataframe to `R`, we need to activate `pandas2ri`

`from rpy2.robjects import pandas2ri`  
`pandas2ri.activate()`

For *single-cell RNA-seq* data analysis, the data are usually stored in `SingleCellExperiment` in `R`. We can create an `AnnData` in Python and use `anndata2ri` to convert from AnnData to `SingleCellExperiment` and back.

Install `anndata2ri` using  
`pip install anndata2ri`

Import and activate  
`import anndata2ri`  
`anndata2ri.activate()`

A detailed example of creating an `AnnData` and using `anndata2ri` to convert to `R`, calling MAST on the converted `SingleCellExperiment`, and converting results back to Python can be found in 
https://github.com/wanglab-georgetown/countmodels/blob/main/tests/mast_test.ipynb


