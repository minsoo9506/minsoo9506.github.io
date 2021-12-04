# [알고리즘] 그래프


그래프 개념

<!--more-->

## 그래프 Graph
- (undirected) graph $G(V,E)$
  - $V$ : 노드(node), vertex
  - $E$ : edge, link
  - 개체들 간의 이진관계를 표현
- directed graph
  - edge가 방향이 있는 것
- weighted graph
  - edge마다 가중치가 있는 것
  - directed, undirected 둘 다 존재

### 그래프의 표현 (undirected)
- 인접행렬 (adjacency matrix)
  - 그래프의 연결을 n*n 행렬로 표현
  - node가 연결되어 있으면 1, 아니면 0
  - 저장 공간 : $O(n^2)$
  - 어떤 node에 인접한 모든 node 찾기 : $O(n)$
  - 어떤 edge가 존재하는지 검사 : $O(1)$
- 인접리스트 (adjacency list)
  - node 집합을 표현하는 하나의 배열과 각 node마다 인접한 node들의 연결리스트
    - 이 때, 인접한 node들을 표현한 연결리스트에서 총 노드 수는 2 * 전체edge수(m)
  - 저장 공간 : $O(n+m)$
  - 어떤 node에 인접한 모든 node 찾기 : $O(degree(v))$
  - 어떤 edge가 존재하는지 검사 : $O(degree(v))$

### 그래프의 표현 (directed)
- 인접행렬은 비대칭
- 인접리스트는 연결리스트에서 총 노드 수는 전체edge수(m)

### 그래프의 표현 (weighted)
- 인접행렬에서 1대신 edge의 가중치를 저장하면 된다

### 경로와 연결성
- undirected에서 두 node 사이의 path(경로)가 존재할 때 연결되어 있다고 한다
- 모든 node 쌍들이 연결된 그래프를 연결된(connected) 그래프라고 한다

## Reference
- 권오흠 교수님 알고리즘
