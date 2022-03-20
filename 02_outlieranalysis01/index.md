# [OutlierAnalysis] Model Combination for Outlier Ensembles


다양한 모델을 통해 구한 outlier score를 ensemble하는 방법 (Outlier Analysis, Charu C. Aggarwal)

<!--more-->
## Introduction
다양한 detector 모델들을 통해 구한 outlier score를 최종적으로 ensemble할 때 (detector들의 score들이 스케일이 다르면 normalize해야 한다), 다양한 방법들이 있다. 이 책에서는 크게 3가지를 설명한다.
- 최종 score ensemble 방법
  - Averaging
  - Median
  - Maximum

이제 이들의 특징과 장단점에 대해 알아보자. 이전의 연구들이 [1,2,3] 상황에 따라서 좋은 성능이 나오는 방법들이 다름을 밝혔다. 따라서 우리는 최종 score를 ensemble할 때, 다양한 방법을 시도하고 가장 성능이 좋은 방법을 선택해야할 것이다. Averaging, median 방법은 상대적으로 stable해서 가장 많이 사용하는 방법이다. bias-variance trade-off 관계에서 이 둘은 variance를 줄이는데 효과가 있는 방법이다. (주의! outlier analysis에서 bias-variance는 classification에서와 완전히 동일하지는 않다, 이에 관해서는 추후에 다뤄보고자 한다) 이에 반해 maximum 방법은 stablility가 떨어지고 bias를 줄이는 것과 관련이 있다. maximum 방법을 통해 irrelevent하거나 weak한 detector들을 de-emphasize할 수 있다. 그런데 maximum 방법을 이용하면 score를 너무 overestimate하는 것 아닌가? 라는 의문이 들 수 있다. 하지만 일단 outlier score는 상대적인 것이기 때문에 괜찮다. 오히려 detector들이 찾기 어려워하는 outlier를 잘 찾을 수 있는 가능성이 존재한다. 찾기 어려워하는 outlier의 score는 inlier에 비해 상대적으로 underestimate될 것이기 때문이다. 물론 maximum의 가장 큰 문제는 variance가 증가할 수도 있다는 것이다. 특히 dataset의 크기가 작으면 그 확률이 올라간다.

- 결론: 상황마다 다르기 때문에 다 해보고 선택해야 한다

## Combining Bias and Variance Reduction
그렇다면 bias와 variance 모두를 고려하는 방법도 당연히 있을 것이다. 다양한 방법이 있겠지만 AOM 방법 [1] 에 대해서 알아보고자 한다. 간단하다. 

### AOM(Average-of-Maximum) method
- 1. $m$개의 components가 있다고 한다면 이들을 각 $m/q$개의 buckets으로 나눈다. (각 bucket에는 $q$개의 components)
- 2. 각 bucket 마다 maximum을 취한다.
- 3. bucket들의 maximum score를 average한다.

그 반대도 가능할 것이다. MOA!

## Related Paper
- 1. Theoretical Foundations and Algorithms for Outlier Ensembles (2015)
- 2. Outlier Ensembles: An Introduction (2017)
- 3. Feature Bagging for Outlier Detection (2005)
