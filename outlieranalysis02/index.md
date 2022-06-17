# [OutlierAnalysis] Supervised Outlier Analysis


label이 있는 경우인 Supervised Outlier Analysis (Outlier Analysis, Charu C. Aggarwal)

<!--more-->
이상치를 찾을 때, label이 있다면 무조건 이를 이용해야한다. 실제로 모델의 성능이 비교할 수 없을 정도로 차이가 난다. 그렇다면 label이 있다면 classification을 위한 모델을 만드는 것인데 supervised outlier detection은 일반적인 classification과 어떤 차이가 있을까?

## supervised outlier detection vs classification
### Class imbalance
가장 큰 차이라고 할 수 있을 것이다. 데이터를 이루고 있는 class의 비율이 차이가 나는 것이다. imbalance가 커질수록 모델을 major class로 biased된다. rare한 class를 major class로 예측하는 경우가 많아질 것이다.

### Contaminated normal class 
이상치의 수가 워낙 적기 때문에 normal이라고 하지만 사실은 이상치이 경우가 존재할 수 있다. 또는 사실은 이상치이지만 새로운 형태의 이상치이기 때문에 normal이라고 잘못된 라벨링이 발생할 수도 있다.

### Partial training information
이상치의 수가 적어서 대부분 normal에 대한 정보만 많다. 그래서 거의 semi-supervised 같은 경우가 될 수도 있는 것이다.

## 방법론
### Cost-Sensitive
예를 들어서 positive label이 1%라고 하자. 그렇다면 모델이 모든 instance를 negative라고 예측하면 accuracy가 99%나 되는 좋은 모델이 만들어진다. 하지만 우리는 1%의 positive label을 잘 찾아내기를 원한다. 따라서 label을 동일한 weight가 아닌 중요한 정도에 따라 weighted된 loss function을 최적화하기를 원할 수 있다. 이러한 관점이 cost-sensitive 라고 할 수 있다. 대표적인 방법으로는 아래와 같은 방법들이 있다.
- Relabeling (MetaCost...)
  - model-agnostic한 방법으로 weight(cost)를 고려하여 확률값을 이용하여 label을 다시하여 학습하는 방법이다.
  - 다만 확률값에 따라서 결과가 천차만별이기에 조심해야 한다. 예를 들어, minor label을 major로 예측하는 경향을 가진 모델이 만들어지면 이미 부족한 label을 잃어버리는 결과가 발생할 수도 있다.
- Weighting model
  - model fitting 과정에서 weight를 주는 방법

### Adaptive Re-Sampling
책에서는 re-sampling 방법을 indirect form of cost-sensitive learning이라고 했다. weight를 주는 것은 결국 imbalanced한 데이터를 평등하게 맞추는 과정이므로 re-sampling(under, over sampling)도 그 의도와 결과가 비슷하다고 할 수 있다.이와 관련한 연구도 상당히 많다. 대표적인 방법론은 SMOTE가 있다.

### Ensemble
Ensemble 모델이 만들어지는 과정에서 cost-sensitive(or re-sampling)의 방법을 같이 접목시킨다. 예를 들어, Adaboost의 경우 예측이 틀린 데이터에 더 가중치를 둬서 모델이 학습하도록 한다. 이 때, minor한 label에 더 가중치를 줘서 학습을 하도록 하는 AdaCost같은 모델이 있다. 또한 모델링 과정에서 SMOTE방법론을 이용하는 SMOTEBoost도 이러한 방법론이다.

다양한 방법론들이 있지만 위 방법론들이 항상 효과적인 것은 아니다. 결국 데이터와 비즈니스상황에 따라 결과가 많이 달라진다. 하지만 방법론들에 대한 전반적인 이해를 갖고 있다면 문제를 해결해 나가는 과정이 좀 더 수월하지 않을까.
