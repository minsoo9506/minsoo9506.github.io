# [인과추론] The Flow of Causation and Association in Graphs


Graph를 통해 causality를 알아보자.

<!--more-->

## Terminology
여기서 graph라고 하는 것은 node와 edge로 이루어진 것을 의미한다.
- undirected graph : edge에 방향성이 없는 graph
- directed graph : edge에 방향성이 있는 graph
  - parent, child : edge가 출발하는 node를 parent, 그 edge가 도착한 node를 child
  - adjacent : 하나의 edge로 연결이 되어있는 두 node들을 adjacent하다고 한다. (un, directed 둘 다)
  - path : (방향성과 상관없이) 두 node들 사이에 edge들이 있으면 path가 있다고 한다.
    - directed path : path중에서 directed edge들의 방향이 일정한 경우
  - Ancestor, Descendant : 특정 노드(Ancestor)에서 시작하여 directed path로 특정 노드(descendant)로 끝날 때, 각 노드를 이렇게 부른다.
- Directed Acyclic Graph (DAG) : cycle이 없는 directed graph
  - cycle : node가 자기 자신으로 돌아오는 directed path가 있는 경우
- Immorality : 아래 사진에서 $A\rightarrow C \leftarrow B$를 의미 (parent가 child를 공유, parent끼리는 연결되지 않음)

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_03_01.PNG?raw=true"  width="200">
</center>

## Baysian networks and causal graphs
Baysian network는 통계에서 probabilistic graphical model의 한 종류이다. 그런데 이 모델의 특징이 causality를 다루기에 좋기 때문에 많이 이용한다. 100% 그대로 쓰는 것은 아니고 조금씩 수정해서 사용하는 것으로 알고 있다.

먼저, causal을 생각하지 않고 진행해보자. joint distribution을 modeling하는 경우 많은 양의 parameter들을 고려해야하는 경우가 생긴다. 그런데 이 때 dependency를 파악할 수 있는 정보가 있다면? 더 적은 수의 parameters 고려해도 된다. 이와 관련한 가정이 Local markov assumption이다.

- Local markov assumption
  - DAG에서 parent가 주어진다면(given) 나머지 non-descendant들과는 independent 하다.

예시를 통해 이해해보자. 먼저 dependency가 없는 경우,

$$P(x_1, x_2, x_3, x_4) = P(x_1)P(x_2 | x_1)P(x_3 | x_2, x_1)P(x_4  |x_3, x_2, x_1)$$

아래의 그림과 같은 DAG와 Local markov assumption을 고려하면

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_03_02.PNG?raw=true"  width="200">
</center>

$$P(x_1, x_2, x_3, x_4) = P(x_1)P(x_2 | x_1)P(x_3 | x_2, x_1)P(x_4  |x_3)$$

이처럼 더 간단해진다.

- Bayesian network factorization theorem
  - 확률분포 $P$와 DAG $G$가 주어지고, $P$는 $G$에 따라 아래의 식처럼 factorize될 수 있다. 
$$P(x_1,...,x_n) = \prod_i P(x_i | pa_i)$$

Bayesian network factorization과 Local Markov assumption은 사실 같은 내용이다 (증명도 있다). 지금까지는 independency에 관한 내용이었는데 dependency에 관한 내용도 당연히 있을 것이다.

- Minimality assumption
  - Local Markov assumption + Adjacent nodes in the DAG are dependent

이때, 왜 *Adjacent nodes in the DAG are dependent*라는 가정이 추가로 필요할까? 예시를 통해 이해해보자. 예를 들어, $X$에서 $Y$로 arrow가 향하고 있는 graph가 있다. 이런 상황에서 local markov assumption만 가정한다면 $p(x,y)=p(x)p(y|x)$ 뿐만 아니라 $P(x,y)=p(x)p(y)$도 성립하게 된다. 후자의 상황을 막기 위해 추가적인 가정이 필요한 것이다.

Minimality assumption을 통해 dependency도 다룰 수 있으므로 우리는 이제 DAG에서 association flow를 다룰 수 있게 되었다. 그렇다면 다음은 causation을 다룰 차례!

- Causal Edges assumption
  - In a directed graph, every parent is a **direct** cause of all its children

## Graphical building blocks
이제는 the flow of association and causation in DAG 를 알아보자. 일단 3가지 building block들을 살펴보자.
- chain, fork, immorality

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_03_03.PNG?raw=true"  width="600">
</center>

위의 사진들의 모습을 잘 기억하자. 이제 각각의 특징을 살펴보자.

### Chains, Forks : dependence
- $X_1,X_3$는 dependent하다.
- $X_1$에서 $X_3$로 association flow가 흐른다. symmetric하게 $X_3$에서 $X_1$으로도 흐른다.
- causation flow는 symmetric하지 않다. 한쪽의 방향으로만 흐른다.

### Chains, Forks : independence

$$P(X_1,X_3 \| X_2)=P(X_1 \| X_2)P(X_3 \| X_2)$$

- (proof)
  - Bayeisan Network Factorization과 Bayes rule을 이용

$$\text{Chain: }P(X_1,X_3 | X_2) = \frac{P(X_1,X_2,X_3)}{P(X_2)} \\\ =\frac{P(X_1)P(X_2|X_1)P(X_3|X_2)}{P(X_2)}=P(X_1 | X_2)P(X_3 | X_2)$$

$$\text{Forks: } P(X_1,X_3 | X_2) =  \frac{P(X_1,X_2,X_3)}{P(X_2)} \\\ =\frac{P(X_2)P(X_1|X_2)P(X_3|X_2)}{P(X_2)}=P(X_1 | X_2)P(X_3 | X_2)$$

위에서 independent한 상황의 경우 blocked path라고 하고 반대는 unblock path라고 부른다.

### Immorality
- $X_2$를 collider라고 한다.
- 위에 두 가지의 경우와 반대
  - conditioning on the collider $\rightarrow$ unblocked path
  - conditioning을 하지 않으면 아래의 식처럼 block path가 존재한다. (independence)

- (proof)

$$P(X_1, X_3) = \sum_{X_2} P(X_1, X_2, X_3) \\\ =\sum_{X_2} P(X_1)P(X_3)P(X_2|X_1,X_3) \\\ =P(X_1)P(X_3) \sum_{X_2} P(X_2|X_1,X_3) \\\ =P(X_1)P(X_3) $$

  - conditioning on descendants of colliders는 이 또한 association을 야기한다.

## The flow of association and causation
- d-separation
  - 두 개(또는 집합)의 노드들 $X,Y$사이의 모든 path가 노드(들) $Z$에 의해 blocked되면
  - 이를 Two (sets of) nodes $X$ and $Y$ are d-separated by a set of nodes $Z$

graph에서 d-separated되면 이를 확률적으로도 conditional independence하게 이용할 수 있다.

$$X \perp_G Y | Z \rightarrow X\perp_P Y |Z$$

일반적인 Bayesian network graph의 경우, association만 다룰 수 있다. 하지만 우리가 여기에 추가적인 가정을 더하면 causal association을 구할 수 있는 것이다.

- *We refer to the flow of association along directed paths as causal association*
- *d-separation implies association is causation*
  - d-separate된 경우, $T$에서 $Y$로의 path를 살리면 해당하는 association은 purely causal association이므로!


## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=BX6hdDH3lqQ&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=3)
