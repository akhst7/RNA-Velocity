# RNA Velocity Analysis of Human HSCs scRNAseq data from Xuan_Zhang et.al
## Introduction

RNA velocity is used to provide aids in understanding dybamic states of cell differentiation, based on asscessing kinetics of spliced and unspliced RNA from clusters of cells that are defined by annotation of scRNAseq data.  It diplays direction as well as momentum of cellular differentiation, inferring potential development of immature to mature stages of groups of cells in question.   The original Nature article by ```Manno et.al.``` (https://www.nature.com/articles/s41586-018-0414-6) describes theoretical basis and alogorithm of RNA velocity.  

There are several R as well as python packages that can model, estimate and visualize RNA velocity from scRNAseq data, however I picked a python package, ```Scvelo``` from Theislab (https://scvelo.readthedocs.io/en/stable/) which probably is the most comprehensive package for the analysis of RNA velocity.  The only issue of using ```Scvelo``` is that I use two R pacakges, ```Seurat``` and ```SingleCellExperiment``` for the bulk of scRNAseq analysis and a lot times, interoperability between python and R packages is not optimum, requiring a few still painstaiking intermediate processes to make ```Seurat``` and ```SingleCellExperiment``` file to be compatible with ```Scvelo.```   Unfortunately, there are not any R packages that are as comprehensive  and capable as ```Scvelo.```  

This section describes perhaps the easiest to overcome some of the major compatibility issues. 
## Generating Spliced, and Unspliced Counts by using STARSolo

STARSolo (scRNAseq version of STAR aligner) was used for counting spliced, unspliced, and ambiguous counts. This portion was already covered.  Please, refer to the ```RNA Velocity``` section of ***An-immunophenotype-coupled_transcriptomic_atlas_of-human_hematopoietic_progenitors***  (https://github.com/akhst7/An-immunophenotype-coupled_transcriptomic_atlas_of-human_hematopoietic_progenitors/blob/main/Analysis%20of%20the%20full%20data%20set.md#rna-velocity)

## Prepartion of a ```h5ad``` file in R

In python, bioinformatics data including scRNAseq ones are generally handeled  by an ```Anndata``` package.  The ```Anndata``` package creates a data object (obj) that is structually ```Anndata``` package to ```Suerat``` or ```SingleCellExperiment``` objs, which gives rise to some issues converting those objs to ```Anndata``` objs.  

### AnnData Obj
![Screenshot 2025-04-04 at 4 01 49 PM](https://github.com/user-attachments/assets/37068cad-312d-43fd-8121-908512227593)

### SingleCellExperiment Obj
![Screenshot 2025-04-04 at 4 03 12 PM](https://github.com/user-attachments/assets/79561cc7-9268-4b66-bcb5-4ec807b60bbb)

It is possible to create the ```Anndata``` ojb in R by using ```anndata``` or ```anndataR``` (newer) packages but the easier way is to somehow convert existing either the ```Seurat``` or ```SingleCellExperiment``` obj to the ```Anndata``` obj, and it turns out that there are a few R packages can do this. Among them, the best is ```Zellkonverter```  and is again from Theislab.  ```Zellkonverter``` takes the ```SingleCellExperiment``` obj (but unfortunately not ```Seurat```), and convert it to the ```AnnData``` obj which then is saved as the ```h5ad``` file.  

### 
