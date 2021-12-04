# [PRML] Chapter10 - Approximate Inference (2)


VI, EP 에 대한 정리 (예시 제외)
<!--more-->

probabilistic model의 중요 목표는
- posterior distribution  $p(\textbf{Z}\|\textbf{X})$ of the latent variable $\textbf{Z}$ given the observed data $\textbf{X}$ 추정
- posterior distribution을 이용한 expectation 추정

라고 할 수 있다. In practice 에서는 posterior와 expectation을 구하기가 infeasible한 경우가 존재한다. 이는
- latent space가 too high dimension이라 directly 어려운 경우
- posterior distribution이 너무 복잡하여 expectation이 analytically intractable한 경우

때문이다. 따라서 우리는 approximation을 해야 한다. 이 방법으로는 크게 두 가지로 나누어서 생각할 수 있다.
- Stochastic 방법 : MCMC
- Deterministic 방법 : (analytical approximations to the posterior distribution) variational inference(or Bayes)

## 10.1 Variational Inference
- *functional* : mapping that takes a function as the input and that returns the value of the functional as the output

우리는 이런 functional을 optimize해서 목표를 이룰 것이다.

fully Bayesian model 을 가정해보자. 즉, 모든 parameter들이 prior 존재한다고 가정하는 것이다. model은 latent variable, latent parameter 가 존재한다. 이들은 모두 $\textbf{Z}$ 로 표현하고 observed variables는 $\textbf{X}$로 표현한다. 우리의 목표는
- find approximation for the posterior distribution $p(\textbf{Z} \| \textbf{X})$
- and model evidence $p(\textbf{X})$

log marginal probability를 분해하면

$$\ln p(\textbf{X}) = L(q) - KL(q||p)$$

$$L(q) = \int q(\textbf{Z}) \ln \{ \frac{p(\textbf{X},\textbf{Z})}{q(\textbf{Z})} \} d \textbf{Z} \\ KL(q||p) = \int q(\textbf{Z}) \ln \{ \frac{p(\textbf{Z}|\textbf{X})}{q(\textbf{Z})} \}$$

이전의 EM과 다른 부분은 parameter vector ${\pmb \theta}$가 안보인다는 점이다. 이는 parameter가 이제 stochastic variable이기 때문에 $\textbf{Z}$에 흡수되서 그렇다.

이제 우리가 할 일은 알다시피 KL을 0으로 만들어주는 과정이 필요하다. 이전에는  latent의 posterior와 동일한 분포를 가정하면 됐지만 지금은 해당 분포가 intractable하다고 가정하고 있다. 그렇다면 제약적이지만 최대한 flexible한 분포를 통해 approximation 해야 한다. 그래서 family of approximation distribution을 parametric distribution으로 제한한다. parameter $\omega$로 $p(\textbf{Z}\|\omega)$를 이용하는 것이다. Lower bound가 $\omega$에 대한 식이 되고 이를 optimization하는 problem을 해결한다.

### 10.1.1 Factorized distributions
$\textbf{Z}$의 각 element들을 disjoint하게 나눈다.

$$q(\textbf{Z}) = \prod_{i=1}^{M} q_i (\textbf{Z}_i)$$

이를 바로 이용해보자!

$$L(q) = \int \prod_i q_i(\textbf{Z}_i) \ln \{ \frac{p(\textbf{X},\textbf{Z})}{\prod_i q_i(\textbf{Z}_i)}\} d \textbf{Z}$$

$$ = \int q_j(\textbf{Z} _ j ) \{ \int \ln p(\textbf{X},\textbf{Z})\prod_{i \neq j}q_i(\textbf{Z}_i) d \textbf{Z}_i \} d \textbf{Z}_j - \int q_j(\textbf{Z}_j) \ln q_j (\textbf{Z}_j) + const $$

$$= \int q_j (\textbf{Z}_j) \ln \tilde{p} (\textbf{X},\textbf{Z}_j) d \textbf{Z}_j - \int q_j(\textbf{Z}_j) \ln q_j (\textbf{Z}_j) d\textbf{Z}_j + const$$

$$\text{where}\\; \ln \tilde{p} (\textbf{X},\textbf{Z} _ j) = E_{i \neq j}[\ln p(\textbf{X,Z})]+const $$

$$  E_{i \neq j}[\ln p(\textbf{X,Z})] =  \int \ln p(\textbf{X},\textbf{Z})\prod_{i \neq j}q_i(\textbf{Z}_i) d \textbf{Z}_i $$

negative KL divergence의 형태가 나오고 lower bound $L(q)$를 최대로 올리기 위해서는 

$$q_j(\textbf{Z} _ j) = \tilde{p}(\textbf{X,Z})$$

general expression for the optimal solution $q_j (\textbf{Z}_j)$는

$$ \ln q_j(\textbf{Z} _ j) = E_{i \neq j}[\ln p(\textbf{X,Z})]+const$$

위에서 const는 normalizing const이다. log를 지워보면서 아래와 같은 식이 나오는데 실제로 사용할때는 위의 식이 더 편하다고 한다.

$$q_j(\textbf{Z} _ j) = \frac{\exp(E_{i \neq j}[\ln p(\textbf{X,Z})])}{\int \exp(E_{i \neq j}[\ln p(\textbf{X,Z})])d\textbf{Z}_j}$$

이 식의 값을 구하기 위해서는 iterative하게 반복해야 한다. $\textbf{Z}$의 모든 element에 적절한 초깃값으로 시작한다. $j$를 제외한 나머지 값들로 $q_j$를 구한다. 이 과정을 모든 element에 반복하면 된다. convergence를 이미 증명되어 있다.

### 10.1.2 Properties of factorized approximations
- minimization of $KL(q\|\|p)$ : tend to find one of modes
- minimization of $KL(p\|\|q)$ : resulting approximations would average across all of the modes

### 예시들
- 10.1.3 Example : The univariate Gaussian
- 10.1.4 Model comparison
- 10.2 Illustration: Variational Mixture of Gaussians
- 10.3 Variational Linear Regression
- 10.4 Exponential Family Distributions
- 10.5 Local Variational Methods
- 10.6 Variational Logistic Regression

## 10.7 Expectation Propagation
deteministic한 또 다른 방법을 알아보자. 아래의 KL-Divergence를 최소화해야 하는 상황이다.

$$KL(p||q)$$

- $p(\textbf{z})$ is fixed
- $q(\textbf{z})$ is exponential family

$$q(\textbf{z}) = h(\textbf{z})g({\pmb \eta})\exp\{ {\pmb \eta}^T u(\textbf{z}) \}$$

$\eta$에 대한 함수인 KL을 구하면

$$KL(p||q) = - \ln q({\pmb \eta}) - {\pmb \eta}^T E_p [u(\textbf{z})] + const$$

이를 최소화하기 위해 $\eta$에 대해 미분하여 0으로 하면

$$- \nabla \ln g({\pmb \eta}) = E_p [u(\textbf{z})]$$

임을 알 수 있다. 그런데 우리가 2장에서 공부했던 내용을 이용하면 ($q(z)$를 parameter에 대해 미분) 아래와 같은 사실을 알 수 있다.

$$- \nabla \ln g({\pmb \eta}) = E_q [u(\textbf{z})]\\ \therefore E_q [u(\textbf{z})] = E_p [u(\textbf{z})]$$

따라서 KL를 최소화하는 optimum solution은 충분통계량의 expectation을 이용하는 것이다. 예를 들어, $q(\textbf{z}) \sim N(\mu, \Sigma)$ 이면 KL을 최소화하기 위해 $p(\textbf{z})$의 mean과 covariance와 같아지면 된다. 이를 *moment matching* 이라고 한다.

이제 approximation을 해보자. 많은 probabilistic model들은 
- joint distribution of data $D$ and hidden variables $\theta$ comprises a product of fators in the form
  - 예를 들면, 각 data들은 iid
  - $f_n({\pmb \theta}) = p(\textbf{x}_n \| {\pmb \theta})$
  - $f_0({\pmb \theta})$는 prior

$$p(D, {\pmb \theta}) = \prod_i f_i ({\pmb \theta})$$

우리의 관심은 예측을 위한 posterior distrbution $p({\pmb \theta} \| D)$, model comparison을 위한 model evidence $p(D)$ 이다.

$$p({\pmb \theta}|D) = \frac{1}{p(D)} \prod_i f_i ({\pmb \theta}) \\ p(D) = \int \prod_i f_i({\pmb \theta}) d {\pmb \theta}$$

Expectation propagation은 posterior distribution을 아래와 같이 approximation하는 것이다.
- $\tilde{f}_i$는 각각 이에 해당하는 $f_i$를 approximate
- $Z$는 normalizing constant

$$q({\pmb \theta}) = \frac{1}{Z} \prod_i \tilde{f}_i ({\pmb \theta})$$

$\tilde{f}_i({\pmb\theta})$를 exponential family로 제한할 것이다. 그래서 충분통계량을 이용할 것이다.

지금까지를 통해 KL을 구하면

$$KL(p||q) = KL(\frac{1}{p(D)} \prod_i f_i ({\pmb \theta}) ||\frac{1}{Z} \prod_i \tilde{f}_i ({\pmb \theta}))$$

그런데 KL에 true distribution이 있기에 intractable하다. 이를 위해 우리는 전체가 아닌 개별의 factor에 대해 KL을 최소화할 것이다. 이는 훨씬 간단해졌다. 또한 noniterative하다. 하지만 개별 factor를 approximation하다보니 이들을 다 product하면 좋지 않은 결과가 나올 수도 있다.

factor $\tilde{f}_j({\pmb \theta})$를 구해보자. 먼저 j factor와 나머지 factor로 나누어서 생각한다.

$$q^{new}({\pmb \theta}) \propto \tilde{f} _ j ({\pmb \theta}) \prod_{i \neq j} \tilde{f}_i ({\pmb \theta})$$

위의 식은 아래의 식과 최대한 비슷하게 만든다.

$$f_j ({\pmb \theta}) \prod_{i \neq j} \tilde{f}_i ({\pmb \theta})$$

이를 위해 approximation $q({\pmb \theta})$에서 j를 제외하여 unnormalized distribution을 만들면

$$q^{-j}({\pmb \theta}) = \frac{q({\pmb \theta})}{\tilde{f}_j ({\pmb \theta})}$$

이를 기존 factor와 결합하여
- $Z_j = \int f_j ({\pmb \theta}) q^{-j} ({\pmb \theta}) d{\pmb \theta}$ : normalizing const

$$\frac{1}{Z_j} f_j ({\pmb \theta}) q^{-j} ({\pmb \theta})$$

이를 이용하여 KL을 최소화하여 우리가 구하고자하는 $\tilde{f}_j$를 구해보자.

$$KL( \frac{f_j({\pmb \theta}) q^{-j}({\pmb \theta})}{Z_j} || q^{new}({\pmb \theta}) )$$

이는 $q^{new}({{\pmb \theta}})$가 exponetial family로 만들어졌고 
- 따라서 parameter들이 $\frac{f_j({\pmb \theta}) q^{-j}({\pmb \theta})}{Z_j}$와 expected sufficient statistic을 맞춤으로 구할 수 있다. (*moment matching*)
- exponential family에서 expected statistic은 normalization coefficient의 derivative와 관련이 되어 있기에 더 쉽게 구할 수 있다.

이전에 구했던 식을 이용하면 아래와 같이 구할 수 있다.

$$\tilde{f}_j ({\pmb \theta}) = K \frac{q^{new}({\pmb \theta})}{q^{-j}({\pmb \theta})}$$

$q^{new}({\pmb \theta})$가 normalized되었다는 사실을 이용하면 

$$K = \int \tilde{f}_j ({\pmb \theta}) q^{-j}({\pmb \theta}) d {\pmb \theta}$$

그리고 $K$의 값은 matching zeroth-order moments를 통해 $K=Z_j$라는 것을 알 수 있다. 따라서

$$\tilde{f}_j ({\pmb \theta}) = Z_j \frac{q^{new}({\pmb \theta})}{q^{-j}({\pmb \theta})}$$

이렇게 approximation factor를 적절한 값으로 초기화하고 converge할 때까지 반복한다.

EP의 단점은
- converge한다는 보장이 없다.
- mixture의 경우는 EP가 적절하지 않다.
  - variational inference는 $KL(q\|\|p)$
  - EP는 $KL(p\|\|q)$
  - $p({\theta})$가 multimodal인 경우 EP는 approximation이 잘 안된다.
  - 하지만 logistic-type models 에서는 EP가 좋다고 한다.
