## Week 8: Foundation Models
### Learning from Large-Scale Biological Data
### Week 8 - Wednesday March 18, 2026

#### Single-cell Foundation Models
Genes are words / tokens. Cells are the sentences. 

#### scGPT overview
- Pretrained on over 33 million cells
- Unigifies generative pre-training workflows

### Introduction
#### Dataset
- CELLxGENE scRNA-seq collection - pretty diverse cell types as well as cancer datasets

#### Data for Downstream Tasks
- Cell Annotation used as labals
- Batch Integration to prevent batch effects
- Perturbation and GRN inference
- Multi-omics - joint profiling technologies

#### Input Embeddings
- Data is processed into a cell-by-gene matrix
#### Gene Token
- Each gene is assigned a unique identifier id(gi), shared voabulary 
#### Expression Values
- Used valuing bining for the variablity that can't be corrected through the batch-correcting or common processing
    - converts all expression counts into relative values
#### Condition Tokens
- Used to embedd diverse meta-informatiion associated with individual genes such as modalites
#### Embedding Layers

### Methods
- Why a foundation models? Very promoring large-scale diverse datasets and combined with the transformer approach
#### scGPT inputs
- Three inputs: gene/peak, expression, and condition tokesn
#### Transformers
- Why transformers? Self-attention mechanisms used to garner context in NLG (Natural Language Generation) # interesting id like to know more about the differences
- Problem that isn't seen in NLG - took much data
#### FlashAttention
- Accelerated self-attention mechanism that is IO aware, keeps track of reads and right, makes transformer training of single-cell more feasible.
#### Cell Representations
- Uses <cls> token to learn the pooling operation within the transformer block, used as a place holder until the embedding takes place
#### Batch/Modality Tokens
- Want it to learn the biology, instead of amplifying patterns # want to know more about the reasons, disadvantages, and costs for this token vs the input tokens.
#### scGPT Pre-training