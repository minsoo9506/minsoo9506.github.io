# [GNN] GAT


GAT에 대해 간단하게 정리

<!--more-->

## GAT

GAT는 GNN에서 attention을 활용한 알고리즘이다. 이전까지는 node의 이웃들의 정보를 동등하게 aggregation해서 업데이트를 진행하였다. 하지만 attention score를 통해서 이웃들 마다 다른 가중치를 주는 것이 이 알고리즘의 아이디어이다.

아래와 같이 attention값을 계산한다.

- weight vector $a \in R^{2F}$ ($F$는 hidden size)
- $\|\|$는 concat을 의미

$$\alpha_{ij} = \frac{\exp (\sigma(a^T (Wh_i \|\|Wh_j)))}{\sum_{k \in N_i} \exp (\sigma(a^T (Wh_i \|\|Wh_k)))}$$

그러면 node $i$의 output은

$$h_i^{'} = \sigma(\sum_{j \in N_i} \alpha_{ij} W h_j)$$

위의 output을 여러번하여 concat하면 multi-head attention도 할 수 있는 것이다.

### 장점

- transformer처럼 병렬화연산에서의 장점이 있다.
- 이웃들마다 다른 가중치를 준다.
- attention으로 해석을 할 수 있다.

## Code

## Reference

- cs224w
- Inductive Representation Learning on Large Graphs

