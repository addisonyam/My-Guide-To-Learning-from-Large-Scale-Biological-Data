## Presentation 1: Stochastic Systems Biology
### Learning from Large-Scale Biological Data
### Monday February 9, 2026

Biophysical modeling with variational autoencoders for bimodal, single-cell RNA sequencing data

### 1 Overview
#### Overview of scRNA-seq
- adapted to two data types usually its one
- measures gene expression on a per-cell basis, here it measures nascent/mature RNA and chromatin accessibility
- benefits - inexpensive, information-rich, great for identifying cellular abnormalities (see how genes mutate or are modified)

#### Gene Expression
- RNA polymerase performs transcription on DNA 
- k - transcribing forming Nascent RNA
- B - Splicing forms Mature RNA 
- Y - Degradation occurs

#### Gene Expression - RNA
- Chemical master equation: k N B M 
- Measures the probability of RNA being in a certain state, good biomarker

#### scVI for scRNA-seq
- scVI - tool utilizes deep learning and variational autoencoder to reduce dimensionality to ensure that the most critical information is retained. Regular autoencoders don't make new data, they try to reconstruct the data
    - A second neural network decodes the encoded information into cell-by-gene parameters
    - Results - conditional likelihood distribution of observed counts
- Pitfalls 
    - current package treats nascent and mature DNA as separate entities

#### biVI
- An extension of scVI that ccounts for biological effects, looks at N and M with splicing as a joint distribution
- input is cell by gene matrix on a per-cell basis

### 2 Data
#### Perliminary Data
- had three kinds of data: (1) simulated data of M and N RNA, (2) real data from mouse brains, (3) supplementary data to compare to
- biVI functionality tested with simulated ground truth data, 20 cell types, 7200 cells with 800 validation cells for 400 epochs, 2000 held out for prediction. This simulated data is from simulating bursting to get the known amounts
- 0kg: Mechanistic model parameters for gene g in cell type K. Assume that model parameters are the same for cells of type K. Assume that biVI and scVI infer unique parameters for every cell and gene

#### Data: Allen Institute Sample
- Mouse library B08, brain (primary motor cortex) data FASTAQ RNA sequences (quality scores tell model how usable the data is), analyzed 200 highly-variable genes in brain tissue (want genes that are more likely to have significant measurable effects)
- Preprocessing - 6418 cell analyzed, remove empty/"doublet" cells. Doublet cells are very different                                ######
- Fitting the model - assume spliced/unspliced counts follow N/M
- ~4000 cells trained, ~500 validation, ~1000 held out for 400 epouchs

#### seqFISH+ Supplementary Data - 6TB
- Analyzed mRNA transcripts, supplementary burst size validation for 17683 genes over 523 cells

### 3 Methods
#### biVI recap
- Continuation of siVI
- Three models - constitutive, extrinsic (outside vairbales), bursty
- decoder output and likelihood function

#### High level methodology steps
- choose the model
- identify one species CME
- derive two-species CME from the CME
- Modify the autoencoder to output variables that parameterize the CME solution

#### 1. Choosing a scVI Generative Model
- Poisson and Negative Binomial
- u = mean gene expression
- p - probability of abundance of gene g in cell
- l - cell size facor
- a - gene-specific dispersion parameters
1. constitutive - possion with mean u assumes a constant transcription rate
2. Extrinsic - negative biomial
3. Bursty - negative binomial, assumes gene turn on and off

#### 2. Identifying a one-species CME
#### Constitive
- Poisson stationary distribution, constant transcription rate
- need to manipulate equation to get mu (u) as an input for scVI
- scVI expects p to be a small number and l to be large (comparable to the cell's gene count)

#### Extrinsic
- NB stationaty distribution
- replaced k with k ~ K, now accounts for mixture
- need to account for new variable eta (zero with slanted diagonal line through it)
- plug in C and we get l and p

#### Bursty
- NB stationary distribution
- CME now has k which is different from constitutive to now measure the rate of transcription event arrival (how often genes are turned on). B numer of produced transcripts, b avergae burst size

#### 3. Dervie a two-species CME from the one-species CME and derive biologically-based parameter value assumptions (second part already showed)
-  Moving from one to two, subsittute X with N and M and bursty (connecting N and M)

#### 4. Modify the scVI autoencoder
- Poisson does not require moditications of the scVI autoencoders
- Extrainsic and Bursty 
    - instead of two alpha parameters, biVI modifies this to produce one shared alpha parameter per gene, enforces biophysical constrinat the N and M RNA from the same fene share the same shape parameter
- Replaces the independent NB (previous paper)

### 4 Results
#### Validation on Simulated Data
- simulated RNA counts
- Scaled inter-cluster distance
- Nearest neighbor fraction - looks at cells cluster around and if they are of the same cell type
- Traininy dynamics
- Done on all three models which performs well as biVI summarizes key parameters well

#### Burst Size Validation with SeqFISH+
- SeqFISH+ orthogonal imaging-based technology
a. SeqFISH+ introns b. SeqFISH+ mRNA - overfitting which is expected as we assume bursty
c. d. variance of SeqFISH+ burst size vs mean of biVI burst size - compared two cell types clusters' busrt size 

#### Degration Rate Validation with scEU-seq
- scEU-seq - time series metabolic labeling - directly measures degradtation rates
- Bursty (mean ~0.55) performs the best over constitive (mean ~0.5) and extrinsic mean 0.42
- Biological validation - known lower degradation of cluster, control shows no relationship as expected
- biVI infers biologically meaningful degradation rates

#### Applcation to Allen Brain Atlas Data
- Took 2000 genes and trained biVI
- Clustering metrics comparable to simulated results
- biVI overperforms scVI just slighty
- Similar same level for nearest neightbor fractions - biVI slightly lower but distributions overlap
- biVI and scVI reconstructino error nearly identical

#### Continuation with blue and purple
- biVI has better empirical distribution fits than scVI (lower KL divergence and Hellinger distance is better)
- Marker genes FOXP2 and RORB upregulated through specific mechanisms
    - both increased burst size 
    Did RORB has the same degradation as before? I see FOXP2 had decreased degration                            #########

#### Continuation with venn diagram
- Cell-type preferences - non neuronal cells prefer brust size modulationl while neurons use mixed strategies (multiiple colors in figure)
- Hidden regulation - hundreds of genes differr in biophysical paramerer siwhtout mean expression changed
- Disease relevant gene examples - higher defgradation in L5 IT cell subclass in TREM2 gene (AD-related), burstier in L6 CT cell subclass in NDNF gene (neuroprotective)

### 5 Discussion
#### Critiques and Pitfalls
- Unbalanced classes - still removed cell clases with <10 cells but still left large range --> decreasing the statistical power for rare cell types and parameter estimates less reliable (simulations do use balanced cell types which is not relastic for biological data)
- Leaky preprocessing - really interested in higuhly variable genes - chance that features in training and validation see leaky
- Comparison to scVI - majority of results and performance metrics are compared to scVI

#### Conclusion
- biVI offer solutions to a flaw in modeling single-cell data
- this tools leverages a biophysical model and apprach rather than forcing and fitting the data to an existin model
- future extensions - ATAC (chromatin) spatial transcirptomics and proteomics