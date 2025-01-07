# imbalanced-classification-task

This repository contains code for a binary classification task to predict whether a client will subscribe to a bank term deposit based on imbalanced data. The given dataset is highly imbalanced, with the "no" class having 29,238 samples and the "yes" class having 3,712 samples. Due to this imbalance, achieving an F1 score of 0.5 is considered quite satisfactory. Our team applied various methods to handle the imbalanced data, including data preprocessing, feature selection, and Bayesian optimization, ultimately achieving a final performance of 0.5054. 👍

## **✅ How are NaN values handled?**

Upon visualizing each feature, we identified certain features with highly skewed data distributions. For example, the job column showed a distribution heavily concentrated in "admin" and "blue-collar" categories. Therefore, we decided to replace NaN values in the job column proportionally based on the existing distribution. Specifically, for the job column, there were a total of 73 NaN values, which were allocated proportionally to the existing categories. The following dictionary shows how NaN values were distributed: 18 values were assigned to "admin," 2 to "entrepreneur," 1 to "unemployed," and so on. Using this approach, we filled the NaN values in features like job, marital, default, and loan, which exhibited significant distribution biases.

{ "admin": "18.25", "entrepreneur": "2.19", "unemployed": "1.46", "services": "7.30", "blue-collar": "16.06", "housemaid": "1.46", "self-employed": "2.19", "retired": "2.92", "technician": "12.41", "management": "5.11", "student": "1.46", "nan": "0.00" }

![image](https://github.com/user-attachments/assets/864eee47-558a-450a-9efb-bfd742f5dfaa)

For features like education and housing that did not exhibit significant distribution biases, we used a KNN (K-Nearest Neighbor) imputer to handle NaN values. KNN imputes NaN values based on the k-nearest neighbors in the dataset, a method typically used for numerical variables. However, we extended its application to categorical data. To use the KNN imputer, the data needed to be converted into numerical types. We encoded the categorical values into integers using the label encoder method.

Using a combination of proportional allocation (for skewed distributions) and KNN imputation (for less skewed distributions) resulted in better performance compared to using the KNN imputer alone. The F1 score improved from 0.4892 to 0.5054.

## **✅ How are features handled?**

As mentioned earlier, the given data exhibits significant bias within each feature. Therefore, instead of treating each feature as a single entity, we attempted to decompose the categories within each feature into individual features. For example, instead of treating "job" as a single feature, we considered 'admin' as 'job_0', 'blue_collar' as 'job_1', and so on, effectively increasing the number of features. This approach also yielded 0.5019 which was quite good performance, but lower performance compared to simply treating each feature as a single entity 0.5054. Thus, we determined to use each feature as it is, but the idea was worth enough to consider when dealing with the given task.

## **✅ How to construct a dataset for the model?**

Our team ultimately utilized the stepwise method; however, I also personally evaluated the performance when using standardization and PCA.

### **1️⃣ Standardization**

Using the mean and standard deviation for each feature, standardization was applied to the preprocessed dataset. The F1 score on the test dataset was 0.5042, confirming that preprocessing was successfully performed.

### **2️⃣ PCA**
Since the dataset contained numerous features, we attempted to extract key features using PCA (Principal Component Analysis). Upon reviewing the explained variance of each principal component using pca.explained_variance_ratio_, we observed that the first principal component explained approximately 90% of the variance in both the train and test datasets. However, the F1 score improved as the number of principal components increased. Consequently, we set the number of principal components to 18 and trained the model on the PCA-applied dataset. This achieved an F1 score of 0.5048 on the test dataset, demonstrating satisfactory performance.

### **3️⃣ Stepwise Method**
To reduce the number of features by selecting only the most useful ones, we employed the stepwise feature selection method. Stepwise method is a combination of forward selection and backward elimination, adding or removing one feature recursively per each step. It considers all the cases of possible subsets of features and is able to find the best subset of features based on p-values, AIC, and BIC.

It uses p-value to determine which is the significant feature for the performance. P-value represents the probability that the coefficient is actually zero. It means that the lower p-value is, the more significant the feature is. Generally, a feature is considered as statistically meaningful if the p-value of a feature is lower than 0.05.

For example, feature ‘age’ is excluded in the optimal subset of features since it has higher p-value (0.866) than 0.05. These are the features that were selected by stepwise method, and we reconstructed train and test datasets consisting of only these features:
['default', 'nr.employed', 'poutcome', 'previous', 'month', 'euribor3m', 'emp.var.rate', 'contact', 'cons.price.idx', 'cons.conf.idx', 'day_of_week', 'education'].

The best performance 0.5054 was achieved using this approach.

## **✅ Which model did we use?**

우리 팀은 bayesian optimization 알고리즘으로 resampling과 assigning weights 두 가지 방법의 모델을 훈련시켰다. 결과적으로, resampling은 PCA를 이용했을 때 최고 성능 0.4315를 기록했고 assigning weights는 stepwise method를 이용했을 때 최고 성능 0.5054를 기록했다. 

### **0️⃣ Bayesian Optimziation**

Bayesian optimization이란 머신러닝 태스크에서 하이퍼파라미터를 연속적으로 탐색하여 최적의 하이퍼파라미터를 경험적으로 대입해보기나 gridsearch보다 더 빠르고 쉽게 찾을 수 있는 알고리즘이다. Bayesian optimization은 최대화하고자 하는 대상을 반환값으로 갖는 함수를 정의해준 뒤, BayesianOptimization의 객체를 생성하여 maximize 내장 함수를 이용해 그 반환값을 최대화하는 하이퍼파라미터와 최대의 반환값을 찾는다. Resampling과 assigning weights 모두 bayesian optimization 알고리즘을 이용해 최적화했다. 
 
### **1️⃣ Resampling**

Resampling의 아이디어는 불균형 데이터를 다루기 위해 SMOTE를 이용해 데이터를 oversampling했다. SMOTE는 소수의 클래스의 데이터를 이용해 그 클래스에 속하는 새로운 데이터를 합성함으로써 클래스 불균형 문제를 완화한다. SMOTE를 이용해 생성한 데이터로 모델을 학습시키는데, 이때 여러 번의 resampling을 통해 sampling으로 인해 모델이 학습할 때 기존 데이터의 특성을 잘 반영하지 못 할 수도 있다는 문제를 해결한다. random_state를 각각 다르게 설정함으로써 다르게 sampling된 데이터로 여러 번 모델을 학습시킬 수 있다. 어떤 데이터를 0으로 분류할 지 1로 분류할 지는 tot_prob 값을 이용한다. tot_prob는 여러 번 sampling한 데이터로 각각 모델을 훈련시켰을 때, 각 모델이 생성한 prob들의 평균이다. 정리하자면, resampling은 SMOTE를 이용해 소수 클래스의 데이터 수를 늘리고, 여러 번의 sampling을 통해 생성한 데이터로 여러 모델을 훈련시킨 결과의 평균을 이용함으로써 편향을 줄인 방법이다. 

그러나, 결과는 standardization과 stepwise method를 데이터셋으로 이용했을 때의 성능은 낮았다. 반면 내가 개인적으로 구축한 PCA 데이터셋에서는 아래와 같은 하이퍼파리미터 환경에서 0.4315를 기록했다. 
```
model = GradientBoostingClassifier(
    random_state=42,
    learning_rate=0.09894,
    max_depth=int(3.987),
    n_estimators=100,
    n_iter_no_change=10
)
```


