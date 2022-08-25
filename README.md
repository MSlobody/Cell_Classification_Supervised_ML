# Cell_Classification_Supervised_ML

## Table of Contents
* [Project Overview](#Project-Overview)
* [Resources](#Resources)
* [Background](#Background)
* [Procedure](#Procedure)
* [Findings](#Findings)
* [Future Work](#Future-Work)

## Project Overview

1. Acquired single cell RNA sequencing (scRNA-seq) data of 68,000 peripheral blood mononuclear cells (PBMCs) from [doi:10.1038/ncomms14049](https://www.nature.com/articles/ncomms14049). These 68,000 cells comprise 11 cell types with the top 1000 variable genes used as features
2. To mitigate class imbalance a weight balanced dataset and a SMOTE balanced dataset were evaluated with Logistic Regression and Random Forest classifiers in comparison to the baseline unbalanced dataset.  
3. Performed hyperparameter tuning of six supervised ML classifiers: KNN, Decision Tree, Random Forest, XGBoost, Logistic Regression and MLP. Identified that Logistic Regression and MLP far outperform other models. 
4. From the Logistic Regression classifier, model coefficients and SHAPLY values were acquired to obtain the most important genes/features for classifying each cell type

   ![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/project_overview.PNG)
   
## Resources
Python Version: 3.9.7\
Packages: imbalanced_learn, imblearn, matplotlib, mygene, numpy, pandas, scikit_learn, scipy, seaborn, shap, xgboost. See requirements.txt.\
Source Data: https://github.com/10XGenomics/single-cell-3prime-paper/tree/master/pbmc68k_analysis  \
Source Data Publication: https://www.nature.com/articles/ncomms14049 

## Background
