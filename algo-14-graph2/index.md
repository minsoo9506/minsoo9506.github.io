# [알고리즘] 그래프 순회


그래프 순회 + python

<!--more-->

## 그래프 순회
- 순회 : 그래프의 모든 노드들을 방문하는 일
- 두 가지 방법
  - BFS 너비우선순회 
  - DFS 깊이우선순회

## BFS (너비우선순회)
- 동심원 형태로 방문
- 특정 출발 노드에서 시작하여서 바로 연결된 이웃들을 방문
- 다 방문한 뒤에 해당 노드들의 바로 연결된 이웃들을 방문

### 큐를 이용한 BFS
1. check the start node
2. insert the start node into the queue
3. queue가 다 빌 때까지, node를 하나씩 뺀다
4. queue에서 나온 node의 이웃 중에서 unchecked 이웃을 check하고 queue에 넣는다
- 결국 모든 node가 check된다

```python
# BFS
def iterative_bfs(start_v):
    discovered = [start_v]
    queue = [start_v]
    while queue:
        v = queue.pop(0)
        for w in graph[v]:
            if w is not in discovered:
                discovered.append(w)
                queue.append(w)
    return discovered
```

### BFS와 최단경로
- BFS로 각 노드에 대해서 최단 경로의 길이를 구할 수 있다

```python
graph = [
    [],
    [2,3,8],
    [1,7],
    [1,4,5],
    [3,5],
    [3,4],
    [7],
    [2,6,8],
    [1,7]
]

from collections import deque

def bfs_min_distance(num_node, graph, start, end):
    q = deque([start])
    # 방문여부 체크
    visited = [False] * (num_node+1)
    visited[start] = True
    # 최단거리
    dist = [False] * (num_node+1)
    dist[start] = 0
    while q:
        v = q.popleft()
        for i in graph[v]:
            if not visited[i]:
                q.append(i)
                visited[i] = True
                dist[i] += dist[v]+1
    return dist[end]

print(bfs_min_distance(8, graph, 1, 5))
```

## DFS (깊이우선순회)

1. 현재 노드를 visited로 mark하고 인접한 노드들 중 unvisited 노드가 존재하면 그 노드로 간다.
2. 1번을 계속 반복한다.
3. 만약 unvisited인 이웃 노드가 존재하지 않는 동안 계속해서 직전 노드로 되돌아간다.
4. 다시 2번을 반복한다.
5. 시작노드로 돌아오고 더 이상 갈 곳이 없으면 종료한다.
- 그래프가 disconnected이거나 혹은 방향그래프라면 DFS에 의해서 모든 노드가 방문되지 않을 수도 있음

```python
# DFS (재귀)
def recursive_dfs(v, visited=[]):
    visited.append(v)
    for w in graph[v]:
        if not w in visited:
            visited = recursive_dfs(w, visited)
    return visited
```

## DAG (Directed Acyclic Graph)
- 방향 사이클(directed cycle)이 없는 방향 그래프
  - 예) 작업들의 우선순위
  
## 위상정렬
- DAG에서 노드들의 순서화
- 단, 모든 edge$(v_i ,v_j)$에 대해서 i < j
- 방법1
  - 먼저, indegree가 0인 노드를 찾는다.
  - 배열 A에 해당 노드를 넣는다. (A[i] = u)
  - 해당 노드와 outgoing edge를 제거한다.
  - 위의 과정을 반복하면 위상정렬된다.

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
