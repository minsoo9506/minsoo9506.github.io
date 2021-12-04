# [Generative Model] Variational Inference


VAE를 공부하기 위해 variational inference에 대해 간단하게 공부해보자.

<!--more-->

## Variational Inference
MCMC와 같이 sampling에 기반한 방법으로 posterior를 구하는 것이 아니라 inference problem을 optimization problem으로 바꾼다는 것이라고 할 수 있다. 전체적인 흐름을 정리하고 공부해보자.

1. latent $\textbf{z}$의 posterior $p(\textbf{z}\|\textbf{x})$를 approximation하는 분포의 *family*를 $Q$라고 하자.
2. 우리는 $Q$에 속하는 분포들 중에서 exact posterior와 가장 가까운 분포 $q(\textbf{z})$를 구하고자 한다.

$$q(\textbf{z})=\arg\min_{q(\textbf{z}) \in Q}D_{KL}[q(\textbf{z}) || p(\textbf{z}|\textbf{x})]$$

결국 approximation하는 $q(\textbf{z})$를 구해서 우리가 원하는 task를 진행하면 된다.

그렇가면 KL-divergence를 최소화하는 optimization을 진행하기 위해서는 적절한 $Q$를 정해야 한다. 여기서 고려해야할 점이 있다.
- 우리가 posterior를 tractable하지 않아서 approximation하는 것이므로 $Q$는 적절한 restriction을 걸어서 tractable하게 정해야한다.
- 하지만 그 restriction이 너무 강하다면 posterior에 근접하지 못한 결과를 얻을 것이다.

## ELBO
이제 variational inference를 어떻게 하는지 살펴보자. 이 과정에 있어서는 다양한 접근방법이 존재한다. 다양한 자료를 살펴보면 이해의 폭을 넓힐수 있을 것이다.

$$D_{KL}[q(\textbf{z}) || p(\textbf{z}|\textbf{x})]=\int q(\textbf{z})\frac{\log q(\textbf{z})}{\log p(\textbf{z}|\textbf{x})}d\textbf{z}$$

$$=E_\textbf{z}[\log q(\textbf{z})]-E_\textbf{z}[\log p(\textbf{z}|\textbf{x})] $$

$$=E_\textbf{z}[\log q(\textbf{z})]-E_\textbf{z}[\log p(\textbf{z},\textbf{x})]+\log p(\textbf{x})$$

그런데 위의 식에서 마지막 부분인 $\log p(\textbf{x})$을 보자. obseved data의 분포를 쉽게 구할 수 있을까? intractable하다. 그래서 우리는 위의 식을 directly optimization하는 것이 아니라 **Evidence lower bound (ELBO)** 를 최대화하는 방법으로 우회한다. 어떻게 하는지 알아보자. 아래의 식에서 우항을 ELBO하고 부른다.

$$\log p(\textbf{x})-D_{KL}[q(\textbf{z})|| p(\textbf{z}|\textbf{x})] = E_\textbf{z} [\log p(\textbf{z},\textbf{x})]-E_\textbf{z}[\log q(\textbf{z})]$$

우리는 원래 $D_{KL}$을 최소화하는 것이 목표였다. 그 의미는 결국 **ELBO를 최대화** 하는 것과 같다. (좌변의 $\log p(\textbf{x})$는 어차피 constant wrt $q(\textbf{z})$)

여기서 또한 왜 ELBO라고 부르는지 알 수 있다. evidence $p(\textbf{x})$의 lower-bound이기 때문인데, KL-divergence가 항상 0이상의 값을 가진다는 사실과 아래의 식을 보면 이해할 수 있다.

$$\log p(\textbf{x}) = \text{ELBO} + D_{KL}[q(\textbf{z})|| p(\textbf{z}|\textbf{x})]$$

ELBO를 조금 더 알아보자.

$$L(q) = E_\textbf{z} [\log p(\textbf{z},\textbf{x})]-E_\textbf{z}[\log q(\textbf{z})] $$
$$= E_\textbf{z} [\log p(\textbf{x}|\textbf{z})] + E_\textbf{z} [\log p(\textbf{z})]-E_\textbf{z}[\log q(\textbf{z})] $$
$$ =E_\textbf{z} [\log p(\textbf{x}|\textbf{z})]-D_{KL}[q(\textbf{z})|| p(\textbf{z})]$$

최종식에서
- 앞부분은 expected log likelihood of the data
- 뒷부분은 KL-D berween prior $p(\textbf{z})$ and $q(\textbf{z})$

으로 이해할 수 있다.

## Restriction on Q

이 글의 초반에 적절한 $Q$를 정해야 한다고 했다. 이를 위해 주로 사용하는 restriction을 두가지 정도 알아보자.

- Parameterization
  - 식 $q$를 parametric distribution으로 한정한다.
  - $q(\textbf{z};\phi)=q_{\phi}(\textbf{z})$
  - $\phi$ : variational parameters
  - 그렇다면 이제 posterior inference는 variational parameters inference가 되는 것이다.
  - VAE에서는 $q_{\phi}(\textbf{z})$를 neural net으로 modeling한다.

- Factorization
  - assume $q(\textbf{z})=\prod_{j=1}^m q_j (z_j)$

마지막으로 variational inference에 대한 간단한 예시를 통해 어떤 식으로 진행되는지 정리해보자.

- 먼저, 어떠한 임의의 model $p(\textbf{x},\textbf{z})$로 시작한다.
- 적절한 approximation function $q_{\phi}(\textbf{z})$를 정한다.
- ELBO를 구한다.
  - 예를 들어, $L(\phi)= \textbf{x}^2\phi^3+\log \phi$
- derivatives를 구해서 optimize한다.
  - $\phi_{t+1} = \phi_{t} + \alpha \triangledown_\phi L(\phi)$
