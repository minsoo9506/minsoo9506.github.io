# [PRML] Chapter2 - Probability Distribution (3)


Gaussian Distribution과 관련한 내용을 알아보자.

<!--more-->

### 2.3.6 Bayesian inference for the Gaussian
이번에는 Bayesian의 방법으로 접근해보자.
- $\sigma^2$ is known, inferring the mean $\mu$

likelihood function은

$$p({\bf x} | \mu) = \prod_{n=1}^{N}p(x_n | \mu) = \dfrac{1}{(2\pi\sigma^2)^{N/2}}\exp \\{-\frac{1}{2\sigma^2}\sum_{n=1}^{N}(x_n-\mu)^2 \\}$$

Gaussian의 conjugate prior는 Gaussian이다. 따라서 prior distribution은

$$p(\mu) = N(\mu | \mu_0, \sigma_0^2)$$

posterior distribution은

$$p(\mu |{\bf x}) \propto p({\bf x}|\mu)p(\mu) = N(\mu | \mu_N, \sigma_N^2)$$

- $\mu_N = \frac{\sigma^2}{N\sigma_0^2+\sigma^2}\mu_0 + \frac{N\sigma_0^2}{N\sigma_0^2+\sigma^2}\mu_{ML}$
- $\frac{1}{\sigma_N^2}=\frac{1}{\sigma_0^2}+\frac{N}{\sigma^2}$

위의 결론을 통해 평균과 분산에 대해 좀 더 살펴보자.
- posterior mean
  - prior mean $\mu_0$ 와 MLE solution $\mu_{ML}$ 사이의 값을 갖는다.
  - $N=0$이면 prior mean쪽으로 $N \rightarrow \infty$이면 MLE solution쪽을 가까워 진다.
- posterior variance
  - 해석의 편의를 위해 precision으로 표현하였다.
  - data의 수가 늘어날수록 precision이 커지고 따라서 posterior variance는 작아진다.
  - $N=0$이면 prior variance의 값과 같다. $N \rightarrow \infty$이면 variance가 0으로 가까워진다.

이번에는 $\mu$ is known, inferring the variance $\sigma^2$

$$p({\bf x} | \lambda) = \prod_{n=1}^{N} N(x_n | \mu, \lambda^{-1}) \propto \lambda^{N/2} \exp \\{ -\frac{\lambda}{2} \sum_{n=1}^{N}(x_n-\mu)^2 \\}$$

precision의 posterior의 conjugate prior는 *gamma* distribution이다.

$$Gam(\lambda | a,b)=\frac{1}{\Gamma(a)}b^a\lambda^{a-1}\exp(-b\lambda) $$

이를 이용하여 posterior를 구하면

$$p({\bf x} | \lambda) \propto \lambda^{a_0-1}\lambda^{N/2} \exp \\{-b_0\lambda-\frac{\lambda}{2}\sum_{n=1}^{N}(x_n-\mu)^2\\}$$

- $a_N = a_0 + \frac{N}{2}$
- $b_N = b_0 + \frac{1}{2}\sum_{n=1}^{N}(x_n-\mu)^2 = b_0 + \frac{N}{2}\sigma_{ML}^2$

precision이 아닌 covariance를 바로 이용하는 경우 gamma distribution이 아니라 *inverse gamma* distribution을 이용한다.

이번에는 $\mu, \sigma^2$ 둘 다 모른다고 하자

$$p({\bf x} | \mu, \lambda) = \prod_{n=1}^{N} \(\frac{\lambda}{2\pi} \)^{1/2} \exp \\{-\frac{\lambda}{2}(x_n-\mu)^2\\} \\\ \propto \[\lambda^{1/2}\exp \(-\frac{\lambda\mu^2}{2}\)\]^N\exp\\{\lambda\mu\sum_{n=1}^{N}x_n-\frac{\lambda}{2}\sum_{n=1}^{N}x_n^2\\} $$

parameter가 2개이기에 prior가 $p(\mu, \lambda)$일 것이다. likelihood function의 모양과 $p(\mu, \lambda) = p(\mu \|\lambda)p(\lambda)$를 이용하면

- *Normal-Gamma* distribution

$$p(\mu, \lambda) = N(\mu|\mu_0, (\beta\lambda)^{-1})Gam(\lambda|a,b) $$

을 구할 수 있다.
- independent한 두 식을 곱한게 아니다. Normal의 precision이 $\lambda$에 dependent하다.
- D-dimension인 경우, *Wishart* distribution을 이용한다.

### 2.3.7 Student's t-distribution
위에서 gaussian precision과 관련하여 gamma prior을 이용했다.
- 이때 $x$에 대한 marginal distribution을 구해보자.

$$p(x/\mu,a,b) = \int_{0}^{\infty}N(x/\mu, \tau^{-1})Gam(\tau/a,b)d\tau \\\
=\int_{0}^{\infty}\frac{b^a e^{(-b\tau)}\tau^{(a-1)}}{\Gamma(a)}\(\frac{\tau}{2\pi}\)^{1/2}\exp \\{-\frac{\tau}{2}(x-\mu)^2 \\}d\tau \\\
= \frac{b^a}{\Gamma(a)}\(\frac{1}{2\pi}\)^{1/2}\[b+\frac{(x-\mu)^2}{2}\]^{-a-1/2}\Gamma(a+1/2) $$

- $z = \tau[b+(x-\mu)^2/2]$로 놓고 식을 전개하면

$$St(x/\mu,\lambda,v) = \frac{\Gamma(v/2+1/2)}{\Gamma(v/2)}\left(\frac{\lambda}{\pi v}\right)^{1/2}\left[1+\frac{\lambda(x-\mu)^2}{v}\right]^{-v/2-1/2} $$

이를 *Student's t-distribution* 이라고 한다. $v$는 자유도이며 이 값이 무한대로 갈수록 gaussian distribution에 가까워진다. 이 분포의 특징 중 하나는 robustness 라는 것이다. 분포모양이 gaussian distribution과 비슷하지만 (좌우대칭) 더 긴 꼬리를 갖고 있다. 이 때문에 outlier(이상치)에 대해 덜 민감하다.

### 2.3.8 Periodic variables
skip

### 2.3.9 Mixtures of Gaussians
- *mixture distribution* : linear combination of basic distribution

K개의 gaussian distribution을 중첩하면

$$p({\bf x}) = \sum_{k=1}^{K}\pi_k N({\bf x} | {\bf \mu}_k, \Sigma_k) $$

이를 *Mixture of Gaussian* 이라고 부른다. 이 때
- 각 $N({\bf x} \| {\bf \mu}_k, \Sigma_k)$ 는 component, $\pi_k$는 mixing coefficients 라고 부른다.

$\pi_k$은 0과 1 사이의 값을 갖고 합이 1이다. 따라사 이를 확률로 이해할 수 있다. 이를 통해 다시 marginal distribution을 전개하면

$$p({\bf x}) = \sum_{k=1}^{K}p(k)p({\bf x}|k)$$

그렇다면 이제 parameter 추정을 해보자.
- prameter는 $\pi, \mu,\Sigma$

$$\ln p({\bf X}|{\bf \pi}, {\bf \mu}, \Sigma) = \sum_{n=1}^{N}\ln \\{\sum_{k=1}^{K} \pi_k N({\bf x}_n|{\bf \mu}_k, \Sigma_k) \\}$$

위의 식에서 MLE를 구하기는 쉽지 않다. log 안에 summation이 있기 때문이다. (미분이 어려움) 이를 구하는 방법은 EM algorithm이다. 나중에 배운다.

