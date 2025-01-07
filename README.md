# imbalanced-classification-task

This repository contains code for a binary classification task to predict whether a client will subscribe to a bank term deposit based on imbalanced data. The given dataset is highly imbalanced, with the "no" class having 29,238 samples and the "yes" class having 3,712 samples. Due to this imbalance, achieving an F1 score of 0.5 is considered quite satisfactory. Our team applied various methods to handle the imbalanced data, including data preprocessing, feature selection, and Bayesian optimization, ultimately achieving a final performance of 0.5054.

**1. How are NaN values handled?**
Upon visualizing each feature, we identified certain features with highly skewed data distributions. For example, the job column showed a distribution heavily concentrated in "admin" and "blue-collar" categories. Therefore, we decided to replace NaN values in the job column proportionally based on the existing distribution. Specifically, for the job column, there were a total of 73 NaN values, which were allocated proportionally to the existing categories. The following dictionary shows how NaN values were distributed: 18 values were assigned to "admin," 2 to "entrepreneur," 1 to "unemployed," and so on. Using this approach, we filled the NaN values in features like job, marital, default, and loan, which exhibited significant distribution biases.
{
  "admin": "18.25",
  "entrepreneur": "2.19",
  "unemployed": "1.46",
  "services": "7.30",
  "blue-collar": "16.06",
  "housemaid": "1.46",
  "self-employed": "2.19",
  "retired": "2.92",
  "technician": "12.41",
  "management": "5.11",
  "student": "1.46",
  "nan": "0.00"
}
- ![image](https://github.com/user-attachments/assets/864eee47-558a-450a-9efb-bfd742f5dfaa)
  
For features like education and housing that did not exhibit significant distribution biases, we used a KNN (K-Nearest Neighbor) imputer to handle NaN values. KNN imputes NaN values based on the k-nearest neighbors in the dataset, a method typically used for numerical variables. However, we extended its application to categorical data. To use the KNN imputer, the data needed to be converted into numerical types. We encoded the categorical values into integers using the label encoder method.

Using a combination of proportional allocation (for skewed distributions) and KNN imputation (for less skewed distributions) resulted in better performance compared to using the KNN imputer alone. The F1 score improved from 0.4892 to 0.5054.

**2. How are features handled?**
- 
