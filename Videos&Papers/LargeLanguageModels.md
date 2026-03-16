## Week 9: Large Language Models for Computational Biology A Primer
### Learning from Large-Scale Biological Data
### Week 9 - Monday March 16, 2026

### Learning from Sequence

Video - [Jian Ma | Large Language Models for Computational Biology A Primer](https://www.youtube.com/watch?v=tRTVxlakJCw) From CMU - treat this video more like a journal club to spark curiousity and discussion

Antonie van Leeuwenhoek was a cloth merchant who built the Leeuwenhoek Microscope and observed a living cell through his microscope ~350 years ago. Jian Mu's lab is about building computational methods to elucidate multiscale single-cell spatial epigenomics. Treats the cell as 'spices' or measure these factors that come together to affect the phenotype of the cell.

#### Recent history of Language Models
- Statistical language models 
    - predict the next word based on the most recent context, e.g., Markov assumptions
    - hard to build high-order language models
- Neutal language models
    - probability of word sequences from neural nets
    - distributed representation of words (e.g., word2vec)
- Pre-trained language models
    - capture context-aware word representations by pre-training
    - pre-training, then fine-tuning on downstream tasks
    - ELMo, pretrained with bi-LSTM — more context sensitive
    - BERT (Google), based on Transformer, context-aware, pretrained on large unlabeled data
    - GPT (OpenAI), based on Transformer

#### Large Language Models
- very large-sized pretrained language models
- Scaling laws
    - Kaplan et al. 2020 (OpenAI)
    - Chinchilla scaling – Hoffman et al. NeurIPS 2022
- Differences compared to LMs
    - Large # of model paramters
    - LLMs display some surprising "emergent abiliites" - when it reaches a turning point of working in a really powerful way different from small models
    - LLMs harbor powerful features such as prompting interface (e.g., GPT-$ API)
    - LLMs need tremendous resouces to build
#### What is a Foundational Model?
- train a model that can be repurposed to perform different tasks
- Foundational models are a replacement for task-specific models
- Large-scale pretraining on large unlabeled datasets
- Finetuning for diverse downstream tasks
- Self-supervised learning
- Transfer learning
- GPT-4, DALL-E 2, BERT, etc.
#### Why we need Transformer?
- We need dynamic representations for context-specific information
    - e.g., "I like it" vs. "I do not like it". The work "like" should have different representations because it has opposite meanings – different context and dependencies
    - Vanilla RNNs are slow with poor memory retention
    - LSTMs are still slow and sequential
    - CNNs can be parallelized but lack dynamic context capture
- Transformer combines the benefits of dynamic computation, good memory, and parallelizability. 
- Transformer archeticure designed to handle sequential data (e.g., text, time-series)
    - captures long-range dependencies and context
- Utilized the self-attention mechanism to extract features for each work in a sentence.
- Consists of an encoder and a decoder. Both contains a core block of "an attention and a feed-foward network" repeated N times. 
#### What is Attention?
- Attention allows models to focus on different parts of an input sequence
- Calculating attention scores (scaled dot product): Attention(Q, K, V) = softmax( Q times K^(T)/sqrt(dk) ) times V
    - Q - current position in input seeking context from other positions
    - K - captures the information that Q attents to
    - V - actual content associated with positions in input
#### Transformer architecture in BERT and GPT
- BERT-style - Encoder-only or Encoder-Decoder
    - Training - Masked Language Models
    - Model type - discriminative
    - Pretrain task - predict masked words (mask some of the words and try to predict them)
- GPT-style - Decoder only
    - Training - Autoregressive Language Models
    - Model type - generative
    - pretrain task - predict next word (using the words prior to that)
- more and more methods are using decoder-only such as GPT, LLaMA
#### Application of (large) language models in genomics
- Large pretrained models can be utilized for finetuning on downstream tasks with limited training data
- Data sparsity problem in biology
    - noisy/sparse data
    - incomplete data in biology, e.g., rare disease, precious samples
- Train embeddings with more generalized knowledge can help mitigate batch effects and biases in the data
- Today, we introduce recent work in the following two directions:
    - Modeling genomic sequences
    - Modeling single cell data
#### Some recent language models for genomic sequence
- Big Bird - uses a transformer architechure but doesn't model the probability of the sequential dependencies of the tokens of a sentence
- DNABERT, Enformer, Nucleotide Transformer, DNABERT-2, HyenaDNA
#### DNABERT
- Pre-trained BERT for DNA sequences based on the human reference genome
- Overlapping k-mer tokenization (how you represent a word in our genomics context)
- Downstream tasks:
    - promoter region prediction, transcriptional factor binding site prediction, splice site prediction, functional variants identification
#### DNABERT vs. DNABERT-2
- Tokenization: overlapping kmer tokenization --> byte pair encoding
    - to prevent information leakage and sample inefficiency
- Replace positional embeddings with attention w/ linear biases
    - to overcome the input length limit of DNABERT
- Other deep learning tricks to increase computation and memory efficiency
- Results - performance varies, still a challenge of how do you represent these sequences in terms of tokenization in order to apply these transformer based models. 
#### Some recent language models for single-cell genomics
- scGPT, scBERT, GeneFormer, scFoundation
#### Full scBERT model training scheme
- First model publlished for using transformers for single cell models
- Pretrains on lot of scRNA-seq data
    - How do you represent tokenization: view cells as sentences and genes as words
    - How do you represent expression (as it is continous and has noise): discretize expression through expression embedding by binning where you chop up several buckets and label high/med/low gene expression of each bin
    - They also try to capture some intrinsic properties of gene-gene dependencies (of underlying regulatory relationships), how: Gene2vec (GRNS), turn a gene into a vector/embedding by incorporating their gene-gene interactions
    - So they capture expression and Gene2vec embeddings
- BERT - mask some of gene expression and predict and recover the rest of gene expression. Then the model uses self-supervised pretraining.
- Once you do pre-train, you can fine tune for different tasks. for example: cell type classification. If I give a single cell with gene expression, can you tell me what its cell type is? 
#### Part 1: Self-supervised pretraining on large-scale datasets 
- learns general gene-gene interactions
#### Part 2: Supervised finetuning for specific tasks
- learns task / dataset specific characteristics
- For models such as these, the improvement over existing models is moderate sometimes it's comparable, even a little worse, or a bit better
#### Geneformer
- Pretrained on 30 million scRNA-seq to enable context-specific predictions
- Discretize gene expression by ranking genes according to their expression
    - tokens are still genes, rank based on expression, housekeeping are everywhere so they won't be ranked high, allows for quantitty to operate on later
- Encodes network hierarchy in the attention weights of the model
    - Context awareness using attention allows for predictions specific to cell states
    - Attentions reflect important genes such as TFs or central regulatory nodes
- In silico preturbation: remove a gene, compare cell and compare gene embeddings before and after. If the differences are large, the gene has big impact.
#### scGPT overview
- Generative pretraining on 30+ million normal human cells from 50+ tissues
- Learn insights concerning genes and cells
- Adapt to specific tasks
    - cell-type annotation
    - multi-batch integration
    - multi-omic integration
    - perturbation prediction
#### Input embedding for scGPT
- Genes are token and discretized the gene expression values
- Additional set of condition tokens to integrate meta information (e.g., perturbations or different batches)
#### Generative pretraining for scGPT
- tries to use the previous information to predict the next unknown gene from the known genes, do this iteratively to capture some dependencies, and add to this continously growing the known gene list
- Attention masking for generative pretraining
    - Use known genes to predict unknown genes
    - Teacher-forcing training
#### scGPT finetuning objectives
- Fine-tuning objectives facilitate the learning of biologically meaningful cell and gene interactions for diverse downstream tasks
- Self-supervised objectives
    - Gene expression prediciton (by MLP)
    - Gene expression prediction for cell modeling (by querying)
    - Elastic cell similiarity (for data integration)
- Supervised objectives
    - Domain adpation via reverse back-propagation
    - Cell type annotation
#### Open questions?
- How to better evaluate LLMs? How to make LLMs more accessible?
- How to embed cell/gene to better maintain biological contexts?
- How to incorporate prior knowledge into the neural netwotk?
- How much finetunning is sufficeny for a specific task/dataset? Will better designed pre-training tasks help shorten finetuning?
- How to extract the knowledge claimed to be distilled by the model?
- Do we have enough data available to pretrain LLMs or Foundation Models for various modalities in genomics?
- DNA and single-cell LLMs have comparable performance compared to existing approaches – need more challenging problems. What are the important problems for LLMs?
- Specific LLMs from molecular and cell biology literature + genomics data?
- Reliable hallucinations from LLMs --> new biological hypothesis?