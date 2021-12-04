# [인과추론] Introduction


Causal Inference시 주의할 점

<!--more-->

## Simpson's paradox
Simpson's paradox는 주로 observational data에 주로 발생하는 경우가 많다. 환자들에게 약(Treatment)을 통해 효과가 어떤지 확인하는 상황을 가정해보자. 아래의 표는 두 가지 약을 처방받은 사람들의 사망률이다.

||Total|
|------|---|
|A|16% (240/1500)|
|B|19%(105/550)|

위의 표에 따른 수치에 따르면 A가 더 좋다. 정말 그럴까? 사망한 환자의 아픈정도(Condition)에 따라 나눠서 생각해보자.

||Mild|Severe|Total|
|------|---|---|---|
|A|15% (210/1400)|30% (30/100)|16% (240/1500)|
|B|10% (5/50)|20% (100/500)|19% (105/550)|

전체적으로는 A약의 사망률이 더 낮은데 상태별로 나눠서 보니 B약의 사망률이 두 가지 경우 모두 낮다. 그렇다면 어떤 약을 사용해야할까?

이에 대해서는 두 가지의 시나리오가 있다.

- 먼저 아픈정도(Condition)가 어떤 약(Treatment)을 투여하는지와 사망률(Outcome) 모두에게 영향을 주는 경우 : 이런 경우는 B 선호
  - 예를 들어, 의사가 상태가 Mild한 환자에게는 A약을 투여하고 상태가 Severe한 환자에게는 B약을 투여한 상황을 생각해보자. 이미 Severe한 환자는 사망할 확률이 높았고 따라서 A약의 사망률이 더 낮게 나온 것이다. (*Condition confounds the effect of treatment on morality*) 이 예시에서처럼 treatment와 outcome 모두에 영향을 미치는 변수를 **confounder** 라고 부른다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_01_02.PNG?raw=true"  width="200">
</center>


- 약(Treatment)이 아픈정도(Condition)와 사망률(Outcome) 모두에게 영향을 주는 경우 : 이런 경우는 A 선호
  - 예를 들어, B약은 약의 특징상 2번 맞아야하는데 그 사이의 시간이 꽤 긴 편이다. 따라서 B약을 처방받은 사람은 그 사이의 시간동안 아픈상태가 Severe해지는 경우가 발생할 수 있다. 그래서 B약을 맞은 사람의 전체사망률이 높아질수 있다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_01_02.PNG?raw=true"  width="200">
</center>

이처럼 어떤 인과모형을 갖느냐에 따라 결론이 바뀌게 된다. 따라서 우리는 인과관계를 통해 decision-making을 할 때, 적절한 과정을 거쳐야하는 것이다.

## Correlation does not imply causation
'신발을 신고 자는 것'과 '일어나서 두통이 있는 것'이 상관관계가 높다. 그러면 신발을 신고자면 다음날 일어나서 두통이 있을 것이라고 말할 수 있을까? 즉, 인과관계가 있다고 할 수 있을까? 그럴 수 없다.

common cause가 있다면? 술을 마신 경우를 생각해보자. 취해서 신발을 신고 자게된다. 숙취 때문에 다음날 일어나서 두통이 있을 확률이 높다. 즉 total association이 confounding association과 causal association으로 이루어진 것이다. (*correlation is technically only a measure of linear statistical dependence. We will largely be using the term 'association' to refer to statistical dependence from now on*)

이처럼 우리는 data를 보고 인과관계를 함부로 판단해서는 안된다. 추후에는 인과관계를 파악하기 위한 방법론들을 알아보자.

### Reference
- https://www.youtube.com/watch?v=DXBPtpBhGqo&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=2
