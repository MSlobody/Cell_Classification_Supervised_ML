# Cell_Classification_Supervised_ML

## Table of Contents
* [Project Overview](#Project-Overview)
* [Resources](#Resources)
* [Background](#Background)
* [Findings](#Findings)

## Project Overview

1. Acquired single cell RNA sequencing (scRNA-seq) data of 68,000 peripheral blood mononuclear cells (PBMCs) from [doi:10.1038/ncomms14049](https://www.nature.com/articles/ncomms14049). These 68,000 cells comprise 11 cell types with the top 1000 variable genes used as features
2. To mitigate class imbalance a weight balanced dataset and a SMOTE balanced dataset were evaluated with Logistic Regression and Random Forest classifiers in comparison to the baseline unbalanced dataset
3. Performed hyperparameter tuning of six supervised ML classifiers: KNN, Decision Tree, Random Forest, XGBoost, Logistic Regression and MLP. Identified that Logistic Regression and MLP far outperform other models
4. From the Logistic Regression classifier, model coefficients and SHAPLY values were acquired to obtain the most important genes/features for classifying each cell type

   ![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/project_overview.PNG)
   
## Resources
Python Version: 3.9.7\
Packages: imbalanced_learn, imblearn, matplotlib, mygene, numpy, pandas, scikit_learn, scipy, seaborn, shap, xgboost \
Source Data: https://github.com/10XGenomics/single-cell-3prime-paper/tree/master/pbmc68k_analysis  \
Source Data Publication: https://www.nature.com/articles/ncomms14049 

## Background

The 68k peripheral blood mononuclear cells (PBMC) dataset has been used as a gold standard for evaluating cell type classification (Zheng et al., 2017). In the original publication, an unsupervised machine learning (ML) approach was used for cell type classification. First, the top 1000 variable genes, ranked by their normalized dispersion between each cell, were identified and used for principal component analysis (PCA). Subsequently, K-means clustering on the top 50 principal components identified 10 distinct cell clusters (Figure 1B). By identifying cluster specific genes, the PBMC subtypes were characterized (Figure 1C). Alternatively, the authors also classified cells using a correlation-based assignment to the expression profile of 11 reference transcriptomes (Figure 1D).

This unsupervised approach may not be powerful enough to distinguish cell types, because it works upon the assumption that the data is linearly separable. Adopting the idea to use the top 1000 variable genes as features, I instead approached the problem using supervised ML algorithms. The cell annotations from the correlation-based assignment were used as a ground truth. Due to the sparse expression pattern of each gene, I converted gene expression values to a value of one if the expression was higher than zero. Additionally, the CD4+ T Helper2 cells were removed due to their low abundance (Figure 1A).

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/figure1_background.PNG)
**Figure 1.** Figure was reprinted from Zheng, G., Terry, J., Belgrader, P. et al. Massively parallel digital transcriptional profiling of single cells. Nat Commun 8, 14049 (2017). The bar plot in panel A was added to the figure, highlighting the cell count of each cell type from the 68k PBMC dataset. 

## Findings

### Mitigating class imbalance
My first goal was to balance the different cell types to ensure the model isn’t biased against the more dominant classes such as CD8+/CD45RA+ Naive Cytotoxic cells, which are approximately 100-fold more abundant than the CD34+ cell type (Figure 1A). To do this I evaluated the performance of a Logistic Regression and Random Forest classifier on the baseline unbalanced dataset, weight balanced dataset and lastly a Synthetic Minority Oversampling Technique (SMOTE) balanced dataset. Random Forest is an ensemble-based method that can work with nonlinear data, unlike a simple Logistic Regression classifier, which assumes the data is linearly separable. By setting the weights for each cell type to be equal the model is not biased for the most overrepresented cells. Another strategy to avoid a biased classifier is to create synthetic data points of the underrepresented cell types using SMOTE. 

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/Cell_Imbalance_figure.PNG)

For all performance metrics, except precision, the Logistic Regression classifier far outperforms the Random Forest. Ensemble models generally perform better than individual classifiers, however these results indicate that the data is likely linearly separable and thus linear models are just as power as non-linear ones in distinguishing cell types. The baseline unbalanced dataset performs better in accuracy, F1 and precision for the Logistic Regression classifier. Because I am interested in keeping false negatives low and classifying all cell types well. I will use the SMOTE dataset for subsequent model selection and hyperparameter tuning.


### Selecting the best classifier

Next, I tested how a variety of different classifiers would perform, given the surprisingly good performance of the Logistic Regression model. Using RandomizedSearchCV with 100 iterations and 3-fold cross validation I optimized the hyperparameters for each model. Next, I plotted the testing data confusion matrices for each optimized classifier. 

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/confusion_matrices_figure.PNG)

The hardest cell types to classify are also some of the least abundant in the original dataset, despite SMOTE balancing. If we examine the KNN, Decision Tree, Random Forest and XGB confusion matrices, these difficult to classify cell types include the CD4+/CD45RO+ Memory and CD4+/CD45RA+/CD25- Naïve T cells. CD4+/CD45 RA+/CD25- Naïve T cells are most commonly misclassified as either CD8+/CD45 RA+ Naïve Cytotoxic or CD4+/CD25+ T Reg cells, which are roughly 5-fold more abundant in the dataset. The CD4+/CD45RO+ Memory cells are most commonly misclassified as CD4+/CD25+ T Reg cells. Interestingly, the clustering results indicate that these four cell types have a great deal of overlap (Figure 1D). Other cell types with clusters that have minimal overlap, such as CD14+ Monocytes, CD19+ B, CD34+, CD56+ NK and CD8+ Cytotoxic T are generally classified well across all 6 models. 

Next, I evaluated the f1, precision, recall, accuracy and AUROC of all 6 optimized classifiers on the testing dataset.

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/Performance_six_models.png)

- Overall, we see a trend from the worst performing to best performing classifiers in the order: KNN, Decision Tree, Random Forest, XGB, Logistic Regression and Multilayer Perceptron (MLP)
- Because of the sparse expression of genes and high dimensional gene/feature space KNN algorithms generally do not perform very well. As seen in the tSNE visualization there is also a lot of overlap between cell type clusters, which may be another explanation for the poor KNN performance (Figure 1D) 
- The Random Forest, an ensemble of decision trees, outperforms a single decision tree classifier
- Interestingly, by sequentially boosting the weight of misclassified cells, the ensemble XGB classifier outperforms a Random Forest classifier (which employs a bagging strategy and runs each decision tree independently)
- Lastly, both the Logistic Regression and MLP classifiers rely on gradient based optimization methods to converge on a class. Given the almost identical performance between the two classifiers I used Logistic Regression for subsequent feature selection


### Finding the most important genes/features for classifying each cell type

Shapley values provide a model agnostic approach to obtain the importance of each feature for cell classification. I explored the top 20 gene feature that have the most dramatic average impact on the model output. 

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/overall_SHAPLY_Features.png)

Some cluster-specific genes used for cell classification in the original publication are seen here such as CD8A/CD8B indicative of CD8+/CD45RA+ Naïve Cytotoxic cells and CD8+ Cytotoxic cells, GNLY indicative of CD56+ NK cells, and lastly CD4 reflecting the 3 different CD4 cell types (Figure 1C). 

A benefit of using a Logistic Regression classifier is the high model interpretability. Accordingly, I examined the top 10 features with the highest and lowest coefficients for each cell type and compared these genes to the top 10 genes derived using SHAPLY values. 

![alt text](https://github.com/MSlobody/Cell_Classification_Supervised_ML/blob/main/Data/SHAPLY_values_vs_Coefficients_example.PNG)

When examining individual cell types, such as Dendritic cells, both SHAPLY values and Logistic Regression coefficients share a lot of overlap between gene features. 
Genes such as CD7, CTSW, GZMM, GNLY have the top 4 ranked SHAPLY values, and have the four most negative Logistic Regression coefficients. High expression of these genes is a strong predictor against Dendritic cells. High expression of the gene LYZ is a strong predictor for Dendritic cells, with both a high SHAPLY value of +10 and the fourth most positive model coefficient. 


