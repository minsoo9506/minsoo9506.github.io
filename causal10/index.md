# [인과추론] Causal Discovery from Observational Data


causality가 어느정도 인지 파악하기 이전에 observational data에서 causal discovery를 먼저 진행해야 한다. 이에 대해 알아보자.

<!--more-->

observational data에서 causal graph가 없다면? 우리가 찾아야 한다. 그 과정을 causal discovery라고 한다. 

## Independence-Based Causal Discovery
### Assumptions
#### Faithfulness Assumption
- Recall the Markov assumption: $X \perp_G Y \| Z \Rightarrow X \perp_P Y \|Z$

Markov assumption은 causal graph에서 data로 확장하는 가정이었다면 지금 우리는 그 반대가 필요하다.

- Faithfulnesss:
$$X \perp_G Y \| Z \Leftarrow X \perp_P Y \|Z$$

하지만 쉽게 위의 반례를 찾을 수 있다. (아래의 그림참조) 그래서 우리는 아래와 같은 경우는 제외하고 가정한다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_10_01.png?raw=true"  width="400">
</center>

#### Causal Sufficiency and Acyclicity Assumption
- Causal Sufficiency: no unobserved confounders of any of the variables in the graph
- Acyclicity: there are no cycles in the graph

### Markov Equivalence and Main Theorem
먼저 Markov equivalence가 무슨 의미인지 알아보자. 이전에 우리는 graph를 이루는 기본 요소들인 chain, fork, immorality를 배웠다. chain과 fork는 같은 markov equivalence class에 속한다. immorality는 혼자이다. 이를 나누는 기준은 해당 graph에서의 indepence관계를 생각하면 된다. 아래의 관계에서 graph는 node 3개, edge 2개를 갖는다.
- Markov equivalence class where $X_1 \perp X_3 \|X_2$ and $X_1 \not\perp X_3$ : chain, fork
- Markov equivalence class where $X_1 \not\perp X_3 \|X_2$ and $X_1 \perp X_3$ : immorality

위의 조건을 만족하는 모양은 (같은 어떤 indepence의 모양) 같은 markov equivalence class에 속한다고 이해할 수 있다. 위처럼 node 3개, edge 2개의 경우 chain, fork에 해당하는 graph는 총 3가지의 모양이 나올 수 있을 것이고 immorality는 한가지 밖에 없다.

 여기서 Skeletons라는 새로운 개념이 나온다. edge에서 방향을 없앤 graph라고 이해하면 된다. 그러면 위에서 전제한 chain, fork의 경우 모두 동일한 모양의 Skeleton을 갖는다.

 그렇다면 graph를 2가지 요소들로 구분할 수 있다.
 - Skeleton
 - Immorality

 이와 관련한 Theorem(Verma & Pearl, 1990; Frydenburg, 1990)이 있는데 *Two graphs are Markov equivalent if and only if they have the same skeleton and same immoralities* 이다.

### The PC Algorithm
- 0. Start with complete undirected graph
- 1. Identify the skeleton
    - $X \perp Y \| Z$인 edge들을 제거해나간다. ($Z$는 empty일 수 있다)
    - 이 과정에서 imomorality에 대한 정보도 어느정도 얻을 수 있을 것이다.
- 2. Identify immoralities and orient them
    - 아래 두 개의 조건을 만족하면 $X-Z-Y$는 immorality이다.
    - $X$와 $Y$ 사이에 edge가 없다.
    - $Z$에 대해 not conditioning할 떄, $X$와 $Y$가 independent하다.
- 3. Orient qualifying edges that are incident on colliders
    - $X \rightarrow Z - Y$에서 $X$와 $Y$를 이어주는 edge가 없다면 $X$ and $Y$ can be oriented as $Z \rightarrow Y$

위와 같은 과정을 지나면 우리는 정확한 graph는 알 수 없지만 data에 해당하는 Markov equivalent class를 identify할 수 있다.

### Removing Assumptions
- No assumed causal sufficiency: FCI algorithm (Spirtes et al, 2001)
- No assumed acyclicity: CCD algorithm (Richardson, 1996)
- Neither causal sufficiency nor acyclicity: SAT-based causal discovery (Hyttinen et al, 2013; 2014)

### Hardness of conditioinal independence Testing
위에서 살펴본 Independence-based causal discovery 알고리즘은 conditional independence testing에 의존할 수 밖에 없다. 하지만 이에 따라 당연히 한계점이 있을 수 밖에 없다. 정확한 test가 되려면 데이터가 많아야 한다고 한다. (Shah & Peters, 2020)

## Semi-Parametric Causal Discovery
Independnce-Based causal Discovery가 가지는 문제점은 어떤게 있을까?
- Requires faithfulness assumption
- Large samples can be necessary for conditional independence tests
- Only identifies the Markov equivalence class

### No Identifiability without Parametric Assumptions
특정한 상황을 가정해보자. 2개의 variable이 있고 infinite data가 있다고 하면 이 둘의 causal한 관계를 알 수 있을까?
- 먼저 markov equivalent의 관점에서 살펴보면? 알 수가 없다. $X \rightarrow Y$, $X \leftarrow Y$ 모두 동일한 skeleton의 모양이다.

그렇다면 이제 parametric form에 대한 가정을 해보자.

### Linear Non-Gaussian setting
#### Linear non-Gaussian Assumption
모든 structural equation(causal mechanisms that generate the data)들은 다음과 같은 form을 갖는다:

$$Y := f(X) + U$$

이 떄, $f$는 linear function, $X \perp U$, $U$는 distributed as some non-Gaussian 이다.
#### Identifiability in Linear Non-Gaussian Setting
왜 Non-Gaussian일까? 일단 이와 관련해서는 (Geiger & Pearl, 1988) *We cannot hope to identify the graph more precisely than the Markov equivalence class in the linear Gaussian noise setting* 이라는 연구가 있다고 한다.

Linear Non-Gaussian setting에서 graph를 identify하는 과정을 알아보자. 먼저 Theorem을 기억하자.

- Theorem (Shimizu et al, 2006):

In the linear non-Gaussian setting, if the true SCM is

$$Y := f(X) + U,\\;\\; X\perp U$$

then there does not exist an SCM in the reverse direction,

$$X := g(Y) + \tilde{U},\\;\\; Y \perp \tilde{U}$$

that can generate data consistent with $P(x,y)$

그렇다면 어떻게 위에서 말하는대로 causal mechanism을 파악할 수 있을까? linear model을 fit하고 residual을 확인하면 된다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_10_02.png?raw=true"  width="300">
</center>

이를 좀 더 extension한 경우들도 있다.
- Multivariate (Shimizu et al., 2006)
- Drop causal sufficiency assumption (Hoyer et al. 2008)
- Drop acyclicity assumption (Lacerda et al. 2008)

### Nonlinear Additive Noise setting
#### Nonlinear Additive Noise Assumption

$$X_i := f_i (pa_i) + U_i,\\;\\;\text{for all } i$$

where $f_i$ is nonlinear.

- Theorem (Hoyer et al. 2008)
    - Under the Markov assumption, causal sufficiency, acyclicity, the nonlinear additive noise assumption, and a technical condition from *Hoyer et al. 2008*, we can identify the causal graph.

#### Post-Nonlinear Setting
- Zhang & Hyvarinen, 2009

$$Y := g(f(X) + U), \;\; X \perp U$$

## Review Articles and Book
- Introduction to the Foundations of Causal Discovery (Eberhardt 2017)
- Review of Causal Discovery Methods Based on Graphical Models (Glymour, Zhang, & Spirtes, 2019)
- Elements of Causal Infernce (Peters, Janzing, & Scholkopf, 2017)

## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=2nDgrNP7XSE&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=10)
