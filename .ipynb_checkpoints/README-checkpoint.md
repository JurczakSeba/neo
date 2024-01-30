# AI Guided Selection of Neoantigens
## Project Description
This is a comprehensive pipeline aimed at identify neoantigens in cancer patients. The adaptive immune system depends on the presentation of protein fragments by MHC molecules. Machine learning models of this interaction are used in studies of infectious diseases, autoimmune diseases, vaccine development, and cancer immunotherapy. The project consists of three stages: 

**1. Classification of known antigens from potential neoantigens candidates.**

A decision tree-based classifier, trained to identify new, as yet unknown antigens. All peptides marked as potential neoantigens are subject to further analysis.


**2. Prediction of the peptides presented on major histocompatibility complex (MHC) class I.**

Antigen binding predictor trained on mass spectrometry-identified MHC ligands. A deep neural network from the MHCflurry library was implemented, which calculates the binding affinity between a given peptide and the MHC complex.

**3. Calculating the probability of recognizing a neoantigen by TCR receptors.**

TCRGP, a GP based probabilistic classifier which can be trained to predict TCR specificity to any epitope given sufficient training data for the specified epitope. GPs implement a Bayesian nonparametric kernel method for learning from data.


## Dependencies
* python 3.8.10
* mhcflurry 2.0.5
* tcrgp 1.0.0
* tensorflow 2.8.0
* scikit-learn 1.0.1

## Installation 
1. Download and extract .gz file
2. Create a new virtual environment

`$ vitualenv venv`

3. Install above-mentioned dependencies from requrirements.txt file

 `$ pip install -r /path/to/requirements.txt`

4. Run example.ipynb

## Data
Together with the pipeline we provide training data for each model. The data was collected from databases such as: IEDB, VDJdb

### Input
The pipeline needs three input .csv files to run:

1. Antigen list in column form,  9 - 12 sequence length

| antigen     | 
| ----------- | 
| AAAANTTAL   | 
| AAASSLLYK   | 

2. Major histocompatability complex list in column form. MHCflurry normalizes allele names using the mhcnames package. Names like `HLA-A0201` or `A*02:01` will be normalized `HLA-A*02:01`

| mhc    | 
| ----------- | 
| C*05:01   | 
| A*02:06   | 

3. TCR sequences list in column form. 

| tcr    | 
| ----------- | 
| CASSLWTGSHEQYF |
| CASSVVGGNEQFF |

Moreover, input data can be entered via python lists.

### Output
Pipeline generates a .csv file with results from all stages in descending order. 

|antigen|mhc_molecule|tcr_sequence|neo_vs_anti|mhc_presentation_score|tcr_probability|
|-------|------------|------------|-----------|----------------------|---------------|
|ERYFRIHSL|A*01:01|KQQVTQIPAALSVPEGEF|1|0.9846125|0.9123411|
|FLARKGIDT|A*02:01|QEVTQIPAALSVPEGENLVF|1|0.8462521|0.8241231|

### Sample dataset
In folder example_data we provide a sample dataset.

## References

[1] Emmi Jokinen, Jani Huuhtanen, Satu Mustjoki, Markus Heinonen, and Harri Lähdesmäki. (2019). Determining epitope specificity of T cell receptors with TCRGP.

[2] Shugay, M. et al. (2017). VDJdb: a curated database of T-cell receptor sequences with known antigen specificity. Nucleic acids research, 46(D1), D419-D427.

[3] Dash, P. et al. (2017). Quantifiable predictive features define epitope-specific T cell receptor repertoires. Nature, 547(7661).

[4] T. O'Donnell, A. Rubinsteyn, U. Laserson. "MHCflurry 2.0: Improved pan-allele prediction of MHC I-presented peptides by incorporating antigen processing," Cell Systems, 2020. 