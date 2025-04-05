# RNA Velocity Analysis of Human HSCs scRNAseq data from Xuan_Zhang et.al
## Introduction

RNA velocity is used to provide aids in understanding dybamic states of cell differentiation, based on asscessing kinetics of spliced and unspliced RNA from clusters of cells that are defined by annotation of scRNAseq data.  It diplays direction as well as momentum of cellular differentiation, inferring potential development of immature to mature stages of groups of cells in question.   The original Nature article by ```Manno et.al.``` (https://www.nature.com/articles/s41586-018-0414-6) describes theoretical basis and alogorithm of RNA velocity.  

There are several R as well as python packages that can model, estimate and visualize RNA velocity from scRNAseq data, however I picked a python package, ```Scvelo``` from Theislab (https://scvelo.readthedocs.io/en/stable/) which probably is the most comprehensive package for the analysis of RNA velocity.  The only issue of using ```Scvelo``` is that I use two R pacakges, ```Seurat``` and ```SingleCellExperiment``` for the bulk of scRNAseq analysis and a lot times, interoperability between python and R packages is not optimum, requiring a few still painstaiking intermediate processes to make ```Seurat``` and ```SingleCellExperiment``` file to be compatible with ```Scvelo.```   Unfortunately, there are not any R packages that are as comprehensive  and capable as ```Scvelo.```  

This section describes perhaps the easiest to overcome some of the major compatibility issues. 
## Generating Spliced, and Unspliced Counts by using STARSolo

STARSolo (scRNAseq version of STAR aligner) was used for counting spliced, unspliced, and ambiguous counts.
```
$ STAR --version (https://github.com/alexdobin/STAR/blob/master/docs/STARsolo.md)
2.7.11b
```
I run a following script to get counts;
```
#!/opt/homebrew/bin/zsh

index=/Volumes/MySlateDrive/star_genome_index/index_human_Gencode_GRCh38_p14_47
whitelist=/Volumes/Bioinformatics/star_genome_index/3M-february-2018.txt
r1=/Volumes/MySlateDrive/wF26_CD34/SRR26369766_1.fastq.gz
r2=/Volumes/MySlateDrive/wF26_CD34/SRR26369766_2.fastq.gz
r3=/Volumes/MySlateDrive/wF26_CD34/SRR26369772_1.fastq.gz
r4=/Volumes/MySlateDrive/wF26_CD34/SRR26369772_2.fastq.gz
r5=/Volumes/MySlateDrive/wF26_CD34/SRR26369773_1.fastq.gz
r6=/Volumes/MySlateDrive/wF26_CD34/SRR26369773_2.fastq.gz
r7=/Volumes/MySlateDrive/wF26_CD34/SRR26369797_1.fastq.gz
r8=/Volumes/MySlateDrive/wF26_CD34/SRR26369797_2.fastq.gz

STAR --genomeDir $index \
--runThreadN 50 \
--readFilesCommand 'pigz -c -d' \
--readFilesIn $r2,$r4,$r6,$r8 $r1,$r3,$r5,$r7 \
--soloCBwhitelist $whitelist \
--soloType CB_UMI_Simple \
--soloCBstart 1 \
--soloCBlen 16 \
--soloUMIstart 17 \
--soloUMIlen 12 \
--soloStrand Forward \
--twopassMode Basic \
--clipAdapterType CellRanger4 \
--soloMultiMappers EM \
--soloFeatures Gene GeneFull Velocyto \
--outFilterMultimapNmax 1 \
--outFilterScoreMin 30 \
--soloCBmatchWLtype 1MM_multi_Nbase_pseudocounts \
--soloUMIfiltering MultiGeneUMI_CR \
--soloUMIdedup 1MM_CR \
--soloCellFilter  EmptyDrops_CR \
--soloCellReadStats Standard \
--outReadsUnmapped Fastx \
--outFileNamePrefix /Volumes/Bioinformatics/WF26_CD34 \
--outSAMattributes NH HI CR CB CY UR UY sM \
--outSAMattrRGline ID:WF26.CD34 \
--outSAMtype BAM SortedByCoordinate
```




## Prepartion of a ```h5ad``` file in R

In python, bioinformatics data including scRNAseq ones are generally handeled  by an ```Anndata``` package.  The ```Anndata``` package creates a data object (obj) that is structually ```Anndata``` package to ```Suerat``` or ```SingleCellExperiment``` objs, which gives rise to some issues converting those objs to ```Anndata``` objs.  

### AnnData Obj
![Screenshot 2025-04-04 at 4 01 49 PM](https://github.com/user-attachments/assets/37068cad-312d-43fd-8121-908512227593)

### SingleCellExperiment Obj
![Screenshot 2025-04-04 at 4 03 12 PM](https://github.com/user-attachments/assets/79561cc7-9268-4b66-bcb5-4ec807b60bbb)

It is possible to create the ```Anndata``` ojb in R by using ```anndata``` or ```anndataR``` (newer) packages but the easier way is to somehow convert existing either the ```Seurat``` or ```SingleCellExperiment``` obj to the ```Anndata``` obj, and it turns out that there are a few R packages can do this. Among them, the best is ```Zellkonverter```  and is again from Theislab.  ```Zellkonverter``` takes the ```SingleCellExperiment``` obj (but unfortunately not ```Seurat```), and convert it to the ```AnnData``` obj which then is saved as the ```h5ad``` file.  

### 
