# [인과추론] Instrumental Variables


unobserved confounding이 있는 경우는 어떻게 할까? 이전 chapter에서도 알아봤지만 여기서는 *instrumental vairable* $Z$을 이용하고자 한다.

<!--more-->

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_08_01.PNG?raw=true"  width="300">
</center>

$Z$는 3가지 특징이 있다.
- (Relevance) treatment $T$에 영향을 준다
- (Exclusion Restriction) $T$를 통해서만 $Y$에 영향을 준다
- (Instrumental Unconfoundedness) $Y$에 대한 $Z$의 영향은 unconfounded하다.

또한 $Z$를 사용하여 causal effect를 identify하기 위해서는 parametric form을 가정해야 한다. Unobserved confounder $U$가 있다고 가정하기 때문에 (backdoor path cannot be blocked) nonparametric identification를 할 수 없다.

## Linear Setting
일단 outcome에 대해 Linear assumption을 해보자.

$$Y := \delta T + \alpha_u U$$

$$E[Y|Z=1] - E[Y|Z=0] \\\ = E[\delta T + \alpha_u U | Z=1] - E[\delta T + \alpha_u U | Z = 0] \\\ = \delta (E[T|Z=1]-E[T|Z=0]) + \alpha_u (E[U|Z=1]-E[U|Z=0]) \\; \text{Exclusion Restriction} \\\ = \delta (E[T|Z=1]-E[T|Z=0]) + \alpha_u (E[U] - E[U])\\;\text{instrumental unconfoundedness} \\\ =\delta(E[T|Z=1] - E[T|Z=0])$$

$$\therefore \delta = \frac{E[Y|Z=1] - E[Y|Z=0]}{E[T|Z=1] - E[T|Z=0]}$$

위의 분모는 Relevance assumption으로 0이 아니라고 가정한다. $\delta$를 나타내는 값을 Wald estimand라고 하고 이에 대한 estimator는

$$\hat{\delta} = \frac{\frac{1}{n_1}\sum_{i:z_i = 1}Y_i - \frac{1}{n_0}\sum_{i:z_i = 0}Y_i}{\frac{1}{n_1}\sum_{i:z_i = 1}T_i - \frac{1}{n_0}\sum_{i:z_i = 0}T_i}$$

이번에는 $T,Z$가 continuous인 경우를 살펴보자. outcome에 대한 Linear assumption은 동일하다.

$$Cov(Y,Z) = E[YZ] - E[Y]E[Z] \\\ = E[(\delta T + \alpha_u U)Z] - E[\delta T + \alpha_u U]E[Z] \\\ = \delta E[TZ] + \alpha_u E[UZ] - \delta E[T]E[Z] - \alpha_u E[U]E[Z] \\\ = \delta (E[TZ] - E[T]E[Z]) + \alpha_u (E[UZ] - E[U]E[Z]) \\\ = \delta Cov(T,Z) + \alpha_u Cov(U,Z) \\\ = \delta Cov(T,Z)$$

$$\therefore \delta = \frac{Cov(Y,Z)}{Cov(T,Z)}$$

$$\hat{\delta} = \frac{\hat{Cov(Y,Z)}}{\hat{Cov(T,Z)}}$$

여기 추가적으로 Two-Stage Least Squares Estimator를 알아보자.
- 1. Linearly regress $T$ on $Z$ to estimate $E[T\|Z]$
- 2. Linearly regress $Y$ on $\hat{T}$ to estimate $E[Y\|\hat{T}]$
- 3. Obtain out estimate $\hat{\delta}$ as the fitted coefficient in front of $\hat{T}$

## Nonparametric Identification of Local ATE
근데 linear assumption말고 nonparametric하게 할 수 있을까? Yes! Binary의 경우를 예시로 이해해보자.

$Z=1$인 경우, treatment가 일어나도록 한다면 이를 $T(1)=T(Z=1)$로 표시할 수 있다. 반대의 경우도 당연히 된다. 물론 항상 $Z=1$이 $T(1)=1$을 만드는 것은 아니다. 이 때 총 4가지 경우의 수를 생각할 수 있다.

- Principal Strata
  - Compliers : $T(1)=1,T(0)=0$
  - Defiers : $T(1)=0,T(0)=1$
  - Always-takers : $T(1)=1,T(0)=1$
  - Never-takers : $T(1)=0,T(0)=0$

그런데 우리는 $Z$를 사용할 때, 이 4가지 경우에서 어떤 경우에 해당하는지 알 수도 있다. Observational data이라 potential outcome이 없기에 identification을 할 수 없다는 것이다. 이를 위해서는 추가적인 가정이 필요할 것으로 보인다. 아래에서 알아보자.

### Local ATE
unobserved confounder때문에 ATE를 nonparametrically identity하기는 어렵지만 대신 *local* ATE를 구할 수 있다.

- Local Average Treatment Effect (LATE) / Complier ATE (CATE)

$$E[Y(T=1) - Y(T=0) | T(Z=1)=1, T(Z=0)=0]$$

LATE를 identify하기 위해서는 linearity assumption이 아닌 monotonicity assumption이 필요하다.

- Monotonicity

$$\forall i, T_i (Z=1) \ge T_i (Z=0)$$

Monotonicity가 의미하는 것은 $Z=1$이 treatment가 발생하도록 encourage하다고 이해 할 수 있다. 그렇게 되면 defiers는 사라지게 된다. 이제 LATE nonparametric identification을 알아보자.

- LATE nonparametric identification

$$E[Y(1) - Y(0) | T(1)=1, T(0)=0] = \frac{E[Y|Z=1] - E[Y|Z=0]}{E[T|Z=1] - E[T|Z=0]}$$

- proof)

$$E[Y(Z=1) - Y(Z=0)] = \\\ E[Y(Z=1)-Y(Z=0)|T(1)=1,T(0)=0]P(T(1)=1,T(0)=0) \\\ +E[Y(Z=1)-Y(Z=0)|T(1)=0,T(0)=1]P(T(1)=0,T(0)=1) \\\ + E[Y(Z=1)-Y(Z=0)|T(1)=1,T(0)=1]P(T(1)=1,T(0)=1) \\\ +E[Y(Z=1)-Y(Z=0)|T(1)=0,T(0)=0]P(T(1)=0,T(0)=0)$$

여기서 defier가 일어날 확률이 0이므로 없어진다. always, never-takers 또한 $E$부분이 0이 된다.

$$\therefore E[Y(Z=1) - Y(Z=0)] \\\ = E[Y(Z=1)-Y(Z=0)|T(1)=1,T(0)=0]P(T(1)=1,T(0)=0)$$

이제 이를 정리하면

$$E[Y(Z=1)-Y(Z=0)|T(1)=1,T(0)=0] \\\ = \frac{E[Y(Z=1) - Y(Z=0)]}{P(T(1)=1,T(0)=0)}$$

위의 식에서 분자는 instrumental unconfoundedness assumption으로 인해 $E[Y\|Z=1] - E[Y\|Z=0]$이 된다. 분모는 compliers의 확률이므로 1에서 compliers가 아닐 확률을 빼서 진행하자. 이때 monotonicity로 인해 defier의 확률은 0이고 always, never-takers의 확률은 각각 $P(T=1\|Z=0),P(T=0\|Z=1)$이 된다.

$$P(T(1)=1,T(0)=0) \\\ = 1 - P(T=0|Z=1) -  P(T=1|Z=0) \\\ = 1 - (1- P(T=1|Z=1)) - P(T=1|Z=0) \\\ = P(T=1|Z=1)-P(T=1|Z=0) \\\ = E[T|Z=1] - E[T|Z=0]$$

linearity assumption에서 구한 결과와 같다. 두 가지 가정 모두 우리에게 Wald estimand를 구할 수 있게 해주었다. Monotonicity의 경우, 이는 ATE는 아니다.  nonparametric하게 ATE는 못구해도 monotonicity가정으로 LATE는 구할 수 있음을 알게 되었다. 다만 monotonicity가 만족한다는 보장은 없는 한계도 존재한다.

지금까지보다 조금 더 general setting도 있다. 조금 더 복잡한 function을 이용하는 것이다.

$$Y:=f(T,W)+U$$

최신 논문에서 $f$를 deep learning으로 구한 경우도 있다.

### Reference
- https://www.youtube.com/watch?v=B0SRWteGoOw&t=1236s
