# [PRML] Chapter6 - Kernel Method (2) : GP


Gaussian Process에 대해 정리하였다.

<!--more-->

## 6.4 Gaussian Processes
이 부분은 카이스트 문일철 교수님의 유투브영상을 보고 정리하였습니다.

### Continuous Domain Data
- GP는 continuous domain data 분석에 유용하다.
  - Time, Space, Spatio-Temporal...
- 어떻게 분석, 모델링?
  - Estimating on the underlying function (ex. Autoregression)
  - Prediction on the unexpected point (ex. extrapolation with autoregression)

### Underlying Function and Observations
$Y = sinc(x)$ 라는 함수를 underlying function이라고 하자. 여기서 gaussian noise를 추가하여 observation들을 생성했다.

지금 그림은 없지만 그림1은 x에 따라 분산이 동일하고 그림2는 x에 따라 분산이 변화(x가 클수록 분산이 커짐)한다. 
- underlying function을 구해야하므로 mean function을 찾는 것은 당연하고
- 추가로 variance(or precision) function도 중요하다.

$$\mu(t), \sigma(t)^2$$

### Simple Analyses without Domain Correlation
mean function을 domain correlation없이 estimate한다고 하자. 즉, 특정 1개의 point에서 mean과 variance를 계산하는 것이다. 그런데 continuous domain에서 사실 같은 $x(t)$에 대해 multiple obsevation이 나올 수 없다. 약간의 discretize라고 할 수 있다. 

해당 domain point에서 observation이 많으면 어느 정도 smooth하게 mean function을 구할 수 있다. 하지만 반대의 경우 좋은 estimation이 어렵다. 
- 그래서 우리는 주위의 다른 domain data point도 사용하는게 좋지 않을까 라는 생각을 할 수 있다.

### Simple Analyses with Domain Correlation
- Moving average
  - time window(특정 구간)를 설정하고 평균
  - 급격하게 변화하는 구간은 잘 안 맞을 수도 있다.
  - time window에 따라 변화
    - window가 커질수록 smooth해진다.
  
$$MA(x) = \frac{1}{N} \sum_{x \in W} y_i$$

그런데 모든 data point에 동일한 가중치를 주는게 다르게 주면 어떨까? 예를 들면,
- Squared Exponential
  - $L$이 커지면 window가 커지는 역할
  - 위에서 window 크기처럼 $L$을 적절히 선택해야한다.
 
$$k(x,x_i;L) = exp(-\frac{|x-x_i|^2}{L^2})$$

위처럼 domain correlation을 다르게 생각하고 거리에 따라 가중치를 다르게 주는 것이다. 가까울수록 큰 가중치!

$$MA(x) = \frac{1}{\sum_{x_i \in D} k(x,x_i)}\sum_{x_i \in D} k(x,x_i) y_i$$

## Random Process
- Random process(=Stochastic process)
  - An infinite indexed collection of random variables $\{ X(w,t) , t \in T \}$
    - index paramter : $t$ (time, space...)
  - A function $X(t,\omega), t \in T \;\text{and}\; \omega \in \Omega$
    - outcome : $\omega$
    - Fixed $t \rightarrow X(t,\omega)$ is a random variable over $\Omega$
    - Fixed $\omega \rightarrow X(t,\omega)$ is a deterministic function of $t$ ; sample function
## Gaussian Process
- GP는 Random Process의 한 종류
- For any set S, a GP on S is a set of random variable ($z_t : t \in S$) such that vector $[z_{t_1}, z_{t_2},...,z_{t_n}]$ is multivariate gaussian

$$P(T) = N(0, (\beta I_N)^{-1} + K) \\ K_{nm} = k(x_n, x_m) = \theta_0 \exp(-\frac{\theta_1}{2}||x_n - x_m||^2) + \theta_2 + \theta_3 x_n^T x_m$$

## Derivation of Gaussian Process
일단 linear regression으로 접근하고 GP에 대해 알아본다.
- gaussian process regression : a nonparametric bayesian regression method using the properties of Gaussian processes

### Mapping Functions
non-linearly separable data set이 있다고 가정하자. 이를 위해 basis space를 증가시키면 될 것이다. mapping function $\phi$를 통해 확장시킨다.

### Linear regression with basis function

$$y(x) = w^T \phi (x)$$

여기서 $w$를 deterministic value가 아니라 probabilistically distributed value라고 생각하자. (Bayesian linear regression의 방법론)

$$P(w) = N(0, \alpha^{-1} I)$$

Y의 확률분포(joint distribution)에 대해 생각해보자. ($w$가 확률분포가 있으니까)

- $Y$도 normal 이겠구나 (multivariate gaussian)

$$Y = (y_1, y_2,...,y_n)$$

- $K$ : Gram matrix

$$E[Y] = E[\Phi w] = \Phi E[w] = 0$$

$$cov(Y) = E[YY^T] = E[\Phi w w^T \Phi^T] \\= \Phi E[ww^T]\Phi^T \\= \frac{1}{\alpha} \Phi \Phi^T$$

$$K_{nm} = k(x_n,x_m) = \frac{1}{\alpha} \phi (x_n)^T \phi (x_m)$$

$$\therefore P(Y) = N(0,K)$$

- 분산이 kernel function을 이용한다는 점을 기억하자

이제 $Y$에 대한 분포를 파악했으니 이를 통해 prediction을 해보자. 

### Modeling Noise with Gaussian distribution
- $t_n$ : observed value with noise
- $y_n$ : Latent, error-free value
- $e_n$ : Error term distributed by following the gaussian distribution

$$t_n = y_n + e_n \\  (t_n = f(x_n)+e_n)$$


$$P(T|Y) = N(Y, \beta^{-1} I)$$

- $\beta$ : hyperparameter of the error precision
- error term들이 independent라고 가정하기에 variance 부분에 $I$이 된다.

$$P(T) = \int P(T|Y)P(Y) dY = \int N(Y,\beta^{-1} I) N(0,K) dY$$

위의 곱해지는 두 분포 모두 multivariate gaussian distribution 이므로 이를 이용하여 구할 것이다.

$$P(T|Y)P(Y) = P(T,Y) = P(Z)$$

$$\ln P(Z) = \ln P(T|Y) + \ln P(Y) \\ = - \frac{1}{2} Y^TK^{-1}Y - \frac{1}{2}(T-Y)^T \beta I (T-Y) + const$$

여기서 변수는 $T,Y$이다. 여기서 second order term을 보면 (second order term을 찾으면 covariance를 찾을 수 있기에)

$$ = \frac{1}{2} \begin{pmatrix} Y \\ T \end{pmatrix}^T \begin{pmatrix} K^{-1} + \beta I & -\beta I  \\ - \beta I & \beta I \end{pmatrix} \begin{pmatrix} Y \\ T \end{pmatrix} \\ = \frac{1}{2}Z^T R Z$$

$R$은 precision matrix가 된다. 이를 inverse 하면 (공식이용)

$$R^{-1} = \begin{pmatrix} K & K \\ K & (\beta I)^{-1} + K \end{pmatrix}$$

$\ln (Z)$의 first order term은 없다. mean이 0라는 것을 알 수 있다. 따라서 최종 결과는

$$P(Z) = N(0, R^{-1})$$

이제 PRML chapter 2에서 봤었던 공식을 이용하면 marginal distribution을 구할 수 있다.

$$P(T) = N(0, (\beta I)^{-1} + K)$$

이제 우리가 관찰한 N개의 data를 통해 $P(T)$를 알게 되었다. 그렇다면 이제 prediction해보자. $t_{N+1}$을 알아내야 한다.

$$P(t_{N+1}|T_N)$$

이를 구하기 위해 N+1의 joint를 구하고 conditional disribution을 만들면 된다.

### Sampling of $P(T)$
- Sampling T of 101 dimension when points
  - $x_n = [-1,-0.98,...,1]$ : 101개의 data point
- mean $0$ : 101 dim zero vector
- cov $(\beta I_N)^{-1} + K$ : 101 * 101 dim cov

$$P(T) = N(0, (\beta I_N)^{-1} + K)$$

kernel의 parameter와 $\beta$값에 따라서 sampling data들이 이루는 모습이 달라진다.

### Mean and Covariance of $P(t_{N+1} \| T_N)$

$$P(t_{N+1}|T_N) = P(T_{N+1}) / P(T_N)$$

$$P(T_{N+1}) = N(0, cov_{N+1})$$

mean은 1차원이 늘어난 zero vector이고 cov는 행과 열이 하나씩 들어간 형태일 것이다. 이는 kernel function과 $\beta$를 통해 어렵지 않게 구할 수 있다.

$$cov_{N+1} = \begin{pmatrix} cov_N & k  \\ k^T & K_{(N+1)(N+1)}+\beta^{-1} \end{pmatrix}$$

이제 joint distribution을 구했으니 conditional distribution을 구할 수 있다. (공식 PRML chap2에 나온다)

$$P(t_{N+1}|T_N) = N(0+k^T cov_N^{-1}(T_N-0),K_{(N+1)(N+1)}+\beta^{-1} -k^Tcov_N^{-1} k )$$

이는 결국 regression을 한 것이다. predictive distribution을 구한 것이다.
- 평균과 분산 모두 new data $x_{N+1}$에 depend하다.
- 분산에서 inverse가 computationally 오래걸려서 approximation하는 방법들이 있다고 한다.

$$\mu_{t_{N+1}} = k^T cov_N^{-1} T_N \\ \sigma^2_{t_{N+1}} = K_{(N+1)(N+1)}+\beta^{-1} -k^Tcov_N^{-1} k$$

우리가 알고 있는 일반적인 regression과는 조금 다른 형태이다. 각 feature들의 weight들이 어디있는지 궁금할 수 있는데 kernel function안의 parameter로 들어갔다.

### Hyperparameter of Gaussian Process Regression
위에서 linear regression에서 parameter optimization을 하는 방법을 알아보자. 아래의 kernel hyperparameter를 추정해야 하는 것이다.

$$ K_{nm} = k(x_n, x_m) = \theta_0 \exp(-\frac{\theta_1}{2}||x_n - x_m||^2) + \theta_2 + \theta_3 x_n^T x_m$$

$$P(T;\theta) = N(0, (\beta I_N)^{-1} + K)$$

$\theta$를 추정하기 위해 likelihood를 최대한 높이는 방법을 택한다. $\theta$에 대해 미분하여 구하면 된다.  

$$\frac{\partial}{\partial \theta_i} \log P(T;\theta) \overset{let}{=}0$$

그런데 closed form은 존재하지 않는다. 그래서 approximation해야 한다. (너무 복잡해서 derivation 생략) 우리는 pytorch와 같은 framework의 도움을 받아서 구한다.

## Gaussian Process Classifier
아래와 같이 일반적인 logistic regression과 거의 동일하다.
- Gaussian process classifier : sigmoid function + Gaussian process
  - Gaussian process : $f(x;\theta)$
  - Gaussian process classifier : $y=\sigma (f(x;\theta))$
  - if $t \in \{0,1\}$, objective function to optimize : 
  
$$P(t | \theta) = \sigma (f(x;\theta))^t (1-\sigma (f(x;\theta)))^{1-t}$$

## Bayesian Optimization with Gaussian Process
어떤 sequence of experiments를 한다고 생각해보자. 다음은 몇 가지 가정사항이다.
- experiment result should be maximized(or minimized)
- 우리는 experiments result를 만드는 underlying function을 모른다.
- input은 우리가 다 알고 조절할 수 있다.
- results and input are continuous
- result have a stochastic element

이런 경우 이전에는
- Grid search
  - no learning of underlying function
- Binary search
  - learning of constraints, not the function

와 같은 방법들이 많이 사용되었다. 이제는 
- learning underlying function
- selecting the next sampling input

을 모두 잡을 수 있는 Bayesian Optimization을 사용하고자 한다.

- GP는 모든 data point에서 predicted mean, predicted std를 알려준다.
- input을 넣고 underlying function을 만든다. 그 후에 mean과 variance를 통해 exploitation or exploration를 결정하여 next sampling input을 결정한다. (그리고 다시 underlying function을 만든다. 이를 반복한다.) 이에 대한 판단 기준이 필요한데 acquisition function을 이용한다.
  - Exploitation : result값이 높은 곳(underlying function mean이 큰) 탐색
  - Exploration : 관측지가 적은 곳(variance가 큰) 탐색

### Acquisition Function : Maximum probability of improvement
Acquisition Function은 다양하다. 그중 MPI에 대해 알아보자.
- Maximum probability of improvement
  - 현재 optimized value $y_{max}$를 어떤 margin m 이상으로 올려줄 확률이 가장 높은 input을 sampling한다. (grid search처럼 value를 계산하는 것이 아니라 확률만 계산하여 진행)
  - $D$는 기존 data, 이를 통해 GP를 만들수 있겠다.
  - $y \sim N(\mu, \sigma^2)$ 이는 GP로 만들어진 것

$$MPI(x|D) = argmax_x P(y \ge (1+m)y_{max} | x, D),\;y\sim N(\mu, \sigma^2) \\=argmax_x P(\frac{y-\mu}{\sigma} \ge \frac{(1+m)y_{max}-\mu}{\sigma})\\=argmax_x \Phi (\frac{\mu - (1+m)y_{max}}{\sigma})$$
