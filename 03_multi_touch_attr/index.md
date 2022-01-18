# [Algorithmic Marketing] Multi-touch Attribution


Multi-touch Attribution model에 대해 알아보자.

<!--more-->
온라인 광고에서는 여러 광고주나 채널을 통해 캠페인이 진행되는 경우가 많다. 그렇다면 conversion이 일어나기까지 funnel에서 각각 attribution이 있을 것이고 multi-touch attribution 모델은 이들의 영향을 측정하려고 하는 모델이다. conversion 직전의 touch만 기억하는 LT모델에 비해 유용하다고 할 수 있다. 다양한 방법이 있지만 그 중 paper하나를 요약해보았다.

## Data-driven Multi-touch Attribution Models (KDD 2011)
논문에서는 크게 크게 아래의 두 가지 방법을 제시한다.
- Bagged Logistic Regression
- Simple probabilistic model

저자들이 MTA에서 중요하게 생각하는 것은 low variability와 low misclassification이라고 할 수 있다. 또한, 결과에 대한 해석도 중요하다고 한다. 모델의 정확도와 해석이 중요한 것은 당연하다는 생각이 들었다. 그런데 low variability는 무슨 의미일까? 사실 이 또한 당연하지만 ad compaign이라는 도메인이기에 저자들이 좀 더 중요하게 다루는 것 같다. 각 contribution에 대해 stable한 estimatation이 되어야 이와 관련한 예산, 향후 campaign 계획 등 비즈니스에 핵심적인 내용이기 때문에 stability가 없으면 이를 믿기 힘들다.

### Bagged Logistic Regression
highly correlated covariates로 인해 생기는 estimation variability를 줄이기 위해 bagging을 이용한다. 또한 해석이 쉬운 Logistic Regression을 이용하는 것이다. 과정은 아래와 같다. 참고로 dataset에서 column은 각 channel이고 row는 각 유저들을 의미한다. 아래 실습파일을 보면 이해하기 쉽다.

- step1. data를 $p_s$만큼 sampling하고 covariate들을 $p_c$만큼 sampling해서 Logistic regression을 fit한다.
- step2. 위의 step1을 $M$번 반복하고 각 계수들의 평균을 구한다.

### Simple probabilistic model
rule-based 방법이라고 할 수 있다. 최종 contribution을 계산하는 과정에서는 channel간의 interaction을 고려한다.

- step1. 주어진 dataset을 이용하여 empirical probability를 계산한다:
    - $y$: conversion event (binary outcome)
    - $x_i,i=1,...,p$: p는 advertising channel
    - $N$: 괄호안에 해당하는 channel에 impression된 유저 수, $pos$가 conversion된 유저를 의미

$$P(y|x_i) = \frac{N_{pos}(x_i)}{N_{pos}(x_i) + N_{neg}(x_i)}$$

$$P(y|x_i,x_j) = \frac{N_{pos}(x_i, x_j)}{N_{pos}(x_i,x_j) + N_{neg}(x_i,x_j)}$$

- step2. 각 positive 유저의 channel $i$의 contribution:
    - $N_{j \neq i}$: channel $i$이외의 다른 channel의 수

$$C(x_i) = p(y|x_i) + \frac{1}{2N_{j \neq i}} \sum_{j \neq i} [p(y|x_i,x_j) - p(y|x_i) - p(y|x_j)]$$

### 궁금한 점
- imbalance가 있을텐데 왜 misclassification rate를 봤을까?
- 그렇다면 이게 잘됐는지 판단하기 위해서 믿을건 모델의 성능뿐?
- 각 유저의 funnel 순서는 고려하지 않아도 되는가?

## 실습
- [Bagged Logistic Regression](https://github.com/minsoo9506/algorithmic-marketing/blob/master/multi_touch_attribution/practice.ipynb)
