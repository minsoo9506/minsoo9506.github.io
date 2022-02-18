# [인과추론] Counterfactual and Mediation


Counterfactual에 관한 기본적인 개념과 Mediation에 대해 알아보자.

<!--more-->

## Counterfactual
이미 이전에 경험했던 개념이다. causal inference가 어려운 이유는 counterfactual을 알 수 없기 때문이라고 배웠다.

- Counterfactual: 

$$P(Y(t)| T=t',Y=y')$$

parametric SCM으로 counterfactual을 파악하는 방법을 알아보자. 먼저 전반적인 과정은 아래와 같다.
- Given: observation $(T,Y)$, 여기서 $Y$는 $Y(T=t)$를 의미한다.
- Necessary: parametric model for the structural equation for $Y$
- Result: access to counterfactuals $Y(t')$ at the unit-level

이를 예시를 통해 이해해보자.
- $Y$: happy or unhappy (1, 0)
- $T$: get a dog or don't (1, 0)
- $U$: unobserved variable describing the individual (1 if dog person, 0 if anti-dog person)
- SCM: $Y:= UT + (1-U)(1-T)$

이런 상황에서 observation이 $T=0,Y=0$이 있다면 $Y(1)$을 알고 싶은 것이다. 
- 가장 먼저 $U$에 대해 식을 푼다. 위의 observation을 이용하면 $U=1$이라는 것을 알 수 있다.
- 그렇다면 (individualized) SCM은 $Y=T$라는 것을 알 수 있다.
- 따라서 우리가 궁금했던 counterfactual은 $Y(1)=1$이다.

structual equation이 있다고 해도 항상 counterfactual을 구할 수 있는 것은 아니다. 위의 예시에서 structural equation이 아래와 같은 경우를 생각해보자.
- $Y:=$
    - 1, $U$ = always happy (P=0.3)
    - 0, $U$ = never happy (P=0.2)
    - $T$, $U$ = dog-needer (P=0.4)
    - $1-T$, $U$ = dog-hater (P=0.1)

이런 경우에는 observation $T=0,Y=0$이 있다면 $U$는 never happy거나 dog-needer 두 가지 모두에 해당한다. 따라서 이의 counterfauctual은 0, 1 둘 중 무엇일지 모른다. 하지만 확률적으로 접근해본다면?

$$P(U=never happy | T=0, Y=0)=\frac{0.2}{0.2+0.4}=\frac{1}{3}$$
$$P(U=dog-hater | T=0, Y=0)=\frac{0.4}{0.2+0.4}=\frac{2}{3}$$

따라서 $P(Y(1)=1)=\frac{2}{3}$ 같이 확률적으로만 접근할 수 있다.

## Mediation
