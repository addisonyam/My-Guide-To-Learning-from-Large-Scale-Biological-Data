## Presentation 4: Predicting 3D genome folding from DNA sequence with Akita
### Learning from Large-Scale Biological Data
### Wednesday March 4, 2026

### Introduction
DNA is organized into chromatin architecture where it is wound around histones forming chromatin which contract and relax to all for transcription. There's a lot of structure on multiple levels. Chromatin is organized into topogically organized domains (TADs) and regulatory loops. Genes interact with enhancers to initiate.
Cohesin and CTCF play crucial roles in 3D chromatin organization. Two CTCF com together. Cohesin form loops that bring the enhancer close to or far from the promoter. 
Chromatin Confirmation Capture (3C) technologies reveal which regions of chromatin interact. 3D is considered a 1 vs 1 method to measure one measurement to another. HiC measures all vs all. 
Hi-C "all vs all" Protocol - add biotinylated tags, show frequencies at which different genomic regions interact with each other. Hi-C-bashed methods reveal chromatin organization on multiple scales. Sometimes you need to perform Micro-C that use MNase. A and B compartments are considered active or inactive regions and TADs
### Data
Two datasets - the Hi-C captures in-contact structures.
Micro-C dataset - use MNase to break down DNA and just get nucleosomes location
Distiller pipeline to map raw sequencing reads to the human genome. Cooler organizes data into bins. ICE Normalization - iterative correction to remove technical biases (ex. some areas of the genome are easier to sequence than another). 
Distance Bias Correction - computed the expected value and took the natural log of the ratio of observed and expected, range of [-2, 2]. 
Data size and scale of 10 MB virtual contigs, 1 MB input sequence, 7008 sequences make up training and 419 validation and 413 test (80-10-10 split).
### Methods
Five main steps performed.
1. Training data collected and preprocessed - five Hi-C and Micro-C datasets, distiller-nf, cooler. Preprocessing pipeline to convert into clean learning targets, includes adaptive coarse graining, distance normalization, log transform, clip to -2 to 2 range to prevent extreme values causing bias, interpolation of filling in missing bins, and gaussian smoothing. The genome was divided into virtual contigs by splitting chromosomes for 80/10/10 split, the training stride is more strict than validation and test. 


Why are the strides different for the validation and training and test sets? Is there a reason why the strides are larger for test? Larger strides give more context for test. 

2. Model archetieture: CNN design - CNN with two components with Trunk (1D) and Head (1D -> 2D). Implemented in TansorFlow and Keras. Total trainable parameters is ~749,000. For the trunk of 1D CNN, the bins i and j are averaged. 
3. Training approach - Loss function with mean squared error. Optimizer - stochastic gradient descent (SGD) with momentum. Training stopped when validation does not improve. Data augmentation - two stochastic augmentations of random shift and reverse complement + map flip, takes into account shifting and complementary sequences.
4. a. In silico motif mutagenesis - measure hwo disruptive a giving transcription factor binding motif effects predicted genome folding. CTCF and CTCFL were the two most disruptive motifs which is a good sign. After mutagenesis of random mutations, fewer patterns were found, but still showed CTCF motif was used as a primary feature. b. In silico CTCF motif inversions - flipping the motif should give inverted Hi-C plots. This is expected for a loop-organized model and Akita takes into account organization of sequences. c. In silico nucleotide-level mutagenesis - saturate mutagenesis around CTCF motfis and unbiased genome-wide mutagenesis. Disruption was measured in all five cell line outputs. d. In silico GTEx mutagenesis - test whether SNPs that are likely causal for gene expression changes (eQTLs) also disrupt predicted 3D genome folding. 
5. Cross species and experimental validation - applied human model to mouse predictions but misinterpretted B2 SINE elements. This improved with a mouse-trained model applied on mouse data

### Results
a. Akita Prediction of 3D Genome Folding - takes 1 sequence, 1-hot encoded, convoluted 1D block, dilate residual conv1D, 2D
b. Log of observed over expected - positive in blue are interactions and negative in red are hindered interactions. 
c. Mean squared error vs Spearman R - green and purple are what were shown in b. 
CTCF motif disruption on genome folding - before and after mutagenesis, calculate the map signal strength. First line is before, second line is after. Also looked at CTCF and CTCFL transcription binding factors. 
Inverting CTCF motifs - demonstrates that orientation is taken into account. 
Base-pair resolution patterns - measured how much the predicted contact map changes which is highest in the center of the CTCF. 

What are additional DNA-binding proteins or genomic features than just CTCF that impact 3D genomic folding? Did they test on more than just one genomic feature CTCF? 

Predicted folding disruption with eQTL - showed that CTCF was such a strong feature that made finding other features harder. 
In-silico deletions - shift boundaries instead of deleting it. 
Species-specific relationship - performance was bad on mouse data and improved slightly improved when trained on mouse data

### Discussion and Pitfalls
Akita is one of the first deep learning models to predict locus-specific 3D genome folding from 1D DNA sequence alone. It learns the rules demonstrated through the mutagenesis, enables nucleotide-resolution in silico mutagenesis, substantial portion of 3D folding factors are encoded in sequence. Limitiation where they could've treat chromosmes as entire training/validation/test set. Model only 1MB window. Idea for future extension - chromosome-level data split, integrate epigenomic context, extend to complex structural variants. 