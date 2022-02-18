# [인과추론] Causal Models


Causal Model이란?

<!--more-->

Causal Estimand를 구하기 위해서 우리는 결국 Statistical Estimand를 Estimate해야한다. 이 때 Causal Estimand를 Statistical Estimand로 만들어주는 것이 Causal Model이 하는 일이다.

## *do*-operator
- Conditioing vs intervening
  - Conditioning이라함은 population에서 subpopulation을 뽑아서 다루는 것이고
  - Intervening이라함은 특정한 treatment를 population에 행하는 것이다. 특정 subpopulation을 고려하지 않는다.

특정한 intervening을 했다는 것을 나타내는 operator가 $do$-operator이다. 이는 이전에 potential outcome framework에서 사용한 정의와도 연결되어 있다.

$$P(Y(t)=y) = P(Y=y|do(T=t)) = P(y|do(t))$$

- $P(Y\|do(T=t))$처럼 $do$-operator가 있으면 *Interventional distibutions*이라고 한다. 
- Interventional data는 주로 실험(experiment)이 필요한 경우가 많다. 이전에 배운 Identifiability가 $P(y \| do(t))$를 $P(y\|t)$로 구하는 것을 의미했다. (confounder가 있다면 $E_X [P(y\|t,X)]$)

## Modularity
어떤 causal mechanism에 대한 정의는 다양할 수 있지만 여기서는 $X_i$가 발생하는 causal mechanism은 conditional distribution $P(X_i \|pa_i)$라고 할 것이다.

causal identification을 얻기 위해서 지금 정의할 가정은 invervention이 local하다는 것이다. 이를 Modularity라고 한다.

### Modularity assumption
- If we intervene on a node $X_i$, then only the mechanism $P(x_i\|pa_i)$ changes. All other mechanisms $P(x_j \| pa_j)$ where $i \neq j$ remain unchanged.
- = independent mechanism, autonomy, invariance, etc

### Modularity assumption (formal)
- If we intervene on a set of nodes $S \in [1,2,...,n]$, setting them to constants, then for all $i$, we have the following:
  - 1. If $i \notin S$, then $P(x_i \| pa_i)$ remains unchanged.
  - 2. If $i \in S$, then $P(x_i \| pa_i)=1$ if $x_i$ is the value that $X_i$ was set to by the intervention; otherwise $P(x_i \| pa_i)=0$.

interventional distribution이 있는 causal graph는 일반적인 observational graph와 동일한 모습이다. 다만 intervened node로 향하는 edge들은 다 지워진다. intervened node는 parent가 없어지는 것이다. 이는 위의 modularity assumption에 의해 intervened node가 발생할 확률이 1이기 때문이다. 다른 시각으로는 intervention하여서 해당 node가 constant이므로 parent들의 영향이 없어진 것으로 바라볼 수도 있다.

### Truncated factorization
- under Markov, modularity assumption
- a set of intervention nodes $S$

$$P(x_1 ,..., x_n | do(S=s)) = \prod_{i \notin S} P(x_i | pa_i)$$

if $x$ is consistent with the intervention (= if $x_i$ is the value that $X_i$ was set to by the intervention). Otherwise

$$P(x_1,...,x_n | do(S=s))=0$$

예시를 통해 더 잘 이해해보자.
#### identification via truncated factorizartion
- Goal: identify $P(y\|do(t))$
- graph: $T \leftarrow X \rightarrow Y, T\rightarrow Y$

위의 상황에서
- Bayesian network factorization:
$$P(y,t,x)=P(x)P(t|x)P(y|t,x)$$
- Truncated factorization:
$$P(y,x|do(t))=P(x)P(y|t,x)$$
- Marginalize:
$$P(y|do(t))=\sum_x P(y|t,x)P(x)$$

## Backdoor adjustment
direct path를 통한 causal flow가 아닌 non-causal association flow가 지나는 모든 path를 *backdoor path*라고 한다. 이들을 잘 block하면 우리가 구하고 싶은 $P(y|do(t))$를 구할 수 있는 것이다. treatment를 intervention(experiment)할 수 있다면 backdoor path는 사라진다. treatment로 향하는 edge가 다 사라지기 떄문이다. 하지만 observation의 경우는 그럴 수가 없다. 이런 경우 어떻게 할 수 있을까? 이전에 배운대로 conditioning하면 된다.

### Backdoor Criterion
- A set of variables $W$ satisfies the backdoor criterion relative to $T$ and $Y$ if the following are true:
  - $W$ blocks all backdoor paths from $T$ to $Y$
  - $W$ does not contain any descendants of $T$

### Backdoor Adjustment
- under modularity assumtion, $W$가 backdoor criterion을 만족하고 positivity가 성립하면 causal effect $T$ on $Y$를 identify할 수 있다:

$$P(y|do(t)) =\sum_w P(y|t,w)P(w)$$

- (proof)
  - graph: $T \leftarrow W \rightarrow Y, T \rightarrow Y$
  - $W$가 backdoor criterion을 만족한다고 가정
  - 아래 수식에서 두번째 줄은 $W$를 conditioning해서 $T$에서 $Y$로 가는 path만 남기 때문이다.
  - 아래 수식에서 세번째 줄은 $Y$가 collider가 되기 때문이다.

$$P(y|do(t)) = \sum_w P(y|do(t),w)P(w|do(t)) \\\ =\sum_w P(y|t,w)P(w|do(t)) \\\ =\sum_w P(y|t,w)P(w)$$

$do(t)$해서 $T$로 들어오는 edge를 없애서 Backdoor path를 막을 수 있지만 observation data에서는 다른 변수들을 conditioning하는 것이다.

## Structural causal models
- structural equation for A as a cause of B:

$$B := f(A)$$

이러한 structural equation은 causal mechanism을 나타낸다. 위의 수식에 unobserved variable $U$을 추가하면

$$B := f(A,U)$$

$f$에 대해서 특정한 모델로 정의하지 않으면 비모수(nonparametric)적인 함수가 될 것이다. 또한 $f$가 deterministic하더라도 $U$가 있기 때문에 stochastic한 mapping을 할 수 있다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_04_01.PNG?raw=true"  width="300">
</center>

위의 그림에서 structual equation:

$$B:=f_B(A,U_B) \\\ C:=f_C(A,B,U_C) \\\ D:=f_D(A,C,U_D)$$

여기서 known variable을 *endogenous* variable, unknown variable을 *exogenous* variable이라고 한다. 그렇다면 이제 SCM의 정의를 알아보자.

- SCM
  - A structural causal model is a tuple of the following sets:
    - A set of endogenous variables $V$
    - A set of exogenous variables $U$
    - A set of functions $f$

## condition on descendants of treatment
- 첫번째 그림
  - $Z$가 collider 이기 때문에 conditioning 하지 말자.
- 두번째 그림
  - $M$의 descendant도 conditioning하면 하지말자. 왜냐하면 우리가 모르는 $U_M$이 존재하는 경우 $M$도 collider가 되기 때문이다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_04_02.PNG?raw=true"  width="300">
</center>

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_04_03.PNG?raw=true"  width="300">
</center>

따라서 결론은 Don't conditionon post-treatment covariates! 물론 pre-treatment를 항상 conditioning해서도 안된다. *M-bias*라는 것이 존재하기 때문이다. 아래의 사진처럼 우리가 모르는 $Z_1,Z_3$가 존재하는 경우 $Z_2$를 conditioning하면 backdoor-path가 생겨버린다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_04_04.PNG?raw=true"  width="300">
</center>

## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=AuZu0L0PEgk&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=4&t=1s)
