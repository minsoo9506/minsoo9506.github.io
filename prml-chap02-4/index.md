# [PRML] Chapter2 - Probability Distribution (4)


Exponential Family와 Nonparametric 방법론에 대해 알아보자.

<!--more-->

## 2.4 The Exponential Family
우리가 이전에 공부했던 대부분의 distribution은 Exponential Family에 속한다.
- The *exponential family* of distribution over $x$, given parameters $\eta$, is defined to be the set of distributions of the form

$$p({\pmb x} | {\pmb \eta}) = h({\pmb x})g({\pmb \eta}) \exp ({\pmb \eta}^T u({\pmb x}))$$

- pdf(pmf) $p({\pmb x} | {\pmb \eta})$ 을 우항과 같이 표현할 수 있다면 exponential family에 속한다.
- ${\pmb x}$는 스칼라, 벡터 둘 다 가능하고 discrete, continous 모두 가능하다.
- ${\pmb \eta}$ 는 *natural parameter* of the distribution 이라고 한다.
- $g(\pmb \eta)$는 distribution의 normalize coefficient (적분해서 1이 되도록 만들어주는) 라고 할 수 있다.

$$g({\pmb \eta}) \int h({\pmb x})\exp\{ {\pmb \eta}^T{\pmb u} ({\pmb x}) \} d {\pmb x}=1 $$

- Bernoulli distribution 예시
  - $p(x | \mu) = Bern(x | \mu)=\mu^x(1-\mu)^{1-x}$
  - 이를 exponential form으로 표현해보자.
  - $f(x)=\exp(\ln{f(x)})$ 을 이용하여

  $$p(x | \mu) = \exp \{x \ln \mu + (1-x) \ln (1-\mu) \} \\\\ = (1-\mu) \exp \\{ \ln \left( \frac{\mu}{1-\mu} \right) x \\} \\\ \therefore \eta = \ln\left(\frac{\mu}{1-\mu}\right) $$

위의 형태를 $\mu$에 대한 식으로 바꿔보면

  $$\mu=\sigma(\eta)=\dfrac{1}{1+\exp(-\eta)} $$

위의 식과 같은 형태의 함수를  *logistic sigmoid* function이라고 부른다.

- Multinomial distribution 예시
    - $p({\pmb x} \| {\pmb \mu}) = \prod_{k=1}^{M}\mu_k^{x_k}= \exp [\sum_{k=1}^{M}x_k \ln \mu_k]$
    - $p({\pmb x}\|{\pmb \eta})=\exp({\pmb \eta}^T{\pmb x})$
      - $\eta_k = \ln \mu_k$
      - ${\pmb \eta} = (\eta_1, \eta_2,...,\eta_k)^T$

M개의 parameter가 있지만 $\sum_{k=1}^{M}{\mu_k}=1$ 이라는 제약때문에 M-1개의 값이 정해지면 마지막 M개는 저절로 정해진다. 이를 이용할 것이다.

$$\exp \\{\sum_{k=1}^{M}x_k\ln\mu_k \\} = \exp \\{\sum_{k=1}^{M-1}x_k\ln\mu_k + \left(1-\sum_{k=1}^{M-1}x_k\right)\ln\left(1-\sum_{k=1}^{M-1}\mu_k\right) \\}\\\
= \exp\\{\sum_{k=1}^{M-1}x_k\ln\left(\frac{\mu_k}{1-\sum_{j=1}^{M-1}\mu_j}\right)+\ln\left(1-\sum_{k=1}^{M-1}\mu_k\right)\\}$$

$$\therefore \eta_k = \ln\left(\frac{\mu_k}{1-\sum_{j \neq k} \mu_j}\right)$$

똑같이

$$\mu_k=\dfrac{\exp(\eta_k)}{1+\sum_{j \neq k}\exp(\eta_j)}$$

위의 식과 같은 형태의 함수를  *softmax* function (normalized exponential) 이라고 부른다.

### 2.4.1 Maximum likelihood and sufficient statistics
$\eta$가 어떤 것인지 알았으니 이제 이를 MLE로 estimate해보자. exponential form을 $\eta$에 대해 미분하면

$$\nabla g({\pmb \eta})\int h({\pmb x})\exp\{ {\pmb \eta}^T {\pmb u}({\pmb x})\}d{\pmb x} + g({\pmb \eta})\int h({\pmb x})\exp\{ {\pmb \eta}^T{\pmb u}({\pmb x})\}{\pmb u}({\pmb x})d{\pmb x} = 0$$

- $g({\pmb \eta}) \int h({\pmb x})\exp\{ {\pmb \eta}^T{\pmb u} ({\pmb x}) \} d {\pmb x}=1$ 을 이용하여

$$-\frac{1}{g({\pmb \eta})} \nabla g({\pmb \eta}) = g({\pmb \eta})\int h({\pmb x})\exp\{ {\pmb \eta}^T{\pmb u}({\pmb x})\}{\pmb u}({\pmb x})d{\pmb x}=E[{\pmb u}({\pmb x})]$$

최종적으로는

$$-\nabla \ln g({\pmb \eta}) = E[{\pmb u}({\pmb x})] $$

이제 iid인 data를 통해 likelihood function을 만들면

$$p({\pmb X}|{\pmb \eta}) = \left(\prod_{n=1}^{N}h({\pmb x} _ n)\right) g({\pmb \eta})^N \exp\\{ {\pmb \eta}^T\sum_{n=1}^{N}{\pmb u}({\pmb x}_n)\\} $$

log를 취한 뒤에 $\eta$에 대해 미분하여 0을 갖도록 하면 아래와 같은 식을 얻을 수 있다.

$$-\nabla \ln g({\pmb \eta} _ {ML}) = \frac{1}{N}\sum_{n=1}^{N}{\pmb u}({\pmb x}_n)$$

이를 통해 우리는 MLE solution이 오직 $\sum_{n=1}^{N} \textbf{u}(\textbf{x}_n)$에 달려 있다는 것을 알 수 있다. 이는 *sufficient statistics* of the distribution 이라고 부른다. parameter에 대한 정보가 여기 다 들어 있어서 충분하다! 라고 이해할 수 있다. 

따라서 우리는 MLE를 구하는 과정에 있어서 각 data를 모두 알고 있을 필요가 없이 충분통계량만 알면 된다. $N \rightarrow \infty$이면 우항은 $E[\textbf{u}(\textbf{x})]$이 되고 ${\pmb \eta}_{ML}$은 true값으로 수렴한다는 것을 알 수 있다.

### 2.4.2 Conjugate priors
exponential family인 prior distribution은

$$p({\pmb \eta} | {\pmb \chi}, v) = f({\pmb \chi}, v)g({\pmb \eta})^v \exp\\{v{\pmb \eta}^T{\pmb \chi}\\}$$

여기에 위에서 보았던 likelihood function을 곱하여 posterior distribution을 구하면

$$p({\pmb \eta}|{\pmb X}, {\pmb \chi}, v) \propto g({\pmb \eta})^{v+N}\exp\\{ {\pmb \eta}^T\left(\sum_{n=1}^{N}{\pmb u}({\pmb x})+v{\pmb \chi}\right)\\}$$

conjugacy를 확인할 수 있다. 또한 parameter $v$는 effective nunber of pseudo-observation 이라고 이해할 수 있다.

### 2.4.3 Noninformative priors
posterior를 만들기 위해 사전의 정보가 충분하면 상관없지만 그렇지 않은 경우, 우리는 prior의 영향을 최소화하고 싶을 것이다. 이런 prior를 *Noninformative prior* 라고 부른다.
- $p(x\|\lambda)$ distribution이 있을 때, prior distribution $p(\lambda)=\text{const}$ 가 적절한 prior가 될 것이다.
  - 만약에 $\lambda$가 $K$ states를 갖는 discrete이면 prior 는 $1/K$로 하면 된다.
  - 하지만 continous하고 domain이 unbounded하면 prior distribution은 합이 1이 되지 않는다 (integral diverge, not correctly normalized). 이런 prior를 *improper prior* 라고 한다.  적분값이 1이 아닌 diverge하는 모든 분포에 해당하는 것은 아니다. prior는 improper해도 posterior는 적절한 pdf가 되어야 한다.

## 2.5 Nonparametric Methods
말 그대로 비모수적인 방법들이다. 이전까지는 parameter를 추정하여 density를 추정하였다면 아래의 방법들은 parameter를 추정할 필요가 없는 data oriented한 방법이라고 생각할 수 있다.

### Histogram
  - 주로 같은 크기의 bin으로 해당 data를 나눈 뒤에 해당 bin에 들어가는 data의 수를 통해 density를 파악한다.
  - 기본적인 것으로 저차원에서 시각화용으로만 사용해야 할 것 같다.
  - (Probability 식) : x를 크기 $\Delta$로 나누고 각 bin i에 들어가는 data의 수를 $n_i$라고 하면 각 bin i의 확률값은 (각 bin의 넓이는 $\frac{n_i}{N} * \Delta$ 이고 histogram 전체 넓이는 1이라)

  $$p_i = \frac{n_i}{N \Delta}$$

  - density는 bin의 크기가 커질수록 smooth해지고 작아질수록 복잡해진다.

### Kernel density estimators
  - D-dimension의 probability density $p({\pmb x})$ 있고 우리는 이 값을 추정하려고 한다.
    - data set은 이 분포에서 나온 $N$개의 observation
    - $R$은 data가 들어있는 어떤 지역
    - 이 지역의 probability mass는

$$P=\int_R p({\pmb x})d{\pmb x}$$

여기서 이 지역에 들어가는 data의 수는 $K$라고 하면 이는 binomial distribution을 따를 것이다.

$$Bin(K/N, P) = \dfrac{N!}{K!(N-K)!}P^K(1-P)^{1-K}$$

data의 수 $N$이 커지면 $K \approx NP$일 것이다. $R$이 충분히 작아서 density $p({\pmb x})$는 해당 지역에서 거의 constant하면 $P \approx p({\pmb x}) V$ 임을 알 수 있다. ($V$는 volume of $R$) 따라서 density estimate하면

$$p({\pmb X}) = \frac{K}{NV}$$

- $V$를 fix : Kernel approach
- $K$를 fix : K-nearest-neighbour

조금 더 자세히 살펴보자.
- $R$을 우리가 구하고 싶은 probability density의 point ${\pmb x}$가 가운데에 있는 작은 hypercube라고 하자. 해당 지역에 들어있는 data 수 $K$를 위해 다음과 같은 함수를 생각해보자.

이 함수는 *kernel function* 의 한 예시이다. 따라서

$$K = \sum_{n=1}^{N}k\left(\frac{ {\pmb x}-{\pmb x}_n}{h}\right) $$

이를 이용하여 density at ${\pmb x}$를 구하면

$$p({\pmb x}) = \frac{1}{N}\sum_{n=1}^{N}\frac{1}{h^D}k\left(\frac{ {\pmb x}-{\pmb x}_n}{h}\right)$$

- $v = h^D$
- 위 식은 함수 $k({\pmb u})$의 대칭성을 생각하여, single cube centered on ${\pmb x}$가 아니라 N cubes centered on the N data point ${\pmb x_n}$ 이라고 이해할 수 있다.

하지만 이는 여전히 불연속적인 단점이 있기에 좀 더 업그레이드해보자. kernel function을 Gaussian으로 정하면

$$p({\pmb x}) = \frac{1}{N}\sum_{n=1}^N\frac{1}{(2\pi h^2)^{D/2}}\exp\\{-\frac{\|{\pmb x}-{\pmb x}_n\|^2}{2h^2}\\}$$

- kernel function은 다양하게 정할 수 있다. 단, 조건은
  - $k(x) \ge 0$
  - $\int k(x)dx = 1$

density는 h가 커지면 smooth해지고 h가 작아지면 더 복잡해진다. 우리는 적절하나 h를 잘 찾아야 하는데 이미 최선의 h는 밝혀져 있다. 아울러 가장 좋은 kernel function도 이미 밝혀져있다.

### Nearest-neighbour methods
  - 이번에는 $K$를 미리 정한 뒤에 이에 적절한 $V$를 찾는 것이다.
  - density는 $K$가 커지면 smooth해지고 작아지면 복잡해진다.
  - KNN classification이 잘 알려져있다.

  지금까지 Nonparametric 방법론을 살펴보았다.
  - 전체 data를 저장하고 있어야 하는 단점이 존재한다.
  - data가 너무 많으면 계산에 어려움이 생기고 너무 적으면 다소 부정확한 근사치를 만들 수 있다.

