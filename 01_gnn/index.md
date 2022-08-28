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
  - (논문에서의 연산과는 차이가 있다 다만 이해를 위해서 이렇게 작성)

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

### Code

먼저 node classification을 하기 위해 데이터를 준비한다.

```python

import torch
import torch.nn.functional as F

# The PyG built-in GCNConv
from torch_geometric.nn import GCNConv

import torch_geometric.transforms as T
from ogb.nodeproppred import PygNodePropPredDataset, Evaluator

dataset_name = 'ogbn-arxiv'
dataset = PygNodePropPredDataset(name=dataset_name,
                                 transform=T.ToSparseTensor())
data = dataset[0]

# Make the adjacency matrix to symmetric
data.adj_t = data.adj_t.to_symmetric()

device = 'cuda' if torch.cuda.is_available() else 'cpu'

data = data.to(device)
split_idx = dataset.get_idx_split()
train_idx = split_idx['train'].to(device)
```

`torch_geometric`을 이용하여 GCN을 만들어보자.

```python
class GCN(torch.nn.Module):
    def __init__(
      self,
      input_dim,
      hidden_dim,
      output_dim,
      num_layers,
      dropout,
      return_embeds=False
    ):

        super(GCN, self).__init__()

        self.convs = torch.nn.ModuleList(
            [GCNConv(in_channels=input_dim, out_channels=hidden_dim)] +
            [GCNConv(in_channels=hidden_dim, out_channels=hidden_dim)
                for i in range(num_layers-2)] +
            [GCNConv(in_channels=hidden_dim, out_channels=output_dim)]
        )

        self.bns = torch.nn.ModuleList([
            torch.nn.BatchNorm1d(num_features=hidden_dim)
                for i in range(num_layers-1)
        ])

        self.dropout = dropout

        # Skip classification layer and return node embeddings
        self.return_embeds = return_embeds

    def reset_parameters(self):
        for conv in self.convs:
            conv.reset_parameters()
        for bn in self.bns:
            bn.reset_parameters()

    def forward(self, x, adj_t):
      # tutorial에는 edge_index인데 여기서는 adj_t
        out = None

        for conv, bn in zip(self.convs[:-1], self.bns):
            out = F.relu(bn(conv(x, adj_t)))
            out = F.dropout(out, self.dropout, self.training)
            x = out
        out = self.convs[-1](x, adj_t)
        if self.return_embeds:
            return out
        else:
            out = F.log_softmax(out, dim=1)

        return out
```

이제 훈련 코드를 작성해보자.

```python
def train(model, data, train_idx, optimizer, loss_fn):
    model.train()
    loss = 0
    optimizer.zero_grad()
    out = model(data.x, data.adj_t)
    loss = loss_fn(out[train_idx], data.y[train_idx].reshape(-1))
    loss.backward()
    optimizer.step()
    return loss.item()

@torch.no_grad()
def test(model, data, split_idx, evaluator):
    model.eval()
    out = model(data.x, data.adj_t)

    y_pred = out.argmax(dim=-1, keepdim=True)

    train_acc = evaluator.eval({
        'y_true': data.y[split_idx['train']],
        'y_pred': y_pred[split_idx['train']],
    })['acc']
    valid_acc = evaluator.eval({
        'y_true': data.y[split_idx['valid']],
        'y_pred': y_pred[split_idx['valid']],
    })['acc']
    test_acc = evaluator.eval({
        'y_true': data.y[split_idx['test']],
        'y_pred': y_pred[split_idx['test']],
    })['acc']

    return train_acc, valid_acc, test_acc
```

이를 이용하여 훈련을 시키면

```python
# Please do not change the args
args = {
    'device': device,
    'num_layers': 3,
    'hidden_dim': 256,
    'dropout': 0.5,
    'lr': 0.01,
    'epochs': 100,
}
model = GCN(data.num_features, args['hidden_dim'],
            dataset.num_classes, args['num_layers'],
            args['dropout']).to(device)
evaluator = Evaluator(name='ogbn-arxiv')

import copy

# reset the parameters to initial random value
model.reset_parameters()

optimizer = torch.optim.Adam(model.parameters(), lr=args['lr'])
loss_fn = F.nll_loss

best_model = None
best_valid_acc = 0

for epoch in range(1, 1 + args["epochs"]):
  loss = train(model, data, train_idx, optimizer, loss_fn)
  result = test(model, data, split_idx, evaluator)
  train_acc, valid_acc, test_acc = result
  if valid_acc > best_valid_acc:
      best_valid_acc = valid_acc
      best_model = copy.deepcopy(model)
  print(f'Epoch: {epoch:02d}, '
        f'Loss: {loss:.4f}, '
        f'Train: {100 * train_acc:.2f}%, '
        f'Valid: {100 * valid_acc:.2f}% '
        f'Test: {100 * test_acc:.2f}%')
```

## Reference

- cs224w
- 설명이 잘 되어 있는 블로그글
  - https://baekyeongmin.github.io/paper-review/gcn-review/
  - https://hip-studyrecord.tistory.com/35
  - https://ganghee-lee.tistory.com/27

