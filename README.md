# Predicting protein-ligand affinity using machine and deep learning
## Context
This project has been done in collaboration with ***Professor John Mitchell*** which I highly thank for his help and for this opportunity. 

Within this repository, you will find my notebooks. To get the exact methodology I used to engineer the data coming from **PDBbind** (http://www.pdbbind.org.cn), please refer to the file ***methodology_and_future_work.md***.

The general research context as well as the research issues I wanted to answer in this project are stated below. 
**Please note that I did not finish the project yet, I need to put this in pause, I will most likely rework on it in the future**. 

## 1. Background
Drug production is a very long process which can take more than 10 years and cost around
2.6$ billion[1]. COVID-19 pandemic has shown the urgency for more powerful computational
methods for drug discovery. 

On this matter, computational chemistry is well established and researchers from this field do their best to speed up the process by using techniques (like QM/MM or molecular dynamics) on massively parallel packages as Tinker-HP which has been developed by a researcher in France, Jean-Philip Piquemal[2,3]. However, quantum calculations remain too complex to be performed by non specialists and these are often expensive and too long.

In today’s world, the use of AI for health issues might be the best solution to quickly and securely find new drugs. This can be done by combining AI and computational chemistry techniques (by estimating molecular properties) or by using AI itself. Machine and deep learning are used to predict protein-ligand interactions (PLI) which can be done by using databases such as PDBbind, BindingDB or the protein data bank. Prediction of PLI can be divided into three categories : prediction of protein-ligand binding site, protein-ligand binding affinity and protein-ligand binding pose[1]. In this project, I will focus on the **protein-ligand binding affinity** prediction.

Predicting protein-ligand binding affinity is still an open challenge in computational drug discovery[1]. Indeed, ML methods can be much faster and less expensive than others. According to Dhakal et al., “the ability to computationally predict binding affinity of small
molecules to specific biological targets is extremely useful in the early stages of drug discovery since it allows a mathematical model to determine PLIs”[1]. \
Currently, the three main ML techniques used are **SVM, Random Forest (RF) and linear regression**[1]. In 2020, Amangeldiuly et al., used RF to build a baseline model for predicting the dissociation rate constants of several protein-ligand complexes based on structural
features[4]. Regarding DL, it is expected to play an increasingly important role within the future. Nowadays, deep neural network, CNN and deep CNN have been used since 2015 to predict the affinity[5,6]. Since ML is supposed to be biassed as it heavily uses biological
feature engineering, DL should decrease this bias as it can extract features from raw data by itself.

## 2. Research questions and general planning
Throughout this project, the research questions I wanted to answer were: 

● Can more advanced decision tree based algorithms perform better than RF for protein-ligand binding affinity prediction ?

● Can recurrent neural networks (RNN), notably Long-Short Term Memory (LSTM), perform well on predicting the affinity ?

During the first phase (for 4/5 months), I plan to take my time to properly perform data preprocessing and good feature engineering before implementing the different algorithms.

During the second phase, trying more advanced decision tree based algorithms such as **XGBoost, CatBoost and LightGBM** would be my focus in order to compare these performances to the RF ones existing in the literature. Moreover, these algorithms offer feature importance rankings which can give an explainability to the results and see if it matches the scientific intuition regarding the studied protein-ligand complexes. 

During the third phase, I would like to try RNN on raw data with a special care for LSTM. LSTM is an algorithm which works very well for time series issues by keeping into memory useful insights. Its implementation is challenging and would be the most difficult to perform on this project but using an algorithm which might keeps in memory the structure of proteins
might be very powerful.

## 3. Work done 
For 5 months (from June to October 2022), I finished the first phase of the project. As stated above, to get more details about the exact methodology used, please refer to this file ***methodology_and_future_work.md***.

## 4. Bibliography
1. Ashwin D., Cole M., John JT., Jianlin C. Artificial intelligence in the prediction of
protein–ligand interactions: recent advances and future directions. Brief in Bioinformatics,
2022, 23, 1–23
2. JBO Mitchell. Artificial intelligence in pharmaceutical research and development. Future
Medicinal Chemistry, 2018, 10, 13
3. Tinker-HP, the massively parallel version of Tinker, accessed 5th October 2022,
https://piquemalresearch.com/2018/04/11/tinker-hp-massively-parallel-version-tinker/
4. Amangeldiuly N, Karlov D, Fedorov MV. Baseline model for predicting protein-ligand
unbinding kinetics through machine learning. J Chem Inf Model 2020;60:5946–56.
5. Wang K, Zhou R, Li Y, et al. DeepDTAF: a deep learning method to predict protein–ligand
binding affinity. Brief Bioinform 2021;22:1–15.
6. Wang Y, Wu S, Duan Y, et al. ResAtom system: protein and ligand affinity prediction
model based on deep learning. arXiv 2021
