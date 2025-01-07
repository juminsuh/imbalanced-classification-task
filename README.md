# imbalanced-classification-task

본 레포지토리는 주어진 imbalanced data을 이용해 client가 bank term deposit를 subscribe할 지를 예측하는 binary classification 태스크에 대한 코드이다. 주어진 데이터의 클래스는 no(29238), yes(3712)개로 매우 불균형하므로 모델의 성능 지표인 f1 score가 0.5만 나와도 매우 준수한 성능을 가졌다고 볼 수 있다. 우리 팀은 Data preprocessing, feature selection, bayesian optimization을 이용해 imbalanced data를 다루는 여러 가지 방법들을 적용해 **최종 성능 0.5054**를 달성했다. 

1. Nan 값을 어떻게 채우는가?
- 각 특징을 시각화했을 때, 특징 내에서 데이터의 분포가 심하게 편향되어 있는 몇 가지 특징을 발견했다. 예를 들어, 'job' 특징의 데이터 분포를 보면
-
- ![image](https://github.com/user-attachments/assets/4f99fbac-bddb-419c-9448-b4d762e3551a)




