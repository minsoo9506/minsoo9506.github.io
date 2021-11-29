# [PRML] Chapter1 - Introduction (2)


Decision Theory에 대해 알아봅시다.

<!--more-->

## 1.5 Decision Theory
Decision Theory는 크게 두 가지의 과정으로 이루어져 있다.
- Determining $p(x,t)$ from a training data set : **inference**
- 이를 통하여 새로운 데이터에 대해 결정(분류,회귀) : **decision**

Decision Theory의 목표는 적절한 Probability들을 이용하여 optimal한 decision을 내리는 것이다. 2-class classification의 상황을 예시로 뒤의 내용을 진행한다. 우리는 input data를 통해 해당 data의 class를 구분하고 싶기에 $p(C_k / \textbf{x})$를 구해야 한다. Bayes' theorem을 생각해보면 posterior를 구해야 하는 것이다.

$$p(C_k | \textbf{x}) = \frac{p(\textbf{x} | C_k)p(C_k)}{p(\textbf{x})}$$

우리는 misclassfication을 최소화하기 위해서 둘 중 더 큰 posterior probability갖는 class에 input data를 분류한다.

### 1.5.1 Minimizing the misclassfication rate
우리의 목표가 misclassfication을 최소화하는게 목표라고 하자. 각 $\textbf{x}$를 class에 분류해야하고 이를 위해 rule이 필요하다. 그 rule에 따라 input space를 region $R_k$로 나눠야 한다. 이 region을 *decision regions* 라고 한다. ($R_k$에 속한 data는 class k라고 분류) decision region간의 경계선을 *decision boundary*, *decision surface* 라고 부른다. misclassfication의 확률은

$$P(mistake) = P(\textbf{x} \in R_1, C_2) + P(\textbf{x} \in R_2, C_1) = \int_{R_1} p(\textbf{x}, C_2)d\textbf{x}+ \int_{R_2} p(\textbf{x}, C_1)d\textbf{x}$$

mistake의 확률을 최소화하기 위해서는 각 integral의 값을 최소화해야 한다. 따라서 만약 $p(\textbf{x}, C_1) >  p(\textbf{x}, C_2)$의 경우, data를 class1으로 분류해야한다.

- $p(\textbf{x}, C_k) = p(\textbf{x}) p(C_k | \textbf{x})$ 이고 우변의 $p(\textbf{x})$는 공통된 부분이므로 우리는 $p(C_k | \textbf{x})$만 고려하면 된다.

### 1.5.2 Minimizing the expected loss
위에서 misclassfication rate를 줄이는 부분에 대해서 살펴봤다. 하지만 실제로 분류를 할 때는 이 접근으로는 부족하다. 예를 들어, 암환자를 분류하는 문제라고 생각해보자. 암이 걸리지 않은 환자를 걸렸다고 잘못 판단하는 것과 암이 걸렸는데 걸리지 않았다고 판단하는 것. 둘 중 후자가 훨씬 심각한 문제이다. 이런 경우 후자에 대해 더 가중치가 있어야 하지 않을까?
- *loss function (cost function)* : overall measure of loss incurred in taking any of the available decisions or actions
- $L_{kj}$ :  (k인데 j로 분류한 경우) loss matrix의 element를 의미한다. misclassfication에 대한 loss라고 이해하면 된다. 예를 들면 암환자의 loss matrix는 아래와 같은 모양이다.

$$\begin{bmatrix} 0 & 100 \\\ 1 & 0 \end{bmatrix}$$

(inference가 끝난 뒤에 decision하는 과정에 해당) optimal solution은 loss function을 최소화하는 것이다. 하지만 loss function은 true class을 알아야 계산할 수 있다. 우리는 true class를 모른다. (예를 들어, 환자의 신상데이터가 있고 이를 통해 암환자인지 아닌지 찾아야하는 상황) 따라서 우리는 expected (average) loss를 최소화하는 방법을 선택한다.

$$E[L] = \sum_{k} \sum_{j} \int_{R_j} L_{kj} p(\textbf{x}, C_k) d\textbf{x}$$

우리의 목표는 expected loss를 최소로 만드는 적절한 $R_j$를 찾는 것이고 이는 각 데이터 $\textbf{x}$가 $\sum_{k} L_{kj}p(\textbf{x}, C_k)$를 최소화 한다는 것을 의미한다.

최종적으로 expected loss를 최소화 하기 위해서는 $\textbf{x}$를 값

$$\sum_{k}{L_{kj} p(C_k|\textbf{x})}= E[L(C_k, \hat{C}_k) | X=\textbf{x}]$$

이 최소가 되는 class $j$로 분류하는 것이다.

### 1.5.3 The reject option
class에 따라 posterior의 비교를 통해 결정하기 애매한 상황이 생긴다. 이런 경우에는 probability에 따라 결정하기 보다는 다른 방법을 사용하는 것이 적절할 수도 있다. (예를 들면, 해당 데이터를 model이 아니라 전문가가 판단하는 방법)  이런 경우 *regect option* 이 있다고 할 수 있는 것이다.

### 1.5.4 Inference and decision
decision 문제를 해결하는 방법을 3가지로 분류할 수 있다. 앞쪽의 방법일수록 복잡한 방법이다.

- **generative model**
  - 아래 식에서 posterior를 구하기 위해서는 분자, 분모를 다 구해야 한다. 한마디로 *approachs that explicitly or implicitly model the distribution of inputs as well as outputs.*
  - 다른 표현으로는, joint distribution $p(\textbf{x}, C_k)$을 구해서 marginalize하여 분모도 구하여 posterior를 구한다.

$$p(C_k | x) = \frac{p(x | C_k)p(C_k)}{p(x)}$$

- **discriminative model**
  - *approachs that model the posterior probabilities directly*
  - 예를 들면 SVM, Tree models, KNN 등등

- **discriminative function**
  - *maps each input x directly onto a class label*
  - 따라서 확률을 고려하지 않는다.
  - inference와 decision stage를 하나로 묶은 것이다.

각각 장단점이 존재한다. 예를 들면, 1번에서 prior $p(\textbf{x})$를 구했으므로 해당 값이 너무 작은 새로운 data는 무시하는 판단을 할 수 있다. (outlier detection하는 것처럼) 그렇다면 이제 (1,2번 선호) posterior를 구하면 어떤 장점이 있는지 알아보자.
- Minimizing risk
  - 이전에 봤던 loss matrix를 수정하여 decision criterion을 수정하기 쉽다.
  - 확률의 threshold를 조정하여 decision criterion을 수정하기 쉽다.
- Reject option
  - expected loss뿐만 아니라 misclassfication rate를 최소화하는 rejection criterion을 정할 수 있게 해준다.
- Compensating for class priors
  - posterior는 prior에 비례하므로 prior를 적절하게 바꿔줌으로서 posterior를 보완할 수 있다.
- Combing models
  - 특정 문제를 subproblem으로 나누어서 생각할 수 있다. 예를 들면, naive bayes model과 같이 independent를 이용하여 posterior를 나누어서 생각할 수 있는 장점이 생긴다.

### 1.5.5 Loss functions for regression
이전까지 classification에 대해 살펴봤으므로 이번에는 regression에 대해 살펴보자. expected (average) loss는

$$E[L] = \int \int L(t, y(\textbf{x})) p(\textbf{x}, t) d\textbf{x}dt$$

이다. regression에서 주로 사용하는 loss function은 squared loss이고 이를 통해 다시 쓰면

$$E[L] = \int \int \{y(\textbf{x}) - t\}^2 p(\textbf{x}, t) d\textbf{x} dt$$

이다. 우리는 이를 최소화하는 $y(\textbf{x})$를 찾는 것이 목표이므로 미분하여 구할 수 있다.

$$\frac{dE[L]}{dy(\textbf{x})} = 2\int \{ y(\textbf{x}) - t\}p(\textbf{x}, t) dt = 0$$

$$y(\textbf{x}) = \frac{\int t p(\textbf{x}, t)dt}{p(\textbf{x})} = \int t p(t | \textbf{x})dt = E_t [t | \textbf{x}]$$

이는 우리가 알고 있는 regression function의 모양이다. (conditional average of t conditioned on x) 이를 이용하여 추가적인 접근을 해보자면

$$\{y(\textbf{x}) - t\}^2 = \{y(\textbf{x}) - E[t | \textbf{x}] + E[t | \textbf{x}] - t \}^2$$

$$E[L] = \int \{y(\textbf{x}) - E[t | \textbf{x}]\}^2 p(\textbf{x})d\textbf{x} + \int \{E[t | \textbf{x}] - t\}^2 p (\textbf{x}) d\textbf{x} $$

두번째 항은 variance of the distribution of t, averaged over x 이다. 따라서 이는 irreducible minumum value of the loss function을 의미한다.

## + Decision Theory 추가

[monk의 강의](https://www.youtube.com/watch?v=KYRAO8f5rXA&list=PLD0F06AA0D2E8FFBA&index=15)에서 decision theory를 다루는데 해당 내용을 추가하고자 한다.

- 일단, loss function은 "0-1 loss" 으로 생각하자.
  - true = prediction : 0
  - true != prediction : 1

두 가지 상황으로 나누어서 살펴보자. 하지만 두 경우 모두의 공통적인 결론은
- $p(y/x)$ 가 핵심이라는 것이다.

### 1. Minimizing conditional expected loss
: Given $x$, minimize $L(y, \hat{y})$ ... but don't know true class $y$
- $(X, Y) \sim P$ : discrete

$$E[L(Y, \hat{y}) | X = x] = \sum_{y} L(y, \hat{y}) P(y | x) = \sum_{y \neq \hat{y} } 1*P(y | x) = 1 - P(\hat{y} | x)$$

$$\therefore \hat{y} = argmin_y E[L(Y,\hat{y}) | x] = argmax_y P(y | x)$$

### 2. Choosing f to minimize expected loss
: Choose $f(f(x) = y)$ to minimize $L(y, f(x))$ but don't know $x$ or $y$

$$E[L(Y, \hat{Y})] = E[L(Y, f(X))] = \sum_{x,y}L(y, f(x))P(x,y)$$

$$ = \sum_{x}\{\sum_y L(y, f(x))P(y  | x)\}P(x) = \sum_{x}g(x,f(x))p(x) = E_x[g(x,f(x))]$$

- suppose for some $x', t$
  - $g(x', f(x')) \ge g(x', t)$
- $f_0(x) =$
  - if $x \neq x', f(x)$
  - if $x = x', t$
- 모든 $x,\; g(x,f(x)) \ge g(x, f_0(x))$

$$\therefore E_x [g(x, f(x))] \ge E_x[g(x,f_0(x))]$$

Choose f to min $g(x,f(x))$

$$f(x) = argmin_t g(x,t)$$

### Big picture
- ### Generative model
  - estimate $p(x,y)$ using data
  - and then $p(y\|x) = \frac{p(x,y)}{p(x)}$

- parameter / latent : $\theta$ 라고 하자
  - $\theta$는 distribution에 관한 parameter / latent
  - $D$는 random (new data)

$$p(y|x,D) = \int p(y|x,D,\theta) p(\theta | x,D) d\theta$$

- $p(y\|x,D)$ : predictive distribution
- $p(\theta \|x,D)$  : posterior distribution  

$p(y\|x,D,\theta)$ 이 부분은 주로 closed form(eg. regression y=wx)으로 구해지며 어렵지 않다. 하지만 posterior 부분은 closed form으로 못 구하는 경우가 많다. 또한 integral 부분도 계산이 어려운 경우가 많다. 그렇다면 이를 어떻게 해결할까? 크게 4가지의 방법을 살펴보자.

- exact inference
  - Multivariate Gaussian, Conjugate prior, Graphical model
- point estimate
  - MLE, MAP (1.2.5를 보면 integral 없이 계산)
  - optimization, EM
- deteministic approximation
  - Laplace approximation, Variational method, Expectation propagation
- stochastic approximation
  - Sampling 기법들 (eg. MCMC)

