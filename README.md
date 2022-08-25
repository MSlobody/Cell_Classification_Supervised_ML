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

The 68k peripheral blood mononuclear cells (PBMC) dataset has been used as a gold standard for evaluating cell type classification (Zheng et al., 2017). In the original publication, an unsupervised machine learning (ML) approach was used for cell type classification. First, the top 1000 variable genes, ranked by their normalized dispersion between each cell, were identified and used for principal component analysis (PCA). Subsequently, K-means clustering on the top 50 principal components identified 10 distinct cell clusters (Figure 1B). By identifying cluster specific genes, the PBMC subtypes were characterized (Figure 1C). Alternatively, the authors also classified cells using a correlation-based assignment to the expression profile of 11 reference transcriptomes (Figure 1D).

This unsupervised approach may not be powerful enough to distinguish cell types, because it works upon the assumption that the data is linearly separable. Adopting the idea to use the top 1000 variable genes as features, I instead approached the problem using supervised ML algorithms. The cell annotations from the correlation-based assignment were used as a ground truth. Due to the sparse expression pattern of each gene, I converted gene expression values to a value of one if the expression was higher than zero. Additionally, the CD4+ T Helper2 cells were removed due to their low abundance (Figure 1A).

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/background_figure.PNG)
 Figure was reprinted from Zheng, G., Terry, J., Belgrader, P. et al. Massively parallel digital transcriptional profiling of single cells. Nat Commun 8, 14049 (2017). The bar plot in panel A was added to the figure, highlighting the cell count of each cell type from the 68k PBMC dataset. 
