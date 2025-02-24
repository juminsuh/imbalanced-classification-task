# imbalanced-classification-task

This repository contains code for a binary classification task to predict whether a client will subscribe to a bank term deposit based on imbalanced data. The given dataset is highly imbalanced, with the "no" class having 29,238 samples and the "yes" class having 3,712 samples. Due to this imbalance, achieving an F1 score of 0.5 is considered quite satisfactory. Our team applied various methods to handle the imbalanced data, including data preprocessing, feature selection, and Bayesian optimization, ultimately **achieving a final performance of 0.5054**. 👍

## **✅ How are NaN values handled?**

Upon visualizing each feature, we identified certain features with highly skewed data distributions. For example, the job column showed a distribution heavily concentrated in "admin" and "blue-collar" categories. Therefore, we decided to replace NaN values in the job column proportionally based on the existing distribution. Specifically, there were a total of 73 NaN values for the job column and we allocated the NaN values proportionally to the existing categories. The following dictionary shows how NaN values were distributed: 18 values were assigned to "admin," 2 to "entrepreneur," 1 to "unemployed," and so on. Using this approach, we filled the NaN values in features like job, marital, default, and loan, which exhibited significant distribution biases.
```
{ "admin": "18.25", "entrepreneur": "2.19", "unemployed": "1.46", "services": "7.30", "blue-collar": "16.06", "housemaid": "1.46", "self-employed": "2.19", "retired": "2.92", "technician": "12.41", "management": "5.11", "student": "1.46", "nan": "0.00" }
```
![image](https://github.com/user-attachments/assets/864eee47-558a-450a-9efb-bfd742f5dfaa)

For features like education and housing that did not exhibit significant distribution biases, we used a KNN (K-Nearest Neighbor) imputer to handle NaN values. KNN imputes NaN values based on the k-nearest neighbors in the dataset, a method typically used for numerical variables. However, we extended its application to categorical data. To use the KNN imputer, the data needed to be converted into numerical types. 
Thus, we encoded the categorical values into integers using the label encoder.

Using a combination of proportional allocation (for skewed distributions) and KNN imputation (for less skewed distributions) resulted in better performance compared to using the KNN imputer alone. The F1 score improved from 0.4892 to 0.5054.

## **✅ How are features handled?**

As mentioned earlier, the given data exhibits significant bias within each feature. Therefore, instead of treating each feature as a single entity, we attempted to decompose the categories within each feature into individual features. For example, instead of treating "job" as a single feature, we considered 'admin' as 'job_0', 'blue_collar' as 'job_1', and so on, increasing the number of features. This approach also yielded 0.5019 which was quite good performance, but lower performance compared to simply treating each feature as a single entity 0.5054. Thus, we determined to use each feature as it is, but the idea to decompose a single feature was worth enough to consider when dealing with the given task.

## **✅ How to construct a dataset for the model?**

Our team ultimately utilized the stepwise method; however, I also personally evaluated the performance when using standardization and PCA. (The reported performance of all datasets are evaluted with assign weights.)

### **1️⃣ Standardization**

Using the mean and standard deviation for each feature, standardization was applied to the preprocessed dataset. The F1 score on the test dataset was **0.5042**, confirming that preprocessing was successfully performed.

### **2️⃣ PCA**

Since the dataset contained numerous features, we attempted to extract key features using PCA (Principal Component Analysis). Upon reviewing the explained variance of each principal component using `pca.explained_variance_ratio_`, we observed that the first principal component explained approximately 90% of the variance in both the train and test datasets. However, the F1 score improved as the number of principal components increased. Consequently, we set the number of principal components to 18 and trained the model on the PCA-applied dataset. This achieved an F1 score of **0.5048** on the test dataset, demonstrating satisfactory performance.

### **3️⃣ Stepwise Method**

To reduce the number of features by selecting only the most useful ones, we employed the stepwise feature selection method. Stepwise method is a combination of forward selection and backward elimination, adding or removing one feature recursively per each step. It considers all the cases of possible subsets of features and is able to find the best subset of features based on p-values, AIC, and BIC.

It uses p-value to determine which is the significant feature for the performance. P-value represents the probability that the coefficient is actually zero. It means that the lower p-value is, the more significant the feature is. Generally, a feature is considered as statistically meaningful if the p-value of a feature is lower than 0.05.

For example, feature ‘age’ is excluded in the optimal subset of features since it has higher p-value (0.866) than 0.05. These are the features that were selected by stepwise method, and we reconstructed train and test datasets consisting of only these features.
```
['default', 'nr.employed', 'poutcome', 'previous', 'month', 'euribor3m', 'emp.var.rate', 'contact', 'cons.price.idx', 'cons.conf.idx', 'day_of_week', 'education']
```
The best performance **0.5054** was achieved using this approach.

## **✅ Which model did we use?**

Our team trained models using two approaches with the Bayesian optimization algorithm: resampling and assigning weights. As a result, **resampling achieved the best performance of 0.4315 with PCA**, while **assign weights achieved the best performance of 0.5054 with the stepwise method**.

### **0️⃣ Bayesian Optimziation**

Bayesian optimization is an algorithm that continuously explores hyperparameters for machine learning tasks and allows us to find the optimal hyperparameters more quickly and easily compared to empirical tuning or grid search. Bayesian optimization defines a function whose return value is the objective to be maximized. By creating a BayesianOptimization object and using the built-in maximize function, it identifies the hyperparameters that maximize the return value. Both resampling and assigning weights were optimized using the Bayesian optimization algorithm.
 
### **1️⃣ Resampling**

The idea behind resampling is to handle imbalanced data by oversampling using SMOTE. SMOTE synthesizes new data belonging to the minority class, alleviating class imbalance problems by creating new samples. We oversampled data multiple times and trained the model with each oversampled data in order to address the potential issue where the original characteristics of the data might not be reflected with a single sampling. By setting different `random_state` values, the model can be trained multiple times on differently sampled datasets.

To determine whether a sample is classified as 0 or 1, we used the `tot_prob` value. The `tot_prob` is the average of the probabilities generated by each model trained on each sampled dataset. In summary, resampling is a method that increases the data size of the minority class using SMOTE, trains multiple models with data generated through multiple samplings, and uses the averaged results to reduce bias.

However, the results showed that performance was lower when using datasets built with standardization or the stepwise method. On the other hand, using the PCA-based dataset I constructed, it achieved a performance of **0.4315** under the following hyperparameter settings.
```
model = GradientBoostingClassifier(
    random_state=42,
    learning_rate=0.09894,
    max_depth=int(3.987),
    n_estimators=100,
    n_iter_no_change=10
)
```

### **2️⃣ Assign Weights**

Assigning weights involves setting the `is_unbalance = True` parameter in the LightGBM classifier. This automatically adjusts the algorithm to assign higher weights to the minority class, mitigating the class imbalance problem. Unlike resampling, this method does not require multiple samplings. Instead, it simply involves modifying the model's parameters, making it a simple and fast way to achieve good performance.

Using the dataset constructed with the stepwise method and the following hyperparameter settings, it achieved the best performance of **0.5054**.

 
```
model = LGBMClassifier(learning_rate=0.08765,
                        max_depth=int(180.0),
                        n_estimators=int(34.66),
                        num_leaves=int(13.51),
                        reg_alpha=0.01698,
                        reg_lambda=0.08781)
```
