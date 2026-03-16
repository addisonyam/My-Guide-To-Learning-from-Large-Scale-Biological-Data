## Week 5: Give me some space: Spatial Data
### Learning from Large-Scale Biological Data
### Week 5 - Wednesday February 18, 2026
Liu et al Zhang. 2023

#### Introduction - Spatial Data
- spatial assay is taking a slice of this bowl and seeing how they relate to each other. 
- bulk - takes the average of cell types, lots of data, cheap
- single cell and spatial are pretty sparse, cell type specific, expensive
- Spatial assays - gives the coordinates of the fruit, able to see the boundaries between 

#### Spatial Data - what it means to collect spatial data
when you do spatial analysis, you slice it up and able to do assays on them and study the features of the layers. 
one method is general histology and staining which is what doctoes have done for a while by staining a part to focus on a tissue. classic stain is H&E, able to see tissue structure, where the cells are, if a tumor is staining differently, ways to see if the tissue is healthy. DAPi shows the cellular structure of the organelles. these are physcial features and how spatial tissue is organized but deosn't tell us the genomics. 

the main thing the veido tlaked a bout is transcriptonics - split into two catgeories. sequency based split into spots. able to take the genetic material fo the cell and do rna sequencing and loks similiar ot rna seq. an alternstive is an image-based by seeign. where every mrna and transcripts are. pretty powerful, high resolution and fully resolved but is extremely expsensive and only gives a subset of genes. sequenced based gets the whole transcriptome. 

these diffeent tehconlogies have different uses and resolutions. different reasons to use the technology.

generally getting these slices by themselves.

#### Great power, many extra steps
you really are limitned byt rhe mcifoscope or slide, so often you need to chop it up into a grid and then image correct and stich them togehter afterwards. next the hard task is image registratino of aligning slices together. you end up with slightly shifted slices. on the other hand if you have spatial expression, you can use the gneomic knowledge. 
after that you have to figure out each spot's location. for a transcirpt this is tricky because it isnt inherenty knwon where theyre from. the idea is to know how much transcript was created by each cell. you can tell with subsellular reoltuion, these organge and blue circles tell you where these cells are. can understand trasnscipion dynmaics based on nascent and mature transcripts. assigning is pretty hard with these approaches. when barcoding it's a bit easier for sequencing based approaches. what's hard though is that these spots may contain multiple cellls. might deconvolve to see what cell type percentages are in the spot. now you can tell where cells are. 
now to figure out cell-cell proximity through a network. so once youve figured out proxumity, can study which cells and transcripts colocalize and are funcitoning together. if you find a lot of transcripts on the edge, they may be involved with cell communication, able to tell the cell types on the boundaries based on which cells are proximal to each other. also able ot build cell neighborhoods, a lot of time the immune cells cluster together. the last one to touch on is to try to ounderstnd th e spatial expresion patterns in context

#### Spaitally vering
some genes that are linked based on the gradient to fiure out the patterns.
belayer - took the geometries and assumeed the y axis came from the same context and abel to find where genes are. there's important variation found spatially in genes. 

#### alignment/integration
lets assuem youve taken a tissue sample and sliced it up, forming four consectuvie slices. there's differences in expression of dots between slices. this methods proposes to do a pairwise alignment to get a full alignment between slices. what they proposed PASTE which uses a spatial similiarity and optimal transport. optimal transport is trying to match two probabilities. the idea behind Gromov-Wasserstein is to minimize distortion and take two spots between slice and minize the change needed to transition from one ot another. this equatino includes genetic similiarity (which it's called fused). left half of equation, c is funciton that measures how similari two spots are genetically, normailization by counts and KL divergence at each spot. next the spatial similiar on the right size, we want to compare structiural differenced between each spots in terms of spaital distance between i and j as well as k and l. if they were combined together, they should match. thsi gives us the cost os moving two samples. i and j are on separate slices. k and l are on the same slice. The prime are about the second sample. 
the goal is to minimize the entire funciton by leanring values of pi for the overall lowest value. if you let any values, its not going to work. we constraints ont hese values. Capital pi is the proababilit of moving one spor to another. make sure these are probabiliteis. make sure that the matrix is broken down by summing to one. uniform probability of 1/n assuming every spot has equal chance of it happening. the lowercase pi are probabilities. picking values of pi to minimize. hyperparameter alpha is juse how important genetic and spatial similiaritiy, alpha must sum to one. 
They minimize F using iterative conditional gradient algorithm. what matters is take the equartion that finds optimal pi for every spot. we have constraints as every spot has to match. 

#### same title plot
- if they make synthetic data, the black line is if every spot is the same. they were adding random counts. what therye showing if it only took into account only spatial and only genetic information. Spatial is not good at all as it just pkaces them on top of each other. just genomics/transcritpomics is does slightly better. 

#### partial alignment 
partial is what realistically happens. you dont have to necessarily align every spot on every slice. this is another way of representing and notating the same full alignment equaiton. what they realized they dont need to change the graident part, but the problem is trying to do this for every spot as the constraint g has to sum to 1. they add the variable s with different constraints, no longer force to g to add to one, just has to equal less than g. but we're not sure if this is enough. but s is the fraction of mass to transported. if assuming uniform probability, this tells us a fraction of the spots aligned. stil assuming that the partial alignment has to equal s

#### more plots
these are similiar plots, PASTE2 allows for partial alignment. what they show, the orangle line is what we don't know, it performs well when alpha=.1. now you have a decent solution. came up with another neat feature. 

#### Pairwise Alignment - not only showing aingment but also the histology
now we can see is that this equation does not have to be constrained to one modality. you can use histological image data to aid alignment to replace the summing of the pairs. they don't talk about it in the paper but yuo can assume the same can be done for the other half of the equation. 

#### Many Alignment/Intgegration Strategies
here is how they solved the partial alignment problem. Everything talked baout today has been trying to match spots. if you inagine your data repsents these grids. there are other methods you can represent all your genetic information and encode, maybe use neural netwotrks. also other ways to encode everything as a graphs where alingning nodes is a big part of computer science. there's a huge world of alignment strategies

#### Spot Deconvolution
want you to know this a really interesting problem in the field. not every spot contains one cell, usually 10. problem when there are mutlitple cell types as they become aggragated and become noise. compare spot based to image based to deconvolve the spots. can use one for each other. Non negative matrix approach. three inputs. Visium is imaging approach, gene vs spots. Xenium is subset of gene vs. spots. Need some way to align, coudl do thorugh PASTE. we have to assuem the genes of Ax are of Av. theyre going to decompose each of these. Need to learn more about NMF. This creates smaller matrices and there are some parts that are shared. QT is the gene expressino for cell types Q. Find matrices: P and Q; N and M are mulitpled out and used to create the original matrix. they add this value of total gene counts for interpretability. P and Q are nonnegative standardization. LEft composed to X. Right is complicated. emphasis that the QT is a vecotr and a subset of Q. MT times T uses this transform gamma and that is multipled by Q. Assume this M is a mixure of wights. Need to opimize by iminimizing the sum by using the gradient algorithm. Was able to get every deconcovled visium spot. Can also imputed/predicted of the gene in the Xenium. 

#### Great powerr, many extra steps
just to show how much work spatial transcriptomics is. 

Next class is presentation paper: Mapping the topography of spatial gene expression

Reading for wednesday: Deep learning sequence models for transcriptonal regulation 
- introudction, deep learning for seuqence modeling and epigenetic mark prediciton. 