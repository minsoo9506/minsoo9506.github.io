# [PRML] Chapter4 - Linear Models For Classification (1)


Classification에 대해 알아보자.

<!--more-->

input space는 *decision regions* 로 나눠지는데 이는 *decision boundaries(decision surfaces)* 에 의해 나눠진다. 이번 챕터에서는 분류 선형모델에 대해 공부하는데  이는 decision surfaces가 **input x의 linear function** 이라는 것을 의미한다. **D차원의 input space가 D-1 차원의 hyperplane으로 나눠지는 것이다.** 크게 3가지로 나누어서 공부한다.

- Discriminant function
- generative
- Discriminative

classification에서는 discrete class labels 이나 각 class가 될 probability를 target으로 예측한다. 후자의 경우 (0,1) 사이의 값을 가질 것이다. 따라서 우리는 linear function of ${\bf w}$를 nonlinear function을 이용하여 transform한다.

$$y({\bf x}) = f({\bf w}^T {\bf x} + w_0)$$

machine learning에서는 $f$를 *activation function* 이라고 부른다. 통계학에서는 inverse of *link function* 으로 부른다. 따라서 이전에 봤던 regression model과는 다르게 더이상 parameter에 linear하지 않는 성질을 가진다.

## 4.1 Discriminant Functions
- discriminant : a function that takes an input vector ${\bf x}$ and assigns it to one of $K$ class
  - 이번 chapter에서는 *linear discriminant* ( : decision surfaces are hyperplane) 로 한정지어 공부할 것이다.

### 4.1.1 two classes
가장 간단한 linear discriminant function을 보면

$$y({\bf x}) = {\bf w}^T {\bf x} + w_0$$

- $y({\bf x}) \ge 0$ 이면 class 1이고 반대면 class 2 이다. 따라서 decision boundary는 $y({\bf x}) = 0$ 이고 $(D-1)$차원의 hyperplane이다.
- decision surface 위에 두 점 ${\bf x}_A , {\bf x}_B$ 이 있다고 가정하면
  - ${\bf w}^T({\bf x}_A - {\bf x}_B)=0$ 이므로 vector ${\bf w}$는 decision surface에 있는 모든 점들과 orthogonal하다. 이는 ${\bf w}$가 decision surface의 orientation을 결정한다는 의미이다.
- 똑같이 ${\bf x}$가 decision surface 위의 점이라고 하고 원점과 decision surface의 거리를 계산하면 아래와 같다.
  - 여기서 ${\bf w}_0$는 decision surface의 위치를 결정한다.

$$\frac{\textbf{w}^T \textbf{x}}{\left|| \textbf{w} \right||} = - \frac{ \textbf{w}_0}{\left|| \textbf{w} \right||}$$

### 4.1.2 Multiple classes
$K$ 가 2보다 큰 multiple class를 분류하는 상황을 생각해보자. linear discriminant로 분류하는 방법은 크게 두 가지로 나눌 수 있다.
- one vs the rest
- one vs one

두 방법 모두 class를 결정하는데 있어 애매한 상황이 발생한다. hyperplane이라는 제약때문에 그 어떤 class에도 속하지 못하는 지역이 발생한다. (PRML figure 4.2 에 잘 보여줌) 이를 해결하기 위해 아래와 같은 $K$개의 linear function을 $K$-class discriminant로 사용한다.

$$y_k(x) = w^T_kx + w_{k0}$$

- $y_k({\bf x}) \ge y_j ({\bf x})$ 인 경우, ${\bf x}$는 $k$로 분류한다. 즉, 큰 값을 가지는 쪽으로!

여기서 만들어지는 decision region은 항상 singly connected and convex하다.
- decision region $R_k$에 들어있는 두 점 ${\bf x}_A, {\bf x}_B$
- 이 두 점을 연결한 선 위에 점 $\hat{ {\bf x} }$이 있다고 가정하자. 이를 표현하면 ($0 \le \lambda \le 1$)

$$\hat{\bf x}=\lambda{\bf x}_A + (1-\lambda){\bf x}_B$$

따라서 discriminant function은 다음을 만족한다.

$$y_k(\hat{ {\bf x} })={\lambda}y_k({\bf x}_A) + (1-\lambda)y_k({\bf x}_B) $$

- $y_k({\bf x}_A)  > y_j({\bf x}_A) , y_k({\bf x}_B)  > y_j({\bf x}_B)$ 을 만족하기에
- $y_k({\hat {\bf x}})  > y_j({\hat {\bf x}})$ 도 성립한다.
  - 따라서, ${\hat {\bf x}}$은 항상 $R_k$에 속한다.

이제 linear discriminant function의 parameter를 학습하는 방법에 대해 배울 것이다.
- least square
- Fisher's linear discriminant
- perceptron algorithm

### 4.1.3 Least squares for classification
이전의 sum of squares error function을 그대로 이용한다. target은 1-of-K binary coding하여 vector ${\bf t}$ 이다. (해당하는 class는 1 나머지 class는 0으로 표현)
- 각 class 마다 $y_k({\bf x}) = {\bf w}_k^T {\bf x} + w _ {k0}$ , 이를 합쳐서 표현하면

$$\textbf{y} (\textbf{x}) = \widetilde{ \textbf{W} }^T \widetilde{ {\bf x} }$$

- $\widetilde{\textbf{W}}$ : 각 컬럼이 $\widetilde{\textbf{w}}_k = ({\bf w} _ {k0}, {\bf w}_k^T)$
- $\widetilde{\textbf{W}}_k^T \widetilde{ {\bf x}}$가 가장 큰 값(class)에 input ${\bf x}$를 할당한다.
- normal equation으로 parameter를 구하면

$$\widetilde{\textbf{W}}=(\widetilde{\textbf{W}}^T \widetilde{\textbf{W}})^{-1}\widetilde{\textbf{W}}^T\widetilde{\textbf{T}}=\widetilde{\textbf{W}}^{\dagger}\widetilde{\textbf{T}}$$

- 특징
  - exact closed-form의 solution이 나온다.
  - output이 확률의 범위 (0,1) 을 넘어가는 경우가 존재한다. (우리는 output이 확률값이길 원한다)
  - least square의 단점인 outlier에 취약하다. input data에 따라서 decision이 급변하다.

### 4.1.4 Fisher's linear discriminant
차원 축소의 역할로 많이 쓰이는데 classification으로도 사용가능하다. 일단은 2-class의 경우만 고려해보자.
- $D$ 차원의 input vector $\textbf{W}$를 1차원에 project한다고 생각하자.

$$y = \textbf{w}^T \textbf{x}$$

이렇게 할 수 있다. 하지만 overlapping되니까 class seperation을 최대화하는 projection을 하는 것이다.
- 각 class의 평균을 $\textbf{m}_1, \textbf{m}_2$이라고 하면 아래의 값을 최대로 하는 ${\bf w}$를 찾아야 한다.
  - ${\bf m_1}=1 / N_1\sum_{n \in C_1}\textbf{x}_n$
  - ${\bf m_2}=1 / N_2\sum_{n \in C_2}\textbf{x}_n$

$$m_2 - m_1 = \textbf{w}^T (\textbf{m}_1 - \textbf{m}_2),\quad where\; m_k = \textbf{w}^T \textbf{m}_k$$

${\bf w}$를 계속 키우면 커지기 때문에 제약식 $\sum {\bf w}_i^2 = 1$을 두고 라그랑지로 풀면

$${\bf w} \propto (\textbf{m}_2 - \textbf{m}_1)$$

의 결론을 얻는다.
- 이에 추가적으로 Fisher는 **within class의 varinace를 최소화** 하고자 했다. 반면에 **between class의 variance는 최대화** 한다.
- class $C_k$의 within variance는
  - $y_n = {\bf w}^T {\bf x}_n$
  - $m_k = {\bf w}^T {\bf m}_k$

$$s_k^2=\sum_{n \in C_k}(y_n-m_k)^2$$

전체 class의 within variance는 $s_1^2+s_2^2$ 이를 통해
- Fisher criterion (ratio of the between-class variance to the within-class variance)은

$$J(\textbf{w}) = \frac{(m_2 - m_1)^2}{s_1^2+s_2^2}$$

- Fisher criterion을 다시 쓰면

$$J(\textbf{w}) = \frac{\textbf{w}^T {\bf S}_B \textbf{w}}{\textbf{w}^T {\bf S}_W \textbf{w}}$$

$${\bf S}_B = (\textbf{m}_2 - \textbf{m}_1)(\textbf{m}_2 - \textbf{m}_1)^T$$

이 값은 between-class covariance matrix이다.

$$\textbf{S} _ W = \sum_{n \in C_1} (\textbf{x} _ n - \textbf{m} _ 1)(\textbf{x} _ n - \textbf{m} _ 1)^T + \sum_{n \in C_2} ({\bf x}_n - \textbf{m}_2)({\bf x}_n-\textbf{m}_2)^T$$

이 값은 total within-class covariance matrix이다.
  
- w에 대해 미분하고 위의 값을 최대화하는 값을 찾으면 $\textbf{w} \propto {\bf S}_W^{-1} (\textbf{m}_2 - \textbf{m}_1)$ . 이 결과를 **Fisher's linear discriminant** 라고 한다. 1차원에 projection한 뒤에 특정 threshold값을 정해 classification할 수 있다.

### 4.1.5 Relation to least squares
Fisher criterion은 least square의 특별한 경우이다. target을 1-of-K encoding의 방법이 아닌
- class 1은 $N / N_1$
- class 2는 $-N / N_2$

으로 encoding 하면 된다. 이렇게 한 뒤에 least square의 방법대로 parameter를 구하면 Fisher criterion이 나온다. (과정은 생략)

### 4.1.6 Fisher's discriminant for multiple classes
- skip

### 4.1.7 The perceptron algorithm
- perceptron 특징
  - 2 class에서만 사용가능하다.
  - based on linear combination of fixed basis function
  - target을 이전에는 주로 1,0 으로 했는데 여기서는 -1, 1 로 코딩한다.
- **perceptron criterion** (error function)

$$E_p ({\bf w}) = -\sum_{n \in M}{\textbf{w}^T \phi_n t_n}$$

$M$은 잘못분류한 케이스를 의미한다. 우리는 이 criterion을 최소화 하고자 한다.
- $\textbf{w}^T \phi_n > 0$ 이면 1로 분류
- $\textbf{w}^T \phi_n < 0$ 이면 -1로 분류
  - 따라서 분류를 잘못하면 ${\textbf{w}^T \phi_n t_n} < 0$ 이고 error가 커지는 것이다.

- 위 perceptron criterion을 SGD로 iterative하게 계산하면

$${\bf w}^{(\tau+1)}={\bf w}^{(\tau)}-\eta\triangledown E_p({\bf w})={\bf w}^{(\tau)}+\eta\phi_n{t_n}$$

 ($\eta$는 learning rate) 이다. 이를 쉽게 해석하면 분류가 맞으면 놔두고 틀리면 그 $\phi_n$ 만큼 더하고 빼고 하는 것이다. 양변에 $-\phi_n t_n$을 곱하면 에러가 줄어듬(parameter가 converge)을 알 수 있다.

$$-{\bf w}^{(\tau+1)T}{\phi}_n{t_n} = -{\bf w}^{(\tau)T}{\phi_n}{t_n}-(\phi_n{t_n})^T\phi_n{t_n} < -{\bf w}^{(\tau)T}\phi_n{t_n}$$

- *perceptron convergence theorem*
  - training data set is linearly separable 하면 perceptron algorithm수렴한다 (반드시 해당하는 decision boundary를 찾을 수 있다) . 아니면 수렴이 안된다.
  - 수렴하기 전까지 이게 non separable 문제인지 아니면 수렴이 천천히 되는 건지 파악하기 어렵다.

## 4.2 Probabilistic Generative Models
data의 분포에 대한 가정을 갖는 decision boundary에 대해 공부해보자. $p(x|C_k), p(C_k)$로 베이즈정리를 이용하여 posterior를 계산한다. (일단 binary classification의 경우)
- posterior probability for class 1 :

$$p(C_1 | {\bf x}) = \frac{p({\bf x}|C_1)p(C_1)}{p({\bf x}|C_1)p(C_1)+p({\bf x}|C_2)p(C_2)}$$

$$ = \frac{1}{1+\frac{p({\bf x}|C_2)p(C_2)}{p({\bf x}|C_1)p(C_1)}} = \frac{1}{1+exp(-a) } = \sigma (a)$$

$$\text{where}\\; a = \ln \frac{p(x|C_1)p(C_1)}{p(x|C_2)p(C_2)}$$

- $\sigma (x) =  \frac{1}{1+exp(-x) }$ 이 식은 *logistic sigmoid* function 이다.
- 이의 inverse는 $x=\ln (\frac{\sigma}{1-\sigma})$ 이고 *logit* function이라고 한다.

이번에는 일반적인 경우에 대해 살펴보자. multi class의 경우

$$p(C_k | {\bf x}) = \frac{p({\bf x}|C_k)p(C_k)}{\sum p({\bf x}|C_j)p(C_j)} = \frac{exp(a_k)}{\sum_j exp(a_j)}$$

$$\text{where}\\; a_k = \ln p({\bf x}|C_k)p(C_k)$$

 이를 *normalized exponential* or *softmax function* 이라고 한다.

### 4.2.1 Continuous inputs
class-conditional density를 Gaussian이라고 가정하고 posterior를 살펴보자. 단 모든 class는 같은 covariance matrix를 가진다. (2-class)

$$p({\bf x}|C_k) = \dfrac{1}{(2\pi)^{D/2}|\Sigma|^{1/2}}\exp \\{-\dfrac{1}{2}({\bf x} - {\pmb \mu}_k)^T\Sigma^{-1}({\bf x} - {\pmb \mu}_k)\\} $$

이므로 이를 이용해 위에서 구한 posterior를 계산하면
- $a = \ln \frac{p(x\|C_1)p(C_1)}{p(x\|C_2)p(C_2)}$

$$p(C_1 | {\bf x}) =\sigma (a) =  \sigma ({\bf w}^T {\bf x} + w_0)$$

$${\bf w} = \Sigma^{-1}({\pmb \mu_1}-{\pmb \mu_2})$$

$$w_0 = -\frac{1}{2}{\pmb \mu_1}^T\Sigma^{-1}{\pmb \mu_1} + \frac{1}{2}{\pmb \mu_2}^T\Sigma^{-1}{\pmb\mu_2} + \ln{\frac{p(C_1)}{p(C_2)}}$$

의 형태가 나온다. class-conditional density를 Gaussian이라고 가정하였기 때문에 logistic sigmoid안에서 ${\bf x}$ 의 linear function의 형태가 나온다.

- K-class의 경우

$$a_k({\bf x})=\ln(p({\bf x}|C_k)p(C_k)) = {\bf w}^T_k {\bf x}+w_0$$

$${\bf w}_k = \Sigma^{-1}{\pmb \mu}_k$$

$$w_{k0} = -\frac{1}{2}{\pmb \mu}_{k}^{T} \Sigma^{-1}{\pmb \mu}_k + \ln p(C_k)$$

- posterior의 decision boundary는 input space에 linear하다. (공분산이 동일하다는 가정하에서)
- 공분산을 각 class마다 다르다고 가정하면 우리는 quadratic function of ${\bf x}$를 얻게 되고 이는 *quadratic discriminant* 이다.

이처럼 posterior probability는

$$p({\bf x}|C_k) = f(\text{linear of}\\;{\bf x})$$

의 형태가 된다.

### 4.2.2 Maximum likelihood solution
MLE를 통해 prameter들을 추정해보자. class-conditional에서 Gaussian을 가정하였는데 그에 해당하는 parameter들 이다.

- prior : $p(C_1) = \pi , p(C_2) = 1- \pi$
- $p(x_n,C_1) = p(C_1)p({\bf x}_n\|C_1) = \pi N({\bf x}_n \| {\pmb \mu}_1,{\pmb \Sigma})$
- $p(x_n,C_2) = p(C_2)p({\bf x}_n\|C_2) =(1- \pi) N({\bf x}_n \| {\pmb \mu}_2,{\pmb \Sigma})$
- Class 1은 1, Class 2는 0 으로 target coding
- likelihood function :

$$p(\textbf{t} | \pi, {\pmb \mu}_1,{ \pmb \mu}_2, {\pmb \Sigma} ) = \prod [\pi N({\bf x}_n | {\pmb \mu}_1, {\pmb \Sigma})]^{t_n}[(1-\pi)N({\bf x}_n | {\pmb \mu}_2, {\pmb \Sigma})]^{1-t_n}$$

이를 log 취하고 미분하여 MLE를 구하면 (K-class도 동일한 방법으로 구할 수 있다)

$$\pi = \frac{1}{N} \sum_{n=1}^{N}{t_n} = \frac{N_1}{N_1 + N_2}$$

$${\pmb \mu} _ 1 = \frac{1}{N_1} \sum_{n=1}^{N}t_n {\bf x} _ n, {\pmb \mu} _ 2 = \frac{1}{N_2}\sum_{n=1}^{N}(1-t_n){\bf x}_n$$

$${\pmb \Sigma} = {\bf S} = \frac{N_1}{N}{\bf S}_1 + \frac{N_2}{N}{\bf S}_2$$

### 4.2.3 Discrete features
각 input data feature가 2가지의 값을 갖는 discrete feature들이라고 가정해보자. 그러면 총 $2^D$의 경우 수가 생긴다. 이를 추정하기에는 너무 복잡하다. 따라서 naive bayes의 가정을 이용하면

$$p({\bf x}|C_k) = \prod_{i=1}^{D}\mu_{ki}^{x_i}(1-\mu_{ki})^{1-x_i} $$

$$a_k({\bf x})=\ln(p({\bf x}|C_k)p(C_k))$$

$$a_k({\bf x})=\sum_{i=1}^{D}\\{x_i\ln \mu_{ki}+(1-x_i)\ln(1-\mu_{ki})\\}+\ln p(C_k)$$

이 또한 linear한 형태이다.

### 4.2.4 Exponential Family
위에서 알 수 있듯이 input이 Gaussian이던 discrete이던지 우리에게 가장 중요한 posterior class probability는 generalized linear model과 sigmoid, softmax activation function에 의해 결정된다.
 - 이러한 특징은 class-conditional density가 exponential family의 경우 해당한다.

$$p({\bf x}\;|\lambda_k) = h({\bf x})g(\lambda_k)\exp(\lambda_k^T u({\bf x})) $$

여기서 제약을 위한 parameter $s$를 추가하고 (잘 이해못함)

$$p({\bf x}\;|\lambda_k, s) = \dfrac{1}{s}h\left(\dfrac{1}{s}{\bf x}\right)g\left(\lambda_k\right)\exp \left(\dfrac{1}{s}\lambda_k^T u({\bf x})\right)$$

linear function을 구할 수 있다.

$$a({\bf x})=\dfrac{1}{s}(\lambda_1-\lambda_2)^T{\bf x}+\ln g(\lambda_1) - \ln g(\lambda_2) + \ln p(C_1) - \ln p(C_2)$$

$$a_k({\bf x}) = \dfrac{1}{s}\lambda_k^T{\bf x}+\ln g(\lambda_k) + \ln p(C_k)$$

+ link function과 exp fam의 관계
  - EX) Bernoulli dist

$$L(\theta) = \prod \theta^{x_i}(1-\theta)^{1-x_i}= \exp \{\sum{x_i \log \theta}+ \sum{(1-x_i)\log (1-\theta)} \}
$$

$$=\exp \{ \sum{x_i} \log(\frac{\theta}{1-\theta})  \}(1-\theta)^n$$

- 위의 식에서 $\log (\frac{\theta}{1-\theta})$ 가 link function이다.
- $\log (\frac{\theta}{1-\theta}) = \beta_0 + \beta_1 x_1+...+\beta_n x_n$ 이 이제 배울 logistic regression 이다.
