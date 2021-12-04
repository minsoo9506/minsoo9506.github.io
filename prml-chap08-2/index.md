# [PRML] Chapter8 - Graphical Models (2)


graphical model에서 inference하는 방법에 대해 정리하였다. (미완성)
<!--more-->

## 8.4 Inference in Graphical Models
node들에 대한 posterior를 계산하고 싶다고 하자. 이번 장에서는 exact inference에 대해 집중해서 알아보자.

### 8.4.1 Inference on a chain
chain의 모습을 갖는 undirected의 joint distribution에 대해 살펴보자. 각 variable들은 $K$개의 states를 갖는 discrete variable이라고 가정한다. 그러면 joint disribution은 $(N-1)K^2$개의 parameter들을 갖고 있다. 

$$p(\textbf{x})=\frac{1}{Z}\psi_{1,2}(x_1,x_2)\psi_{2,3}(x_2,x_3)...\psi_{N-1,N}(x_{N-1},x_N)$$

이제 marginal distribution $p(x_n)$를 inference해보려고 한다. 가장 쉽게 보이지만 복잡하고 시간이 오래걸리는 방법은 아래처럼 다 summation하는 것이다.

$$p(x_n) = \sum_{x_1}...\sum_{x_{n-1}}\sum_{x_{n+1}}...\sum_{x_{N}}p(\textbf{x})$$

joint는 K개의 state를 갖는 N개의 variable이 있기 때문에 $K^N$개의 값이 존재하고 이를 계산하는 것은 비효율적이다. 그렇다면 chain의 특징을 이용해서 조금 더 효율적인 방법을 이용해보자.

$$p(x_n)=\frac{1}{Z}[\sum_{x_{n-1}}\psi_{n-1,n}(x_{n-1},x_n)...[\sum_{x_2}\psi_{2,3}(x_2,x_3)[\sum_{x_1}\psi_{1,2}(x_1,x_2)]]...] \\\ [\sum_{x_{n+1}}\psi_{n,n+1}(x_n,x_{n+1})...[\sum_{x_N}\psi_{N-1,N}(x_{N-1},x_N)]...]$$

위의 방법으로 구하면 total cost는 $O(NK^2)$이다. chain처럼 conditional independence를 찾아서 이용하는 것의 장점을 느낄 수 있다. 위와 같은 방법은 local *messages*를 보내는 것으로 해석할 수 있다. 크게 보면 marginal $p(x_n)$은 두 개의 factor로 나누어서 생각할 수 있다.

$$p(x_n) = \frac{1}{Z}\mu_{\alpha}(x_n)\mu_{\beta}(x_n)$$

각각 $x_n$의 앞, 뒤에서 흘러오는 message로 이해할 수 있다.

### 8.4.2 Trees
graph에서 tree는 어떤 두 개의 node를 선택했을 때 오직 하나의 path만 존재하는 것을 의미한다. 그리고 모든 node는 하나의 parent node를 갖고 가장 위에 있는 node는 root라고 부른다. local message passing을 이용한 inference를 이 tree에 이용하는 sum-product algorithm에 대해 배울 것이다.

### 8.4.3 Factor graphs
graph에 node를 추가하여서 decomposition을 explicit하게 하는 것이다. $x_s$를 subset of the variable이라고 하면 joint를 아래와 같이 나타낼 수 있다.

$$p(\textbf{x}) = \prod_s f_s (\textbf{x}_s)$$

여기서 $f_s$는 a function of a corresponding set of variables 이다. 각 factor $f_s(\textbf{x}_s)$는 directed의 경우 local conditional distribution의 역할과 같고 undirected의 경우 potential function이라고 할 수 있다.

<center><img src="../assets/img/chap08-2.jpg" width="50%"></center>

undirected, directed 모두 factor graph로 일반화가 가능해지는 것으로 이해할 수 있을 것 같다.

### 8.4.4 The sum-product algorithm
tree-structured graph에서 exact inference를 하기 위한 방법을 알아보자. variable들은 discrete이라고 가정하기에 summation으로 계산을 진행한다. (물론 continuous도 동일하게 가능) loop없는 directed graph에서 exact inference하는 알고리즘은 *belief propagation*이라 하고 이는 sum-product algorithm의 특별한 경우에 해당한다.

original graph는
- undirected tree, directed tree, polytree

이고 이에 대응되는 factor graph는 tree structure를 가진다. original graph는 factor graph로 바꾸는 과정을 통해 undirected, directed에 동일한 방법을 적용할 수 있게 된다. 우리는 이런 과정을 통해 최종적으로 얻고자 하는 바는 아래와 같다.
- to obtain an efficient, exact inference algorithm for finding marginals
- in situations where several marginals are required to allow computations to be shared efficiently

먼저 marginal을 구하는 것부터 시작해보자.

$$p(x) = \sum_{\textbf{x}-x}p(\textbf{x})$$

우리는 tree structure를 다루고 있고 이를 통해 joint distribution의 factor들을 그룹으로 partition할 수 있다.

$$p(\textbf{x})=\prod_{s\in ne(x)}F_s(x,X_s)$$

- $ne(x)$ : 이웃 variable을 의미
- $X_s$ : factor node $f_x$를 통해 $x$와 연결된 subtree에 있는 set of all variables
- $F_s(x,X_s)$ : the product of all the factors in the group associated with factor $f_s$

이를 통해 marginal식을 살펴보면

$$p(x) = \sum_{\textbf{x}-x}\prod_{s\in ne(x)}F_s(x,X_s)\\\ =\prod_{s\in ne(x)}\sum_{\textbf{x}-x}F_s(x,X_s) \\\ =\prod_{s\in ne(x)}\mu_{f_s \rightarrow x}(x)$$

여기서 우리는 새로운 a set of functions $\mu_{f_s \rightarrow x}(x) = \sum_{\textbf{x}-x}F_s(x,X_s)$을 만나게 된다. 이는 factor nodes $f_s$에서 $x$를 향하는 *messages*라고 볼 수 있다. 그래서 marginal은 node $x$에 도착하는 message들의 product라고 이해할 수 있다.

$F_s(x,X_s)$을 조금 더 factorize해보자.

$$F_s(x,X_s) = f_s (x,x_1,...,x_M)G_1(x_1, X_{s1})...G_M(x_M, X_{sM})$$

$$\mu_{f_s \rightarrow x}(x) =\sum_{x_1}...\sum_{x_M}f_s(x,x_1,...,x_M) \prod_{m \in ne(f_s)-x}[\sum_{X_{sm}}G_m(x_m, X_{sm})] \\\ =\sum_{x_1}...\sum_{x_M}f_s(x,x_1,...,x_M) \prod_{m \in ne(f_s)-x}\mu_{x_m \rightarrow f_s}(x_m)$$

이번에는 $\mu_{x_m \rightarrow f_s}(x_m) = \sum_{X_{sm}}G_m(x_m,X_{sm})$ 이번에는 variable에서 factor로 가는 message를 의미한다.

이처럼 message를 보내는 flow를 이용하여 marginal distribution을 구할 수 있다. 구체적인 예시는 PRML책 409page를 보면 된다. 내용이 꽤 길어서 일부 생략하기로 한다.

### 8.4.5 The max-sum algorithm
이번에는 high probability를 갖는 latent variable을 구하고 싶은 경우를 생각해보자.

$$\textbf{x}^{\text{max}} = \arg \max_{\textbf{x}} p(\textbf{x}) \\ p(\textbf{x}^{\text{max}}) = \max_{\textbf{x}}p(\textbf{x})$$

이를 구하기 위해 먼저 chain 예시를 한 번 살펴보자.

- 아래 식을 전개하는데 이용한 식
  - $\max_{\textbf{x}}p(\textbf{x}) = \max_{x_1}...\max_{x_M}p(\textbf{x})$
  - $\max (ab,ac) = a \max (b,c),\;\text{where}\;a>0$ 

$$\max_{\textbf{x}}p(\textbf{x}) = \frac{1}{Z}\max_{x_1}...\max_{x_N}[\psi_{1,2}(x_1, x_2)...\psi_{N-1,N}(x_{N-1}, x_N)]\\\ =\frac{1}{Z} \max_{x_1}[\psi_{1,2}(x_1,x_2)[...\max_{x_N}\psi_{N-1, N}(x_{N-1},x_N)]]$$

이는 이전의 sum-product algorithm처럼 message를 전달하는 것으로 이해할 수 있다. 이제는 tree-structured factor graph를 통해 일반적인 경우로 알아보자. 

sum-product algorithm과 거의 비슷하다. sum이 max로 바뀐 경우라고 이해할 수 있다. 거기에 추가로 product를 log를 씌워서 sum으로 implement한다.

$$\mu_{f \rightarrow x}(x) = \max_{x_1,...,x_M}[\log f(x,x_1,...,x_M)+\sum_{m\in ne(f_s)-x}\mu_{x_m\rightarrow f}(x_m)]\\\mu_{x \rightarrow f}(x)=\sum_{l \in ne(x)-f}\mu_{f_l\rightarrow x}(x)$$

### 8.4.6 Exact inference in general graphs

### 8.4.7 Loopy belief propagation

### 8.4.8 Learning the graph structures
