# AI Guided Selection of Neoantigens
## Project Description

This repository houses an advanced bioinformatics pipeline designed for the identification and prediction of immunogenic neoantigens from whole exome sequencing (WES) or whole genome sequencing (WGS) data of matched tumor-normal samples. 
The pipeline integrates cutting-edge computational methods and bioinformatics tools to process raw sequencing data, identify high-confidence genetic variants and predict the immunogenic potential of neoantigens through a multi-stage analysis.

### Predictive Analysis Stages ###

**1. Stage 1: Antigen Classification**

A Random Forest-based classifier, trained on data from immune epitope databases, classifies mutant peptides into antigens and neoantigens. 
The classifier's output is probabilistic, facilitating its integration into generative models for neoantigen prediction.


**2. Stage 2: MHC-Peptide Interaction Prediction**

The MHCflurry 2.0 package is employed to predict the binding affinity and cleavage efficiency of peptides with MHC molecules. 
This stage assesses the binding affinty of a peptide being presented by MHC molecules to T-cells.

**3. Stage 3: TCR-Neoantigen Interaction Strength**

Using the TCRGP package, this stage predicts the interaction strength between neoantigens and TCRs, providing insight into the potential efficacy of immune responses against the identified neoantigens.

**4. Defining Thresholds**

Optimal thresholds for distinguishing between binding and non-binding events are determined through a comprehensive analysis, including cross-validation and examining distributions of predicted scores. 
This enables precise identification of neoantigens with high immunogenic potential.

### Neoantigen Generation Techniques ###

Two approaches are employed for generating neoantigen candidates:

**1. Reinforcement Learning-Based Generator**

Leverages a Q-network to predict cumulative rewards from the discriminator, guiding the generation of neoantigen candidates. 
This model employs a reinforcement learning framework to optimize peptide sequences for immunogenicity.

***Components***

1. Discriminator
   Functions similarly to the one in the Monte Carlo approach, providing a cumulative reward to each generated peptide sequence.
2. Q-Network
   Trained to predict cumulative rewards from the discriminator.
   It consists of three fully connected layers with ReLU activation functions.
   The input is an aggregated one-hot encoded representation of peptide, MHC, and TCR sequences, while the output predicts the discriminator's cumulative reward.
   
***Process***

1. Step Limit
   Each episode is limited to 100 steps, promoting exploration of peptide sequences.
2. Reward Threshold
   An episode ends if the cumulative reward threshold of 0.8 is not met within the maximum steps.
3. Mutation Process
   In each step, peptide sequences undergo mutations, including amino acid replacement, appendage, or deletion.
4. Exploration and Exploitation:
   An epsilon-greedy strategy is employed for action selection.
5. Ranking
   After the completion of episodes, neoantigens are ranked based on their cumulative rewards, yielding the top-100 list of selected candidates.

**2. Monte Carlo-Based Generator**

Iteratively introduces mutations into peptide sequences, assessing their immunogenic potential through a cumulative reward mechanism provided by the discriminator, which consists of the three predictive stages.
Initial step begins with a random initialization of the peptide sequence. Peptide can be mutated by: replacing an arbitrary amino acid, appending a random amino acid at the end of the sequence and deleting an amino acid at an arbitrary position.
Cumulative Reward is calculated as the product of scores from the three predictive stages of the pipeline. 
This Monte Carlo approach, together with the Reinforcement Learning technique, forms part of our comprehensive predictive pipeline. 
These methods are applied in two regimes: screening lists of peptides from patients' genomic data and analyzing generated peptides. 
This dual approach ensures a robust and diverse selection of neoantigen candidates for further investigation.

### Needleman-Wunsch-Based Sequence Similarities ###

To characterize the diversity of amino acid sequences among antigens, MHC-I molecules, and TCRs, the pipeline utilizes the Needleman-Wunsch algorithm. 
This dynamic programming approach optimizes sequence alignments, providing a quantitative measure of sequence similarity that aids in the selection of diverse and potentially immunogenic neoantigens.

For a more comprehensive exploration of our methodology and findings, please refer to our paper
   
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
Together with the pipeline we provide training data for each model. The data was collected from databases such as: IEDB, VDJdb7-6

### Input
The pipeline needs as an input .csv file to run:

1. Antigen list in column form,  9 - 12 sequence length
2. Major histocompatability complex list in column form. MHCflurry normalizes allele names using the mhcnames package. Names like `HLA-A0201` or `A*02:01` will be normalized `HLA-A*02:01`
3. TCR sequences list in column form. 


| antigen     |  mhc_molecule    |  tcr_sequence | 
| ----------- |  ----------- | ----------- |
| AAAANTTAL   |  C*05:01   | CASSLWTGSHEQYF |
| AAASSLLYK   |  A*02:06   | CASSVVGGNEQFF |

Moreover, input data can be entered via python lists.

### Output
Pipeline generates a .csv file with results from all stages in descending order. 

|antigen|mhc_molecule|tcr_sequence|neo_vs_anti|mhc_presentation_score|tcr_probability|
|-------|------------|------------|-----------|----------------------|---------------|
|ERYFRIHSL|A*01:01|KQQVTQIPAALSVPEGEF|1|0.9846125|0.9123411|
|FLARKGIDT|A*02:01|QEVTQIPAALSVPEGENLVF|1|0.8462521|0.8241231|

### TUTORIAL
**1. Identyfication of neoantigens**
- Launch Jupyter Notebook
- Navigate to extracted folder and open `example.ipynb` file in Jupyter Notebook
- Enter the path to the csv file that contains peptides, MHC molecules and TCR sequences
- Run notebook.

**2. Generate neaoantigens**
- Launch Jupyter Notebook
- Navigate to extracted folder and open `RL.ipynb` or `MonteCarlo.ipynb` file in Jupyter Notebook
- Enter the path to the csv file that contains peptides, MHC molecules and TCR sequences (to generate new peptides as input is using MHC molecules and TCR sequences)
- Run notebook.

### Important ###
To run tutorial we provide a generated synthetic genomic data. This is a file `synthetic.vcf`. 
## References

[1] Emmi Jokinen, Jani Huuhtanen, Satu Mustjoki, Markus Heinonen, and Harri Lähdesmäki. (2019). Determining epitope specificity of T cell receptors with TCRGP.

[2] Shugay, M. et al. (2017). VDJdb: a curated database of T-cell receptor sequences with known antigen specificity. Nucleic acids research, 46(D1), D419-D427.

[3] Dash, P. et al. (2017). Quantifiable predictive features define epitope-specific T cell receptor repertoires. Nature, 547(7661).

[4] T. O'Donnell, A. Rubinsteyn, U. Laserson. "MHCflurry 2.0: Improved pan-allele prediction of MHC I-presented peptides by incorporating antigen processing," Cell Systems, 2020. 