# Methodology
## Choice of database  
Data used: 
- PDBbind (http://www.pdbbind.org.cn/)
- Train set: Refined 2020 dataset (http://www.pdbbind.org.cn/download.php)
- Test set: CASF-2016 coreset (http://www.pdbbind.org.cn/casf.php)

NB: For each complex, we have a folder with its PDB code. Inside, there are 4 different files describing the structure of the given complex. For train and test sets, **protein structures** were taken from the "code_protein.pdb" files (e.g 1a1e_protein.pdb) and **ligand structures** were taken from "code_ligand.mol2" files (e.g 1a1e_protein.mol2). From pdb files, only atomic information were used whereas with mol2 files, atomic and bond information were taken (cf *feature_eng_baseline* notebook).

## Steps performed within the project 
### First phase of the project: data preprocessing and feature engineering
1- Remove **from the train set** the 263 comon complexes between the train and test sets  to avoid data leakage (cf *feature_eng_baseline* notebook)

2- *Sequence alignment clustering* performed using **CD-HIT** algorithm to decrease the bias within the train set. Indeed, many complexes belong to the same protein familly which can make the data highly biased. \
    - Parameters used: *90%* cutoff 
    - Results: The original 5316 complexes present in the train were clustered into **1591** clusters  
To access the website, use this link: https://github.com/weizhongli/cdhit-web-server. Creating a docker image will be necessary to access the website and perform clustering.

3- Once clusters have been done and after some cleaning, **1394** clusters were kept (cf dataset.xlxs file). Out of those, I decided to generate 3 different datasets by choosing from each cluster 3, 5 and 10 complexes with different binding affinity constants distributions (cf *feature_eng_baseline* notebook) \
    - dataset 1: choose complexes with minimum, median and maximum Kds \
    - dataset 2: choose complexes with minimum, 20th centile, median, 80th centile and maximum Kds \
    - dataset 3: choose complexes with minimum, 25th centile, median, 75th centile and max Kds \
The idea was to keep 3 datasets with different binding affinity distributions to test each of those against the ML algorithms and compare the results to a baseline we'd define to **find out which dataset is less biased and would therefore give more reliable results**.
As shnown below, dataset 2 will be chosen as being the best. 

4- Then, feature engineering was done on the 3 different datasets to create 3 final datasets (cf *feature_eng_baseline* notebook)
    - Be careful to perform the same feature engineering on the protein dataframes for the train and test 

5- Finally, these 3 datasets were used to perform feature selection and calculate a baseline algorithm (cf *feat_sel_1* notebook). **Pearson's correlation** and **RMSE** were the calculated metrics to compare our results to the ones provided in the litterature by the CASF-2016 paper (https://pubs.acs.org/doi/10.1021/acs.jcim.8b00545).

## Use of the CASF-2016 script: 
On the CASF-2016 folder, there is the *coreset* folder in which there are files which were used to build the test set with 285 complexes. More than that, there is a *power_scoring* folder which contains a **scoring_power.py** script. The latter is very important and should be used to compare the predicted results gotten by algorithms to the results available in the litterature. Indeed, this script was used to establish a best scoring function ranking for the different published algorithms in the litterature.

In my case, I used the **scoring_power_copy.py** script to do so (I slightly modified the original script to make it work locally). To make the script work, binding affinity predictions need to be saved in a *.dat* file (cf training_outputs/Predictions folder). Then, this command needs to be runned in the terminal (using an anoncada environment preferably): *python scoring_python_copy.py -c CoreSet.dat -s bind_aff_pedictions.dat -p positive -o bind_aff_predictions*. To get the detailed meaning of each parameter, please refer to the *README* file within the CASF-2016 folder. After running the script, a *name_file_processed_score* (cf training_outputs/Script_outputs folder) will be generated with the actual pKa, the predictions and a summary of the scoring power will be provided on the **terminal**. This summary should be used to compare our algorithm to the litterature. 

## Results 
These results come from the pythons scipt as described previously: 
| Date | Features | Models | Hyperparameters | Performance |
|------|----------|--------|-----------------|-------------|
|30/10/2022| No feature selection | linear regresion | Default | Dataset 1: N: 245 Peason's: 0.198 RMSE: 2.15 |
| |  |  |  | Dataset 2: N: 266 Peason's: 0.318 RMSE: 2.04 |
| |  |  |  | Dataset 3: N: 247 Peason's: 0.219 RMSE: 2.16 |
| |  |  |  | Average: N: 97 Peason's: 0.117 RMSE: 2.04 |
|01/11/2022| Feat sel with RFE | linear regresion | Default | Dataset 1: N: 279 Peason's: 0.134 RMSE: 2.15 |
| |  |  |  | Dataset 2: N: 265 Peason's: 0.307 RMSE: 2.04 |
| |  |  |  | Dataset 3: N: 267 Peason's: 0.106 RMSE: 2.14 |
| |  |  |  | Average: N: 267 Peason's: 0.305 RMSE: 2.05 |
| |  |  |  | => **Dataset 2 gives the best results, the remaining will be performed using the latter** |
07/11/2022| Feat sel with RFE with noise feature | linear regresion | Default | Dataset 2: N:  Peason's:  RMSE |

# Future work 
Perform phases 2 and 3 of the project (cf README). Then, developing a website to allow users to test the underlying score function should be done.
