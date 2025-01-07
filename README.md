# imbalanced-classification-task

본 레포지토리는 주어진 imbalanced data을 이용해 client가 bank term deposit를 subscribe할 지를 예측하는 binary classification 태스크에 대한 코드이다. 주어진 데이터의 클래스는 no(29238), yes(3712)개로 매우 불균형하므로 모델의 성능 지표인 f1 score가 0.5만 나와도 매우 준수한 성능을 가졌다고 볼 수 있다. 우리 팀은 Data preprocessing, feature selection, bayesian optimization을 이용해 imbalanced data를 다루는 여러 가지 방법들을 적용해 **최종 성능 0.5054**를 달성했다. 

1. Nan 값을 어떻게 채우는가?
- 각 특징을 시각화했을 때, 특징 내에서 데이터의 분포가 심하게 편향되어 있는 몇 가지 특징을 발견했다. 예를 들어, job 열의 데이터 분포를 보면 'admin'과 'blue-collar'에 데이터가 치중되어 있는 것을 확인할 수 있다. 따라서 job 열의 nan값을 대체할 값들의 비율을 존재하는 분포에 비례하도록 정했다. job 열의 nan 값은 총 73개이고 73을 원래 있던 직업들의 개수에 비례하도록 할당한다. 아래 딕셔너리를 보면, 채워야 할 nan 값들 중 18개는 'admin', 2개는 'enterpreneur', 1개는 'unemployed'...이런 식으로 nan을 채워나가는 것이다.
- {'admin.': '18.25', 'entrepreneur': '2.19', 'unemployed': '1.46', 'services': '7.30', 'blue-collar': '16.06', 'housemaid': '1.46', 'self-employed': '2.19', 'retired': '2.92', 'technician': '12.41', 'management': '5.11', 'student': '1.46', nan: '0.00'}
- ![image](https://github.com/user-attachments/assets/864eee47-558a-450a-9efb-bfd742f5dfaa)





