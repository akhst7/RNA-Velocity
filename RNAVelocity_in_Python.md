# RNAVelocity Analysis by using Python libraries including Scanpy, ScVelo, and CellRank #
## Introduction ##
There are several R packages that deals with RNAVelocity related analyses such as pseudotime and lineagetrace, however, Python defintely has edges over R regarding these types of analyses.  There is a suite of Python packages including ```Scanpy, ScVelo, and CellRank``` that provide a computational basis for rather complicated and broader filed of the genomic biology.  

Similar to R, You can run Python straight in the terminal however, using Python IDE makes your life much easier. I used to use ```Jupyter lab``` but recently, I've made a switch to ```Positron```, an new multiplatform IDE developed by Posit.  It turns out that ```Positron``` can run R as well, and I thought I could run both Python and R at same time, side by side in the same UI instead of running separate Python and R IDEs, ```Jupyter lab``` and ```Rstudio``` respectively, x  Unfortunately however, ```Positron``` in its current form does not replace ```Rstudio```.  

A following does not cover installation of these Python packages, which are well described in their github sites.  

## Initial Setup ##
How to install all the **Python packages**  and use ***Positron*** wont be covered here, which are well described in correspoding github pages.  You can either use ```conda``` or  ```uv``` to setup an ```Python environment``` where all the analysis relevent pacakges including scanpy, scVelo, and CellRank will be installed. Installation should be smooth without any struggles.  

## Initial Step with Scanpy ##
The first thing to do in ```Python``` enviroment is to import all the necessary packages and to setup some basic parameters(e.g. ```matplotlib``` graphic parameters) as follows;
```
import matplotlib_inline.backend_inline

matplotlib_inline.backend_inline.set_matplotlib_formats('svg', 'png')

import matplotlib as mpl

mpl.rcParams['figure.dpi'] = 400
mpl.rcParams['font.sans-serif'] = 'Arial'

import pandas as pd
import anndata as ad
import scanpy as sc
import scvelo as scv
import numpy as np
import cellrank as cr
import hdf5plugin
import glasbey
import matplotlib.pyplot as plt
```
Then, import ```h5ad``` file;
```
ad=ad.io.read_h5ad('ad.h5ad')
ad
AnnData object with n_obs × n_vars = 10944 × 78932
    obs: 'rn', 'orig.ident', 'nCount_RNA', 'nFeature_RNA', 'percentMT', 'percentRB', 'nCount_SCT1', 'nFeature_SCT1', 'SCT_Harmony.snn.res8', 'seurat_clusters', 'SCT_Harmony.snn.res7', 'SCT_Harmony.snn.res6', 'Level3M', 'Level3R', 'SingleR.Level3M', 'Level3M.noNA'
    uns: 'X_name'
    obsm: 'X_sct1_pca', 'X_sct1_umap'
    layers: 'spliced', 'unspliced'
```



