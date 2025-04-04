# RNA Velocity Analysis of Human HSCs scRNAseq data from Xuan_Zhang et.al
## Introduction

RNA velocity is used to provide aids in understanding dybamic states of cell differentiation, based on asscessing kinetics of spliced and unspliced RNA from clusters of cells that are defined by annotation of scRNAseq data.  It diplays direction as well as momentum of cellular differentiation, inferring potential development of immature to mature stages of groups of cells in question.   The original Nature article by ```Manno et.al.``` (https://www.nature.com/articles/s41586-018-0414-6) describes theoretical basis and alogorithm of RNA velocity.  

There are several R as well as python packages that can model, estimate and visualize RNA velocity from scRNAseq data, however I picked a python package, ```Scvelo``` from Theislab (https://scvelo.readthedocs.io/en/stable/) which probably is the most comprehensive package for the analysis of RNA velocity.  The only issue of using ```Scvelo``` is that I use two R pacakges, ```Seurat``` and ```SingleCellExperiment``` for the bulk of scRNAseq analysis and a lot times, interoperability between python and R packages is not optimum, requiring a few still painstaiking intermediate processes to make ```Seurat``` and ```SingleCellExperiment``` file to be compatible with ```Scvelo.```   Unfortunately, there are not any R packages that are as comprehensive  and capable as ```Scvelo.```  

This section describes perhaps the easiest to overcome some of the major compatibility issues. 

## Prepartion of a ```h5ad``` file in R

In python, bioinformatics data including scRNAseq ones are generally handeled  by an ```Anndata``` package.  The ```Anndata``` package creates a data object (obj) that is structually dissimilar to ```Suerat``` or ```SingleCellExperiment``` objs.  
![Screenshot 2025-04-04 at 3 59 02 PM](https://github.com/user-attachments/assets/36e59641-76b2-4b59-ae3f-cfe8fb89af77)
