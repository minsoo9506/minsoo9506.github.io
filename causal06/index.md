# [인과추론] Estimation


causality를 estimate하는 방법을 알아보자.

<!--more-->

## Conditional Outcome Modeling (COM)

$$\tau = E[Y(1) - Y(0)]=E_W [E[Y|T=1,W]-E[Y|T=0,W]]$$

좌항은 causal estimand이고 우항은 statistical estimand이다. 이제 이를 estimate값을 구하는 과정을 알아보자. 가장 직관적인 방법은 conditional expectation $E[Y\|T,W]$을 statistical(or ML) model로 구하는 것이다. 그리고 이를 $n$개의 data가 있다고 하면 empirical mean을 구하면 우리가 원하는 값을 얻을 수 있다. 편의를 위해 아래의 notation을 사용하자.

$$\mu(1,w) - \mu(0,w) = E[Y|T=1,W]-E[Y|T=0,W]$$

우리는 $\mu$를 approximation하기 위해 모델링을 할 것이고 우리가 구한 모델을 $\hat{\mu}$라고 한다. 이 모델을 *conditional outcome model*이라고 한다. 이를 이용하여 ATE에 대한 model-assisted estimator를 아래와 같이 구할 수 있다.

$$\therefore \hat{\tau} = \frac{1}{n}\sum_i [\hat{\mu}(1,w_i)-\hat{\mu}(0,w_i)]$$

이런 estimator를 COM estimator라고 부른다.

CATE estimation에서 $\mu$는 아래와 같고

$$\mu(t,w,x) = E[Y|T=t, W=w, X=x]$$

이를 통해 COM estimator for the CATE $\tau(x)$는

$$\hat{\tau}(x) = \frac{1}{n_x}\sum_{i:x_i=x} [\hat{\mu}(1,w_i,x)-\hat{\mu}(0,w_i,x)]$$

여기서 $n_x$는 $x_i = x$인 data의 갯수를 의미한다.

### COM estimation's many faces
- G-computation estimators
- Parametric G-formula
- Standardization
- S-learner (S us for Single)

### Problem with COM estimation in high dimensions
예를 들어 COM estimator를 구하기 위해 모델링을 한다고 가정해보자. $T$의 경우 1차원, $W$의 경우는 고차원인 경우가 발생할 것이다. 이를 Neural net에 넣는다고하면 $T$의 effect는 무시당할 확률이 크다. 따라서 ATE estimate는 0으로 가게되는 것이다. 이외에도 COM estimator가 zero biased된다는 논문도 있다. 

## Grouped Conditional Outcome Modeling (GCOM)
위에서 COM estimator의 문제를 알아보았다. 그렇다면 어떻게 모델이 $T$를 무시하지 못하게 할 수 있을까?

아래와 같은 모델 두 개를 만들면 된다.

$$\mu_1 (w) = E[Y|T=1,W=w],\;\mu_0 (w) = E[Y|T=0,W=w]$$

$T$에 따라 그룹을 나누어서 모델을 훈련시키는 것이다. 이렇게 얻어진 estimator를 *grouped conditional outcome model estimators*라고 한다.

$$\hat{\tau} = \frac{1}{n}\sum_i [\hat{\mu}_1(w_i)-\hat{\mu}_0(w_i)]$$

CATE를 위한 GCOM estimator는

$$\hat{\tau}(x) = \frac{1}{n_x}\sum_{i:x_i=x} [\hat{\mu}_1(w_i,x)-\hat{\mu}_0(w_i,x)]$$

GCOM estimation을 통해 COM estimation에서 발생하는 zero treatment effect를 방지할 수 있다. 하지만 이 또한 데이터를 나누어서 모델링을 해야하는 단점이 존재한다. 효율적이지도 않고 variance가 높아질 수 있다. 물론 이를 보완하기 위한 모델링 방법도 존재한다.

## Increasing Data Efficiency
### TARNet
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_06_01.PNG?raw=true"  width="300">
</center>

### X-Learner

- 1. Estimate $\hat{\mu}_1(x)$ and $\hat{\mu}_0(x)$
- 2. Impute ITEs
  - Treatment group: $\hat{\tau}_{1,i}=Y_i(1) - \hat{\mu}_0 (x_i)$
  - Control group: $\hat{\tau}_{0,i}=\hat{\mu}_1 (x_i) - Y_i(0)$ 
- 3.Fit a model $\hat{\tau}_1(x)$ to predict $\hat{\tau}_{1,i}$ from $x_i$ in treatment group and Fit a model $\hat{\tau}_0(x)$ to predict $\hat{\tau}_{0,i}$ from $x_i$ in control group.
- 4. $\hat{\tau}(x) = g(x)\hat{\tau}_0(x) + (1-g(x))\hat{\tau}_1(x)$ where $g(x)$ is some weighting function (ex; propensity score)

## Propensity Scores
vector of variables $W$가 backdoor critetion을 만족한다고 하자. 그러면 $W$를 conditioning하여 causality를 파악하려고 할 것이다. 그러데 $W$가 고차원이면 이를 계산하기가 까다로워진다. 이를 해결하기 위한 것이 propensity score $e(w)$이다.

$$e(w) = P(T=1|W=w)$$

$e(w)$는 scalar이므로 상당히 계산이 간단해진다.

- Propensity Score Theorem
  - Given positivity, unconfoundedness given $W$ implies unconfoundedness given the propensity score $e(W)$

$$(Y(1),Y(0))\perp T | W \Rightarrow (Y(1),Y(0))\perp T | e(W)$$

이는 수학적으로 증명되있기도 하다. graphical하게 이해해도 된다. 그렇다면 $e(W)$는 어떻게 구할까? logistic regression같은 모델을 이용하여 $T$를 target, $W$는 input으로 놓고 모델링을 하면 된다.

## Inverse Probability Weighting (IPW)
confounder $W$에서 $T$로 가는 edge를 없애는 것이 우리가 하고 싶은 일이다. 이를 가중치를 통해 해결하는 것이 IPW방법론이다. $T=1$인 경우, $1/e(W)$의 가중치를 곱하고 continuous의 경우에는 $1/P(T\|W)$를 곱한다. 따라서

$$E[Y(t)] = E[\frac{1(T=t)Y}{P(t|W)}]$$

$$\tau = E[Y(1)-Y(0)] = E[\frac{1(T=1)Y}{e(w)}] - E[\frac{1(T=0)Y}{1-e(w)}]$$

- (proof)

$$E[Y(t)] = E[E[Y|t, W]] \\; \text{(given unconfoundedness and positivity)} $$

$$ = \sum_w \sum_y y P(y|t,w)P(w) \\\ = \sum_w \sum_y y P(y|t,w)P(w) \frac{P(t|w)}{P(t|w)} \\\ = \sum_w \sum_y y P(y,t,w)\frac{1}{P(t|w)} \\\ = \sum_w E[1(T=t,W=w)Y]\frac{1}{P(t|w)} \\\ = E[\frac{1(T=t)Y}{P(t|W)}]$$

## Other Methods
- Doubly Robust Method
  - Using both conditional outcome model and propensity score
  - Consistent if either $\hat{\mu}(t,w)$ or $\hat{e}(w)$ is consistent
  - Theoretically converge to the estimand at a faster rate than COM/IPW
- Match
  - treat와 control 그룹 간에 비슷한 객체들을 찾는 방법
- Double Machine Learning
  - stage1
    - Fit a model to predict $Y$ from $W$ to get the predicted $\hat{Y}$
    - Fit a model to predict $T$ from $W$ to get the predicted $\hat{T}$
  - stage2
    - Partial out $W$ by fitting a model to predict $Y-\hat{Y}$ from $T-\hat{T}$
- Causal Trees and Forests

## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=_Wr4cWr_ZjU&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=6)
