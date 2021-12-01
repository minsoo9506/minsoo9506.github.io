# [PRML] Chapter3 - Linear Models For Regression (1)


Regression에 대해 알아보자.

<!--more-->

- 목표는 predictive distribution $p(t\|x)$를 찾는 것
- 주로 loss funciton은 squared loss를 사용하며 이 때 optimal solution은 conditional expectation of t : $E[t\|x]$

## 3.1 Linear Basis Function Models
가장 기본적인 linear model for regression은

$$y(x,w) = w_0+w_1x_1+...+w_D x_D$$

의 형태일 것이다. 하지만 basis function $\phi_ j(\textbf{x})$을 이용하여 nonlinear의 성질을 추가할 수 있다.
- basis function은 다양하다.
  - gaussian distribution의 형태
  - polynomial의 형태
  - 원래의 input data를 마음대로 변화가능

$$y(\textbf{w},\textbf{x}) = w_0 + \sum_{j=1}^{M-1}{w_j \phi_j(\textbf{x})} = \textbf{w}^T {\pmb \phi}( \textbf{x})$$

하지만 여전히 linear model이다. 여기서 linear의 의미는 계수 w에 linear하다는 의미이기 때문이다. 그렇기에 여전히 interpretation에 대한 장점은 갖고 있다. 단점은 너무 단순하다는 것이다.

### 3.1.1 Maximum likelihood and least sqaures
- $t$ : target variable
- $y(\textbf{x}, \textbf{w})$ : deterministic funciton
- $\epsilon \sim N(0, \beta^{-1})$ : noise

$$t = y(\textbf{x}, \textbf{w})+ \epsilon$$

$$p(t | \textbf{x},\textbf{w},\beta) = N(y(\textbf{x}, \textbf{w}), \beta^{-1})$$

위의 gaussian 가정에서는 parameter $w$를 추정할 때, likelihood를 이용하는 것과 least square의 방법을 이용하는 것이 똑같다. (그 과정은 직접 해보면 쉽게 파악가능, chapter1에도 있다)

- optimal prediction은 conditional mean of the target variable 이므로
  - unimodal이라는 한계가 존재

$$E[t | {\bf x}] = \int tp(t | {\bf x})dt = y({\bf x}, {\bf w}) $$

이제 likelihood function을 통해 MLE를 구하는 과정을 간단히 살펴보자.

$$\ln{p({\bf t}|{\bf w}, \beta)} = \sum_{n=1}^{N}\ln{N( {\bf w}^T{\pmb \phi}(x_n), \beta^{-1})}\\\
=\dfrac{1}{2}\ln{\beta}-\dfrac{1}{2}\ln{2\pi}-\beta{E_D({\bf w})}$$

$$E_D({\bf w})=\dfrac{1}{2}\sum_{n=1}^{N}\{t_n-{\bf w}^T {\pmb \phi}(x_n)\}^2$$

위의 식을 미분하고 정리하면 ($\Phi$ : N*M design matrix)
- normal equation을 얻는다.

$${\bf w}_{ML} = (\Phi^T\Phi)^{-1}\Phi^T{\bf t} $$

- bias : $w_0 = \bar{t} - \sum_{j=1}^{M-1}{w_j \bar{\phi}_j}$
  - 실제 얻어지는 샘플들의 타겟 값들의 평균과,
이 때 basis function에 parameter를 곱하여 얻어진 결과의 평균값의 차이를 보정하는 역할
- noise precision : $\frac{1}{\beta_{ML}} = \frac{1}{N}\sum_{n=1}^{N}{\{ t_n - w_{ML}^T \phi(x_n)\}^2}$

### 3.1.2 Geometry of least squares
- least square의 방법에서 우리가 prediction한 값의 의미를 기하학적으로 살펴보자. 증명의 과정은 ESL에 잘 나와있다. 물론 봐도 이해하기는 어렵다. 결론만 언급하자면 **"input vector가 span하는 space에 true t의 값을 orthorgonal하게 projection한 값이 우리가 예측한 t의 값이다"**
- 추가적으로 multicolinearity에 대한 해결책으로는 PCA, SVD와 같은 방법으로 input들을 orthorgonal하게 만들어주는 것과 ridge regression과 같이 regulrarization 항이 있는 모델을 쓰는 것이다.

### 3.1.3 Sequential learning
이 부분에서는 parameter를 최적화하는 과정에 있어서 gradient descent의 방법을 말하고 있다. 그게 Sequential하게 update하는 것이라 그런 것 같다. 데이터의 크기가 크면 normal equation의 방법이 오래걸리는 단점을 보완할 수도 있다.

$$\textbf{w}^{\tau+1}=\textbf{w}^{(\tau)}+{\eta}(t_n-{\bf w}^{(\tau)T}{\pmb \phi}_n) {\pmb \phi}_n$$

### 3.1.4 Regularized least squares
기존의 error function에 regularization term을 추가하여서 *parameter shrinkage*를 하고자 한다. 이를 통해 overfitting을 완화시킨다. lasso 같은 경우 sparse한 model을 만들어서 feature selection의 역할도 한다.

- regularized error takes the form
  - 아래 식에서 q가 1이면 lasso, 2이면 ridge regression이다.
  - $\lambda$가 커질수록 model complexity가 낮아진다.

$$\frac{1}{2}\sum_{n=1}^{N}{\{t_n - \textbf{w}^T{\pmb \phi}(x_n)\}^2}+ \frac{\lambda}{2}\sum_{j=1}^{M}{ \left| \textbf{w}_j\right|^q }$$

- ridge의 경우 error function이 $\textbf{w}$에 대해 quadratic form이라서 closed form으로 solution이 존재한다.

$$w_{ridge} = (\Phi^T \Phi + \lambda I)^{-1}\Phi^T t$$

## 3.2 The Bias-Variance Decomposition
모델링을 할 때 overfitting을 피하기 위해 제약을 두면 complexity를 못 잡을 수도 있다. 너무 모델을 복잡하게 하면 overfitting이 될 수도 있다. 이는 상당히 어려운 문제이다. 이 부분에 있어서 frequentist의 입장에서 바라보는 bias-variance trade off 관계를 공부하고자 한다. 이해를 위해 square loss (regression)의 경우의 예시를 살펴보자.

- square loss function 에서 optimal solution :

$$E[t | \textbf{x}] = \int t p(t | \textbf{x})dt  = h(\textbf{x})$$

- expected squared loss :

$$E[L] = \int \{ y(\textbf{x}) - h(\textbf{x})\}^2 p(\textbf{x})d\textbf{x} + \int \{h(\textbf{x}) - t\}^2 p(\textbf{x},t)d\textbf{x}dt$$

우리는 우항의 첫번째를 최대한 작게하는 $y(\textbf{x})$을 만들고자 한다. 위의 식에서 우항의 두번째는 우리가 줄일 수 없는 intrinsic noise이다. 첫 번째 항을 decompose 해보자. 

- 일단 $\{ y(\textbf{x};D) - h(\textbf{x})\}^2$ 값은 특정한 dataset $D$에 대한 값이다. 이제 dataset이 여러개가 있다고 가정하고 이에 대해 average한 경우를 생각해보자.

- $E_D[y(\textbf{x};D)]$ 을 더하고 빼서

$$E_D[\{ y(\textbf{x};D)-h(\textbf{x}) \}^2] =\\ \{E_D[y(\textbf{x};D)] -h(\textbf{x})\}^2+ E_D[\{ y(\textbf{x};D) - E_D[y(\textbf{x};D)]\}^2]$$

이렇게 나타낼 수 있다. 즉, **expected loss = (bias)^2 + variance +noise** 인 것이다.

- bias 의미 : average prediction over all datasets 이 우리가 알고 싶은 true (regression) function과 차이나는 정도
- variance 의미 : 해당 하나의 dataset이 average 와 차이나는 정도, function $y(\textbf{x};D)$이 특정한 dataset에 얼마나 민감한지
- 이 둘은 **trade-off** 관계 : 한쪽이 커지면 한쪽이 작아진다.

하지만 이런 **bias-variance의 관계는 average에 기반을 한 개념이기 때문에(bias, variance의 계산하는 과정이 D에 대해 평균) 한계점이 분명 존재** 한다. 우리가 가지고 있는 데이터는 한정적이기 때문이다. 독립적인 데이터가 여러 개이면 각 데이터로 복잡한 모델을 만들어서 평균을 내면 좋은 결과를 얻을 수 있지만 우리는 데이터가 부족하다. 그래서 저자는 Bayesian 접근법을 소개한다.

### + monk의 설명
- 정의
  - MSE of an estimate $\hat{\theta} = f(D)$ for $\theta$ is

  $$MSE(\hat{\theta}) = E[(\hat{\theta} - \theta)^2 | \theta]$$

- $bias(\hat{\theta}) = E[\hat{\theta}] - \theta$
- $var(\hat{\theta}) = E[(\hat{\theta}-E[\hat{\theta}])^2]$

$$MSE(\hat{\theta}) = bias^2(\hat{\theta}) + var(\hat{\theta})$$

- (proof)
  - let $\mu = E[\hat{\theta}]$

$$E[(\hat{\theta} - \theta)^2] = E[(\hat{\theta} - \mu + \mu -\theta)^2] \\\ = E[(\hat{\theta} - \mu)^2 + 2(\hat{\theta} - \mu)(\mu - \theta) + (\mu - \theta)^2] \\\ = (\mu - \theta)^2 + E[(\hat{\theta}-\mu)^2] \quad \because E[(\hat{\theta} - \mu)(\mu - \theta)] = 0 $$

- 쉬운 예시
  - $X \sim N(\theta,1)$
  - $\theta$는 non random, unknown
  - $\hat{\theta}_1 = X \rightarrow bias^2 = 0, var = 1, MSE = 1$
  - $\hat{\theta}_2 = 0 \rightarrow bias^2 = \theta^2, var = 0, MSE = \theta^2$

### + 문일철 교수님의 설명
- Sources of Error in ML
  - 크게 두 가지로 나눌 수 있다 : Approximation and generalization
- $E_{out} \le E_{in} + \Omega$
  - $E_{out}$ : estimation error
  - $E_{in}$ : error from approximation by the learning algorithm
  - $\Omega$ : error caused by the variance of the observations

뒤에서 사용할 notation에 대해 알아보자.
- $f$ : the target function to learn (true function)
- $g$ : the learning function of ML
- $g^{(D)}$ : the learned function by using a dataset
- $\bar{g}$ : the average hypothesis of a given infinite numbers of D ( $\bar{g}(x) = E_D [g^{(D) } (x)]$ )

하나의 dataset D에 대한 Error는

$$E_{out}[g^{(D)}(x)] = E_x[(g^{(D)}(x) - f(x))^2]$$

그렇다면 expected error of the infinite dataset은

$$E_D [E_{out}[g^{(D)}(x)] ] = E_D [E_x[(g^{(D)}(x) - f(x))^2]] = E_x [E_D[(g^{(D)}(x) - f(x))^2]]$$

일단 안쪽에 있는 term부터 확인해보자.

$$E_D[(g^{(D)}(x) - f(x))^2] = E_D [( g^{(D)}(x) - \bar{g}(x) + \bar{g}(x)  -  f(x) )^2]$$

$$= E_D [(g^{(D)}(x) - \bar{g}(x) )^2] + (\bar{g}(x)  -  f(x))^2$$

$$\therefore E_D [E_{out}[g^{(D)}(x)] ]  = E_D [(g^{(D)}(x) - \bar{g}(x) )^2] + (\bar{g}(x)  -  f(x))^2 $$

여기서 우리는 variance와 bias를 정의할 수 있는데
- $Var = E_D [(g^{(D)}(x) - \bar{g}(x) )^2]$
- $Bias^2 = (\bar{g}(x)  -  f(x))^2$

이들이 의미하는 바는
- var는 제한적인 dataset 때문에 model을 average hypothesis로 훈련시킬 수 없는 부분을 의미
- bias는 average hypothesis조차도 (true) real world hypothesis를 맞출수 없는 부분을 의미

그렇다면 var과 bias를 줄이기 위해서는?
- **var를 줄이기 위해서는 data를 더 모은다.**
- **bias를 줄이기 위해서는 더 복잡한 model을 사용한다.**

하지만 문제는 var와 bias는 trade-off 관계를 가진다. 예를 들어, 우리가 갖고 있는 dataset에 잘 맞는 복잡한 모델을 사용하면 평균적인 모델과는 차이가 커질 것이다.
- 간단한 model은 낮은 variance, 높은 bias를 갖는다.
- 복잡한 model은 높은 variance, 낮은 bias를 갖는다.

따라서 적잘한 model을 만드는 것이 관건이다.

- Occam's Razor
  - **같은 error를 갖는 모델이라면 둘 중 더 간단한 모델을 선택하라!**
