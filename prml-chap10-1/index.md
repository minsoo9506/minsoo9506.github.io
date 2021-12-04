# [PRML] Chapter10 - Approximate Inference (1)


PRML chapter10 에 대한 카이스트 문일철 교수님 강의 정리하였다.
<!--more-->

### Variational transform
- concave한 $y = \ln (x)$ 를 조금 더 간단한 함수(linear)로 근사하고자 한다. (특정 $x$에서)
- linear model $f(x) = \lambda x + b(\lambda)$ 으로 근사하자.
- How?
  - $\min_x (\lambda x + b(\lambda) -\ln x)$
  - 이를 위해 $x$에 대해 미분한다. 미분해서 구하면 $\lambda= \frac{1}{x}$
  - 이를 이용하면 $b(\lambda) = -\ln \lambda -1$
- $f(x) = \lambda x - \ln \lambda -1$ 의 결과가 나온다. $x$에 대해서 선형인 결과이다. 하지만 optimization하기에 non linear한 $\lambda$가 다시 생긴다.

그렇다면 logistic function에서는 variation transform이 가능할까?
- s curve라 concave하지도 convex하지도 않다.
- 이런 경우 log 을 취해준다.

$$\log \frac{1}{1+e^{x}} = -\log(1+e^x)$$

이렇게 하면 log함수의 형태이므로 linear approximation(variational transform)이 가능하다. 그리고 linear model을 다시 exp를 취해주면 될 것이다.

### Convex duality
그렇다면 위와 같은 경우를 좀 더 systematic variational transform할 수 없을까?
- Utilize the convex duality

우리는 log같은 concave(or convex) function의 형태만 되면 선형으로 근사할 수 있다.

- Dual function(=Conjugate function) : $f^{ * }(\lambda)$

$$f(x)=\min_{\lambda}\{ \lambda^T x - f^{* }(\lambda) \} \Leftrightarrow f^{* }(\lambda)=\min_{x}\{ \lambda^T x - f(x)\}$$

위에서 봤듯이 복잡한 함수를 approximate했지만 그 복잡성은 사라지는 것이 아니고 $\lambda$와 같이 파라미터의 형태로 존재하게 된다.

앞에서 배운 내용을 확률모델에서 생각해보자.
- probability distribution function도 결국 function이니까 그대로 이용할 수 있다. (variational transform)
  - 아래 식에서 $\pi (i)$는 $i$의 given을 의미
    - eg. $P(S) = P(S_1)P(S_2\|P_1,P_3)P(S_3)$ 에서 $\pi(2)=\{1,3\}$
  - variational parameter $\lambda_i^U$

$$P(S) = \prod P(S_i | S_{\pi(i)}) = \min_{\lambda}P^U (S_i | S_{\pi(i)}, \lambda^U_i )$$

### Variables of E and H
- $E$ is observed, fixed
- $H$ is estimated, infered
- $E \cup H = S$ (전체) 라고 할 때
- $P(E) = \sum_H P(H,E) = \sum_H P(S) = \sum_H \prod_i P(S_i  \| S_{\pi(i)})$

우리가 구하고자 하는 것은 $P(H\|E) = P(H,E) / P(E)$ 이다. variational inference를 통해 $P(E)$를 approximate해야 한다. (바로 구하기 어려운 경우)

### Setting the minimum criteria
$$\ln P(E) = \ln \sum_H P(H,E) = \ln \sum_H Q(H|E) \frac{P(H,E)}{Q(H|E)}$$

- log는 concave(위로 볼록) 하기에 

$$\ln \sum_H Q(H|E) \frac{P(H,E)}{Q(H|E)} \ge \sum_H Q(H|E) \ln \frac{P(H,E)}{Q(H|E)} \\ = \sum_H Q(H|E)[\{ \ln P(E|H) + \ln P(H) \} - \ln Q(H|E)] \\= E_{Q(H|E)}\ln P(E|H) - KL(Q(H|E) || P(H))$$

### Optimizing the Lower Bound
- $Q(H \| E,\lambda)$ : variational function(distribution)
  - $\lambda$ : variational parameter

- $\theta$는 model parameter
  
$$\ln P(E | \theta) \ge  E_{Q(H|E)}\ln P(E|H,\theta) - KL(Q(H|E) || P(H|\theta))$$

- ELBO(evidence lower bound)

$$L(\theta, \lambda) = \sum_H Q(H|E,\lambda)\ln P(H,E | \theta) - Q(H|E,\lambda)\ln Q(H|E,\lambda)$$

이를 최적화하기 위해서 (9장에서 공부한 EM과 거의 유사) KL을 0으로 만들고
- $Q(H\|E,\lambda)=P(H\|E,\theta)$으로 만든다.
    - $\lambda^{t+1} = \arg\max_{\lambda}L(\theta^t, \lambda^t)$
- 그리고 $\theta$를 optimization한다. (미분해서)
  - $\theta^{t+1} = \arg\max_{\theta}L(\theta^t, \lambda^{t+1})$

그렇다면 어떻게 Q를 구할까?

### Factorizing Q
Q를 안다는 것은 좋은 $\lambda$와 Q의 distribution 형태를 골라야하는 것이다. 이전에는 Q가 $P(H\|E,\theta)$이 되도록 하였지만 이를 쉽게  구하기 어려운 경우도 존재한다. 그렇다면 Q를 approximate 해보자. 
- Mean field approximation
  - hidden variable들이 독립이라는 가정 given variational parameter
  - simple, easier to hadle
  - strong assumption

$$Q(H) = \prod_{i \le |H|}q_i(H_i | \lambda_i)$$

### Focusing on Single Variable in Q
이번에는 Factorizing한 Q에서 하나의 variable에 대해 살펴보자.

$$L(\theta, \lambda) = \sum_H [ \prod_{i \le |H|}q_i(H_i |E, \lambda_i)\ln P(H,E | \theta) \\\ - \prod_{i \le |H|}q_i(H_i |E, \lambda_i)\ln \prod_{i \le |H|}q_i(H_i | E,\lambda_i) ]$$

위에서 single variable에 대해 정리하면 (j와 관련없으면 constant C로 처리)

$$L(\lambda_j) = \sum_H [ \prod_{i \le |H|}q_i(H_i |E, \lambda_i)\ln P(H,E | \theta) \\\ - \prod_{i \le |H|}q_i(H_i |E, \lambda_i)\ln \prod_{i \le |H|}q_i(H_i | E,\lambda_i) ]$$
$$= \sum_H [ \prod_{i \le |H|}q_i(H_i |E, \lambda_i) \{ \ln P(H,E | \theta) - \ln \prod_{i \le |H|}q_i(H_i | E,\lambda_i)\} ] $$
$$=  \sum_{H_j} q_j (H_j | E, \lambda_j) \sum_{H_{-j}} \prod_{i \le |H|, i \neq j} q_i(H_i |E,\lambda_i) \ln P(H,E|\theta) \\\ -\sum_{H_j} q_j (H_j|E,\lambda_j) \ln  q_j (H_j|E,\lambda_j)+C$$

이제 새로운 P function을 정의하면

$$\ln \tilde{P}(H,E | \theta) = \sum_{H_{-j}}\prod_{i\le |H|, i \neq j}q_i(H_i | E,\lambda_i) \ln P(H,E | \theta) $$
$$ = E_{q_{i \neq j} }[\ln P(H,E | \theta)]+C$$

이 새로운 P function을 $L(\lambda_j)$ 에 대입하면 이전에 우리가 봤던 형태가 나온다. 그렇다 KL divergence! 이를 최적화하기 위해서는 KL divergence = 0 을 만드는 분포를 이용하면 된다. 따라서

$$\ln q_j (H_j | E, \lambda_j) = \ln \tilde{P}(H,E | \theta)=E_{q_{i \neq j}}[\ln P(H,E | \theta)] + C$$

### 예시
- $\mu \sim N(\mu_0, \frac{1}{\lambda_0 \tau})$
- $\tau \sim Gamma(a_0, b_0)$
- $x_i \sim N(\mu, \frac{1}{\tau})$
- H : $\mu, \tau$
- E : x

먼저 joint 부터 구하면

$$p(H,E | \theta) = p(X,\mu,\tau | \mu_0, \lambda_0, a_0, b_0)$$
$$ =p(X| \mu, \tau)p(\mu | \tau, \mu_0, \lambda_0)p(\tau | a_0, b_0)$$
$$=\prod_i p(x_i | \mu, \tau)p(\mu | \tau, \mu_0, \lambda_0)p(\tau | a_0, b_0)$$

우리는 2개의 variational parameter가 필요하다. by mean field approximation

$$Q(H|E,\lambda) = Q(\mu,\tau | X,\mu', \tau') = q(\mu | X,\mu')q(\tau | X,\tau')$$

그럼 이제 optimal variational parameter를 구해보자.
- 일단 $\mu$에 관하여 진행

$$\ln q(\mu | X,\mu') = E_{\tau}[\ln p(X,\mu, \tau | \mu_0,\lambda_0, a_0, b_0)]+C_1 $$
$$ = E_{\tau}[\ln \prod_i p(x_i | \mu, \tau)p(\mu | \tau, \mu_0, \lambda_0)p(\tau | a_0, b_0)]+C_1 \\\ = E_{\tau}[\sum(\frac{1}{2}(\ln \tau - \ln 2\pi) - \frac{(x_i-\mu)^2 \tau}{2})] \\\ + E_{\tau}[\frac{1}{2}(\ln \lambda_0 + \ln \tau - \ln 2 \pi) - \frac{(\mu - \mu_0)^2 \lambda_0 \tau}{2}]+C_2 \\\ = - \frac{E_{\tau}[\tau]}{2}\{ \sum (x_i - \mu)^2 + (\mu - \mu_0)^2 \lambda_0 \}+C_3$$
$$ = - \frac{1}{2}\{ (\lambda_0+N)E_{\tau}[\tau] (\mu - \frac{\lambda_0 \mu_0 + \sum x_i}{\lambda_0 + N})^2 \} + C_4$$


그렇다면 이제 $q(\mu \| X,\mu')$를 normal이라고 가정해보면 (우리는 $q(\mu;i;p)$에 대한 아무런 가정이 없기 때문에 이렇게 접근하는 것)

$$q(\mu | X,\mu') \sim N(\frac{\lambda_0 \mu_0 + \sum x_i}{\lambda_0 + N},\frac{1}{(\lambda_0 +N)E_{\tau}[\tau]})$$

위에서 우리가 모르는 부분은 $E_{\tau}[\tau]$ 이므로 위와 똑같은 과정을 반복해서 모르는 부분에 대해 찾아야 한다.

$$\ln q(\tau | X,\tau') = E_{\mu}[\ln p(X,\mu,\tau | \mu_0, \lambda_0, a_0, b_0)]+C_1$$
$$= -\tau\{ b_0 + \frac{1}{2}E_{\mu}[\sum(x_i - \mu)^2 + (\mu-\mu_0)^2 \lambda_0]\} + (a_0 + \frac{N+1}{2}-1)\ln \tau + C_2$$

이를 잘 보면 $q(\tau \| X,\tau')$ 는 Gamma 꼴이라고 생각할 수 있다.

$$q(\tau | X,\tau') \sim Gamma(a_0 + \frac{N+1}{2}, b_0 + \frac{1}{2}E_{\tau}[\sum(x_i - \mu)^2 + (\mu - \mu_0)^2\lambda_0])$$

이를 통해 우리가 구해야 하는 모든 것을 구했다. 정리하면 

- $E_{\tau}[\tau] : E_{\mu}[\mu],E_{\mu}[\mu^2]$ 이 필요하다
- $E_{\mu}[\mu]$ 는 바로 구할 수 있음
- $E_{\mu}[\mu^2] : E_{\tau}[\tau]$ 필요
- $\lambda$ 는 임의의 수로 시작

interlocked 된 상태이기에 iterative하게 구하면 된다.

### 기타 예시
- prof.Moon
  - Latent Dirichlet Allocation
