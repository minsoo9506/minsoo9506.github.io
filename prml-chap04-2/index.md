# [PRML] Chapter4 - Linear Models For Classification (2)


Classification에 대해 알아보자.

<!--more-->

## 4.3 Probabilistic Discriminative Models
이전과 다르게 parameter 추정을 $p(C_k|x)$에서 Maximum likelihood 를 이용하여 directly 하고자 한다. 이전에 본 generative한 방법에 비해
- parameter가 더 적다
- class-conditional density 가정이 잘못되면 성능이 좋지 않을 수 있다

### 4.3.1 Fixed basis functions
이제부터는 basis function $\phi ({\bf x})$을 사용할 것이다.
- basis function이 비선형이라 decision boudary는 original space에 linear하지 않을 것이다.
- basis function에는 $\phi ({\bf x})=1$ bias를 기본적으로 넣는다.
- original이 아닌 basis function을 사용했다고 항상 결과가 좋은 것은 아니다.

### 4.3.2 Logistic regression
2-class의 경우로 시작해보자. 이전에 공부했듯이 일반적인 가정하에서 posterior는 sigmoid에 linear function of $\phi$ (feature vector) 가 들어간 형태이다.

$$p(C_1 | \phi) = y(\phi) = \sigma ({\bf w}^T \phi)$$

- logistic regression 의 장점
  - (2 class) M 차원이라고 가정하면 M개의 parameter가 있을 것이다. 반면에 generative한 상황을 생각하면 Gaussian class conditional density 의 경우 2M개의 평균, M(M+1) / 2개의 covariance matrix, prior 까지 총 M(M+5)/2+1 개의 parameter가 필요하다.
  - interpretable하다.
  - parameter estimation에 있어 computationally efficient 하다.
  - multiclass도 가능하다.
- 단점
  - prediction performance가 좋은 편은 아니다.
  - basis가 fixed되어 있다.

likelihood로 parameter를 추정하는 과정을 살펴보자.
- Given : $D = [({\bf x}_1,y_1),({\bf x}_2,y_2),..,({\bf x}_n,y_n)]$
- model : $t_i \sim^{iid} \text{Bern}[\sigma({\bf w}^T \phi({\bf x}_i))]$

$$y_n = p(C_1 | \phi_n)=\sigma({\bf w}^T \phi({\bf x}_n))$$

$$p(\textbf{t}|{\bf w}) = \prod_{n=1}^{N}{y_n^{t_n}(1-y_n)^{1-t_n}}$$

- $\textbf{t} = (t_1,...t_N)^T$ : true target
- cross-entropy error function  :

$$E({\bf w}) = - \ln p(\textbf{t} | {\bf w}) = - \sum{ \{ t_n \log y_n + (1-t_n)\log(1-y_n) \} }$$

- ${\bf w}$에 대해 미분하면

$$\bigtriangledown E({\bf w}) = \sum_{n=1}^{N}{(y_n - t_n)\phi_n}$$

- 이를 구하는 방법은 chain rule을 사용한다. 아래의 값들을 곱하면 위의 식이 나온다.

$$\frac{\partial E}{\partial y_n} = \frac{1-t_n}{1-y_n} - \frac{t_n}{y_n} = \frac{y_n-t_n}{y_n(1-y_n)}$$
$$\frac{\partial y_n}{\partial a_n} = \frac{\partial \sigma(a_n)}{\partial a_n} = \sigma(a_n)(1-\sigma(a_n)) = y_n(1-y_n)$$
$$ \frac{\partial a_n}{\partial {\bf w}} = \phi_n$$

- 이전에 linear regression과는 다르게 MLE가 closed form으로 존재하지 않는다. 따라서 approximation하는 방법이 필요하다.
- 이를 Gradient descent 방법을 통해 답을 구할 수도 있다. 하지만 뒤에서는 약간 다른 방법으로 해결해본다. (전통적인 통계방법)

### 4.3.3 Iterative reweighted least squares

+일단은 교재의 내용을 보기 전에 이해를 돕기 위해 추가 설명을 한다.

우리는 logL를 미분했을 때, 이를 0으로 만드는 MLE를 찾고싶다.
- $g'(x)$ 는 미분가능
- $g''(x) \neq 0$

위의 조건을 만족하는 경우 taylor expansion을 이용하여 (1차 근사)

$$0 = g'(x) \approx g'(x^{t}) + (x-x^t)g''(x^t)$$

이를 정리하면

$$x = x^t - \frac{g'(x^t)}{g''(x^t)}$$

이제 교재의 내용을 살펴보자.  
logisitc regression은 sigmoid function의 non-linearlity 때문에 closed-form의 해를 구할 수 없다. 그래서 우리는 error function의 최소화하는 방법으로 **Newton-Raphson iterative opimization** algorithm을 사용한다.

$${\bf w}^{new} = {\bf w}^{old} - {\bf H}^{-1} \bigtriangledown E({\bf w})$$

- ${\bf H}$ : hessian matrix whose elements comprise the second derivatives of $E({\bf w})$ with respect to the component of ${\bf w}$

$$\bigtriangledown E({\bf w}) = \sum_{n=1}^{N}{(y_n - t_n)\phi_n} = \Phi ^T (\textbf{y}-\textbf{t})$$

$${\bf H} = \bigtriangledown \bigtriangledown E({\bf w}) =   \sum_{n=1}^{N}{y_n(1-y_n)\phi_n \phi_n^T} = \Phi^T\textbf{R}\Phi$$

$\Phi^T\textbf{R}^{1/2} \textbf{R}^{1/2} \Phi =(\textbf{R}^{1/2} \Phi)^T (\textbf{R}^{1/2} \Phi)$ 이기에 positive semi definite이고 이를 통해 $E({\bf w})$가 convex하다는 것을 알 수 있다.

- $\textbf{R}$ : N*N diagonal matrix with elements $R_{nn} = y_n(1-y_n)$
  - $y_n$의 식이므로 parameter ${\bf w}$에 dependent하다. 따라서 ${\bf R}$에 대해서도 iterative하게 업데이트가 필요하다.

아래처럼 iterative하게 parameter를 업데이트 한다.

$${\bf w}^{(new)} = {\bf w}^{(old)} - (\Phi^T{\bf R}\Phi)^{-1}\Phi^T({\bf y}-{\bf t})$$
$$= (\Phi^T{\bf R}\Phi)^{-1}\\{\Phi^T{\bf R}\Phi{\bf w}^{(old)}-\Phi^T({\bf y}-{\bf t})\\}$$
$$= (\Phi^T{\bf R}\Phi)^{-1}\Phi^T{\bf R}{\bf z}$$

$${\bf z} = \Phi{\bf w}^{(old)} - {\bf R}^{-1}({\bf y}-{\bf t})$$

마지막 줄을 보면 이 형태는 weighted least-square 문제에서의 normal equation의 형태이다. 하지만 ${\bf R}$이 상수가 아니기에 iterative하게 답을 구해야 하고 이러한 이유로 *iterative reweighted least square* 라고 부른다.
- ${\bf R}$의 대각성분을 variance라고 해석할 수도 있다.
  - 대각성분이 $y_n(1-y_n)$ 인데 이는 $t_n$의 variance이기 때문이다.

### 4.3.4 Multiclass logistic regression
위에서 본 binary와 똑같이 할 수 있다. multiclass에서는 softmax function을 이용한다.

$$p(C_k|\phi) = y_k(\phi) = \frac{\exp(a_k)}{\sum_j \exp(a_j)}$$

likelihood function을 구하면

$$p({\bf T}|{\bf w} _ 1,...{\bf w} _ K) = \prod_{n=1}^{N}\prod_{k=1}^{K} p(C_k|\phi_n)^{t_{nk}} = \prod_{n=1}^{N}\prod_{k=1}^{K}y_{nk}^{t_{nk}}$$

negative log를 취하면

$$E({\bf w} _ 1, ..., {\bf w} _ K) = -\ln p({\bf T}|{\bf w} _ 1, ...,{\bf w} _ K) = - \sum_{n=1}^{N} \sum_{k=1}^{K} t_{nk} \ln(y_{nk})$$

똑같이 미분을 취하고 Gradient descent나 IRLS 방법을 통해 parameter를 추정한다.

$$\nabla_{ {\bf w} _ j } E({\bf w} _ 1, ..., {\bf w} _ K) = \sum_{n=1}^{N} (y _ {nj} - t _ {nj}) \phi_n $$

### 4.3.5 Probit regression
이전과 마찬가지로 generalized linear model의 형태

$$p(t=1|a)=f(a)=f({\bf w}^T \phi)$$

를 유지하지만 조금 다른 activation function을 알아보자.
- link function으로 noisy threshold model을 생각해보면
  - $t_n = 1 \text{ if } a_n\ge \theta $
  - $t_n=0 \text{ otherwise}$

$\theta$는 random variable이고 probability density가 $p(\theta)$라고 하자. 이에 따라 activation function을 CDF형태

$$f(a) = \int_{-\infty}^{a}p(\theta)d\theta$$

로 표현할 수 있다. probability density를 $N(0,1)$로 가정하면

$$\Phi(a) = \int_{-\infty}^{a}N(0, 1)d\theta $$

이고 이를 *probit* function이라고 한다. 모양은 sigmoid function과 거의 유사하다. 이를 모델에서 사용할 때는 약간 다른 모습을 이용한다. (계산의 편리함 때문인듯) *erf function* 은

$$erf(a) = \frac{2}{\sqrt{\pi}} \int_{0}^{a} \exp(-\theta^2) d\theta$$

이를 통해 탄생한 generalized linear model을 *probit regression* 이라고 한다.

$$\Phi(a) = \frac{1}{2} \\{1+erf\left(\frac{a}{\sqrt{2}}\right)\\}$$

probit은 뒤에 나올 Bayesian logistic regression에서 사용된다.

- logistic, probit regression 모두 outlier에 취약한 편이다.
  - 근데 probit은 $exp(-x^2)$이 있어서 더 취약하다.
- data가 mislabelling된 경우, 새로운 probability $\epsilon$을 추가하여 사용할 수 있다.

$$p(t|{\bf x}) = (1-\epsilon)\sigma({\bf x}) + \epsilon(1-\sigma({\bf x})) = \epsilon + (1-2\epsilon)\sigma({\bf x})$$

## 4.4 The Laplace Approximation
4.5장에서 logistic regression에 Bayesian 방법을 사용하고자 한다. 근데 ${\bf w}$의 posterior가 더 이상 Gaussian이 아니기 때문에 integrate하기가 어렵다. 따라서 특정 범위에 있는 함수를 Gaussian으로 approximation하는 방법을 이용하고자 한다. 먼저 single variable의 경우부터 살펴보자.

- Suppose the distribution $p(z)$ is defined by
$$p(z) = \frac{1}{Z}f(z),\;\; Z = \int f(z)dz$$

우리의 목표는 $p(z)$의 mode를 중앙(평균)으로 갖는 Gaussian distribution을 approximation하는 것이다.
- 먼저, mode를 찾아야한다.

$$p'(z_0) = 0$$

- Taylor expansion

$$\ln f(z) \simeq \ln f(z_0) - \frac{1}{2}A(z-z_0)^2$$

$$A=-\left.\dfrac{d^2}{dz^2}\ln f(z)\right|_ {z=z_0} $$

따라서

$$f(z) \simeq f(z_0) \exp \{ - \frac{A}{2}(z-z_0)^2\}$$

$$q(z) = (\frac{A}{2\pi})^{1/2} \exp \{ -\frac{A}{2}(z-z_0)^2 \}$$

- 우리는 $p(z)$를 approximate한 Gaussian $q(z)$를 찾을 수 있다! 이 과정이 *Laplace approximation* 이다.
- Gaussian approximation에서  ($f(z)$를 두 번 미분하여 $z_0$를 대입) precision $A$는 양수이다. 따라서 $z_0$는 local maximum이다.

이제 다차원의 형태로 살펴보자.
- Hessian Matrix  $\textbf{A} = - \bigtriangledown \bigtriangledown \ln f(\textbf{z}_0)$
- $f(\textbf{z}) \simeq f(\textbf{z}_0) \exp \{ -\frac{1}{2} (\textbf{z} - \textbf{z}_0)^T \textbf{A} (\textbf{z}-\textbf{z}_0) \}$

$$q({\bf z}) = \dfrac{|{\bf A}|^{1/2}}{(2\pi)^{M/2}}\exp\\{-\dfrac{1}{2}({\bf z}-{\bf z}_0)^T{\bf A}({\bf z}-{\bf z}_0)\\} = N( {\bf z}_0, {\bf A}^{-1})$$

- Laplace approximation 특징
  - Mutimodal인 distribution은 다양한 Laplace approximation이 생길 수 있다.
  - CLT에 의해 Laplace approximation은 data가 많을수록 좋다.
  - 위에서 알 수 있는이 $Z$에 대해 알 필요가 없다.
  - Gaussian에 기반하므로 실수 변수에만 사용이 가능하다.
  - global한 특징을 잡기 어렵다.

### 4.4.1 Model comparison and BIC
normalization constraint $Z$에 대해 approximation해보자.

$$Z = \int f({\bf z})d{\bf z} \simeq f({\bf z}_0)\int\exp\\{\dfrac{1}{2}({\bf z}-{\bf z}_0)^T{\bf A}({\bf z}-{\bf z}_0)\\}d{\bf z}=f({\bf z}_0)\dfrac{(2\pi)^{M/2}}{|{\bf A}|^{1/2}}$$

우리는 이 결과를 통해 이전에 공부했던 Bayesian model comparison에서 model evidence를 approximation해볼 것이다.
- model evidence $p(D\|M_i)$
  - $M_i$ 생략

  $$p(D)=\int p(D|{\pmb \theta})p({\pmb \theta})d{\pmb \theta}$$

- 아래와 같이 정의하고 우리는 model evidence를 approximation하면
  - $f({\pmb \theta}) = p(D\|{\pmb \theta})p({\pmb \theta})$
  - $Z = p(D)$

  $$\ln p(D)\simeq \ln p(D|{\pmb \theta} _ {MAP}) + \ln p({\pmb \theta} _ {MAP}) + \dfrac{M}{2}\ln(2\pi) - \dfrac{1}{2}\ln|{\bf A}| $$

- 첫번째 term은 log likelihood evaluated using the optimized parameters
- 두번째 term부터 마지막 term까지 *Occam factor* 라고 부른다.
  - penalizes model complexity
- ${\pmb \theta}_{MAP}$ : mode of posterior distribution
- ${\bf A}$ : Hessian matrix

$${\bf A} = - \nabla\nabla p(D|{\pmb \theta} _ {MAP})p({\pmb \theta} _ {MAP}) = -\nabla\nabla\ln p({\pmb \theta} _ {MAP}|D)$$

model evidence를 approximation한 식에서
- Gaussian prior가 broad하고
- Hessian이 full rank이면

우리는 해당 식을 더 간단하게 (의미없는 상수 생략)

$$\ln p(D) \simeq \ln p(D|{\bf \theta}_{MAP}) - \frac{1}{2}M\ln N$$

이는 *BIC(Baysian Information Criterion)* 이다.
- $M$은 parameter의 갯수, $N$은 data의 수를 의미한다.
- AIC보다 더 간단한 모델을 추구한다.
- BIC를 쉽게 계산할 수 있지만 full rank라는 가정이 만족하기 쉽지 않아서 한계가 존재한다.

## 4.5 Bayesian Logistic Regression
Logistic regression에 Bayesian적으로 접근해보자.

### 4.5.1 Laplace approximation

일단 prior는 Gaussian으로 가정한다.

$$p({\bf w}) = N( \textbf{m}_0 , \textbf{S}_0)$$

이제 posterior를 구해보자.

$$p(\textbf{w}| \textbf{t}) \propto  p(\textbf{w})p(\textbf{t}|\textbf{w})$$

양변에 log를 취하면

$$\ln p(\textbf{w} | \textbf{t}) = - \frac{1}{2}(\textbf{w}-\textbf{m} _ 0)^T \textbf{S} _ 0^{-1} (\textbf{w} - \textbf{m} _ 0 )$$

$$+ \sum_{n=1}^{N}{\\{ t_n \ln y_n + (1-t_n)\ln (1-y_n) \\}+ const}$$

posterior에 대한 Gaussian approximation하였다고 가정하자. maximize하는 parameter를 ${\bf w}_{MAP}$라고 하고 covariance는

$${\bf S}_N^{-1} = -\nabla\nabla \ln p({\bf w}|{\bf t}) = {\bf S} _ 0^{-1} + \sum _ {n=1}^{N} y_n(1-y_n)\phi_n\phi_n^T$$

따라서 Gaussian approximation한 posterior distribution의 form은

$$q(\textbf{w}) = N(\textbf{w}_{MAP} , \textbf{S}_N)$$

이제 approximation하여 구한 posterior로 Predictive를 구해보자.
### 4.5.2 Predictive distribution
2-class의 경우라고 가정하자. predictive distribution은

$$p(C_1 |  \phi, \textbf{t} ) = \int p(C_1 | \phi, \textbf{w})p(\textbf{w} | \textbf{t}) \simeq \int \sigma (\textbf{w}^T\phi) q(\textbf{w})d\textbf{w}$$

- Funtion $\sigma ({\bf w}^T \phi)$ depends on w only through tis projection onto $\phi$
- (교재에 설명이 다소 빈약) 그냥 아래처럼 변형

$$\sigma({\bf w}^T\phi) = \int \delta(a-{\bf w}^T\phi)\sigma(a)da$$

이를 predictive distribution에 대입하면

$$\int \sigma({\bf w}^T\phi)q({\bf w})d{\bf w} = \int \sigma(a)p(a)da$$

$$p(a) = \int \delta(a-{\bf w}^T\phi)q({\bf w})d{\bf w}$$

$p(a)$는 Gaussian distribution이 되는데
- delta function ($\delta$) imposes a linear constraint on ${\bf w}$이고
-  $q({\bf w})$ 는 정의에 의해 Gaussian distribution
- Gaussian의 marginal도 Gaussian

$$\mu_a = E[a] = \int p(a)a da = \int q({\bf w}){\bf w}^T \phi d{\bf w} = {\bf w}_{MAP}^T\phi$$

$$\sigma_a^2 = var[a] = \int p(a)\{ a^2 - E[a]^2 \}da $$
$$= \int q({\bf w}) \{({\bf w}^T\phi)^2 - ({\bf m}_N^T\phi)^2 \}d{\bf w} = \phi^T{\bf S}_N\phi$$

따라서 predictive distribution은

$$p(C_1|{\bf t}) = \int \sigma(a)p(a)da = \int \sigma(a)N(\mu_a, \sigma_a^2)da$$

sigmoid-gaussian을 analytically 구할 수 없기 때문에 이 또한 approximation을 해야한다. sigmoid와 비슷한 모양을 가지는 Probit function을 이용한다. ($\sigma(a) \approx \Phi(\lambda a) ,\lambda^2 = \pi / 8$)
- probit function을 이용한 approximation의 장점은 Gaussian과 만나서 analytically 또 probit function으로 아래와 같은 결과가 나온다.

$$\int \Phi (\lambda a )N ( \mu, \sigma^2)da = \Phi (\frac{\mu}{(  \lambda^{-2}+\sigma^2 ) ^{1/2}})$$

$$\int \sigma(a) N(\mu,\sigma^2)da \simeq \sigma (k(\sigma^2)\mu)$$

- $k(\sigma^2) = (1+\pi \sigma^2 / 8)^{-1/2}$

최종 결과 approximate predictive distribution은

$$p(C_1 | \phi, \textbf{t}) = \sigma (k(\sigma^2_a)\mu_a)$$
