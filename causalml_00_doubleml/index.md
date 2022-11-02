# [Causality] Double Machine Learning


Double Machine Learning에 대해 알아보자.

<!--more-->

관련한 논문 *Double machine learning for treatment and causal parameters*에서는 수학적인 증명이 많이 나온다 이 부분은 대부분 생략하고 주요 아이디어만 정리하고자 한다.

ML은 prediction에서는 좋은 성능을 보이지만 causal parameter에 대한 estimation or inference에서는 그렇지 못하다. 또한 double(orthogonalized) ML과 sample splitting을 통해서 causal parameter(treatment effect parameter)를 잘 추정할 수 있고 interval도 구할 수 있다.

## double ML

먼저 기본적인 notation을 정리하자. 일반적인 causal inference를 하려는 상황은 아래와 같다. outcome은 treatment와 confounder의 식으로 설명되고 동시에 confounder는 treatment에게도 영향을 주기에 다른 식으로 설명할 수 있다.

$$Y=D\theta_0 + g_0(Z) + U,\;\;E[U|Z,D]=0$$
$$D=m_0(Z)+V,\;\;E[V|Z]=0$$

- $Y$: outcome variable
- $D$: policy/treatment variable
- $\theta_0$: target parameter of interest
- $Z$: high-dimensional vector of other covariates (called confounders or controls)

이러한 상황에서 Double ML 방법론은 아래와 같이 진행한다. 먼저 위에서 $D$를 $Y$의 식에 넣으면

$$Y=(m_0(Z)+V)\theta_0 + g_0 (Z) + U \\ = m_0 (Z)\theta_0 + g_0 (Z) + V \theta_0 + U$$

이와 같이 정리를 할 수 있고 이를 새로운 notation으로 작성해서 아래와 같이 진행한다.

$$Y - (m_0 (Z)\theta_0 + g_0 (Z) ) = V \theta_0 + U$$

$$W=V \theta_0 + U$$

- $V=D-m_0(Z)$, $W=Y-l_0 (Z)$
- $l_0(Z) = E[Y|Z] = m_0(Z)\theta_0 + g_0(Z)$

1. $Z$를 이용하여 $Y,D$ 각각을 예측한다. (이는 ML 모델을 이용)
   $$\hat{E[Y|Z]}, \hat{E[D|Z]}$$
2. 이를 이용하여 residual을 구한다.
   $$\hat{W} = Y - \hat{E[Y|Z]},\;\; \hat{V}=D-\hat{E[D|Z]}$$
3. $\hat{W}$를 $\hat{V}$으로 회귀식을 구하여 $\hat{\theta_0}$를 구한다.

## Reference

- [Double machine learning for treatment and causal parameters, 2016](https://www.econstor.eu/bitstream/10419/149795/1/869216953.pdf)
- https://www.youtube.com/watch?v=eHOjmyoPCFU
- https://github.com/microsoft/EconML/blob/main/notebooks/Double%20Machine%20Learning%20Examples.ipynb

