## Session 3: Data Doesn't Come From God: Stochastic Systems Biology
### Learning from Large-Scale Biological Data
### Wednesday February 4, 2026
This main topic of today's session is how stochasticity  portays biological systems better than if we used constant variables and how equations and graphs can capture and represent these variables. 

#### Single-Cell RNA Sequencing
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/scRNAseq.png)
- Look at a direct view of the cell and what is happening
- Today's required video talked about getting the gene x cell matrix from this and today we're looking at this data and studying the mechanisms that generated this data.

#### Central Dogma - What does our data even look like?
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/CentralDogma.png)
- Let's plot one row of this gene x cell matrix
- Based on what we know, we can assume RNA-transcripts follow a Poisson distribution
    - Poisson distribution - discrete probability distribution that expresses the probability of a given number of events occuring in a fixed interval of time if these events occur with a known constant mean rate and independently of the time since the last event
- Is gene expression Poission distributed? Usually PD has a mean and variance that you can't change. We can look at all genes, get the mean and vairance of all genes but does it follow the distribution?
- But when we plot it, at some point the data does not follow the PD, something's missing about capturing the relationship correctly → This is called over dispersed ~ the variance is higher than the mean
    - Whyy? What other distrbutions might better capture this? What is happening mechanisticly?
- There are two key phrases and assumptions that must be violated 
    - process occurs at a mean rate
    - independent of the time since the last event

#### Central Dogma - What is happening inside each cell?
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/CentralDogma2.png)
- Measuring the amount of RNA in the cell 
    - k: a constant transcription rate
- Eventually that RNA has to be degraded
    - γ: a constant degradation rate
- Can be measured as the Chemical Master Chemical Equation
    - N: amount
- This matches our intitial intution of PD
- Although this simple model tries to capture what is happening, it's not entirely correct

#### Central Dogma - Let's make the equation not fixed
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/CentralDogma3.png)
- K - stochastic rate of transcription - removes assumption about fixed rate of transcription
- What happens when we let it run to convergence?
    - It follows a negative binomial that let's the variance deviate from the mean
    - Negative binomial is more what happens in real life, similiar to PD by -1
- Let's look at a gene with over distribution

#### Negative Binomial Fits scRNA-seq Count Data Well
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/NegativeBinomial.png)
- PD is not capturing the data
- NB is fitting the gene count better and is a lot more accurate

#### The Central Dogma - Bursty model of transcription
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/CentralDogma3.png)
- Turns out we don't need to just change one assumption
- Bursty transcription B - genes turn on and off and on and off, RNA is created in bursts
- k can be the burst frequency to capture an even more accurate view
- But this is violates the other assumtpion of independent → won't follow PD
- Get's us the chemical master equation which gives a steady state of negative binomial distribtion
- Turns out considering the process, we can derive the same distribtion when we plot the raw data and can generate a distribition that looks like the actual read counts

#### We want to understand where our data came from
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/Complicated.png)
- Unfortuanely, this is more complicated
- I did skip a step, the mRNA transcripts need to processed and spliced prior to translation
- To capture reality better, let's capture splicing:
    - Nacsent unspliced inside the nucleus
    - Mature spliced outside of the nucleus
- Splicing connects these two by removing introns
- We want to understand how to separate nascent and mature, let's incorporate that now

#### Incorporating Splicing - Constant K
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/IncorporateSplicing0.png)
- Still have constant transctiotion and degradation
- New - constand splicing rate 
- Can be written as new chemical master equation
- What would this look like? Simple PD because everything is constant
- Not only are N and joint distribution is PD
- But still this isn't reality, let's up the stakes

#### Incorporate Splicing - Stochastic K
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/IncorporateSplicing.png)
- K stochastic, keep constant splicing and degraded rate
- N here is splicing 
- k ~ K is nascent and B is mature
- Both marginals M and N are NB
- But now, this is okay at the assumption of stochastic? but we know what is actually biologically happening: bursty transcription

#### Incorporate Splicing - Bursty transcription B
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/IncorporateSplicing2.png)
- B captures bursty 
- Nothing seems to change, what can we expect? We no longer observe NB
- The Splicing part still is NB in k, but B introduces dependence
- The joint distribution becomes comlpicated → complex 

#### Summary the Data Generating Process
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/SummaryDataGenerate.png)
- One-species microscopic model - one type of RNA → NB - explains total RNA counts
- Consistent two-species model - capturing bursty and splicing → complicated diagram, ongoing research → next paper tries to use this to capture latent features in single-cell data
- Now we're going to look at a different part of this process: splicing, Nascent vs Mature RNA

#### Looking at Spliced vs Unspliced Counts
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/SplicedVsUnspliced.png)
- We take our reads, sequence them, align them, look at them to see if they are spliced (do they have introns or only exons)
- Take a gene and look at one cell, is it going to tell us a biological process, we're going to try to understand this graph. 
- These are colored by different cell types, different cells have different splicing ratios. what can we learn from this?

#### Dynamics of Splicing
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/DynamicSplicing.png)
- Transcription over time t broken down between splicing and degrading
- Diagram - 
    - Purple - initial guess of transcription over time without splicing and degrading
    - Blue - bursting happens, reach equiblireum flatten off, then transcription turns off
    - Orange - splicing happens, then over time degradation rate 
- We see different peaks and decrease in the rate after the peak, we can learn about time and where we are in the process. Get the slopes over time with derivates
- Large Diagram on the right 
    - Steady State - combine these slopes - increase in the beginnning - there's a burst of expression of creating more nascent than mature → a prcoess is booting (re booting) up. Later on - decrease in speed
    - In general, in genes partaking as housekeepng genes see an increase and decrease

#### Looking at Splied vs Unspliced Counts
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/Caveats.png)
- Now we can add these arrows by inference
    - Induction ~ increase
    - Repression ~ decrease
- Optimistic take - using unspliced and spliced we infer dynamic processes, unspliced is a leading indicator of what will happen next in the future, this is amazing, but this class is about pitfalls, what are the caveats? if you want to look more into this, more caveats in paper

#### What is the right unspliced/spliced count?
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/RightSplicedCount.png)
- Biology is more complicated than removing all the introns
- Realistically, not all introns are taken out, different versions are created
- We can really get the counts about the genes just from using RNA, a lot of papers ignore this (unsplice/splice). 

#### Single-cell Data is very Noisy
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/Noisy.png)
- We had this plot of induction and repression arrows, now when we look at raw data
- We don't see those beatufiul curve
- We can see when bursting happens, there's so much noise that we are capturing approximate vague snapshots.
- Can't use this unspliced/splice counts to infer data, let's try to fix these signals
- k=50 - what happens when we average points with its nearest 50 neighbors
- Well, what is the right amount of neighbors? But these curves aren't the same
- ELAVL4 50 shows a steady rate but k=200 has both increase/decrease
- Let's make an inference: if you don't smooth the data, you can't make an inference and rate. The inference changes based on the k. Smoothing is doing the inference over k
- Black captures the upregulated and downregulated
- Transcription's degradation and splicing seem like a great use of data in theory but it't not ideal

#### Aggregating Velocities Creates Distortion
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/Distortion.png)
- Tumor or blood cells - you want to measure the directionality of the aggregation, how do we make these aggragations?
- PCA - average velocity of each gene, points at the next cell at the next most highest mature becasue that's the direction it'll be going into
- some of these arrows (right side) aren't the same between velocity calculation strategy. The aggragation isn't perfectly captured
- If we try to do this in compressed UMAP/tSNE
    -  look at the directionality, we said last class tSNE is a pretty misleading, use the embedding, if you try to use the velocity, you'll actually observe total chaos of arrows as you're mixing too many biological phenomens at once

#### Variable Cell Growth Rates
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/GrowthRates.png)
- This was just presented in Dec 2025
- Found not only do we need to worry about the problems of aggragation and unspliced/spliced, this ratio actually correlates with the cell growth. 
- when the cell is growing in excess, as the cell grows, all velocities increase. but you get completely different trends for fast and slow growing cell. if we dont account for this, those curves from before, they may inflate 

#### Looking at Spliced vs Unspliced Counts
![alt text](/My-Guide-To-Learning-from-Large-Scale-Biological-Data/03_Stochastic/Caveats2.png)
- We need to keep in account the caveats

#### Presentation Paper 
- [Biophysical modeling autoencoders for bimodal, single-cell RNA sequencing data](https://www.nature.com/articles/s41592-024-02365-9)
- Autocoder based model tries to use this relationship (nascent/mature counts) to better encode single cell data

#### Reading/Viewing for next class 
- [Gene regulatory network inference in the era of single cell multi omicsl](https://www.nature.com/articles/s41576-023-00618-5)
- Read introduction, inference of GRNs, and downstreme GRN