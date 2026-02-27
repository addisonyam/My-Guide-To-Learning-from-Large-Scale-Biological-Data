## Presentation 3: Spatial Transcriptomics - GASTON
### Learning from Large-Scale Biological Data
### Wednesday February 25, 2026

### Spaitially Resolved Transcripmtomics
- Finds spots (RNA is transferred through visium)
- Probe extension - barcodes added, cDNA is degraded, then sequenced
- Limitation - the data is very sparse

### Biological Features Measured
- RNA transcripts counts
- Different tissues provide different geometric structures
### Belayer
- Unsupervised - straight line boundary restricted - can't assign regions to the same domain
- Supervised - manual
### SpaceFlow
- Each value in the domain is a layer which contain constant values - may miss a lot of gene expression
    - ex: misses granule cells
### ENVI
- Same problem of intradomain values
    - ex: When they matched two genes with no spatial transcription
### Current Limitations
- Assuming constant expression within domains 
- Can't distinghish between boundaries and inside of domains
### The Idea: Gene Expression Topography
- Isodepth - describes gene expression gradient and reveal spatial regions
### GASTON
- Unsupervised deep learning 
- Models as a piecewise function
- Downstream applications - can model continous and discontinous gene expression patterns
### Overview of Datasets
- Unique molecular identifier (UMIs)- a stamp associated with each molecule
- Less genes for the dataset

### Methods
### Existing Methods prior to GASTON
- Felt incomplete
- Did one of two things 
    1. Finding spatial domains - missed gradual changes within region
    2. Finding spatially varying genes - 
### Which brought into thought of GASTON
- The method models both sparp boundaries between regions (discontinous) and smooth gradients within regions (continous)
### Isodepth Math
- It is conceptually a coordinate system and take the input of location and through a scalar function, it outputs the isodepth usually in micrometers
### Parameters - Spatial Gradients
- Vectors pointing in the direction of steepest increase
    - Direction - which way is fastest change in gene expression
    - Magnitide - 
### Gene Expression Model
- Full Equation - linear function of the isodepth of the expressed genes at each location
- Sum up all the domains
- Domain indicator - tell us if the gene is expressed in that region (TRUE/1 or FALSE/0)
- If TRUE, use a linear function per domain - fancy way of writing y=mx+b. If FALSE, zero multipled.
### Example - Gene Sbk1
- Expression model f(x,y) = y-intercept + slope
### Why Piecewise Linear Functions
- There are boundaries and changes between domains and you can differentiate the boundaries and get gradients within domains
### Learn Isodepth from Data
- Lot of preprocessing done, the data is very sparse where many zeros tell us it isn't being expressed
- Needed to remove the noise for more accuracy
- Forced the model to learn a measurement by putting it into a hidden layer
### Phase 1
- The input is N spots/cells with coordinates, matrix - rows are single cells and columns are counts of UMI counts in the cell, goes through math, layer 1 predicts isodepth (able to measure expression of gene)
- Preprocessing - remove genes <= 10 UMIs
- Compute top K=20 GLM-PCA
    - Deals with sparse matrix
    - Top 20 PCs are the "top genes"
- Loss Function - Poisson Negative Log-Likelihood
    - Measures the amount of data loss between data and prediction
    - Trains model to get optimized amount of loss (least as possible)
### Phase 1 Training Loops - 500 epochs
### Phase 1 Output Trained Parameters --> Isodepth
### Phase 2 Learn Piecewise Linear Parameters
- Gets an isodepth map

### Results
1. Looking a the spatial organization of the mouse cerebellum
    a. How the isodepth map looks like, used to convert to distance metric
    b. Map of regions and spatial domains and cell types
    c-f. Compares with other methods - NSF anf GraphST are pretty good - used known cellular models with spatial weights of genes
    g. Spatial coherence score - calculates 'Z' score of its neighbors, GASTON is the best
    h. RCTD - identifies layers, blue dots are spots that weren't measured
    j. Now that we have cell-type layers, map cell-type assignments back to the isodepth, does into the direction of the gradient
2. GASTON uncovers continous and discontinous variation
- For each gene g, GASTON learns a piecewise function hg(d) of the isodepth d
    a-c. Looked at one gene
    d. Looked at methods in marker gene identification, GASTON has the highest Area Under the Precision-Recall Curve (AUPRC)
    e. Frmpd4 - known marker gene
    - Summary of quality or attribute of genes betweend domains
    - Able to classify genes, but 35% of variation isn't assigned to cell-type. Slopes of zero that may be explained by biological processes such as neurons firing.
3. GASTON reveals spatial gradients in the TME (Tumor Microbe Environment) - Stage 4 Cancer
    a. Staining
    b. Red is the tumor, blue is tumor-adjacent
    d. Gradients and type gene groups classified
    g, e. DE performed, compare between these cell groups
    h. Specific gene group gradients
- GASTON is able to see the expression of these gene groups
- GASTON showed that the boundary of tumor samples is undergoing EMT to stem-like state

### Limitations
- First assumption is assuming all genes share the same vector field
- Second assumption - assume the vector field is conservative (does not rotate in space)
- Pitfall - dependent on how splits are made and used to learn in the model
- GASTON identifies dominant cell types, the choice of piece-wise linear function doesn't capture all cellular expression
- Challenge - automated "Kneedle" method used to select the number of domains, not always reliable, had to manually override output of 8 to 7