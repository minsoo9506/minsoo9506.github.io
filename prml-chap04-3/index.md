# [PRML] Chapter4 - Linear Models For Classification (3)


PRML에서 Naive bayes 와 Logistic regression에 대해 공부하였는데 이 둘의 관계에 대해 간단히 정리해보고자 한다. (문일철 교수님의 강의에 대해 정리하였습니다.) 몇 가지 가정(constraint)가 더해지면 Naive bayes와 Logistic regression이 같아진다.

<!--more-->

## Gaussian Naive Bayes
Naive Bayes에 대해서는 이전에 공부하였다. 이번에는 거기에 조금 더 추가하여 각 conditional distribution들이 Gaussian distribution이라고 가정해보자.

$$f_{NB} = \arg\max_{Y=y}P(Y=y)\prod_{i=1}^{D}P(X_i=x_i|Y=y)$$

$$P(Y=k) = \pi_k$$

$$P(X_i=x_i|Y=y) = \frac{1}{c \sigma_k^i } \exp(- \frac{1}{2}(\frac{x_i - \mu_k^i}{\sigma_k^i})^2)$$

이제 Naive bayes classifier이 Logistic regression의 형태가 되는 과정을 살펴볼 것이다.
- Logistic regression : $P(Y=k \| X)$
- Naive Bayes : $\frac{P(X\|Y=k)P(Y=k)}{P(X)}$
  - generative 방법의 Naive bayes로 부터 Discriminative한 Logistic regression으로 가보자

$$ = \frac{p(Y=k)\prod_{i=1}^D P(X_i|Y=y)}{p(Y=k)\prod_{i=1}^D P(X_i|Y=k) + p(Y=k^C)\prod_{i=1}^D P(X_i|Y=k^C)}$$
$$= 1/[1 + \frac{\pi_2 \prod \frac{1}{c \sigma_{not\;k}^i } \exp(- \frac{1}{2}(\frac{x_i - \mu_{\text{not}\;k}^i}{\sigma_{\text{not}\;k}^i})^2) }{\pi_1 \prod \frac{1}{c \sigma_{k}^i } \exp(- \frac{1}{2}(\frac{x_i - \mu_{k}^i}{\sigma_{k}^i})^2) }]$$

여기서 $\sigma_{not \; k} = \sigma_{k}= \sigma$라고 가정하면

$$=1/[1+\frac{\pi_2 \prod \exp(- \frac{1}{2}(\frac{x_i - \mu_{not\;k}^i}{\sigma^i})^2) }{\pi_1 \prod \exp(- \frac{1}{2}(\frac{x_i - \mu_{k}^i}{\sigma^i})^2) }] $$
$$ = 1/[1+exp(-\frac{1}{2 {\sigma^i}^2} \sum \{ 2(\mu_{not\;k}^i-\mu_k^i)x_i + {\mu_{not\; k}^i}^2 - {\mu_k^i}^2 + \log\pi_2 - \log\pi_1  \}] $$

최종식을 보면 sigmoid function에 $w^T x$가 들어가있는 형태이다. 즉, Logistic regression이 되는 것이다.

- Naive Bayes의 conditional independent 가정
- conditional한 상황에서 각 feature들이 Gaussian이고 분산이 같다는 가정

