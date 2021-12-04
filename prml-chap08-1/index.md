# [PRML] Chapter8 - Graphical Models (1)


graphical model에 대해 정리하였다.
<!--more-->

- probabilistic graphical models 의 장점
  - probabilistic model의 구조를 시각화하기 좋다.
  - model에 대한 insight를 얻을 수 있다.
  - 복잡한 modeling을 graphical적인 방법으로 다룰 수 있다.

probabilistic graphical model에서 각 **node**는 random variable을 의미하고 **link**는 그들의 관계를 의미한다. joint distribution이 각 factor들의 product로 decomposed되는 모습을 주로 나타낸다.
- *Bayesian network* (directed graphical model) : link가 특정한 방향을 가르키는 경우
- *Markov random field* (undirecte graphical model) : 방향이 없는 경우

## 8.1 Bayesian Networks
먼저 간단한 예시를 보자.

$$p(a,b,c) = p(c|a,b)p(b|a)p(a)$$

위의 식처럼 우리는 **joint distribution을 분해**할 수 있다. 이때 node a는 node b의 *parent* node라고 부른다. 반대로 node b는 node a의 *child* node이다. 그리고 link의 방향은 given에서 출발한다. 즉, node a에서 node b로 화살표가 그려지는 것이다. 그리고 이와 같이 (특정순서대로) 모든 node에게서 link를 받으면 이 graph를 *fully conneted* 하다고 한다. 이를 일반화하면

$$p(\textbf{x}) = \prod_{k=1}^{K}{p(x_k|pa_k)}$$

의 형태이다. $pa_k$는 parent node들을 의미한다. 우리가 다루는 directed graph는 no *direted cycle* 이라는 제약이 있다. 이는 directed acyclic graph라는 의미로 parent node로 다시 돌아가는 link가 없다는 의미이다.

### 8.1.1 Example : Polynomial regression
polynomial regression에서 prediction을 graphical model로 나타내면

<center><img src="../assets/img/chap08-1.jpg" width="50%"></center>

색이 칠해진 $t_n$ 부분은 given의 의미이다.

### 8.1.2 Generative models
- 주어진 probability distribution에서 sampling을 해야하는 경우가 있다. 다양한 방법들 중에 여기서는 graphical model과 관련있는 *ancestral sampling* 에 대해 알아보자. joint distribution $p(x_1,...x_k)$에 sampling을 하고자 한다. 우리는 이를 적절한 directed acyclic graph로 factorization할 것이다. 그리고 높은 숫자 node에서 낮은 숫자 node로의 link는 없다고 가정한다.
- 따라서 먼저 $p(x_1)$에서 sampling을 하고 $\hat{x}_1$이 parent로 있는 conditional distribution에서 sampling을 한다. 이를 반복해서 $p(x_i\|pa_i)$을 구하다보면 결국 우리가 원하는 joint distribution에서 sample을 얻는 것과 동일한 결과를 만들 수 있다. 특정 변수의 marginal distribution을 얻고 싶으면 필요한 sample만 쓰면 된다.
- probabilistic model에서 주로 higher-numbered node는 observation을 나타내고 lower-numbered node는 주로 latent variable에 해당한다. 이러한 구조를 이용하여 observed data가 만들어지는 과정을 해석할 수 있다.
- graphical model는 observed data가 만들어진 *causal* 과정을 잡을 수 있다. 이러한 이유로 그런 model를 *generative* model이라고 한다. 앞에서 본 polynomial regression같은 경우는 generative model이 아니다. input $x$과 관련한 probability distribution이 없기 때문이다.

### 8.1.3 Discrete variables
discrete variable들의 joint distribution을 DAG로 표현해보자. probability distribution $p(\textbf{x}|{\pmb \mu})$ for a single variable $\textbf{x}$이 있다. K개의 states를 가질 수 있다고 하면

$$p(\textbf{x} | {\pmb \mu}) = \prod_{k=1}^{K}{\mu_k^{x_k}}$$

이번에는 2개의 discrete variable의 joint distribution을 modeling한다고 하자.

$$p(\textbf{x} _ 1, \textbf{x} _ 2 | {\pmb \mu}) = \prod_{k=1}^{K} \prod_{l=1}^{K} \mu_{kl}^{x_{1k} x_{2l}}$$

여기서 $\sum_k \sum_l \mu_{kl}=1$이라는 제약식이 있다. 따라서 이 distribution에서 parameter는 $K^2-1$개 이다. M개의 variable의 경우는 $K^M-1$이 될 것이고 이는 상당히 많은 양(exponentially)의 parameter를 요구하는 것이다. 
- 만약 variable들이 서로 independent하다면 $M(K-1)$개의 parameter가 필요하다. link를 drop하여 더 간단한 model이 되는 것이다. 대신에 제한적인 distribution을 얻는다.
- markov chain과 같은 형태라고 가정하면 필요한 parameter는 $K-1+(M-1)K(K-1)$개 이다.
- parameter의 수를 줄이는 또 다른 방법은 parameter를 sharing하는 방법이다. 예를 들어 $p(x_i \| x_{i-1})$의 conditional distribution이 모두 동일한 $K(K-1)$개의 parameter를 공유한다고 하면 전체 parameter는 $K-1 + K(K-1)$개가 된다.
- 또 다른 방법은 parameterized model을 이용하는 것이다. 예를 들어, $p(y=1\|x_1,...,x_M)=\sigma(\textbf{w}^T\textbf{x})$와 같은 형태를 이용하면 필요한 parameter는 $M$에 linear한 갯수가 필요하게 된다. 

위의 방법들은 probabilistic modeling에서 parameter의 갯수를 줄일 수 있는 방법들이다.

### 8.1.4 Linear-Gaussian models
각 node i는 single continuous random variable $x_i$ (Gaussian distribution) 를 나타낸다. 해당 분포의 mean은 parent node들의 linear combination으로 이루어져있다.

$$p(x_i|pa_i) = N(x_i|\sum_{j \in pa_i} w_{ij}x_j + b_i, v_i)$$

log of joint distribution은 log of product of all conditional node 이고 이를 전개하면

$$\ln p(\textbf{x}) = \sum_{i=1}^{D} \ln p(x_i|pa_i) \\ = - \sum_{i}^{D} \frac{1}{2v_i} (x_i - \sum_{j \in pa_i}w_{ij}x_j - b_i)^2 + \text{const}$$

여기서 $\textbf{x}$의 quadratic form이라는 것을 알 수 있고 따라서 joint distribution $p(\textbf{x})$은 multivariate gaussian이라는 것을 알 수 있다.

우리는 joint distribution의 mean과 covariance를 recursively 구할 수 있다. 각 variable $x_i$는 아래와 같은 형태를 갖고 있는데

$$x_i = \sum_{j \in pa_i} w_{ij}x_j + b_i + \sqrt{v_i}\epsilon_i$$

$$E[x_i] = \sum_{j \in pa_i} w_{ij} E[x_j] + b_i \\ E[\textbf{x}] = (E[x_1], E[x_2],...,E[x_D])^T$$

따라서 우리는 lowest numbered node에서 시작하여 recusively mean을 구할 수 있다.

이제 covariance를 구해보면

$$cov[x_i, x_j] = E[(x_i-E[x_i])(x_j-E[x_j])]$$
$$=E[(x_i-E[x_i])\{ \sum_{k \in pa_j} w_{jk}(x_k-E[x_k])+\sqrt{v_j}\epsilon_j\}]$$
$$=\sum_{k \in pa_j}w_{jk}cov[x_i,x_j]+I_{ij}v_j$$

여기서도 똑같이 recusively 구할 수 있다.

따라서 위에서 discrete에서 공부한 것처럼 parameter의 갯수를 줄이는 방법들을 여기에서도 동일하게 적용할 수 있다.

## 8.2 Conditional Independence

$$p(a|b,c) = p(a|c)$$

위와 같은 경우 a is conditionally independent of b given c 라고 한다. 이는 아래와 같은 경우도 해당한다.

$$p(a,b|c)=p(a|b,c)p(b|c) = p(a|c)p(b|c)$$

두 가지 버전의 conditional independence 정의이다. 또 다른 notation으로는

$$a \bot b | c $$

conditional independence는 probabilistic model에서 model의 structure와 computation을 단순화한다. joint distribution에서 conditional independence의 특징을 graphical한 차원에서 바로 파악할 수 있다. 이 방법을 *d-seperation* 이라고 한다.

### 8.2.1 Three example graphs
총 3가지의 경우에 대해 살펴 볼 것이다.

- tail-to-tail

$$p(a,b,c) = p(a|c)p(b|c)p(c)$$

여기서 모든 변수들이 observed되지 않았고 a,b의 independent를 보기 위해 c에 대해 marginalize하면

$$p(a,b) = \sum_c p(a|c)p(b|c)p(c)$$

이는 $p(a)p(b)$로 factorize되지 않는다. 즉 independent하지 않다는 의미이다. 그렇다면 이번에는 c가 given인 상황을 가정해보자.

$$p(a,b|c) = \frac{p(a,b,c)}{p(c)} = \frac{p(a|c)p(b|c)p(c)}{p(c)} = p(a|c)p(b|c)\\ \therefore a \bot b | c $$

- 여기서 node c는 *tail-to-tail* 이라고 한다.
  - link가 node c에서 node a,b로 간다.
  - 이를 통해 node a와 node b사이의 path가 생긴다.
  - 이는 두 node가 dependent하다는 의미이다.
  - 하지만 node c가 observed되는 순간 (conditioned on c) a와 b사이의 path를 block한다.
  - 그래서 a와 b는 conditionally independent해진다.

- head-to-tail

$$p(a,b,c) = p(a)p(c|a)p(b|c) \\\ p(a,b) = p(a)\sum_c p(c|a)p(b|c)=p(a)p(b|a)$$

여기서도 마찬가지로 independent하지 않다. 하지만 c가 given이면

$$p(a,b|c) = \frac{p(a,b,c)}{p(c)} = \frac{p(a)p(c|a)p(b|c)}{p(c)}=p(a|c)p(b|c)$$

- 여기서 node c는 *head-to-tail* 이라고 한다.
  - node a에서 b로 가는 사이에 존재한다.
  - 이는 두 node가 dependent하다는 것이다.
  - 하지만 node c가 given되는 순간 두 node의 path는 block된다.
  - 그래서 a와b는 conditionally independent해진다.

- heat-to-head

$$p(a,b,c) = p(a)p(b)p(c|a,b) \\\ p(a,b) = p(a)p(b)$$

이번에는 위에 나왔던 내용들과 반대이다. c가 given이 아닌 경우, a와b가 independent하다. 오히려 c가 given이면 independent가 아닌 상태가 된다.

$$p(a,b|c) = \frac{p(a,b,c)}{p(c)} = \frac{p(a)p(b)p(c|a,b)}{p(c)}$$

- 여기서 node c는 *head-to-head* 이라고 한다.
  - node a,b에서 node c로 화살표가 향하는 형태이다.
  - node c가 unobserved되면 이는 path를 block한다.
  - 그래서 a와 b는 independent하다.
  - 오히려 c가 observed되면 dependent해진다.

### 8.2.2 D-seperation
위에서 살펴본 3가지의 종류에 따라서 joint distribution을 factorization한다. D-seperation이 특별한 방법론은 아니고 graphical적인 접근으로 conditional independent를 효율적으로 파악하고 이용한다. 책에서는 예시를 통해 이 과정을 설명한다. 책에서는 directed graph를 filter처럼 생각하라고 한다. joint distribution을 filter를 거치게 되면 d-seperation에 따라 conditional independent를 확인하고 factorization까지 한다.

## 8.3 Markov Random Fields
*Markov random fields* (markov network, undirected graphical model)는 link에 방향성이 없는 것이다. 똑같이 각 node들은 a variable or group of variable을 의미하며 link에는 화살표가 없다. 마찬가지로 conditional independence와 fatorization과 관련하여 공부할 것이다.

### 8.3.1 Conditional independence properties
위에서 d-seperation을 통해 conditional independence에 대해 파악할 수 있었다. 그런데 head-to-head 때문에 다소 개념이 헷갈리는 부분이 분명 존재한다. 이에 대한 수요로 undirected graphical model이 나오게 되었다.

A,B,C 라는 set of nodes들이 있다고 가정하자. 우리가 궁금한 conditional independence는 아래와 같다.

$$A \bot B | C $$

 A에서 C를 거쳐서 B로 가는 모든 길이 block되면 conditional independence가 성립한다. 하나 이상의 길이 존재하면 이는 성립하지 않는다. 즉, 이 조건을 만족하지 않는 어떤 random variable의 분포가 하나라도 있다면 conditional independence라고 할 수 없는 것이다. 다른 시각으로는 C의 모든 node를 없애면서 이와 연결된 link도 없앤 후에 A와 B사이의 path가 존재하지 않는다면 conditional independence하고 할 수 있다.

### 8.3.2 Factorization properties
이제 undirected graph에서 factorization rule에 대해 알아보자.
- two node $x_i,x_j$가 연결되지 않았다고 가정하자.
  - 해당 변수들은 conditionally independent given all other nodes
  - 즉, 두 node끼리의 direct path는 없고 indirect path가 지나는 변수들은 observed된 것이다.

$$p(x_i,x_j|\textbf{x} _ {-(i,j)})=p(x_i|\textbf{x} _ {-(i,j)})p(x_j|\textbf{x} _ {-i,j})$$

따라서 joint distribution을 factorization하면 두 node i, j는 같은 factor에 속하지 않을 것이다. 여기서 *clique*의 개념이 나온다.
- clique
  - subset of the nodes in a graph 이고
  - 모든 node들이 directly 연결되어 있다. (fully connected)

따라서 joint distribution을 decomposition할 때 각 factor들이 clique의 변수들로 이루어진 함수들이다.

- clique를 $C$라고 하고 $\textbf{x}_C$를 해당 clique에 속하는 variable이라고 하자.
-  joint distribution은 아래와 같이 decomposition할 수 있다.
- *potential functions* $\psi_C (\textbf{x}_C)$의 곱으로 이루어져있다.
  - $\psi_C(\textbf{x}_C) \ge 0$
  - $Z$ 는 normalization constant
  - 아래식은 discrete이라 summation이고 continuous의 경우 integral

$$p(\textbf{x}) = \frac{1}{Z} \prod_C \psi_C(\textbf{x} _ C)\\\ Z=\sum_{\textbf{x}} \prod_C \psi_C (\textbf{x}_C)$$

directed graph에서는 각 factor들이 conditional distribution given parents의 형태였다. 하지만 여기서 potential function을 선택하는데 있어 제약이 없다. 하지만 그에 반해 normalization constant의 존재가 undirected graph의 가장 큰 한계점이라고 할 수 있다.

factorization과 conditional independence의 관계를 알아보기 위해서는 potential function은 strictly positive한 값을 가진다고 가정한다. 그래서 exponential form을 이용한다.

$$\psi_C(\textbf{x}_C) = \exp\{-E(\textbf{x}_C)\}$$

- $E(\textbf{x}_C)$ : *energy function*
- 위의 식처럼 exponential한 표현을 *Boltzmann distribution*이라고 한다.

joint distribution은 각 potential function의 곱으로 이루어져있으므로 따라서 joint distribution은 각 clique의 energy function의 합으로 이루어지는 것을 의미한다.

directed graph에서의 joint distribution과 다르게 확률적인 해석을 하기 어렵다. 하지만 potential function을 자유롭게 선택할 수 있으며 이는 domain마다 적절하게 선택해야 한다.

### 8.3.3 Image de-noising
binary image에서 noise removal을 하는 예시를 살펴보자.
- noise-free image가 binary pixel $x_i \in \{-1,1\}$ (unknown)
- pixel 일부를 random하게 반대로 바꾸면 noise image의 binary pixel $y_i \in \{-1,1\}$ (known)

이를 통해 clique를 정하여 energy function을 찾아보자. energy function을 작게 만들어야 joint distribution을 커지게 할 수 있다.
- 각 pixel별로 clique를 만들수 있을 것이다. $\{x_i, y_i\}$로 만들 수 있다.
  - $-\eta x_i y_i$ : 두 변수가 같은 값이면 lower energy, 반대부호이면 higher energy
  - $\eta > 0$
- 이번에는 pixel의 바로 옆 neighborhood의 경우 $\{x_i,x_j\}$
  - $-\beta x_i x_j$ : 두 변수가 같은 값이면 lower energy, 반대부호이면 higher energy
  - $\beta > 0$
- 마지막으로 biasing the model toward one particular sign 위해
  - $h x_i$ : pixel i in the noise-free image

이를 통해 complete energy function과 joint distribution을 구하면

$$E(\textbf{x},\textbf{y}) = h \sum_i x_i - \beta \sum_{\{i,j\}} x_i x_j -\eta \sum_i x_i y_i$$

$$p(\textbf{x},\textbf{y}) = \frac{1}{Z} \exp\{-E(\textbf{x},\textbf{y})\}$$

$\textbf{y}$는 observed value이고 따라서 conditional distribution $p(\textbf{x}\|\textbf{y})$을 define할 수 있다. 여기서 우리는 확률을 높이는 $\textbf{x}$를 구해야 할 것이다. 이를 위해 iterative한 방법을 사용할 것인데
- *iterated conditional modes* 라는 방법이다.
  - $x_i = y_i$로 시작한다.
  - 각 $x_i$ 마다 (-1 or 1로) 값을 바꿔가면서 lower energy를 갖는 값을 선택한다.
    - 하나의 $x_i$값을 계산하기 때문에 빠르게 진행할 수 있다.
  - 특정 criterion을 만족할 때까지 반복한다.

뒤에서 high probability를 찾는 max-product algorithm에 대해 살펴볼 것이다.

### 8.3.4 Relation to directed graphs
예시를 통해 둘의 관계에 대해 알아보자.

먼저, MC형태의 directed graph의 joint distribution이 아래와 같다.

$$p(\textbf{x}) = p(x_1)p(x_2 | x_1)...p(x_N | x_{N-1})$$

이를 undirected graph로 나타내보자. (maximal) clique를 고려하여 joint distribution을 나타내면

$$p(\textbf{x}) = \frac{1}{Z} \psi_{1,2}(x_1, x_2)\psi_{2,3}(x_2, x_3)...\psi_{N-1,N}(x_{N-1}, x_N)$$

그렇다면 어렵지 않게

$$\psi_{1,2}(x_1, x_2)= p(x_1)p(x_2 | x_2)\\ \psi_{2,3}(x_2, x_3)=p(x_3 | x_2) \\\ ... \\\ \psi_{N-1,N}(x_{N-1}, x_N) = p(x_N | x_{N-1})$$

로 나타낼 수 있을 것이다. conditional distribution에 관련한 variable들을 같은 clique의 멤버로 하면 된다. 또 다른 예시를 보자.

$$p(\textbf{x})=p(x_1)p(x_2)p(x_3)p(x_4 | x_1,x_2,x_3)$$

위와 같은 경우를 undirected graph로 나타내기 위해서는 마지막항으로 인해 원래는 directed graph에는 없던 link를 만들어야한다. (같은 clique에 속하게 해야하니까) 이렇게 'marrying parents' 하는 과정을 *moralization*이라고 하며 link를 만들고 arrow를 없애는 과정으로 만들어진 graph를 *moral graph*라고 한다.

이처럼 directed를 undirected로 바꾸는 과정에서 conditional property를 많이 잃게 된다. 그래서 최대한 이를 지켜주면서 바꾸는게 중요한 듯 하다. 그리고 반대로 undirected에서 directed로 바꾸는 경우는 많지 않다고 한다. 그 이유중 하나는 normalization constant 때문이라고 할 수 있다.
