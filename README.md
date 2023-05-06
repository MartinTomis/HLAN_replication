# HLAN replication

## CS 598 - Deep learning for health care - paper replication
## Replication of "Explainable Automated Coding of Clinical Notes using Hierarchical Label-wise Attention Networks and Label Embedding Initialisation" by Hang et al

### Introduction
This is a repository with the code of my independent replication of a medical paper using deep learning. It provides a to a maximum degree possible a standalone code to prepare data and run end-to-end the model.

### Getting started
Unless stated otherwise, it is assumed that one clones this repo and sets it to the working directory.

### Data preparation
#### Getting data access
The model uses MIMIC-III dataset, which is not in open domain. One can obtain access via physionet.org, after completed data privacy and protection trainings.
The following datasets are required:
- D_ICD_DIAGNOSES.csv
- D_ICD_PROCEDURES.csv
- NOTEEVENTS.csv
- DIAGNOSES_ICD.csv
- PROCEDURES_ICD.csv

#### Preparing raw dataset
The replicated paper refers to https://github.com/jamesmullenbach/caml-mimic for pre-processing. 

After cloning the repository, one needs to add MIMIC-III data into the following folders:

mimicdata
|   D_ICD_DIAGNOSES.csv
|   D_ICD_PROCEDURES.csv
|   ICD9_descriptions (already in repo)
└───mimic3/
|   |   NOTEEVENTS.csv
|   |   DIAGNOSES_ICD.csv
|   |   PROCEDURES_ICD.csv
|   |   *_hadm_ids.csv (already in repo)

Also file constants.py needs to be adapted. It contains the following key variables:
- MIMIC_3_DIR - directory where output will be save - setting it to absolute path ending with .../HLAN_replication/Data/mimic-iii is recommended.
- DATA_DIR - directory of source data (/caml-mimic/mimicdata/mimic3)

#### Running-data prep
The caml-mimic repository includes also notebooks - dataproc_mimic_III.ipynb. It enables certain setting - e.g. selecting that only 50 most common codes are used - this is also the setting for my replication. - Running this (from the /caml-mimic/mimicdata/notebooks subfolder) prepares the data. The original notebook is few years old and does note reflect some of the changes of the packages it uses - namely gensim. I hence also upload into this repo the slightly adapted data-prep code.

If everything ran smoothly, the following files should be available in the MIMIC_3_DIR:
- /train_50.csv
- /test_50.csv
- /processed_full.embed

### Running the model
Assuming that one clones this repo and sets it to the working directory, the HLAN_replication.ipynb can be executed ("Run All") to run the model.