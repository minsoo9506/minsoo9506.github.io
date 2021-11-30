# [PRML] Chapter1 - Introduction (3)


Information Theory에 대해 알아봅시다.

<!--more-->

## 1.6 Information Theory
일단 entropy에 대해 두 가지의 시선으로 살펴볼 예정이다. 일단 discrete random variable $x$와 이 variable의 specific value를 알아내면서 얻는 정보의 양이 얼마나 되는지로부터 시작한다.
- 어떤 사건이 일어날 확률이 큰 경우보다 일어날 확률이 작은 사건이 더 정보가 많다고 한다.

이에 따라 우리는 information content ($h(x)$ 라고 하자) 를 측정하는 방법이 필요하다. 그 방법의 조건은
- 확률 $p(x)$에 대해 monotonic function
- 두 개의 사건이 독립적이면 information gain은 두 information의 합으로 표현

이런 조건을 만족시키기 위해 우리는 logarithm을 이용한다. ($\log_2$인 이유는 2진수 bit단위의 정보전달의 측면에서 접근하기 위해)

$$h(x) = - \log_2 p(x)$$

information은 0이상의 값을 갖기에 음수부호를 붙여서 사용한다. 이제 sender가 receiver에게 random variable의 값을 전달해야하는 상황이라고 가정하자. 그들이 전달하는 정보의 평균적인 양은

$$H[x] = - \sum_{x}{p(x)\log_2 p(x)}$$

이를 우리는 *entropy* of the random variable x 라고 부른다.
- (예시는 생략) nonuniform distribution은 uniform distribution보다 더 작은 entropy값을 갖는다.

entropy의 값을 정보전달의 측면 (bit단위라고 생각) 에서 생각해보자. 예를 들면, A집단의 entropy가 2, B집단의 entropy가 3의 값을 가진다. A의 내용을 전달하기 위해서는 최소 2bit, B는 최소 3bit가 필요한 것을 의미하고 이처럼
- entropy는 the state of a random variable을 전달하기 위해 필요한 bits 수의 lower bound이다.

이번에는 entropy에 대해 다른 시각으로 살펴보자. N개의 물체를 bin에 나누어 담아야 한다. $i^{th}$ bin 에는 $n_i$개의 물체가 들어간다. 물체를 나누어 담는 경우의 수 $W$ 를 생각해보면

$$W = \frac{N!}{\prod_i n_i !}$$

이를 *multiplicity* 라고 부른다. entropy를 만들기 위해 logarithm과 적절한 scaled 취하면

$$H = \frac{1}{N}\ln w = \frac{1}{N} \ln N! - \frac{1}{N}\sum_{i} \ln n_i !$$

N이 무한대로 가고 $n_i / N$은 fixed 된다.  그리고 Stirling's approximation을 이용하면
- $\ln N ! \approx N \ln N - N$
- $\ln n_i ! \approx n_i \ln n_i - n_i$

이를 대입하면

$$H = - \lim_{N \rightarrow \infty} \sum_{i} \frac{n_i}{N} \ln \frac{n_i}{N} = - \sum_{i}{p_i \ln p_i}$$

- $\sum_i n_i = N$
- $p_i = \lim_{N \rightarrow \infty} (n_i / N)$ : 물체가 i bin에 들어갈 확률

우리는 bin을 random variable X의 state $x_i$ 라고 할 수 있다. 따라서 random variable X의 entropy는

$$H[p] = - \sum_{i}{p(x_i)\ln p(x_i)}$$

우리는 Lagrange를 이용하여  위의 식의 최댓값을 구할수 있고 최댓값은 **Uniform distribution** 일 때이다.  

이제 continuos variable에서도 생각해보자. (과정 생략) continuos variable의 entropy는 아래 값을 가진다.

$$H[x] = - \int p(x) \ln p(x) dx$$

이를 *differential entropy* 라고 부른다. discrete의 경우에서와 마찬가지로 continuos에서는 어떤 distribution이 가장 큰 entropy를 가질까? (과정 생략, 똑같이 Lagrange 사용) 정답은 **Gaussian distribution** 인 경우이다.

### 1.6.1 Relative entropy and mutual information
우리가 모르는 distribution $p(x)$가 있다고 가정해보자. 이를 approximation하는 $q(x)$를 모델링하였다. 이전에 정보전달에서의 측면에서 생각해보면 우리는 approximation 했기에 random variable 의 value 전달을 위해 추가적으로 리소스 bit가 더 필요하다. 더 필요한 정도는

$$KL(p\| q) = - \int p(\textbf{x}) \ln q(\textbf{x})d\textbf{x} -  (- \int p(\textbf{x}) \ln p(\textbf{x})d\textbf{x}) \\ = - \int p(\textbf{x})\ln \frac{p(\textbf{x})}{q(\textbf{x})}d\textbf{x}$$

이를 *Kullbak-Leibler divergence (Relative entropy)*  between $p(\textbf{x})$ and $q(\textbf{x})$ 라고 부른다. 그리고 아래와 같은 특징을 갖는다. (이러한 특징을 통해 Kullbak-Leibler divergence는 *measure of the dissimilarity of the two distributions $p(\textbf{x}), q(\textbf{x})$* 라고 할 수 있다.)
- $KL(p\| q) \ge 0$
- $KL(p\| q) = 0$, if and only if, $p(\textbf{x}) = q(\textbf{x})$

증명은 Jensen's inequality로 쉽게 할 수 있다. convex function $f(x)$는

$$f(\sum_{i=1}^{M}{\lambda_i x_i}) \le \sum_{i=1}^{M}{\lambda_i f(x_i)}$$

- $\lambda \ge 0$
- $\sum_i \lambda_i = 1$  

의 특징을 갖는다. 위에서 $\lambda_i$를 x의 확률분포라고 생각하면 $f(E[x]) \le E[f(x)]$ 이다. 따라서 ($f$ 를 -log라고 생각하면 된다)

$$KL[p\|q] = - \int p(\textbf{x}) \ln \frac{q(\textbf{x})}{p(\textbf{x})}d\textbf{x} \ge - \ln \int q(\textbf{x}) d\textbf{x} = 0  $$

KL divergence를 최소화하는 것은 결국 likelihood function을 최대화하는 것과 같은데 이에 대해 살펴보자.
- 우리가 모르는 (approximation해야하는) 분포 $p(\textbf{x})$에서 data가 generate되었다고 하자.
- 우리는 어떤 parametric distribution $q(\textbf{x} \| \theta)$ 를 통해 approximation하고자 한다.

$\theta$를 정하는 방법은 $\theta$에 대해 KL divergence를 최소화하는 것을 찾는 것이다. 그런데 우리는 $p(\textbf{x})$를 모르는 상황이기에 directly할 수 없다. 대신 N개의 train data가 존재하므로 이를 이용하면

$$KL(p\|q) \approx \sum_{n=1}^{N}{\{ - \ln q (\textbf{x}_n \| {\bf \theta}) + \ln p(\textbf{x}_n)} \}$$

우변의 두번째항은 parameter와 independent하다. 첫번째항은  negative log likelihood function for $\theta$ of under the distribution $q(\textbf{x} \| \theta)$ (train data를 통해 만들어진 분포) 이므로

- **KL divergence를 최소화하는 것은 likelihood function을 최대화하는 것이다.**

이번에는 joint distribution을 생각해보자. 두 변수가 independent이면 $p(\textbf{x}\textbf{y}) = p(\textbf{x})p(\textbf{y})$ 이다. 하지만 independent가 아닌 경우, 우리는 KL divergence를 통해 얼마나 independent와 가까운지 생각해볼수 있다.

$$I[\textbf{x}, \textbf{y}] \equiv KL(p(\textbf{x}, \textbf{y}) \| p(\textbf{x})p(\textbf{y})) = - \int \int p(\textbf{x}, \textbf{y}) \ln \frac{p(\textbf{x})p(\textbf{y})}{p(\textbf{x}, \textbf{y})}d\textbf{x} d\textbf{y}$$

이를 *mutual information* between the variable x and y 라고 부른다.
- conditional entropy의 측면에서
  - $I[\textbf{x}, \textbf{y}] = H[\textbf{x}] - H[\textbf{x}|\textbf{y}] = H[\textbf{y}] - H[\textbf{y}/\textbf{x}]$
  - 따라서 MI는 y를 알고 난 뒤에 x의 uncertainty가 줄어든 정도 (반대도 성립) 라고 할 수 있다.
- Bayesian의 입장에서는
  - $p(\textbf{x})$가 pior이고 $p(\textbf{x} | \textbf{y})$는 y data를 얻은 후의 posterior이다.
  - 따라서 MI는 새로운 observation y의 등장으로 줄어든 x의 uncertainty라고 할 수 있다.

