# [PRML] Bayesian Optimization


Bayesian Optimization으로 모델의 성능을 올려보자.
<!--more-->

## Bayesian Optimization with Gaussian Process
어떤 sequence of experiments를 한다고 생각해보자. 다음은 몇 가지 가정사항이다.
- Interested in finding a global maximizer(minimizer) of $f(\bf{x})$
- 우리는 experiments result를 만드는 underlying function $f(\bf{x})$를 모른다.
- input은 우리가 다 알고 조절할 수 있다.
- result have a stochastic element
  - $y_t \sim N(f,\sigma^2_{noise})$
- results and input are continuous
  - 일반적인 경우는 continous를 고려하지만 discrete, hybrid의 경우도 존재한다.

다양한 task에서 사용할 수 있지만 우리는 주로 hyperparameter tuning을 할 때 사용하게 된다.
- Grid search
  - no learning of underlying function
- Binary search
  - learning of constraints, not the function

위와 같은 방법들이 많이 사용되었다. 이와 다르게 BOP는
- learning underlying function with surrogate model
- selecting the next sampling input

같은 task를 통해서 최적의 결과를 얻어내고자 하는 것이다. 그렇다면 어떤 과정으로 최적의 결과를 얻어낼까?

- GPR은 모든 data point에서 predicted mean, predicted std를 알려준다.
- input을 넣고 underlying function을 만든다 (GPR을 fitting하는 것). 그 후에 mean과 variance를 통해 exploitation or exploration를 결정하여 next sampling input을 결정한다. (그리고 다시 underlying function을 만든다. 이를 반복한다.)
  - Exploitation : result값이 높은 곳(underlying function mean이 큰) 탐색
  - Exploration : 관측지가 적은 곳(variance가 큰) 탐색
- 이떄, 이에 대한 판단 기준이 필요하다. acquisition function을 이용한다.

이에 대해 한번 더 정리하자면

1. Surrogate model : Compute $p(f|D_{1})$, yielding $\mu_{1}({\bf x})$ and $\sigma_{1}({\bf x})$.
2. Acquisition function: Choose ${\bf{x}} _ {2}$ such that ${\bf x} _ {2}=argmax_{ {\bf x} \in \mathcal{X} } a ({\bf x}|\mathcal{M}_{1})$
3. Augment data, $D_2 = D_1 \cup \\{ ({\bf x}_{2}, y _ {2}) \\}$
4. Surrogate model : Compute $p(f|D_2)$, yielding $\mu_{2}({\bf x})$ and $\sigma_{2}({\bf x})$.
5. Acquisition function: Choose ${\bf x} _ 3$ such that ${\bf x} _ {3}=argmax_{ {\bf x} \in \mathcal{X} }a({\bf x}|\mathcal{M}_{3})$
6. Augment data, $D_ 3 = D_2 \cup \\{ ({\bf x} _ {3},y _ {3}) \\}$
7. Repeat theses till the final round T, to compute $\mu_{T}({\bf x})$
8. ${\bf x}^{*} = argmax_{ {\bf x} \in {\bf x}_1,...,{\bf x} _ T }  \mu _ {T}({\bf x})$

### surrogate model
다양한 모델을 사용할 수 있다. 하지만 해당 point의 mean, variance를 알 수 있는 stochastic한 모델이여야 할 것이다.
- Random Forest
  - Empirical하게 mena, variance를 구할 수 있다.
  - scable, faster
  - continuous, discrete 변수 모두 handle 가능하다. (GP는 kernel을 따로 design해야 한다고 함)
  - extrapolation을 잘 못한다.
- GP regression
  - Nonparameteric Bayesian Regression
  - Not scalable
    - 10dim이 넘어가면 standard GP로는 힘들다.
    - sample dsata의 수가 많아져도 힘들다.

### Acquisition Function
Acquisition Function은 다양하다. 몇 가지만 간단히 알아보고 코드를 통해 실습을 진행해보자.

#### Maximum Probability of Improvement (PI)
현재 optimized value $y_{max}$를 어떤 margin m 이상으로 올려줄 확률이 가장 높은 input을 sampling한다. grid search처럼 value를 계산하는 것이 아니라 확률만 계산하여 진행한다.
- $D$는 기존 data, 이를 통해 GPR을 만들수 있겠다.
- $y \sim N(\mu, \sigma^2)$ 이는 GPR로 만들어진 것이다.

$$MPI(x|D) = \argmax_x P(y \ge (1+m)y_{max} | x, D)$$

$$y\sim N(\mu, \sigma^2) = \argmax_x P(\frac{y-\mu}{\sigma} \ge \frac{(1+m)y_{max}-\mu}{\sigma})$$

$$= \argmax_x \Phi (\frac{\mu - (1+m)y_{max}}{\sigma})$$

그런데 PI는 잘 안쓴다고 한다.

#### Maximum Expected Improvement (EI)
MPI를 조금 더 디벨롭시킨 것이다. MPI에서는 m을 고려해야했다. 그렇게 하지 말고 0부터 infinite으로 고려하면 되지 않을까? 라는 접근을 한다. 구체적으로 식을 구하는 과정은 생략한다.
- expected improvement w.r.t. the best observed objective value $y_{b}$ so far is defined as 

$$EI	= E _ y \[ \max (y - y_{b} ,0) \]$$

$$=\int \max (y-y_{b}) N (y | \bar{y}, \sigma^{2})dy$$

$$=(\bar{y} - y_b) \Phi ( \frac{\bar{y}-y_b}{\sigma} ) + \sigma \phi ( \frac{\bar{y} - y_b}{\sigma} )$$ 

#### Gaussian Process-Upper Confidence Bound (GP-UCB)
posterior mean과 variance의 적절한 trade-off를 고려하여 data point를 선택한다. 아래의 수식에 따라서 point를 선택한다.
- $\beta_t$ : appropriate constants
- $\nu$ : hyperparameter involving the degree of exploration

$$\bf{x} _ t = \argmax_{\bf{x}} ( \mu_{t-1}(\bf{x}) + \sqrt{\nu \beta_t} \sigma_{t-1}(\bf{x}))$$

#### Thompson Sampling
posterior에서 function을 sampling하는 방법이다.

### 이해를 위한 코드

```python
# NHN cloud
    # https://www.youtube.com/watch?v=PTxqPfG_lXY

import numpy as np
from scipy.stats import norm
from sklearn.gaussian_process import GaussianProcessRegressor
from sklearn.gaussian_process.kernels import RBF

# Acquisition function
def expected_improvement(mean, std, max):
    z = (mean - max) / std
    return (mean - max) * norm.cdf(z) + std * norm.pdf(z)

# Objective function
def f(x):
    return x * np.sin(x)

# hyperparameter space
min_x, max_x = -2, 10

# Observation data
X = np.random.uniform(min_x, max_x, 3).reshape(-1, 1)
y = f(X).ravel()

# GP model
gp_model = GaussianProcessRegressor(kernel=RBF(1.0))

for i in np.arange(10):
    # surrogate model fit
    gp_model.fit(X, y)
    # predict -> mean, std 계산
    xs = np.random.uniform(min_x, max_x, 10000)
    mean, std = gp_model.predict(xs.reshape(-1, 1), return_std=True)
    # acq 계산
    acq = expected_improvement(mean, std, y.max())
    # acq가 가장 큰 값 선택
    x_new = xs[acq.argmax()]
    y_new = f(x_new)
    # 데이터에 추가
    X = np.append(X, np.array([x_new])).reshape(-1, 1)
    y = np.append(y, np.array([y_new]))
```

## Reference
- [문일철 교수님 강의](https://www.youtube.com/watch?v=sbbR-XRft9o&list=PLbhbGI_ppZIRPeAjprW9u9A46IJlGFdLn&index=54)
- [NHN cloud 발표](https://www.youtube.com/watch?v=PTxqPfG_lXY)
- paper
  - Taking the Human Out of the Loop: A Review of Bayesian Optimization (2016) 
  - A tutorial on Bayesian optimization (2018)
