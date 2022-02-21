# [인과추론] Causal Discovery from Interventions


Intervention을 통해 Causal Discovery를 진행해보자.

<!--more-->

## Structural Interventions
### Single-Node Intervensions
#### Intervention in Two-Variable Setting
변수가 2개일 때, intervention을 하는 모든 경우를 살펴보자. 우리가 보고 싶은 것은 특정 intervention을 가해졌을 때, True graph를 essential graph로 나타낸 것이다. 여기서 essential graph은 이전에 배웠던 skeleton과 immoralities로 이루어진 graph라고 이해하면 된다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_11_01.png?raw=true"  width="400">
</center>

위의 그림을 통해서 이해할 수 있겠지만 하나의 intervention으로 graph를 찾아내기란 어렵다. intervention으로 essential graph를 구했어도 이에 해당하는 true graph가 2개가 존재하기 때문이다. 따라서 *Two interventions are sufficient and necessary to identify the graph.* 라고 한다. 즉, 위에서 $A$ 또는 $B$를 intervention한 결과가 아니라 두 가지 결과를 모두 알고 있다면 true graph를 파악할 수 있게 된다. 이제 이를 일반화해보자. 

- Complete Graphs are the worst case
    - complete graph에서는 immoralities가 있을 수가 없기 때문에 (collider를 uncoditioning하면 두 node가 independent해야하는데 complete에서는 불가) skeleton graph만 얻을 수 있고 이는 당연히 true graph를 identify하는데 더 어렵게 만든다.

- Single variable interventions
    - $n-1$ are sufficient for $n>2$ (Eberhardt et al., 2006)
        - empty set은 제외
        - 왜 그럴까? 2개의 node가 있을 때, 둘 중 하나만 intervene하고 그 둘이 독립인지 아닌지 확인하면 edge의 방향을 알 수 있다. 이런식으로 n-1번하면 된다.
    - $n-1$ are necessary in the worst case (complete graph) (Eberhardt et al., 2006)

### Multi-Node Intervention
한 번에 하나의 node에만 intervention이 아니라 여러개의 node에 할 수 있다면?
- Multiple-Node Interventions
    - $\lfloor \log_2 n \rfloor + 1$ are sufficient (Eberhardt et al., 2005)
    - $\lfloor \log_2 n \rfloor + 1$ are necessary in the worst case (Eberhardt et al., 2005)

그렇다면 worst case (complete graph)가 아닌 경우에는 interventions가 얼마나 필요할까? (given that we can intervene on unlimited nodes per intervention) 이 때, graph에서 clique로 판단한다고 한다. 아래의 정의를 보자.

- Theorem: $\lceil \log_2 c \rceil$ multi-node interventions are sufficient and necessary in the worst case, where $c$ is the size of the largest clique. (conjectured by Eberhardt (2008) and proven by Hauser & Buhlmann (2014))

## Parametric Interventions
parametric 방법이 structural을 포함한다고 할 수 있다. 더 general한 개념이라고 이해하면 된다. 따라서 우리가 주로 parametric(soft, imperfect라고도 부름)이라고 부르는 방법은 structural이외의 parametric한 방법이다.

parametric intervention은 structural와 다르게 기존 node들에 intervention을 가하는게 아니라 parameter를 이용해서 intervention을 가한다. 예를 들어, 원래 $Y := f(A,B,C)$와 같은 형태였다면 $Y := f(A,B,C, \theta)$와 같은 방법으로 intervention을 가하는 것이다. 

### Number of Parametric Single-Node Interventions
- Number of interventions for identification (Eberhardt & Scheines, 2007)
    - $n-1$ interventions are sufficient
    - $n-1$ interventions are necessary in the worst case

## Interventional Markov Equivalence
### Interventions Introduce Immoralities: Single-Node
먼저, 아래와 같은 경우가 존재한다.
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_11_02.png?raw=true"  width="300">
</center>

이제 여기에서 intervention을 가하면 아래처럼 된다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_11_03.png?raw=true"  width="400">
</center>

이런 식으로 우리는 true graph를 찾아갈 수 있는 것이다.
- Theorem: Two graphs augmented with single-node interventions are interventionally Markov equivalent if any only if they have the same skeletons and immoralities (Tian, & Pearl, 2001)

- Number of interventions for identification (Eberhardt & Scheines, 2007)
    - $n-1$ interventions are sufficient
    - $n-1$ interventions are necessary in the worst case

### Interventional Graph: Multi-Node Interventions
multi-node intervention이라는 것은 하나의 새로운 node를 통해 동시에 여러개의 기존 node에 intervention을 가하는 것을 의미한다.

- Theorem: Given the observational data, two graphs augmented with multi-node intervetions are interventionally Markov equivalent if and only if the have the same skeletons and immoralities (Yang et al., 2018)

## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=2nDgrNP7XSE&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=11)
