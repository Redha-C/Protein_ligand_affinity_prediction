                                            First trial
- Macro steps:
    - ML part: 
        - There is a huge work of organizing the data first. It is necessary to finally get **680** rows with each row being a complex. Features should be meaningful for the algorithm.
        - Once the df is ready with 680 features, **data augmentation** should be performed to let the algorithm generelize better
    - DL part: 
        - Try to treat the protein and ligand as human text with NLP (look at tokenize, encoder, word embedding, text similarities, TF-IDF, word embedding)


- Next steps : 
    - Download PDBBind data : **done** 
    - Check what is molecular docking and write a slide on it : **done**
    - Think about how to organize the data with the different data I have : 
        - try to concatenate the protein df with the ligands df horizontally 
        - think about choosing the data coming from MD calculations or initial structures 
    - Data structuration: 
        - Build two dataframes with 680 rows each: 
            - one based on _WAT.pdb files organized with protein features next to ligand features 
            - on based on _WAT.pdb, protein.pdb and .mol2 files to get individual protein features next to individual ligand features next to complex features 
    - Deal with missing values within df_wat_total and df_ligands_bonds 
    - Choose the metric to use (check litterature)
    - Perform feature engineering on df_lig_bonds to calculate the number of bond_types in each ligand : v 
    - Try to perform word embedding to give a direction from an atom to another within each ligand (read the paper)
    - Perform some feature engineering to calculate distances between atoms and complexes on df_wat_total (check biopandas and start with df_lig_bonds) 
    - Transform df_wat_total to get protein and ligands features (df with 680 rows and x columns)
    - Do some feature engineering on df_lig_bonds and join the aggregated form of this df with the aggregated form of df_wat_total 


- Tasks done: 
    - Downloaded PDBBind data 
    - Created 5 different dataframes : 
        - one with 3D structures of proteins 
        - one with 3D structures of ligands 
        - one with bond structures of ligands 
        - one with 3D structures of complexes surrounded by water molecules (which were deleted subsequently)
        - one with the dissociation rate constants 
    - Creation of unique keys to make joins simpler: 
        - protein_id ranging from 0 to 679 
        - ligand_id ranging from 0 to 679 
        - complex_id ranging from 0 to 679 
    - Deleting unuseful columns / Dealing with missing values : 
        - Deletion: 
            - blank_1,2,3,4 ==> total empty column 
            - alt_loc, insertion, segment_id ==> total empty column
            - charge ==> too much missing value, 98% even if I take ligands charges from df_lig_atoms 
    - Choice of the metric ? 
    - Feature engineering : 
        - Ligands:
            - Number of bonds (with respect to bond types) within each ligand organised in dataframes within 680 rows to make the join easy with df_wat_total 
            - Average number of bonds (with respect to bond types) within each ligand organised in dataframes within 680 rows to make the join easy with df_wat_total 
            - Difference between number of bonds and the mean for each ligand 
            - Average distances between atoms within a ligand
            - Total charge by ligand + average charge for each atom within a ligand 
        - Proteins: 
            - Average distances between atoms within a protein
            - Not taking into account b_factor and occupancy features bc always the same value

    - Related to the CASF-2016 paper:
        - Found the same values than the table S4 in the SI for power scoring using miniconda command line 



                                            Second trial: for the first trial, I used wrong data 


*****************************************************************************************************************************************
**General steps performed**
*****************************************************************************************************************************************
- **Train set:** Refined 2020 dataset 
- **Test set:** CASF-2016 coreset

1- Remove the comon complexes between the train and test from the train set to avoid data leakage : **done**

2- Need to perform clustering based on sequence alignment or structures to decrease the bias within the data \
    - Sequence clustering has been performed using CD-HIT with a **90%** cutoff and I got a total of **1591** clusters with different size 

3- Generate 3 different datasets by choosing from each cluster 3, 5 and 10 complexes with different binding affinity constants distributions \
    - dataset 1: choose clusters with minimum, median and maximum Kd \
    - dataset 2: choose clusters with minimum, 25th centile, median, 75th centile and maximum kd \
    - dataset 3: choose the 10 clusters based going from minimum to maximum Kds breaking down into 10 centiles \
The idea being to keep 3 datasets with different binding affinity distributions, to test each of those against the ML algorithms and compare the results to a baseline we'd define to **find out which dataset is less biased and would therefore give more reliable results**.

4- Perform feature engineering on the 3 different datasets and create 3 final datasets 
    - Be careful to perform the same feature engineering on the protein dataframes for the train and test 

5- Perform feature selection if doable 

6- Split the datasets into train/validation datasets and find the best CV (do not perform as not enough rows)

7- Try different algorithms with a baseline score to compare different algorithms (check which algo are used in the litterature and which 
one to use when small number of rows are used)

8- Fine tune the best algorithm 

*********************************************************************************************************************************
**Feature engineering performed** 
*********************************************************************************************************************************
- df_lig_bonds: (no preprocessing)
    - nb of simple, double, ar, am bonds with their means and differences
- df_ligand_atoms:
    - lig_distances: calculation of euclidian distances between atoms from the origin of the orthogonal axis (preprocessing: shift of the values to get minimum to 1 + log)
    - charges: (no preprocessing)
        - sum 
        - median 
        - mean 
        - difference between sum and mean 
        - difference between sum and median 
    - atom_type (no preprocessing): 
        - nb and average nb of atom types for each ligand 
- df_proteins:
    - feature eng on residue_name ==> number of residues by proteins and number of residues by proteins - average number of residues(and median)
    - calculate the distances between atomic proteins with x,y and z 
    - feature eng on occupancy (check EDA, **no preprocessing**) ==> check the meaning of the feature on pdb files
        - https://proteinstructures.com/structure/protein-databank/#:~:text=As%20mentioned%20above%2C%20they%20describe,This%20is%20called%20occupancy. 
    - feature eng on b_factor (check EDA,**no preprocessing**) ==> check the meaning of the feature on pdb files 
        - The term B-factor, sometimes called the Debye-Waller factor, temperature factor, or atomic displacement parameter, is used in protein crystallography to describe the attenuation of X-ray or neutron scattering caused by thermal motion
        - https://sci-hub.hkvisa.net/10.1021/acs.chemrev.8b00290
- Add noisy feature to perform feature selection 
*********************************************************************************************************************************
**Results** 
*********************************************************************************************************************************
These results come from the pythons scipt given with the CASF-2016 paper: 
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
| |  |  |  | ==> Dataset 2 gives the best results, the remaining will be performed using the latter |
07|/11/2022| Feat sel with RFE with noise feature | linear regresion | Default | Dataset 2: N:  Peason's:  RMSE |