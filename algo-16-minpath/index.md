# [알고리즘] 최단경로문제


Dijkstra, Floyd + python

<!--more-->

## 최단경로
- 가중치 (방향) 그래프
- 경로의 길이는 경로상의 모든 가중치의 합을 의미
- 가중치가 없으면 BFS로 최단경로 쉽게 구할 수 있다.
- $\delta(u,v)$ : 노드 u에서 v까지의 최단경로의 길이

### 최단경로문제의 유형
- single-source : 하나의 출발 노드부터 다른 모든 노드까지의 최단 경로
  - **Dijkstra의 알고리즘**
- single-destination : 모든 노드로부터 하나의 목적지 노드까지의 최단 경로
  - single-source 문제와 동일
- single-pair : 주어진 하나의 출발 노드 s로부터 하나의 목적지 노드 t까지의 최단 경로를 찾아라
  - 최악의 경우 시간복잡도에서 single-source 문제보다 나은 알고리즘이 없음
- All-pairs : 모든 노드 쌍에 대해서 최단 경로
  - **Floyd 알고리즘**

### 최단경로와 음수 가중치
- 음수 가중치는 없다고 가정하는 알고리즘이 대부분이다. (Dijkstra 알고리즘)
- 음수 사이클이 있고 이들이 포함되면 최단 경로가 정의되지 않는다. 음수 사이클이 없다고 가정하는 경우가 대부분이겠구나.

### 최단경로의 기본특성
- 최단 경로의 어떤 부분경로도 역시 최단경로이다.
- 최단 경로는 사이클을 포함하지 않는다.

### single-source 최단경로문제
- 입력 : 음수 사이클이 없는 가중치 방향그래프와 출발노드 s
- 목적 : 각 노드에 대해서 다음을 계산한다.
  - $d[v]$ : distance estimate
    - 처음에는 $d[s]=0,\;d[v]=\infty$ 로 시작한다.
    - 알고리즘이 진행됨에 따라서 감소해간다. 하지만 항상 $d[v] \ge \delta(s,v)$ 유지한다.
    - 최종적으로는 $d[v]=\delta(s,v)$가 된다.
  - $\pi [v]$ : s에서 v까지의 최단경로상에서 v의 직전 노드
    - 그런 노드가 없는 경우 $\pi [v] = null$
    - 이를 통해 최단경로가 어떤 노드를 거쳐왔는지 알 수 있겠다.
- 대부분의 single-source 최단경로 알고리즘의 기본구조
  - 1. 초기화
  - 2. edge들에 대한 반복적인 relaxation
- 위의 과정을 거치면 최단경로인가? 그럼 몆번 반복?
  - s부터 v까지의 최단 경로라면 edge의 수는 최대 n-1개이고
  - 각각 node마다 relaxation하면 $d[v_i]=\delta(s,v_i)$가 되기 때문이다.
  - 즉, n-1번의 반복으로 충분하다.

### 기본연산 : Relaxation
- 대부분의 최단경로 알고리즘이 갖고 있는 기본연산
- u에서 v로 간다고 가정
```python
# pseudo code
RELAX(u,v,w)
    if d[v] > d[u] + w(u,v)
        then d[v] = d[v] + w(u+v) # 더 짧은 경로가 있다면 갱신
             pi[v] = u
```

## Dijkstra의 알고리즘
- 음수 가중치가 없다고 가정
- 진행과정
  - 시작노드 s로부터 최단경로의 길이를 이미 알아낸 노드들의 집합 S를 유지한다.
    - S의 시작은 공집합
  - S에 속하지 않은 노드 u에 대해서 $d(u)$는 이미 **S에 속한 노드들만** 거쳐서 s로부터 u까지 가는 최단경로의 길이라고 하자.
    - 이 때, $d(u)$가 s에서 u까지의 최단경로의 길이 $\delta (s,u)$ 이다!
  - 이제 u를 집합 S에 추가한다.
  - S가 변경되었으므로 다른 노드들의 $d(v)$값을 갱신한다.
    - $d(v) = min {d(v), d(u)+w(u,v)}$만 하면 된다.
- 시간복잡도
  - prim 알고리즘과 동일
  - 우선순위 큐를 사용하지 않고 단순하게 구현할 경우 $O(n^2)$
  - 이진힙을 우선순위 큐로 사용할 경우 $O(n\log_2 n + m \log_2 n)$

```python
import heapq
INF = int(1e9)
# 노드의 개수, 간선의 개수
n, m = map(int, input().split())
# 시작 노드 번호 입력
start = int(input())
# 각 노드에 연결되어 있는 노드에 대한 정보
# 인덱스는 1부터 시작하도록
graph = [[] for i in range(n+1)]
# 최단 거리 table
distance = [INF] * (n+1)

# 그래프에 대한 정보
for _ in range(m):
    a, b, c = map(int, input().split())
    graph[a].append((b,c)) # 여기서 b : 연결된 노드, c : weight

def dijkstra(start):
    q = []  
    heapq.heappush(q, (0,start))
    distance[start] = 0
    while q:
        # 최단거리가 가장 짧은 경우 꺼내기
        dist, now = heapq.heappop(q)
        # 현재 노드가 이미 처리되었으면 넘어가기
        if distance[now] < dist:
            continue
        # 현재 노드와 연결된 인접노드들에 대한 relaxing
        for i in graph[now]:
            cost = dist + i[1]
                # 현재 노드를 지나서 가는게 더 짧은 경우
                if cost < distance[i[0]]:
                    distance[i[0]] = cost
                    heapq.heappush(q, (cost, i[0]))

# 실행        
dijkstra(start)

for i in range(1,n+1):
    print(distance[i])
```

## Floyd-Warshall 알고리즘
- 가중치 방향 그래프
- **모든 노드 쌍들간의 최단경로의 길이를 구함**
- $d^k [i,j]$ : 
  - 중간에 노드집합 {1,2,...,k} 에 속한 노드들만 거쳐서 노드 i에서 j까지 가는 최단경로의 길이
- 노드 i에서 j까지 가는 최단경로는 두가지 경우
  - 노드 k를 지나지 않는 경우, k를 지나는 경우
  - $d^k [i,j] = min (d^{k-1}[i,j], d^{k-1}[i,k]+d^{k-1}[k,j])$
  - 이를 반복하면 최단경로를 다 구할 수 있겠다!

```python
INF = int(1e9)
# node 개수, edge 개수
n, m = map(int, input().split())
# 2차원 리스트를 만들고 무한대로 초기회
graph = [[INF]*(n+1) for _ in range(n+1)]
# 자기자신은 0으로 초기화
for a in range(1, n+1):
    for b in range(1, n+1):
        graph[a][b] = 0
# input
for _ in range(m):
    a, b, c = map(int, input().split())
    graph[a][b] = c

# 알고리즘 실행
for k in range(1, n+1):
    for a in range(1, n+1):
        for b in range(1, n+1):
            graph[a][b] = min(graph[a][b], graph[a][k]+graph[k][b])

# 결과 출력
for a in range(1, n+1):
    for b in range(1, n+1):
        if graph[a][b] == INF:
            print('no', end=' ')
        else:
            print(graph[a][b])
    print()
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
- 이것이 코딩 테스트다 (나동빈지음)
