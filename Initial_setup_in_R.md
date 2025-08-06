# RNA Velocity Analysis of Human HSCs scRNAseq data from Xuan_Zhang et.al. 
## Introduction

RNA velocity is used to provide aids in understanding dybamic states of cell differentiation, based on asscessing kinetics of spliced and unspliced RNA from clusters of cells that are defined by annotation of scRNAseq data.  It diplays direction as well as momentum of cellular differentiation, inferring potential development of immature to mature stages of groups of cells in question.   The original Nature article by ```Manno et.al.``` (https://www.nature.com/articles/s41586-018-0414-6) describes theoretical basis and alogorithm of RNA velocity.  

There are several R as well as python packages that can model, estimate and visualize RNA velocity from scRNAseq data, however I picked a python package, ```Scvelo``` from Theislab (https://scvelo.readthedocs.io/en/stable/) which probably is the most comprehensive package for the analysis of RNA velocity.  The only issue of using ```Scvelo``` is that I use two R pacakges, ```Seurat``` and ```SingleCellExperiment``` for the bulk of scRNAseq analysis and a lot times, interoperability between python and R packages is not optimum, requiring a few still painstaiking intermediate processes to make ```Seurat``` and ```SingleCellExperiment``` file to be compatible with ```Scvelo.```   Unfortunately, there are not any R packages that are as comprehensive  and capable as ```Scvelo```.

This section describes perhaps the easiest to overcome some of the major compatibility issues. 
## Generating Spliced, and Unspliced Counts by using STARSolo

STARSolo (scRNAseq version of STAR aligner) was used for counting spliced, unspliced, and ambiguous counts. This portion was already covered.  Please, refer to the ```RNA Velocity``` section of ***An-immunophenotype-coupled_transcriptomic_atlas_of-human_hematopoietic_progenitors***  (https://github.com/akhst7/An-immunophenotype-coupled_transcriptomic_atlas_of-human_hematopoietic_progenitors/blob/main/Analysis%20of%20the%20full%20data%20set.md#rna-velocity)

## Prepartion of a ```h5ad``` file in R

In Python, bioinformatics data including scRNAseq ones are generally handeled  by an ```Anndata``` package.  The ```Anndata``` package creates a data object (obj) that is structually distinct from ```Suerat``` and ```SingleCellExperiment``` objs, which gives rise to some issues converting those objs to ```Anndata``` objs.  

### AnnData Obj
![Screenshot 2025-04-04 at 4 01 49 PM](https://github.com/user-attachments/assets/37068cad-312d-43fd-8121-908512227593)

### SingleCellExperiment (SCE) Obj
![Screenshot 2025-04-04 at 4 03 12 PM](https://github.com/user-attachments/assets/79561cc7-9268-4b66-bcb5-4ec807b60bbb)

It is possible to create the ```Anndata``` ojb in R by using ```anndata``` or ```anndataR``` (newer) packages but the easier way is to somehow convert existing either the ```Seurat``` or ```SingleCellExperiment``` obj to the ```Anndata``` obj, and it turns out that there are only a few R packages can do this. Among them, the best is ```Zellkonverter```  from once again from Theislab.  ```Zellkonverter``` takes the ```SingleCellExperiment``` obj (but unfortunately not ```Seurat```), and convert it to the ```AnnData``` obj which then is saved as the ```h5ad``` file.  

### Scanpy, ScVelo, and CellRank ###
<img width="558" height="244" alt="Scanpy_Logo_BrightFG" src="https://github.com/user-attachments/assets/5d443e35-f028-4e2d-b35b-d86f0ce816b6" />
<img width="1920" height="515" alt="light_mode_logo" src="https://github.com/user-attachments/assets/fac6498f-2506-43fb-9ed5-8de5fe86ef02" />

All these Python packages will be used to estimate RNAvelocity with a vivid graphically represented direction and velocity of cellular differetiation within a tissue in question.  All these packages are originally invented by Theis lab except ```CellRank```(https://cellrank.readthedocs.io/en/latest/) is a collaboration between Theis and Peer labs.  ```Scanpy``` (https://scanpy.readthedocs.io/en/stable/index.html)  is a general informatic scRNAseq analysis tool just as powerful as ```Seurat``` in R, and is now maintained by ```ScVerse``` (Foundational tools for omics data in the life sciences).  ```scVelo```was a packages originally developed for RNAVelocity but ```CellRank``` currently has taken over a majority of ```scVelo``` functions. 
## Brief Description of Key Steps
An output of ```STARSolo``` contains multiple folders. The folder that contains data for RNAVelocity is called a **Gene** and **Velocyte** (see below).  
```
fs::dir_tree("/Volumes/Document/WF26_CD34Solo.out")
/Volumes/Document/WF26_CD34Solo.out
├── Barcodes.stats
├── Gene
│   ├── CellReads.stats
│   ├── Features.stats
│   ├── Summary.csv
│   ├── UMIperCellSorted.txt
│   ├── filtered
│   │   ├── barcodes.tsv
│   │   ├── features.tsv
│   │   └── matrix.mtx
│   └── raw
│       ├── UniqueAndMult-EM.mtx
│       ├── barcodes.tsv
│       ├── features.tsv
│       └── matrix.mtx
├── GeneFull
│   ├── CellReads.stats
│   ├── Features.stats
│   ├── Summary.csv
│   ├── UMIperCellSorted.txt
│   ├── filtered
│   │   ├── barcodes.tsv
│   │   ├── features.tsv
│   │   └── matrix.mtx
│   └── raw
│       ├── UniqueAndMult-EM.mtx
│       ├── barcodes.tsv
│       ├── features.tsv
│       └── matrix.mtx
└── Velocyto
    ├── Features.stats
    ├── Summary.csv
    ├── filtered
    │   ├── ambiguous.mtx
    │   ├── barcodes.tsv
    │   ├── features.tsv
    │   ├── spliced.mtx
    │   └── unspliced.mtx
    └── raw
        ├── ambiguous.mtx
        ├── barcodes.tsv
        ├── features.tsv
        ├── spliced.mtx
        └── unspliced.mtx
```
Basically, you need these files,```spliced.mtx, unspliced.mtx and features.tsv from ```filtered``` under ```Velocyte``` folder as well as everything in a ```filtered``` folder under ```Gene``` (for constructing a main "counts" matrix).  Based on these files, a SEC obj with 3 matrixes (gex, spliced, and unspliced) will be created.
First thing first is import all the count matrix to R;
1. Importing a GEX matrix
```ReadMtx(mtx = "/Volumes/Bioinformatics/WF26_CD34Solo.out/Gene/filtered/matrix.mtx", cells = "/Volumes/Bioinformatics/WF26_CD34Solo.out/Gene/filtered/barcodes.tsv", features = "/Volumes/Bioinformatics/WF26_CD34Solo.out/Gene/filtered/features.tsv")-> gex.mtx```
2. Importing a Spliced matrix
```spliced.mtx<-ReadMtx(mtx = "/Volumes/Document/WF26_CD34Solo.out/Velocyto/filtered/spliced.mtx", cells = "/Volumes/Document/WF26_CD34Solo.out/Velocyto/filtered/barcodes.tsv", features = "/Volumes/Document/WF26_CD34Solo.out/Velocyto/filtered/features.tsv")```
3. Importing a Unspliced matrix
```unspliced.mtx<-ReadMtx(mtx = "/Volumes/Document/WF26_CD34Solo.out/Velocyto/filtered/unspliced.mtx", cells = "/Volumes/Document/WF26_CD34Solo.out/Velocyto/filtered/barcodes.tsv", features = "/Volumes/Document/WF26_CD34Solo.out/Velocyto/filtered/features.tsv")```

Then, the SCE ojb will be created as follows;
```
WF26CD34Velo.sce<-SingleCellExperiment(list(counts=gene.mtx, spliced=spliced.mtx, unspliced=unspliced.mtx))
```
At this point, it is a good practive to save ```WF26CD34Velo.sce```.  Since this sce obj is moderately large,it is nice to speed up a saving process by invoking a multithreaded/core feature in a ```xz compressor``` as follows;
```
con<-pipe("xz -T0 --best > WF26CD34Velo.sce", "wb")
save(WF26CD34Velo.mini, file = con, envir = .GlobalEnv);close(con = con)
```
A parameter, ***-T0*** forces xz to use a full thread/core. 
Once this is done, meta data and reduced dim reductions (pca and umap from ```Seurat obj```) will be added to the sce. The completed sce, ```WF26CD34Velo``` has a following struture;
```
> WF26CD34Velo
class: SingleCellExperiment 
dim: 78932 10944 
metadata(0):
assays(3): counts spliced unspliced
rownames(78932): DDX11L16 DDX11L1 ... ENSG00000297844 ENSG00000309258
rowData names(0):
colnames(10944): AAACCCAAGCTACTGT AAACCCACAACGGCCT ... TTTGTTGTCTCATAGG TTTGTTGTCTCGTCAC
colData names(16): rn orig.ident ... SingleR.Level3M Level3M.noNA
reducedDimNames(2): X_sct1_pca X_sct1_umap
mainExpName: NULL
altExpNames(0):
```
This sce obj, ```WF26CD34Velo``` will be converted and saved as an ```anndata``` format by using a R package called, ```zellkonverter``` which by far is the best a ***sce to ad*** file converter.  
```
SCE2AnnData(WF26CD34Vel.mini)->ad

ad
AnnData object with n_obs × n_vars = 10944 × 78932
    obs: 'rn', 'orig.ident', 'nCount_RNA', 'nFeature_RNA', 'percentMT', 'percentRB', 'nCount_SCT1', 'nFeature_SCT1', 'SCT_Harmony.snn.res8', 'seurat_clusters', 'SCT_Harmony.snn.res7', 'SCT_Harmony.snn.res6', 'Level3M', 'Level3R', 'SingleR.Level3M', 'Level3M.noNA'
    uns: 'X_name'
    obsm: 'X_sct1_pca', 'X_sct1_umap'
    layers: 'spliced', 'unspliced'
```
```Zellkonverter``` placed  ```sce``` slots to the corresponding slots in ```anndata``` perfectly.  Also, ```zellkonverter``` transforms ```dgCMatrix``` to ```dgRMatrix``` which is nice since you really dont have to do this manually.  
The last step is to save ```ad``` to a ```h5_ad``` file on the disk so that it could be loaded into a ```Python environment``` for the RNAvelocity analysis.  For this, a package called ```anndata``` or an newer ```anndataR``` will be used as follows;
```
library(anndata)
ad$write_h5ad(filename="../../Python/ad5.h5ad")
```
That is all!! 


