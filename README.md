# FloraBERT

scripts/: directory for production code

0-data-loading-processing/:
01-gene-expression.py: downloads and processes gene expression data and saves into "B73_genex.txt".
02-download-process-db-data.py: downloads and processes gene sequences from a specified database: 'Ensembl', 'Maize', 'Maize_addition', 'Refseq'
03-combine-databases.py: combines all the downloaded sequences within all the databases
04a-merge-genex-maize_seq.py:
04b-merge-genex-b73.py:
05a-cluster-maize_seq.sh: clusters the promoter sequences into groups with up to 80% sequence identity, which may be interpreted as paralogs
05b-train-test-split.py: divides the promoter sequences into train and test sets, avoiding a set of pairs that indicate close relations ("paralogs")
06_transformer_preparation.py:
07_train_tokenizer.py: training byte-level BPE for RoBERTa model
1-modeling/
pretrain.py: training the FLORABERT base using a masked language modeling task. Type python scripts/1-modeling/pretrain.py --help to see command line options, including choice of dataset and whether to warmstart from a partially trained model. Note: not all options will be used by this script.
finetune.py: training the FLORABERT regression model (including newly initialized regression head) on multitask regression for gene expression in all 10 tissues. Type python scripts/1-modeling/finetune.py --help to see command line options; mainly for specifying data inputs and output directory for saving model weights.
evaluate.py: computing metrics for the trained FLORABERT model
2-feature-visualization/
embedding_vis.py: computing a sample of BERT embeddings for the testing data and saving to a tensorboard log. Can specify how many embeddings to sample with --num-embeddings XX where XX is the number of embeddings (must be integer).
module/: directory for our customized modules

module/: our main module named florabert that packages customized functions
config.py: project-wide configuration settings and absolute paths to important directories/files
dataio.py: utilities for performing I/O operations (reading and writing to/from files)
gene_db_io.py: helper functions to download and process gene sequences
metrics.py: functions for evaluating models
nlp.py: custom classes and functions for working with text/sequences
training.py: helper functions that make it easier to train models in PyTorch and with Huggingface's Trainer API, as well as custom optimizers and schedulers
transformers.py: implementation of RoBERTa model with mean-pooling of final token embeddings, as well as functions for loading and working with Huggingface's transformers library
utils.py: General-purpose functions and code
visualization.py: helper functions to perform random k-mer flip during data processing and make model prediction
Pretrained models
If you wish to experiment with our pre-trained FLORABERT models, you can find the saved PyTorch models and the Huggingface tokenizer files here

Contents:

byte-level-bpe-tokenizer: Files expected by a Huggingface transformers.PretrainedTokenizer
merges.txt
vocab.txt
transformer: Both language models can instantiate any RoBERTa model from Huggingface's transformers library. The prediction model should instantiate our custom RobertaForSequenceClassificationMeanPool model class
language-model: Trained on all plant promoter sequences
language-model-finetuned: Further trained on just maize promoter sequences
prediction-model: Fine-tuned on the multitask regression problem
Personal Updates on Forked Repo:
The following updates have been done using python scripts under 0-data-loading-processing/:

Data from step 2 using Refseq links:

Install zip file from here --> (contains data folder after step 2 using refseq links)
unzip and add to florabert, if needed
further testing required
Data from step 2 using Ensembl links:

Install zip file from here --> (contains data folder after step 2 using ensembl links)
unzip and add to florabert, if needed
further testing required
Data from step 2 using Maize NAM links:

Install zip file from here --> (contains data folder after step 2 using maize_nam (from MaizeGDB FTP; only NAM lines) links)
unzip and add to florabert, if needed
further testing required
Previous 3 steps, if used together, need to be merged to get the correct folder structure and then run 3rd file under data-loading folder

Data from step 3:

Install zip file from here --> (contains data folder after step 3)
unzip and add to florabert, if needed
further testing required
Data from step 5:

Install file from here --> (contains data folder after step 5)
add to florabert, if needed
further testing required
Data from step 6:

Install file from here --> (contains data folder after step 6)
add to florabert, if needed
further testing required
First module has been completed. All data / outputs are under data or models. Moving to Second Module. The following steps were essential for this script.

The following updates have been done using python scripts under 3-RNAseq-quantification/:

The scripts under this module requires a lot of resources and time (patience). We opted to use the Bioinformatics website Galaxy. This provides every user 250GB storage and allows the ability to use a number of very useful and important bioinformatics tools.

The scripts under the module dealt with 26 NAM lines / cultivars of Maize. We replicated the entire process under this module in this website, with some minor changes (not in output). The first step was to get all the runs corresponding to each cultivar and unique organsim part for each, to avoid repitition.

This was achieved by getting the base data from EBI and searching for the 2 Bioprojects mentioned in the supplementary material (under research_papers). This data was then used alongside the helper_codes scripts to get the file unique_orgs_runs.tsv. This file contains the runs corresponding to unique organism parts of each cultivar.

A workflow was then created / implemented / configured (the base workflow was created by user vasquex11 on the mentioned website) to align with the scripts. The runs were first uploaded per cultivar to the website (after logging in) in txt format, one per line. Next, fasterq-dump tool was used with --split-files option selected to get the fastq files corresponding to the runs.

The created workflow FloraBERT Test (Trimmomatic + HISAT2 + featureCounts) was used to perform all the actions mentioned in the module. The final output are the featureCounts files corresponding to each run ( extending to unique organsim part of cultivars ). The steps are self-explanatory (using the research papers).
