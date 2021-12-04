# [인과추론] Potential Outcomes Framework


Potential Outcomes 이란? 

<!--more-->

## notation
- $T$ : observed treatment
- $Y$ : observed outcome
- $i$ : to denote a specific unit/individual
- $Y_i (1)$ : potential outcome under treatment
- $Y_i (0)$ : potential outcome under no treatment
- (individual) Causal effect : $Y_i (1) - Y_i (0)$

## The fundamental problem of causal inference
- (individual) Causal effect : $Y_i (1) - Y_i (0)$

위의 식에서 causal effect를 알 수가 없다. 예를 들어서,
- 약을 먹으면 병이 낫는지 알고 싶다. 즉, causal effect를 알고 싶다.
- 그런데 약을 먹었을 때의 결과 $Y(1)$와 먹지 않았을 때의 결과 $Y(0)$를 동시에 알 수 없기 때문에 문제가 발생한다.
- 결국에는 모든 개체 $i$들의 outcome은 둘 중 하나의 potential outcome만을 얻을 수 밖에 없는 것이다. (이 때 반대의 예상되는 결과는 counterfactual outcome이라고 함)

이것이 우리가 마주하게 되는 문제이다. 그렇다면 이를 어떻게 해결해야 할까?

## Average treatment effect (ATE)
위에서 처럼 모든 개체들의 두 가지 outcome을 얻을 수 없다면 평균이라는 강력한 무기를 이용할 수 있다.

- Average treatment effect (ATE) : 

$$E[Y_i (1) - Y_i (0)] = E[Y(1) - Y(0)] = E[Y(1)] - E[Y(0)]$$

- Associational difference :

$$E[Y|T=1] - E[Y|T=0]$$

그렇다면 ATE와 associational difference가 같도록 하는 가정은 무엇일까. 하나씩 알아보자.

## Ignorability (exchangeability)
- Ignorability : 

$$(Y(1),Y(0)) \perp T$$

또 다른 시점으로는 (계산, 수학적으로 같은 의미)
- exchangeability :
  - 두 그룹을 바꿔서 treatment나 not treatment를 해도 똑같은 결과를 나오는 가정

$$E[Y(1)|T=1] = E[Y(1)|T=0] \rightarrow = E[Y(1)] \\\ E[Y(0)|T=1] = E[Y(0)|T=0] \rightarrow = E[Y(0)]$$

결국 treatment, non treatment 두 그룹이 comparable해야한다는 것이다. 위의 가정을 만족한다면 우리는 아래와 같은 식을 얻을 수 있다.

$$E[Y(1)] - E[Y(0)] = E[Y(1)|T=1] - E[Y(0)|T=0]$$

위의 식을 보면 causal notation에서 점점 statistical notation으로 변화하는 느
낌을 받을 수 있다. data를 통해 결국 측정하는 것은 statistical inference의 영역인 것이다. 여기서 identifiability라는 개념이 나온다.

- identifiability
  - 만약에 causal quantity (ex, $E[Y(t)]$)를 statistical quantity(ex, $E[Y\|t]$)로 계산할 수 있다면 우리는 이 causal quantity가 identifiable하다고 한다.

위의 ignorability(exchangeability)라는 중요한 가정을 배웠다. 그런데 이를 실제로 만족하도록 하기 위해서는 어떻게 해야할까? Randomized control trial (RCT)이 필요하다. 동전을 던져서 그룹을 나누는 것이다. 즉, 그룹이 나누어질 때 confounder를 없어지고 두 그룹이 동일한 특징(comparable)을 가진다는 것이다.

## Conditional exchangeability
하지만 obserbational data에서는 ignorability가정을 만족하기가 어렵다. 이유는 당연히 RCT를 하지 못한 상태이기 때문에 confounder가 있을 수 밖에 없다. 따라서 이를 해결하기 위해 conditional exchangeability가정을 주로 이용한다. covariate $X$들을 conditioning함으로서 subgroup들이 exchangeable하게 만드는 것이다. confounder들을 제어함으로서 RCT처럼 동일한(comparable) 두 집단을 만들어가는 과정이라고 볼 수 있다. 물론 모든 confounder를 파악할 수 없기에 RCT와 동일하다고 할 수는 없다.

- Conditional Exchangeability (Unconfoundedness):

$$(Y(1),Y(0)) \perp Y |X$$

graphical하게 표현하면 아래와 같다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_02_01.PNG?raw=true"  width="200">
</center>

$$E[Y(1) - Y(0) | X] = E[Y(1)|X] - E[Y(0)|X] $$
$$ = E[Y(1)|T=1,X]-E[Y(0)|T=0,X]$$

marginal effect는 

$$E[Y(1) - Y(0)] = E_X E[Y(1) - Y(0)|X]$$

## Positivity/Overlap and Extrapolation
위의 unconfoundedness도 중요한 가정이지만 추가적으로 고려해야하는 가정도 존재한다.

- Positivity (Overlap, Extrapolation) :
  - 모든 covariates $x$에 대해

$$0 < P(T=1|X=x) < 1$$

이 가정이 필요한 이유를 먼저 수학적으로 살펴보자.

$$E[Y(1) - Y(0)] = E_X [E[Y|T=1, X] - E[Y|T=0, X]]$$

$$\sum_x P(X=x)(\sum_y y P(Y=y|T=1,X=x) - \sum_y y P(Y=y|T=0,X=x)) $$

$$ =\sum_x P(X=x)(\sum_y y \frac{P(Y=y, T=1,X=x)}{P(T=1 | X=x)P(X=x)} - \sum_y y \frac{P(Y=y,T=0,X=x)}{P(T=0 | X=x)P(X=x)})$$

위 식에서 분모에 $P(T=1\|X=x)+P(T=0\|X=x)=1$이다. 따라서 $P(T=1\|X=x)$이 0 또는 1이면 문제가 생긴다.

이에 대해 생각해보면 당연한 결과이다. 특정 covariate값에서 관찰할 수 있는 것이 treatment 또는 non- treatment의 결과만 존재한다면 이는 causal effect를 제대로 측정하지 못하게 되는 것을 의미한다.

## No interference, Consistency
마지막으로 추가적인 가정들을 알아보자.

- No Interference
  - 내 outcome이 다른 이들의 treatment로 영향을 받지 않아야한다

$$Y_i (t_1, t_2,...t_i,..., t_n) = Y_i (t_i)$$

- Consistency
  - 우리가 관측하는 $Y$의 값이 관측된 treatment $T$의  potential outcome이여야 한다.

$$T=t \rightarrow Y = Y(t) \\\ Y = Y(T)$$

Consistency에 대해 예를 들어보자. 
- 강아지와 함께 살면 행복지수가 높아질 것이다.
- 위와 같은 causal effect를 구하고자 한다. 의도한 것은 강아지의 어떤 친밀함과 같은 교류 덕에 행복지수가 높아지는 효과(potential outcome)일 것이다. 그런데 나이가 많은 강아지가 treatment가 되었다면 우리가 의도한대로 결과가 나오지 않을 수도 있다.
- 이처럼 treatment specification으로 잡히지 않은 요인들 때문에 consistency가 중요한 것이다. (*no muliple version of treatment*)

consistency 가정이 성립한다면 

$$E[Y(1) - Y(0) | X] = E[Y(1)|X] - E[Y(0)|X] \\\ = E[Y(1)|T=1,X]-E[Y(0)|T=0,X] \\\ =E[T|T=1,X] - E[Y|T=0,X]$$

지금까지의 가정들을 총집합해보면
- Unconfoundedness
- Positivity
- No interference
- Consistency

$$E[Y(1) - Y(0)] = E[Y(1)] - E[Y(0)]\\;\text{(linearity of expectation)} \\\ =E_X [E[Y(1)|X] - E[Y(0)|X]] \\;\text{(law of iterated expectations)} \\\ =E_X [E[Y(1)|T=1,X] - E[Y(0)|T=0,X]] \\;\text{(unconfoundedness, positivity)} \\\ =E_X [E[Y|T=1,X] - E[Y|T=0,X]] \\; \text{(consistency)}$$

최종적으로 우리는 통계적인 추론을 통해 (최종식이 모두 확률변수, 평균 등으로 이루어진 식) 우리가 궁금해했던 ATE를 구할 수 있게 된다.

### Reference
- https://www.youtube.com/watch?v=5x_pPemAVxs&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=2
