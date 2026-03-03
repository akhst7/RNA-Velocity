## A pseudotime analysis of the CITEseq and scRNAseq of human BM sample, WF26_CD34+ from An-immunophenotype-coupled_transcriptomic_atlas_of-human_hematopoietic_progenitors with Palantir
### INTRODUCTION
I am including a section describing `pseudotime analysis` by a Python package, `Palantir` created by `dpeerlab` (https://palantir.readthedocs.io/en/latest/notebooks/Palantir_sample_notebook.html).  **Pseudogtime** is the trajectory model of cell differentiation projected on the low dimentional manifold and is computatinally derived based on patterns of single cell gene expression that follow propagation of progenitor lineages.  

At any rate, I've been trying to construct a pseudotime trajectory from HSC to downstream lineages from Human BM scRNAseq data. This scRNAseq was done with the CITESeq, and annotation was done with CITESeq and transcriptome data (and initially analyzed by `Seurat` and `SingleCellExperiment`, ), 

There are several Python as well as R pakcages that estimate pseudotime with their own unique mathematical models.  `Scanpy` has a  `Diffusion Pseudotime (DPT)` function but `Palantir` is prefered because of its strength (particuarly by integration of `Markov chain`) in analyzing the pattern of cell differentiation with more complex and highly branched lineages.  A following described how the `Palantir` pakcage can be deployed and used for derivation of `pseudotime` of human hematopoietic stem cell differentiation.  

