# [GNN] Graph Convolutional Network


GCN에 대해 간단하게 정리

<!--more-->

## Graph Convolutional Network (GCN)

그래프에서 node를 임베딩시키서 활용하고 싶은 경우가 많다. 다양한 활용방안이 있겠지만 여기서는 node classification으로 한정지어서 GCN을 이해해보자.

GCN의 주요 특징은 아래와 같다.

- node의 이웃들의 정보들을 이용
- 각 node의 인접행렬(Adjacency matrix)과 node의 feature 값들도 이용
- CNN처럼 weight sharing의 특성

### 과정

- 먼저, adjacency matrix를 $A$라 하고 feature matrix를 $H^{(0)}$이라고 하자.

$$A=\begin{bmatrix} 0 & 1 & 0 \\\ 1 & 0 & 1 \\\ 0 & 1 & 0 \end{bmatrix}$$

$$H^{(0)}=\begin{bmatrix}  & h_a^{(0)'} &  \\\ & h_b^{(0)'}  &  \\\  & h_c^{(0)'}  &  \end{bmatrix}$$

- 특정 노드는 아래의 식처럼 이전 layer의 이웃의 정보를 이용한다.
  - 아래 식에서는 빠졌지만 자기 자신의 정보도 이용

$$h_b^{(k+1)} = \sigma (W_k \sum_{u \in N(b)}\frac{h_u^{(k)}}{|N(b)|})$$

이제부터 행렬로 일반화해서 과정을 알아보자.

- 먼저 k번째 layer의 hidden state $H^{(k)}$에 learnable parameter $W_k$를 곱한다.
  - $W_k$는 $m$개의 weight parameter vector로 이루어진 matrix이다. 이들은 $m$개의 filter라고 생각하면 된다.

$$
H^{(k)}W_k=
\begin{bmatrix}  & h_a^{(k)'} &  \\\ & h_b^{(k)'}  &  \\\  & h_c^{(k)'}  &  \end{bmatrix}
\begin{bmatrix}  &  &  \\\  w_1^{(k)} &  ... &  w_m^{(k)} \\\  &  &  \end{bmatrix}
$$

- 그리고 각 노드들은 이웃들의 state에 영향을 받도록 하기 위해 $\tilde{D}^{-1}\tilde{A}$를 곱한다.
  - $\tilde{A}$: $A$ + $I$, 자기자신의 이전 hidden state의 정보도 포함하기 위해서
  - $\tilde{D}$: degree matrix + $I$

$$
\tilde{D}^{-1}\tilde{A}H^{(k)}W_k=
\begin{bmatrix} 1/2 & 0 & 0 \\\ 0 & 1/3 & 0 \\\ 0 & 0 & 1/2 \end{bmatrix}
\begin{bmatrix} 1 & 1 & 0 \\\ 1 & 1 & 1 \\\ 0 & 1 & 1 \end{bmatrix}
\begin{bmatrix}  h_a^{(k)'} w_1^{(k)} & ... & h_a^{(k)'} w_m^{(k)} \\\   h_b^{(k)'} w_1^{(k)} & ... & h_b^{(k)'} w_m^{(k)}  \\\   h_c^{(k)'} w_1^{(k)} & ... & h_c^{(k)'} w_m^{(k)}   \end{bmatrix}
$$

$$
= \begin{bmatrix} \frac{1}{2} (h_a^{(k)'} w_1^{(k)} + h_b^{(k)'} w_1^{(k)})& ... &\frac{1}{2}( h_a^{(k)'} w_m^{(k)} + h_b^{(k)'} w_m^{(k)} )\\\  \frac{1}{3}(  h_a^{(k)'} w_1^{(k)} + h_b^{(k)'} w_1^{(k)} + h_c^{(k)'} w_1^{(k)})& ... & \frac{1}{3}(h_a^{(k)'} w_m^{(k)} + h_b^{(k)'} w_m^{(k)} +h_c^{(k)'} w_m^{(k)} ) \\\ \frac{1}{2}( h_b^{(k)'} w_1^{(k)} + h_c^{(k)'} w_1^{(k)})& ... & \frac{1}{2}(h_b^{(k)'} w_m^{(k)} + h_c^{(k)'} w_m^{(k)} )  \end{bmatrix}
$$

- 그래서 위의 결과를 통해서 다음 hidden state 값을 구하는 것이다.

$$H^{(k+1)} = \sigma(\tilde{D}^{-1}\tilde{A}H^{(k)}W_k)$$

## Reference

- cs224w
- 설명이 잘 되어 있는 블로그글
  - https://baekyeongmin.github.io/paper-review/gcn-review/
  - https://hip-studyrecord.tistory.com/35
  - https://ganghee-lee.tistory.com/27

