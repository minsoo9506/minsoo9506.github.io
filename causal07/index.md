# [인과추론] Unobserved Confounding, Bounds, and Unobserved Confounding


No unobserved confounding은 솔직히 비현실적이다.

<!--more-->

- 이 때 사용하는 좀 더 robust한 방법
  - nonparametric bounds
  - sensitivity analysis

## Bounds
unconfoundendness 가정을 좀 약하게 하면? point($E[Y(1)-Y(0)]$)가 아니라 interval(set identification)로 생각하는 것이다.

일반적으로 potential outcome은 bounded! 그래서 ITE, ATE도 아래와 같은 bound를 갖고 있다.

$$a \le Y(t) \le b$$

$$a-b \le E[Y(1)-Y(0)] \le b-a$$

이제 Bound를 정의하는 다양한 방법을 알아보자. 이 방법에 따라 interval의 length도 달라질 것이다. 우리는 interval의 크기가 작은게 좋다. 그리고 위에서 가장 basic한 접근법으로 interval의 length는 $2(b-a)$이다. 이를 기억하자.

### No-Assumptions Bound
먼저 ATE를 decomposition해보자.

$$E[Y(1) - Y(0)] = E[Y(1)] - E[Y(0)] $$
$$= P(T=1)E[Y(1)|T=1] + P(T=0)E[Y(1)|T=0] \\\ - (P(T=1)E[Y(0)|T=1] + P(T=0)E[Y(0)|T=0])$$
$$= P(T=1)E[Y|T=1] + P(T=0)E[Y(1)|T=0] \\\ - (P(T=1)E[Y(0)|T=1] + P(T=0)E[Y|T=0])$$

이를 Observational-Counterfactual Decomposition이라고 한다. 하지만 $E[Y(1)|T=0],E[Y(0)|T=1]$은 counterfactual이라 우리가 알 수 없다. 대신에 interval을 구할 수 있지 않을까?

- No-Assumptions Bound
  - $\pi$가 $P(T=1)$라고 하고 $T$가 binary random variable이라고 하자.
  - outcome $Y$가 $[a,b]$ 사이의 값을 가지면

$$E[Y(1)-Y(0)] \le \pi E[Y|T=1] + (1-\pi)b - \pi a - (1-\pi)E[Y|T=0] \\\ E[Y(1)-Y(0)] \ge \pi E[Y|T=1] + (1-\pi)a - \pi b - (1-\pi)E[Y|T=0]$$

이때 interval의 length는 $b-a$ 이다. 별다른 가정도 없었는데 (consistency assumption은 필요) length가 절반으로 줄었다.

- Example
  - outcome $Y$가 $[-1,1]$
  - $\pi = 0.3$
  - $E[Y\|T=1]=0.9,\;E[Y\|T=0]=0.2$
  - No-Assumptions Bound에 따르면

$$-0.17 \le E[Y(1)-Y(0)] \le 0.83$$

### Monotone Treatment Response
- assume always $Y_i (1) \ge Y_i (0)$
  - 반대의 가정도 있다 : Nonpositive MTR  $Y_i (1) \le Y_i (0)$

- Example
  - outcome $Y$가 $[-1,1]$
  - $\pi = 0.3$
  - $E[Y\|T=1]=0.9,\;E[Y\|T=0]=0.2$
  - nonnegative MTR에 따르면

$$0 \le E[Y(1)-Y(0)] \le 0.83$$

### Monotone Treatment Selection
- Treatment groups' potential outcomes are better than control groups'

$$E[Y(1)|T=1] \ge E[Y(1)|T=0] \\ E[Y(0)|T=1] \ge E[Y(0)|T=0]$$

$$\therefore E[Y(1)-Y(0)] \le E[Y|T=1] - E[Y|T=0]$$

- Example
  - outcome $Y$가 $[-1,1]$
  - $\pi = 0.3$
  - $E[Y\|T=1]=0.9,\;E[Y\|T=0]=0.2$
  - MTS에 따르면

$$-0.17 \le E[Y(1)-Y(0)] \le 0.7$$

### Optimal Treatment Selection Assumption
- individuals always receive the treatment that is best for them:

$$T_i = 1 \rightarrow Y_i (1) \ge Y_i (0) \\ T_i = 0 \rightarrow Y_i (0) \ge Y_i (1)$$

$$\therefore E[Y(1)-Y(0)] \le \pi E[Y|T=1] - \pi a$$
$$\therefore E[Y(1)-Y(0)] \ge (1 - \pi)a - (1 - \pi)E[Y|T=0] - a$$

- Example
  - outcome $Y$가 $[-1,1]$
  - $\pi = 0.3$
  - $E[Y\|T=1]=0.9,\;E[Y\|T=0]=0.2$
  - nonnegative MTR에 따르면

$$-0.14 \le E[Y(1)-Y(0)] \le 0.27$$

## Sensitivity Analysis
confounder가 $W$뿐만 아니라 unobserved confounder $U$가 있다고 가정해보자. 그러면 confounding bias가 발생한다. 이와 관련해서 분석하는 것이 sensitivity analysis라고 할 수 있다. 우리가 구한 ATE에 대해 validataion하는 것이다.

### Linear Single Confounder
먼저, noiseless linear data generating process를 가정해보자.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_07_01.PNG?raw=true"  width="200">
</center>

$$T:=\alpha_w W + \alpha_u U \\ Y := \beta_w W + \beta_u U + \delta T$$

여기서 $W,U$ 모두를 알고 있으면 ATE는

$$E[Y(1)-Y(0)] = E_{W,U}[E[Y|T=1,W,U]-E[Y|T=0,W,U]]=\delta$$

이제 bias를 계산해보자.

$$E_W [E[Y|T=t,W]] = E_W [E[\beta_w W + \beta_u U + \delta T|T=t,W]] \\\ =E_W [\beta_w W + \beta_u E[U|T=t,W]+\delta t]\\\ =E_W [\beta_w W + \beta_u (\frac{t-\alpha_w W}{\alpha_u}) + \delta t] \\\ = E_W [\beta_w W + \frac{\beta_u}{\alpha_u}t - \frac{\beta_u \alpha_w}{\alpha_u}W + \delta t] \\\ = \beta_w E[W] + \frac{\beta_u}{\alpha_u}t - \frac{\beta_u \alpha_w}{\alpha_u}E[W] + \delta t$$

따라서 ATE estimate는

$$E_W [E[Y|T=1,W]-E[Y|T=0,W]]=\delta + \frac{\beta_u}{\alpha_u}$$

따라서 bias $\frac{\beta_u}{\alpha_u}$가 발생한다.

### Towards More General Settings
이와 관련해서 다양한 논문들이 존재한다. 이들에 대해 간단히 알아보자.

- Binary Treatment
  - Rosenbaum & Rubin (1983), Imbens (2003)
    - simple parametric form for $Y$, $T$
    - $U$ : binary, scalar

$$P(T=1|W,U) = sigmoid(\alpha_w W + \alpha_u U)$$
$$Y = \beta_w W + \beta_u U + \delta T + Noise$$

그런데 최근에 Cinelli & Hazlett (2020)에서 위를 더 발전시켰다고 한다.

- Making Sense of Sensitivity: Extending Omitted Variable Bias (Cinelli & Hazlett 2020)
  - no parametric form for structural equation for $T$
  - Allows for multiple confounders (can be a vector)
  - No assumed distribution on $U$

- Sense and Sensitivity Analysis (Veitch & Zaveri 2020)
  - Both the treatment mechanism and the outcome mechanism can be modeled with arbitrary machine learning models, and we still get a closed-form expression for the bias

  강의 마지막 부분에 논문을 추천해준다. 나중에 필요하면 참고하자.

  ## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=K6kwNUsYK7Y&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=8)
