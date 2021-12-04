# [Generative Model] Introduction to Generative Models


Generative model에 대해 간단하게 정리하였다.

<!--more-->

## Discriminative vs Generative models in supervised learning
공통적인 목적은 *learn a function to map* $\textbf{x}\rightarrow y$ 이라고 할 수 있다.

- discriminative
  - goal : directly estimate $p(y\|\textbf{x})$
  - should : $f(\textbf{x})=\arg\max_y p(y\|\textbf{x})$
  - 그래서 decision bounday를 구한다.
- generative
  - goal : estimate $p(\textbf{x}\|y)$, then find $p(y\|\textbf{x})$
  - should : $f(\textbf{x})=\arg\max_y p(\textbf{x}\|y)p(y)$
  - probability distribution을 구한다.

## Generative model in unsupervised learning
unsupervised learning에서 generative model은 주로
- density estimation
- sample generation

의 역할을 한다.

generative model은 크게 두 가지로 나눠서 이해할 수 있다 (Taxonomy of generative models)
- explicit density estimation
  - explicitly define and solve $P_\text{model} (\textbf{x})$
- implicit density estimation
  - learn a model that can sample from $P_\text{model} (\textbf{x})$ w/o explicitly defining it

주로 공부할 분야는 explicit에서 approximation에 해당하는 VAE와 implicit에서의 GAN에 대해 공부할 것이다. 그렇다면 왜 요즘 들어 neural net을 이용한 generative models이 연구되고 있을까? 전통적인 generative model의 단점을 살펴보자.

- data structure에 대한 강한 가정을 필요로 한다.
- approximation을 하는 경우 suboptimal한 결과를 얻는다.
- MCMC같은 경우 computationally expensive하다.

그럼 이에 반해 neural net기반 모델들은 어떤 장점이 있을까?

- backprop을 통해 function approximation에서 강한 면모를 보여준다.
- 예를 들어 VAE의 경우,
  - 강한 가정이 필요없다.
  - 훈련속도가 빠르다.
  - approximation이기는 하지만 성능이 좋다.

그렇다면 neural net기반의 generative model의 종류를 살펴보자.
- Autoregressive model
  - model $p(\textbf{x})$ by $\prod_{i=1}^n p(\textbf{x}_i \| \textbf{x} _{i-1},\textbf{x} _{i-2},...,\textbf{x} _1 )$
  - eg) PixelRNN, PixelCNN, WaveNet...
- Helmholtz machine
  - model $p(\textbf{x})$ by $\int p(\textbf{z})p(\textbf{x}\|\textbf{z})d\textbf{z}$
  - eg) VAE
- Generative adversarial network
  - no explicit density modeling
  - train models by solving minmax problem

복잡해 보이는 generative model을 왜 공부해야하는지 Goodfellow가 몇가지 알려줬는데 살펴보자.
- to represent/manipulate high-dim/complicated probability distribution
- can be incorporated into RL
- can be trained with missing data (expand to semi-sup-learning)
- enable machine learning to work with multi-modal outputs
- enable inference of latent representations
- realistic generation of samples

## Posterior inference
비단 딥러닝뿐만 아니라 통계학에서도 posterior inference은 상당히 중요하다. 결국에는 모델은 구하고 훈련하는 모든 과정은 posterior inference를 하는 것이다.

latent variables $\textbf{z}$와 observed variables $\textbf{x}$가 있을 때 우리는 latent variables의 posterior를 구하는 것이 목표이다. 주로 우리가 해야하는 task는 posterior distribution을 구하는 경우나 posterior를 이용한 expectation을 구하는 경우가 많다. 하지만 이는 쉽지 않다. 왜?
- latent space가 너무 차원이 높다.
- posterior가 너무 complex해서 analytically tractable하다.
- (deep learning의 경우) hidden variable간의 interaction이 너무 많다.

그래서 우리는 approximation을 한다.

## Approximate posterior inference
우리는 posterior를 왜 approximation하는지 알아봤다. 이제는 그럼 approximation하는 방법론에 대해 간단히 알아보자.

- Stochastic
  - 대표적인 방법론은 MCMC
  - computationally demanding
- Deterministic
  - 대표적인 방법론은 Variational Inference
  - posterior에 analytical approximations
  - never generate exact results
