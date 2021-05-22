---
layout: post
mathjax : true
date: 2021-05-22
title: "[TS] 허위상관(Spurious Correlation)"
---

허위상관(Spurious Correlation) 이란?

## correlation을 믿어도 될까?
*A Primer on Spurious Statistical Significance in Time Series Regression* 이라는 article을 읽고 간략하게 생각을 정리하고자 한다. 전통적인 분석 또는 예측 모델인 linear regression에서 우리는 $R^2$가 높고 계수들이 통계적으로 유의미하다면 기쁜 마음으로 해당하는 모델을 사용하고자 한다.

하지만 이 때 주의해야할 점이 있다. 상관관계가 높다고 해서 그대로 믿지말자! 유명한 예시로 여름철 아이스크림 판매량과 익사하는 사람의 수가 높은 상관관계를 가지는 예시가 있다.

일반적으로 시계열 데이터는 (stochastic) trend가 있는 경우가 자주 있다. 이 때 두 변수가 (stochastic) trend가 있으면 독립적으로 만들었더라고 통계적으로 유의미한 경우가 많이 생긴다. 실제로 저자는 독립적인 random walk들을 만들었는데 통계적으로 유의미한 상관관계를 가지는 경우가 75%나 됐다고 한다. 즉, trend가 있는 데이터를 다룰 때는 특히 조심해야하는 것이다. 해당 article에서는 이를 *trend, deterministic or stochastic, can do the 'trick'* 으로 표현하였다.

그러면 어떻게 해당 문제를 해결할까? 주로 detrend하는 과정을 거친다! 물론 detrend가 완벽한 것은 아니다.

## Cointegration
- stochastic trends가 있는 두 변수 사이의 true relationship이 있는 것을 말한다.
- 두 변수가 cointegrated되어 있다면, detrend하지 않고 correlation 이나 regression을 통해 분석해도 되고 오히려 더 좋다고 한다.
  - 노벨상을 받은 경제학자들이 수학적으로 증명
  - 이유는 nonstationary variable이 더 정보가 많기 때문이다.
- 그러면 cointegration인지 어떻게 판별할까?
  - 학자들이 만든 statistical test들이 있다고 한다.
  - 현실에서는 test를 사용할 필요까지는 없을 것 같다.

### Reference
- https://www.bateswhite.com/media/publication/99_Deng,%20ABA%20newsletter%202015.pdf