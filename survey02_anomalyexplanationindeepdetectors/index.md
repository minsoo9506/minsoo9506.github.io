# [Survey] Anomaly Explanation in Deep Detectors (KDD 21 tutorial)


Anomaly Detection에서 설명가능한 모델과 관련한 tutorial

<!--more-->
Anomaly Detection과 관련한 분야 중 하나로 anomaly explanation이 있다. 해당 데이터가 왜 anomaly로 판단되는지 설명할 수 있다면 상당히 유용할 것이다. 이러한 방법론들은 어떤게 있는지 알아보자. 먼저 해당 tutorial에서는 크게 2가지로 나누어서 설명한다.
- outlying aspect mining
- unified anomaly detection and explanation

## Outlying aspect mining
해당 방법론은 detector가 anomaly를 찾았을 때, 다른 normal data와 다른 부분(the most unusal aspects)을 찾으려고 하는 방법이다. 크게 아래같은 절차로 진행된다.
1. anomaly detector를 통해 anomaly를 찾는다.
2. anomaly와 관련한 the most outlying feature subspace를 찾는다.

따라서 단점은 detection과 explanation 단계가 서로 독립적이라는 것이다. 이제 접근방법을 2가지 정도 알아보자.

### Finding relevant features for imbalanced binary classificaion
outlying aspects mining을 imbalanced binary classification에서 feature selection/weighting 하는 것으로 생각하는 것이다.
- classification accuracy as outlierness
  - Explaining outliers by subspace separability (2013)
  - subset feature를 통해 분류를 하고 classification accuracy가 가장 높은 subset을 찾는다.
- Attention-guided feature weighting
  - Beyond Outlier Detection: Outlier Interpretation by Attention-Guided Triplet Deviation Network 2021)
- 장단점
  - 일반적인 imbalanced binary classification에서 사용하는 방법론들을 사용할 수 있다
  - efficient, scalable
  - Difficult to generate multiple complementary outlying feature subspaces
  - Dependent on the quality of outlier oversampling and/or inlier downsampling
- other similar methods
  - Outlier detection in axis-parallel subspaces of high dimensional data (2009)
  - Discriminative features for identifying and interpreting outliers (2014)
  - Learning homophily couplings from non-IID data for joint feature selection and noise-resilient outlier detection (2017)
  
### Score-and-search approach
- 방법
    1. Define a measure of the outlyingness degree for a data point in any specified subspace
    2. The outlyingness degree of the query object will be compared across all possible subspaces
    3. The subspaces with the highest outlyingness degree will be selected for user further inspection

다양한 모델과 방법론을 이용해서 outlierness degree가 가장 큰 값을 갖는 feature subspaces를 찾는다. 가능한 전체 경우의 수는 $2^d - 1$일 것이다.

- 2-D pictorial explanation
  - LP-Explain: Local Pictorial Explanation for Outliers (2020)
  - a set of detected anomalies의 outlierness degree가 가장 큰 2-D feature subspaces를 찾는 방법
- 장단점
  - Many anomaly detection measures can be adapted for the scoring in feature subspace
  - Multiple relevant feature subspaces can be easily returned
  - dimensionality와 관련이 있는 measure들은 dimensionality bias를 고려해야 한다
  - feature subspace search를 하는 cost
- other similar methods
  - Outlying property detection with numerical attributes (2017)
  - Contextual outlier interpretation (2018)
  - Sequential feature explanations for anomaly detection (2019)

## Unified anomaly detection and explanation
detection과 explanation이 독립적으로 이루어지는 것이 아니라 detection을 하는 과정에 녹아들어 있다고 할 수 있다.

### shallow method
- Sequential anomaly ensemble of surrogate sparse regression
  - Sparse modeling-based sequential ensemble learning for effective outlier detection in high-dimensional numeric data (2018)

### Deep method
