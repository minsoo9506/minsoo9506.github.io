# [PRML] Chapter14 - Combining Models


Ensemble method에 대해 정리하였다.

# 14. Combining Models
말 그대로 모델들을 섞는 것이다. 머신러닝 예측 모델에서 가장 좋은 성능을 보이고 있다는 Boosting이 이 부분의 내용이다.

## 14.1 Bayesian Model Averaging
model combination methods 와 Bayesian model averaging 을 구분하는 것은 중요하다. Density estimation using a mixture of Gaussians 예시를 통해 살펴보자.

- model combination 
  - 각 data들은 iid, $X = \{\textbf{x}_1, \textbf{x}_2,...,\textbf{x}_n \}$
  - $X$는 전체 dataset을 의미

$$p(X) = \prod_{n=1}^{N}{p(\textbf{x} _ n)} = \prod_{n=1}^{N}{[\sum_{z_n}p(\textbf{x}_n, \textbf{z}_n)]}$$

- bayesian model averaging
  - models indexed by $h=1,...,H$ with prior

$$p(X) = \sum_{h=1}^{H}{p(X|h)p(h)}$$

이 둘의 차이점은
- model combination의 경우 각 data point는 latent variable $z$로 부터 generated되고 bayesian model averaging은 single model이 전체 dataset을 generate하는 과정을 담는 것이다.

## 14.2 Committees
bootstrap으로 M개의 data sets을 만들었고 각각 $y_m(\textbf{x})$ 모델을 훈련시켰다고 가정

$$y_{COM}(\textbf{x}) = \frac{1}{M}\sum_{m}^{M}{y_m(\textbf{x})}$$

이를 **bagging** 이라고 한다. (bagging은 variance를 줄이는 방법론)

우리가 구하고 싶은 true regression predict function을 $h(\textbf{x})$ 라고 하면

$$y_m(\textbf{x}) = h(\textbf{x}) + \epsilon_m(\textbf{x})$$

average sum-of-squares error는 

$$E_{\textbf{x}}[\{y_m(\textbf{x}) - h(\textbf{x}) \}^2]$$

따라서 average error는

$$E_{AV} = \frac{1}{M}\sum_{m=1}^{M}{E_x[\epsilon_m(\textbf{x})^2]}$$

Committees의 expected error는

$$E_{COM} = E_{\textbf{x}} [\{ \frac{1}{M}\sum_{m=1}^{M}{y_m(\textbf{x}) - h(\textbf{x})} \}^2]=E_{\textbf{x}}[\{ \frac{1}{M}{\sum_{m=1}^{M}{\epsilon_m(\textbf{x})}} \}^2]$$

여기서 error term에 대한 가정은
- $E[\epsilon_m(\textbf{x})] = 0$
- $E[\epsilon_m(\textbf{x})\epsilon_n(\textbf{x})]=0, m \neq n$

따라서 우리는 $E_{COM} = \frac{1}{m}E_{AV}$ 라는 결과를 얻는다. 하지만 현실에서는 error term의 가정이 지켜지기 어렵다. error term이 highly correlated되어 있기 때문이다. 그래도 COM의 E가 AV 이하라는 것은 변하지 않는다.

$$\{ \frac{1}{M}{\sum_{m=1}^{M}{\epsilon_m(\textbf{x})}} \}^2 \le \frac{1}{M}\sum_{m=1}^{M}\epsilon_m(\textbf{x})^2\;\;\because \text{Cauchy's inequality}$$

## 14.3 Boosting
간단히 말하자면 weak learner 모델을을 순차적으로 학습하는 것이다. PRML에서는 Adaboost만 다루고 있다.

### AdaBoost
1. Intialize the data weighting coefficients by setting $w_n^{(1)} = 1/N$, n=1,...,N  
   
2. For m=1,...,M  
   
(a) Fit a Classifer $y_m(\textbf{x})$ to the training data by minimizing the weighted error function

$$J_m = \sum_{n=1}^{N}w_n^{(m)} I (y_m (\textbf{x}_n)\neq t_n)$$

(b) Evaluate the quantities

$$\epsilon_m = \frac{\sum_{n=1}^{N}{w_n^{(m)}I(y_m(\textbf{x}) \neq t_n)}}{\sum_{n=1}^{N}{w_n^{(m)}}}$$

and then use these to evaluate

$$\alpha_m = \ln \{ \frac{1-\epsilon_m}{\epsilon_m} \}$$

(c) Update the data weighting coefficients

$$w_n^{(m+1)} = w_n^{(m)} \exp \{ \alpha_m I(y_m(\textbf{x}_n) \neq t_n)  \}$$

3. Make prediction using the final model, which is given by
  
$$Y_M(x) = \text{sign}(\sum_{m=1}^{M}{\alpha_m y_m (x)})$$

위의 과정을 다시 한 번 정리해보자.
- 일단 각 data point는 uniform한 가중치를 갖는다.
- weak model과 해당 시점의 가중치를 이용하여 train한다.
- 해당 model과 가중치를 이용하여 해당 model의 error $\epsilon_m$을 구한다.
- $\epsilon_m$을 이용하여 해당 model의 가중치를 의미하는 $\alpha_m$을 구한다.
  - 여기서 해당 model의 error가 작을수록 $\alpha_m$은 큰 값을 가진다.
- 다음 시점의 각 data point별 가중치를 update한다.
  -  $w_n^{(m+1)} = w_n^{(m)} \exp \{ \alpha_m I(y_m(\textbf{x}_n) \neq t_n)  \})$
  -  좋은 model ($\alpha_m$가 큰)이 틀린 data point는 다음 시점에서 더 큰 가중치를 갖는다.
  -  나쁜 model이 틀린 data point의 경우 가중치는 더 커지지만 위의 경우보다는 작다.
  -  맞춘 data point는 동일한 가중치를 갖는다.

### 14.3.1 Minimizing exponential error
Friedman이 2000년에 논문을 통해 boosting에 대한 다른 시점을 발표했다. boosting을 sequential minimization of an exponential error function으로 바라보는 과정을 알아보자.

- exponential error function

$$E = \sum_{n=1}^{N}{\exp \{ -t_n f_m(\textbf{x} _ n)\}} \\\  \text{where}\\; f_m(\textbf{x}) = \frac{1}{2} \sum_{l=1}^{m}{\alpha_l y_l (\textbf{x})} \text{: linear combination of base classifiers}$$

우리의 목적은 parameter $\alpha_l, y_l (x)$ 에 대해 E를 최소화하는 것이다. global error function minimization 대신에 $\alpha_m, y_m (x)$를 제외한 나머지 값들을 fixed시키고 진행한다. 그러면

$$E = \sum_{n=1}^{N}{\exp \{ -t_n f_{m-1}(\textbf{x} _ n) - \frac{1}{2}t_n \alpha_m y_m (\textbf{x} _ n) \}} \\\ = \sum_{n=1}^{N}{w_n^{(m)} \exp \{ -\frac{1}{2} t_n \alpha_m y_m (\textbf{x}_n) \}}$$

$T_m$을 잘 분류한 data point라고 하고 $M_m$을 틍린 data point라고 하면

$$E = e^{-\alpha_m / 2}\sum_{n \in T_m}{w_n^{(m)}} + e^{\alpha_m / 2} \sum_{n \in M_m}{w_n^{(m)}} $$
$$ = (e^{\alpha_m / 2} - e^{- \alpha_m / 2}) \sum_{n=1}^{N}{w_n^{(m)} I(y_m (\textbf{x} _ n) \neq t_n)} + e^{-\alpha_m / 2} \sum_{n=1}^{N}{w_n^{(m)}}$$

- $y_m (x)$에 대해 최소화하면 두번째 term은 constant가 된다. 그래서 2-(a)가 나온것
- $\alpha_m$에 대해서 계산하면 2-(b)가 나온것
- $w_n$ 의 update도 이렇게 나온 것

### 14.3.2 Error functions for boosting
Expected error는

$$E_{\textbf{x},t}[\exp \{ -ty(\textbf{x}) \}]=\sum_t \int \exp \{ -ty(\textbf{x}) \}p(t|\textbf{x})p(\textbf{x})d\textbf{x}$$

위의 식을 variational minimization으로 $y(\textbf{x})$을 구하면

$$y(\textbf{x}) = \frac{1}{2}\{ \frac{p(t=1|\textbf{x})}{p(t=-1|\textbf{x})} \}$$

임을 알 수 있다. 이를 통해
> AdaBoost is seeking the best approximation to the log odds ratio, within the space of functions represented by the linear combination of base classifiers, subject to the constrained minimization resulting from the sequential optimization.

- exponential loss는 cross-entropy보다 outiler에 덜 robust하다. error에 대한 가중치가 더 크다.
- exponential loss는 cross-entropy와 다르게 $K>2$ class의 경우로 일반화할 수 없다.

## 14.4 Tree-based Models
이 부분은 이미 알고 있는 내용이여서 자세히 정리하지는 않을 것이다.
- 어떤 변수를 나눌 것인가? 나눈다면 그 threshlod는? 이에 대해서는 greedy 하게 최적점을 찾는다. optimal한 기준을 찾는 기준은 regression에서는 sum of square error, classification에서는 gini index, cross entropy 일 것이다.
- node를 몇 개까지 만들것인지도 정해야 한다. 이는 model complexity를 정하게 해주므로 cross validation과 같은 방법으로 찾는다.

## 14.5 Conditional Mixture Models
- standard decision trees are restricted by hard, axis-aligned splits of the input space.
- 그래서 이를 완화하는 방법으로 soft, probabilistic splits that can be functions of all of the input variables !

### 14.5.1 Mixtures of linear regression models
$K$개의 linear model을 이용한다. GMM과 비슷한 맥락을 가진다.

$$p(t | \theta) = \sum_{k=1}^{K}{\pi_k N(t | w_k^T \phi, \beta^{-1})}$$

이를 통해 parameter를 구하기 위해 이전에 배웠던 GMM에서의 EM과 같은 방법을 이용하여 구하면 된다.

$$\ln p(\textbf{t}, \textbf{Z} | \theta) = \sum_{n=1}^{N}\sum_{k=1}^{K}{z_{nk}\ln\{ \pi_k N(t_n | w_k^T\phi_n, \beta^{-1}) \}}$$

결과는 아래의 사진을 참고하자.

<center><img src="../img/chap14-1.jpg" width="80%"></center>

### 14.5.2 Mixtures of logistic models
위와 같은 방법인데 logistic의 경우에 해당한다.

### 14.5.3 Mixtures of experts
위의 방법들보다 조금 더 advanced한 방법론이다. mixing coefficients를 input의 함수로 정의한다.

$$p(t | \theta) = \sum_{k=1}^{K}{\pi_k(x) p_k(t | x)}$$

