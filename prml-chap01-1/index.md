# [PRML] Chapter1 - Introduction (1)


Probability Theory에 대해 알아봅시다.

<!--more-->

## 1.1 Example : polynomial curve fitting
- 예를 들어, 회귀에서 error function이 quadratic function of w이면 w에 대한 미분은 w에 linear하고 unique한 closed form의 해를 구할 수 있다.  
- 모델의 overfitting을 항상 조심하고 데이터의 수가 늘어날수록 그 정도는 약해진다. MLE 방법은 overfitting에 취약하며 Bayesian 모델링으로 보완할 수 있다.
- ridge와 같이 error function에 패널티항을 추가하여 overfitting을 막는 방법도 있다. 이를 shrinkage 방법이라 부른다. (딥러닝에서는 weight decay)
- 이런 모델의 복잡한 정도를 정하는 데에 validation data set을 만들기도 하는데 이는 다소 낭비이므로 다른 방법을 공부할 것이다. (아마 Bayesian approach일듯)

## 1.2 Probability Theory
패턴인식에서 가장 중요한 컨셉은 uncertainty이다. Probability Theory는 이런 uncertainty을 quantification하고 manipulation하는 방법을 제시한다. (확률을 이용하여)

- The rules of Probability
  - sum rule : $p(X) = \sum_{Y}{p(X,Y)}$
  - product rule : $p(X,Y) = p(Y\|X)p(X)$

- Bayes' Throrem (rule)
  - $p(Y\|X) = \frac{p(X\|Y)p(Y)}{p(X)}$
  - $p(Y)$ : prior probability
  - $p(Y\|X)$ : posterior probability

### 1.2.1 Probability densities
- probability density : if the probability of a real-valued variable $x $ falling in the interval $(x, x+\delta x )$ is given by $p(x)\delta x$ for $\delta x \rightarrow 0$, then $p(x)$ is called the *probability density*
  - 값은 항상 0 이상, 합하면 1을 가진다.

### 1.2.2 Expectations and covariances
- expection of $f(x)$ : $E[f(x)] = \int{p(x)f(x)dx}$
  - it can be approximated as

$$E[f] \approx \frac{1}{N}\sum_{n=1}^{N}{f(x_n)}$$

- $E_x [f(x,y)]$는 y에 대한 함수이다. x에 대해 averaged over 된 것이다.
- conditional expection :  $E[f(x)\|y] = \int{p(x\|y)f(x)dx}$
- variance of $f(x)$ : $var[f] = E[(f(x) - E[f(x)])^2]$
  - $f(x)$가 mean 주위에서 얼마나 variability가 있는지 보여준다.

### 1.2.3 Bayesian probabilities
우리가 일반적으로 알고 있는 확률(probability)은 frequentist의 견해이다. bayesian은 frequentist와는 아예 다른 접근법을 갖는다.
- Frequentist
  - 분모가 되는 전체 사건이 무한대로 일어나고 우리가 궁금한 사건이 그 중 몇번 일어나는지를 확률로 생각한다.
  - parameter 추정이 목표이며 parameter는 fixed 되어 있다고 생각한다.
  - 주로 estimator로서 likelihood function을 최대화하는 MLE로 사용한다.
- Bayesian
  - 확률 : uncertainty를 quantification한 것으로 생각한다.  
  - parameter는 fixed 된 것이 아니라 (probability) distribution을 갖는 것이라고 생각한다.
  - posterior distribution을 찾는 것이 목표이다.

- Bayes' theorem

$$p(\textbf{w} | D) = \frac{p(D | \textbf{w})p(\textbf{w})}{p(D)}$$

  - parameter에 대해 원래 갖고 있던 믿음을 data D에 대한 정보를 얻은 뒤에 posterior probability로 업데이트 한다. (분모는 posterior가 합이 1이 되기 위한 normalization constant)
  - prior probability : $p(w)$
  - likelihood function : $p(D/w)$
  - posterior probability : $p(w/D)$
  - posterior $\propto$ likelihood * prior

### 1.2.4 The Gaussian distribution

$$N(x | \mu, \sigma^2) = \frac{1}{\sqrt{2\pi \sigma^2}}exp\{ - \frac{(x-\mu)^2}{2 \sigma^2} \}$$

- mean : $\mu$
- variance : $\sigma^2$
- standard deviation : $\sigma$
- precision : $\beta = 1/ \sigma^2$
- normal (gaussian) 분포는 mode와 mean이 같다.

i.i.d (independent and identically distributed : data point가 독립적이고 같은 분포에서 나왔다) 인 경우, likelihood function은

$$p(\textbf{x} | \mu, \sigma^2) = \prod_{n=1}^{N}{N(x_n | \mu, \sigma^2)}$$

이고 이를 최대화하는 mean과 variance의 MLE는 sample mean, sample variance이다. MLE를 구하는 방법은 likelihood function에 log를 취한 후 미분하여 0을 만족하는 parameter를 찾으면 된다. 이때 단점은 maximum likelihood 접근법이 분포의 variance를 underestimate한다(bias 발생)는 점이다. N이 커지면 문제가 없지만 복잡한 모델에서는 이런 bias때문에 문제가 발생할 수 있다. (나중에 공부한다)

### 1.2.5 Curve fitting re-visted
 data를 통해 polynomial curve를 fitting해보자. target t에 대한 uncertainty를 probability를 통해 표현하면 (under gaussian noise distribution)

 $$p(t | x, \textbf{w}, \beta) = N(t | y(x,\textbf{w}), \beta^{-1})$$

위의 식을 이용하여 우리는 parameter $\textbf{w}$ 추정한다. likelihood를 최대로 하는 MLE를 찾으면 되는 것이다. log likelihood function은 아래와 같은 모양을 갖는다.

$$\ln p(\textbf{t} | \textbf{x}, \textbf{w}, \beta) = - \frac{\beta}{2}\sum_{n=1}^{N}{\{ y(x_n, \textbf{w}) - t_n \}^2} + \frac{N}{2}\ln \beta - \frac{N}{2}\ln (2 \pi)$$

위 값을 최대화 하는 $\textbf{w}_{ML}$을 찾으면 된다. 이는 결국 least square 방법과 동일한 의미를 갖는다. (추정선과 target의 차이를 최소화해야되므로) parameter를 추정한 뒤에 이제 prediction을 해야한다. 우리는 확률모델을 갖고 있기에 t에 대한 point estimate만이 아니라 predictive distribution을 만들 수 있다.

$$p(t | x, \textbf{w} _ {ML}, \beta _ {ML}) = N(y(x,\textbf{w} _ {ML}), \beta _ {ML}^{-1})$$

지금까지는 frequentist의 영역이였다면  Bayesian들은 어떤 접근을 하는지 살펴보자. 일단 우리가 추정해야하는 parameter에 대한 prior를 갖고 있다. prior distribution를 gaussian 분포로 가정하면 아래와 같이 나타낼 수 있다. (Mth order의 polynomial)

$$p(\textbf{w} | \alpha) = N(\textbf{0}, \alpha^{-1}\textbf{I})  = (\frac{\alpha}{2\pi})^{(M+1)/2} \exp \{ -\frac{\alpha}{2} \textbf{w}^T \textbf{w}\}$$

이를 통해 우리는 posterior distribution를 구할 수 있다. posterior는 likelihood와 prior의 곱에 비례하므로

$$p(\textbf{w} | \textbf{x}, \textbf{t}, \alpha, \beta) \propto p(\textbf{t} | \textbf{x}, \textbf{w}, \beta)p(\textbf{w} | \alpha)$$

위의 posterior distribution을 최대화로 만드는 parameter를 *MAP* (MLE에 대응되는 point estimate)라고 부른다. posterior distribution에 negative log를 취한다. posterior를 최대로 만드는 것은 아래를 최소화 하는 것과 같다.

$$\frac{\beta}{2}\sum_{n=1}^{N}{\{ y(x_n, \textbf{w}) - t_n \}^2} + \frac{\alpha}{2}\textbf{w}^T\textbf{w}$$

위 결과를 통해 posterior distribution를 maximizing하는 것은 regularized sum-of-squares error function을 minimizing하는 것과 동일하다는 것을 알 수 있다.. (L2 regularization, Ridge regression)

### 1.2.6 Bayesian curve fitting
위의 Bayesian 접근법은 point estimate를 구했기 때문에 살짝 아쉽다. 좀 더 Bayesian적인 방법은 $\textbf{w}$의 모든 값에 대해 integral over하는 것이다. $\textbf{w}$에 대해 marginalize하면 되는데 이는 뒤에 자주 나오는 방법이므로 잘 기억하자. 이제 predictive distribution을 구해보자.
- training data : $\textbf{x},\textbf{t}$
- new data : $x$
- hyperparameter (assume we know) : $\alpha, \beta$ (아래식에서는 생략)

$$p(t | x, \textbf{x}, \textbf{t}) = \int p(t | x, \textbf{w})p(\textbf{w} | \textbf{x}, \textbf{t}) d\textbf{w} = N(t| \mu(x), s^2(x))$$

$$\mu (x) = \beta \phi (x)^T \textbf{S} \sum_{n=1}^{N}{\phi (x_n)} t_n $$

$$s^2 (x) = \beta^{-1} + \phi (x)^T \textbf{S} \phi (x)$$

$$\textbf{S}^{-1} = \alpha \textbf{I} + \beta \sum_{n=1}^{N}{\phi (x_n) \phi (x)^T}$$

- vector $\phi (x)$ : element $\phi_i (x) = x^i$ for $i = 0, ... M$

## 1.3 Model Selection
여러 가지 모델을 선택할 때, train score로 model을 선택하는 것은 적절하지 않다. 그래서 validation set을 이용한다. 하지만 validation set에 overfitting하는 경우도 있기에 test set으로 최종 점검까지 하는 것이다. data가 제한적인 경우 cross validation의 방법을 사용한다. 하지만 이는 상당히 computationally expensive하다.

## 1.4 The Curse of Dimensionalty
차원의 저주를 보여주는 몇가지 예시들
- nearest neighborhood 알고리즘에 해당하는 부분 : sample space를 cubic형태로 나눈다고 생각했을 때, 차원이 커짐에 따라 지수적으로 cubic의 갯수가 많아진다. 따라서 cubic에 data가 텅 비지 않으려면 많은 양의 데이터가 필요하다.
- polynomial 의 경우 : Mth order의 polynomial 모델을 사용한다고 하면 $D^M$ 으로 parameter의 수가 증가한다.
- data를 sphere하게 생각해보자. 차원이 높아질수록 sphere의 표면쪽에 data가 몰려있다. 즉, 중심쪽이 sparse해지는 것이다.

