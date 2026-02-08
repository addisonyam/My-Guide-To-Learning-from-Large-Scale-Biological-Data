## Week 4 Paper: Gene Regulatory Network Inference in the era of Single-cell Multi-omics 
### Learning from Large-Scale Biological Data
### Week 4 - February 9-11, 2026
[Badia et al, 2023](https://www.nature.com/articles/s41576-023-00618-5) Quizzed on the following sections: Introduction, Inference of GRNs, and Downstream GRN Analysis

### Introduction
- Chromatin, usually inaccessible, is made up of nucleosomes of tighlty packed genomic DNA. Pioneer TFs unravel this DNA inaccessibility and other TFs bind to distal cis-regulatory elements of DNA, such as cofactors, enhancers, and promoters. All these elements recruit RNA polymerase to initiate transcription.
- Gene regulatory networks (GRNs) are computational models representing gene expression regulation as graphs where nodes are genes and TFs and the edges are the regulatory interactions. 
- Single-cell technologies overcame the limitation of bulk profiling across cell types in transcriptomics data being only specific to one cell-type, now alllowing for multiple cell types and states to be inferred.
- This paper reviewed general techniques of GRNs and their limitiations, tips for more accurate GRNs, novel tools, downstream analyses, current challenges, and future directions.

### Inference of GRNs

#### From transcriptomics data
Using data about the mRNA transcripts, the intermediate between DNA and proteins.
- Weighted gene co-expression network analysis (WGCNA) - method performing pairwise correlations to identify modules of co-expressed genes. Outputs a gene co-expression network with undirected interactions. The lack of links leads to many false positive associations. 
- GENEIE and GRNBoost2 overcome this by distinguishing TFs from target genes (with known data) and train models predicting gene expression from only those TFs, resulting in directed interactions

Transcriptomics data on its own is not enough as it usually results in many false positives and ignores other mechanisms such as chromatin accessibility ~ moderate success.

#### From TF binding data or chromatin accessibility
Using ChIP-seq and CUT&Tag data which map protein and TF and histones bind to DNA. 
- GRN employs this data by mapping TFBS to genes. They assign bound TFs to genes through proximity, while ignoring distal interactions, 
- Or, chromatin accessibility data (ATAC-seq, DNase-seq, and NOME-seq) is used to predict regulatory elements that TFs target. The first step is assigning TF to peaks of open chromatin regions (through TF binding motif databases and motif matcher algorithms). Secondly, assign these peaks or CREs to genes (within a specific genomic distance).


#### From single-cell transcriptomics data
scRNA-seq and scATAC-seq
- Single-cell RNA-sequencing (scRNA-seq) has been used to map cell type-specific TF-gene interactions (by utilizing the tools like SCENIC) and identify dynamic cell cell states and transitions (by utilizing psuedotime to infer directionality).
- Single-cell chromatin accessibility profiling (like scATAC-seq) can be utilized in conjunction with single-cell transcriptomics to study cell type-specific differentiation, development, and infection mechanisms.
- Multimodal data can be either paired if both come from the same cell or unpaired (which integration is performed) depending on the tool requirements.
- Mutlimodal data goes a step further than single-modatlity methods by predicting gene expression from TF gene expression where from accessible CREs (and binding motif information), TFs are assigned and then  CREs are assigned genes based on a specific distance. 
- To predict TF binding, a variety of TF binding motif databases (Box 1) and prediction algorithms are available (Table 1). Strategies differences ermege due to different databases, motif matching algorithms, and genomic distance cutoffs where these tools may have default choice.
Mathematical strategies differed whether using ltinear vs nonlinear relationship, frequenist vs Bayesian probability, regression, and trajectories.
### Downstream GRN Analysis

#### Topological analysis
Ways to characterize topology
- Network centrality measures - identify TFs and genes that are more important for connectivity and flow of the network. Measures include degree centrality, closeness centrality, betweeness centrality, and eigenvector centrality in order to identify TFs driving cell fate changes.
- Spectral graph theory - explore properties of a network when represented as a matrix. Negative matrix factorization has identified a group of TFs dtving lineage transitions in a specific cell type. Clustering has identified regulators for another cell type differentiation. 
#### Comparative analysis
Comparative analysis can discover rewiring events driving differences between cell types, cell states, disease states, treatment approaches and organisms.
- The easiest method, pairwise subtraction of TF-gene interctions has identified key regulators, groups of TFs for transdifferentiation, and trans-regulators of diseases.
- Topic modelling strategies such as latent Dirichlet allocation filter noise and capture the differences in regulatory relationships.
#### Ingerence of TF activities
Coupling GRNs with enrichment methods can infer TF activities from transcriptomic data by integrating observed gene expression to GRN topology, extracting relevant-role TFs in specific contexts. 
#### Perturbation and prediction of cell fate
GRNs can stimulate gene expression values over time by propagating TF expression to genes through an iterative manner. In silico perturbations are carried out by changing a candidate TF's expression and observing the effect on the transcriptome after a number of iterations. Then, simulated values are compared with gene expression of local neighboring cells to estimate cell identity transitions probabilities. 