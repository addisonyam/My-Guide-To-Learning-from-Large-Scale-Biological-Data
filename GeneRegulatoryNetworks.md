## Session 4: Gene Regulatory Networks
### Learning from Large-Scale Biological Data
### Wednesday February 11, 2026


#### Gene Regulatory Networks
- TF and genes are nodes, arrows are activation and inhibition
- TFs bind to promoters/enhancers and regulatory regions to lead to downstream gene expression
- Mechanism needed to just transcribe a set of genes
- What we believe is happening in cells, there is a fundamentally gene regulatory network. Cycle can be used to see co-expressed genes, once we get directionality --> Assignment of TFs, check if the TF actually binds --> TF binding motif analysis, using multimodal data such as chromatin accessibility --> more accurate GRN

#### Module Networks
- lets go back to 2003. people already knew that genes are encoded near DNA, are transcribed, creating a transcript. knew central dogma but couldn't measure. if we know the baseline level of transcript, in some context such as yeast, there are more transcripts of one than another. assumption there's an activator as well as a repressor repressing the activator
- this being 2003 didnt have RNA seq and single cell data, used micro arrays which measure contrast green and red - changes in the transcript. subject plates to different treatments such as heat shock and measure the gene expression ~ micro arrays. Same y axis today, different x axis. 
- thought they could combine this to form trees. Activators split the genes and repressors split it even more. Decision programs - this is what they called modular networks

#### Module Networks
- before they can even build regulatory modules, needed to partion the conditions. Intitially cluster genes into 50 modules. all genes going at once to get simliar treatment. 
- still needd a notion of what genes are regulators. didnt have many of tha tphysical binding data. got plausible gene regulators, didn't know where it was bound and how
- iterative maximation procedure. at the time was sophisticated. they buldt regression trees for each module using regulators to partion the modules
- regression tree - partion on continous variables, find candidate regulatot at the head and the conditions split the tree, then iterate
- they adapted this from computer science. assumption if there's a reuglator expressing up, then the gene is being turned. This is based on co expression of genes nearby. This trees assumes there's a causal relationship. 
- built 50 regresion trees for their 50 modules. this was basd on co-expression but this isn't the best way
- step two look at every gene and see if it would geneate that tree --> generation probability - see the probability of seeing that gene based on the condition. assumed columnsa re normal distribition, where does the z score fall on the distribution. each gene has a different spot on the disribution. assumed each are independent. ge tthe overall probability of the gene expression, then reassign the gene to the probable module, repeated over and over, rebuild the regression tree. This loop creates even better regression trees.
- the very first approach ot leanrig gene regulation netowkrs
- dont want to critique based on it was 2003 and not a lot of computation. what are the potential issues?
- this original clustering into 50 modules - theyre creating dependencies with the geens within the modles. they did this to have intitial data and assumed this would balance out when reassigned. 
- there;s a second part we havent talked about yet. do we know how to fit the score of a regression tree? normalize it. the problem all the outcomes have to normalized, they nromalize acorss columns (conditions) which each are its own experiement but is it creating fair conditions/metrics. 
- you have to assume there's a normal distribution. there's no reaons to make this assumption, pretty standard assumption at the time. the issue is that not all follow the ND. 
- they also assumed indpeendce wherre they coudl multiple values and add them together to get a meaingful measurement. but you method may be biased on what was done to each
- they are many ways to score and converge a regression tree. they really built shallow trees, they're really easy to overfitting. here you can split the data in some many ways that perfectly explain the conditions. 
#### Module Networks - Figure
- got pretty good regulators to explain a set of conditons, implying a causal chain of events leading to regulator. at the time didnt have goog knowledge of TF binding. but just knowiong what the motif is, there's a way to see enriched motifs. they saw enriched motifs and correlated regulators to motifs. using this method they could deduce frequent sequences. 
#### Moving into Single-cell Data
that was 2003. Now we know we can do so much more
- our modern data can measure gene expression from tens of thousands of cells to understand how regulation happens
- this trantiiiosn - single cell data is large and sparse. Need to think of methods to combat this robust nature. 
- so people thought about this for a while. the first approach that came out
#### Regulons
IN 2017, to simplify the netowork, build regulons that believe are stable to sparse data, tool called SCENIC, two setps, co expression by finding co expressed genes, assign them to TF. Proceduce to see if TF are enrcihed to build regulons, whih are then combined to make GRN. Tool on left GENEIE3 and tool on. right RcisTarget

#### Genie3 Co-expression
want to build these coexpresion modules, main thing look at main TF. they used the hottest method at the time - random forest regression (now deep learning has replaced this). thye have to structure this data so RF can learn these TFs and genes and see which match. 
- basice set - set up by gene, pick one gene that is the ouptu, theyre try to predict the measurement of gene and the outpbth is every TF, this is one model. perform tree ensemble learning. the standard set up for RF, need to do it on a bootstrapped (random)smaple, bulid tress on the subsampled data. 
- they also at each trees, picked K attributes. 
- then rank how important the TF were for that gene. for each node per tree, can look at the reduction and variance across all nodes and trees and sum that up --> measure of the importance of TF, sees overall is that TF always important for that gene. 
- do that over all genes. for every gene, gets the improtantce of TF. 
- then they rank the gene ranks
- quote: some authors recommend to avoid within-sample normalization. points out this pitfall. most people normalize the data first and leads to inflated and false positives and leakage, should use raw data
- now they have the co rxpression modules

#### Motif Discovery
- wanted to know thecausal fo the direction, is the TF aculat regulating. 
- we can score bindng motifs
- RsciTarget kook for enrichment for bidnign around the gene
1. first setp is find all motifs around TSS, literallly take the TSS (contast for next class's paper) around the 5KB region. classic assumption, 2KB regions are promoters. seems like with this size, they get a good amount. how can we take a motif and score it? even if its not enough match, get the probability of seeing that base within the repeated sequence. with the probabilites, multiply them out for one sequence --> get the probability for that sequence. 
2. need to find a way to rank these TFs, are they actually occuring int hese motifs. compare this motif vs background (genome-wide genes). get an idea how of how the TF across the genome.
- go through an ordering, get a ROC curve. in some cases, there's random background (follow red diagonal), if the area is high, the TF has preferential binding to the genes. 
3. this doesn't tell you whcih genes contrubute to binding. find the biggest gap between blue and red in ROC curve, everything to the left (leading edge) theyre the one's contributing the most to TF binding, check those specific genes in this pink region. The ROC curve is climbing but not as much as the random background. Can reduce space with the regulons. 
- authors point out, this can only find positive regulation as it discard negatives. 
- for the 5KB, this is a big assumption, we can do a lot bettr by considering distal
- the point of this is to create regulons (yellow/green), they're really stable for this large sparse data when there's genes and data missing --> robust for recovery
- in practice, you see disconnected networks

#### Downstream Analysis
- what they did really nicely, ran this in mouse and human, could look at the same regulon for the same gene, see the same genes being regulated but a lot of differences. TF/regulon can see species-gene changes
- some art? back in old lecture, we looked at the diffeences in densities of tSNE but if you rerun it you get different differences. here they plotted regulons and see densitiy. can just say that the red and purple are highloighted in the same region, can say they bind for that cell. cant deduce similiar regulons occur in similiar cell types. misleading tSNE.

#### Gene Regularoy Networks
- Let's circle back. through this we want to create a try underlying system. 
1. using chromatin accessibility it can tell us scenarios when TF binding 
2. this actually isn;t a linear relationship, non-linearity of TF-gene relationships
3. hard to model the correlation structure between TFs
3. another thing that modern biology can measure is distal contacts. earlier lectures in hi-c data, there was contact between two very far parts. there's a lot leading to TF binding to genes
5. Pertubation for causal inference, can use inferenc to see if there's a true dependence and get more confidence on our edges

next presentation
https://www.nature.com/articles/s41592-023-01938-4

watch 20 minutes  ben raphael - models and methods for spatial transcriptomics
https://www.youtube.com/watch?v=CRuSrd8JWI0