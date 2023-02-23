# iterative-ML-COVID-pipeline
The following pipeline was implemented for classification of COVID-19 patients in the article: Daamen et al. Classification of COVID-19 Patients into Clinically Relevant Subsets by a Novel Machine Learning Pipeline Using Transcriptomic Features. IJMS (2023)

![image](https://user-images.githubusercontent.com/126176409/220999959-96991bab-a42e-4932-b422-02dcf797a5fa.png)


                    Iterative Machine Learning (ML) Pipeline

GSVA enrichment scores for 40 gene modules from publicly available whole blood bulk RNA-seq datasets were used as input for 9 ML algorithms in each of 4 binary classifications. The pipeline consisted of 3 ML iterations each of which was repeated 10 times. After the first 2 ML iterations, the top 50% of features from the top 5 performing algorithms were used as input for the next iteration. Then, the top 10 gene modules were filtered to remove re-dundant genes with correlation coefficients > 0.8 and log2 gene expression values from the remaining genes were normalized across datasets. Normalized z-scores of gene expres-sion values were used as input for the final ML iteration to select the top 20 gene features for each classifier. Detailed methods for each step in the pipeline are described below. 


    ML Classification Algorithms
 
Binary classifications were carried out in Python (v3.8.2) using scikit-learn (v0.24.1). Nine ML algorithms were implemented to distinguish COVID-19 patients from healthy individuals, critical from non-critical COVID-19 patients, and COVID-19 patients admitted to the ICU from non-COVID ICU patients (Logistic Regression (LR), Random Forest (RF), Support Vector Machine (SVM), Decision Tree (DTREE), Ada Boost (ADB), Gaussian Naïve Bayes (NB), Linear Discriminant Analysis (LDA), k Nearest Neighbors (KNN), and Gradient Boosting Classifier (GB)). Synthetic Minority Oversampling Tech-nique (SMOTE) was used to account for class imbalances between patient groups used for each classification by expanding the number of samples in the minority class to ensure equal numbers between classes used for ML. For each classification, datasets were split into 70% training and 30% validation and SMOTE was applied to the training data. Classification models for each ML algorithm were then built on the training set using default parameters from the scikit-learn library and evaluated on the validation set. Average algorithm performance was calculated based on the following metrics: sensi-tivity, specificity, accuracy, and areas under the Receiver Operating Characteristic (ROC) and Precision-Recall (PR) curves. ROC and PR curves were plotted using the Python library matplotlib (v3.3.4). Binary classification of ICU and non-ICU COVID patients from an independent dataset were used as validation for results of the iterative ML pipeline. For this classification, SMOTE was not applied. To optimize model performance on the validation dataset, a 75% training, 25% testing split was used and the class_weight=’balanced’ parameter was added to the SVM algorithm.


    Feature Importance Calculation

Feature importance was calculated for the top 5 performing algorithms in each ML iteration. For each input feature a higher feature importance score indicates how relevant that feature is towards the output of each classifier. The method of calculating feature importance was determined based the appropriate metric for each algorithm and im-plemented through the scikit-learn Python library. Gini importance, or average decrease in impurity, was used for RF, DTREE, ADB, GB, LR, and LDA while permutation im-portance was used for SVM, KNN, and NB.


    Gene feature correlation analysis

Feature redundancy was calculated for genes in the top 10 modules of each classifier using Pearson correlations between each feature and every other feature. When two genes shared a correlation coefficient > 0.8, the gene with the highest degree of correlation with all other genes in the module was removed. Pearson correlations were computed using the cor() function in R and plotted using the corplot library.


    z-score Gene Expression Normalization

Log2 transformed gene expression values were normalized before the final ML it-eration to account for batch effects across separate datasets. To do this, z-scores were calculated for each gene across samples in each dataset using the scale() function in R and then normalized datasets were combined into a dataframe used as input for the 9 ML classification algorithms.


    SHAP Value Calculation
    
The precise contribution (magnitude and directionality) of the top 20 gene features output from each classifier was determined using Shapley Additive Explanations (SHAP). SHAP values were calculated based on the RF algorithm for the final iteration of each classifier. SHAP summary plots were visualized in Python using the shap library (v0.39.0).

