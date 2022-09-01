# [GNN] GraphSAGE


GraphSAGE에 대해 간단하게 정리

<!--more-->

## GraphSAGE

### embedding generation (forward propagation)

일단 model은 이미 훈련이 되었고 parameter들은 fixed되어있다고 가정하자.

- depth: $K$
- weight matrices: $W^k,\forall k \in \{1,...,K\}$
- non-linearity: $\sigma$
- differentiable aggregator functions: $AGGREGATE_k,\forall k \in \{1,...,K\}$

이제 아래의 과정을 depth만큼 반복하면 된다.

- 모든 node $v\in V$에 대해

$$h_{N(v)}^k \leftarrow AGGREGATE_k(\{ h_u^{k-1},\forall u \in N(v) \})$$

$$h_v^k \leftarrow \sigma (W^k \cdot \text{CONCAT}(h_v^{k-1},h_{N(v)}^k))$$

그렇다면 위에서 이웃은 어떻게 정의했을까?

- 특정 node와 연결된 모든 node들을 이웃으로 사용한 것이 아니다.
- fixed-size의 이웃들만 uniformly sample하여서 이웃으로 사용하였다. $k$ iteration마다 새로 sampling한다.
- 이를 통해 computational하게 이점이 있고 정해진 size 덕분에 batch 연산이 예측가능하며 효율적이다.

### Learning the parameters of GraphSAGE

fully unsupervised의 경우 weight matrices와 aggregator function을 학습시키기 위해서는 아래의 loss function을 이용한다. 이는 가까이 있는 node들(co-occurs in fixed-length random walk)이 비슷한 representations를 가지도록 하는 것이다. 반면에 그렇지 않은 node들은 다르게 만든다 (negative sampling 이용).

$$L(z_u) = -\log (\sigma(z_u^T z_v)) - Q\cdot E_{v_n} \log (\sigma(-z_u^T z_{v_n}))$$

여기서 $z$는 앞선 과정을 통해 node마다 최종 representation vector를 의미한다.

### Aggregator Architectures

논문에서는 3가지의 Aggregator function을 제시한다.

- Mean aggregator
  - elementwise mean of the vectors
  - GCN에서의 접근과 유사하다. 하지만 자기자신은 mean하지 않고 나중에 concat을 한다는 차이가 있다.
- LSTM aggregator
  - LSTM은 sequential하게 진행되기 때문에 node의 이웃들의 random permutation으로 연산을 진행한다.
- Pooling aggregator
  - elementwise max pooling을 의미한다.

## Code

## Reference

- cs224w
- Inductive Representation Learning on Large Graphs

