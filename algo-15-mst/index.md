# [알고리즘] 최소신장트리(MST)


Kruskal, Prim + python

<!--more-->

## 최소신장트리 Minimum spanning tree
- 예시
  - 입력 : n개의 도시, 도시와 도시를 연결하는 비용
  - 문제 : 최소의 비용으로 모든 도시들이 서로 연결되게 한다.
  - 해가 유일하지는 않음, 즉 같은 graph에서 MST는 여러개가 나올 수 있다.
- 무방향 가중치 그래프

### 왜 트리라고 부르는걸까?
- **cycle이 없는 connected 무방향 그래프**를 트리라고 한다.
  - 우리가 일반적으로 생각하는 root에 내려오는 트리는 rooted tree라고 할 수 있다. 조금 더 좁은 의미라고 이해하면 될 것 같다.
- MST 문제의 답은 항상 트리가 된다.
  - MST에서 cycle은 필요없다.
  - 모두 연결되어 있다.
  - 노드가 n개인 트리는 항상 n-1개의 엣지를 갖는다.

### Generic MST 알고리즘
- 어떤 MST의 부분집합 A에 대해서 $A + {(u,v)}$도 역시 어떤 MST의 부분집합이 될 경우 edge $(u,v)$는 A에 대해서 *safe(안전하다)* 하다고 한다.
- Generic MST 알고리즘
  - 처음에는 A를 공집합으로 시작
  - 안전한 edge 하나 찾은 후 이것을 A에 더한다.
  - edge의 갯수가 n-1개가 될 때까지 2번을 반복한다.
  - 대표적인 종류 2가지
    - kruskal, prim 알고리즘

### 안전한 edge 찾기
- 그래프의 정점들을 두 개의 집합 $S$와 $V-S$로 분할한 것을 *cut* $(S, V-S)$라고 부른다.
- $u\in S,\;v\in V-S$, edge (u,v)가 존재한다면 이 edge는 cut $(S, V-S)$를 *cross* 한다고 한다.
- edge들의 부분집합 A에 속한 어떤 edge도 cut $(S,V-S)$를 cross하지 않을 때 cut $(S,V-S)$는 A를 *respect* 한다고 한다.
- 결론
  - A가 어떤 MST의 부분집합이고
  - $(S,V-S)$는 A를 respect하는 cut이라고 하자.
  - 이 cut을 cross하는 edge들 중 가장 가중치가 작은 edge $(u,v)$는 A에 대해서 safe하다.

## Kruskal의 알고리즘
- edge들을 가중치의 오름차순으로 정렬한다.
- edge들을 그 순서대로 하나씩 선택해간다.
  - 단, 이미 선택된 edge들과 cycle을 형성하면 선택하지 않는다.
- n-1개의 edge가 선택되면 종료한다.
- 시간복잡도 : $O(E \log_2 E)$

### 왜 Kruskal의 알고리즘이 MST를 찾는가?
- Kruskal의 알고리즘의 임의의 한 단계를 생각해보자.
- A를 현재까지 알고리즘이 선택한 edge의 집합이라고 하고 A를 포함하는 MST가 존재한다고 가정하자.
- 이를 두 집합으로 cut한 상태를 가정하자.
- 이 때 cycle을 만들지 않는 lightest edge를 만드는 것이 Kruskal 알고리즘이고
- 이는 cut을 cross하고 A에 대해서 safe하므로 다시 MST의 부분집합이 된다. (by 안전한 edge 찾기 결론)

### cycle 검사
- Kruskal 알고리즘을 진행하는 한 단계를 생각해보자.
- 연결이 되어있는 노드마다 집합으로 표현하면
  - {a}, {b,c,d}, {d,e}
  - 이런식으로 생각할 수 있다.
- 이제 가중치가 최소인 edge를 고려하는데 그 edge가 이미 한 집합인 노드간 edge인 경우 cycle을 만들게 되므로 skip해야한다.

그러면 Kruskal 알고리즘을 구현하기 위해 위의 disjoint한 집합을 어떻게 표현할지 고민이 든다.

### 집합들의 표현
- Kruskal을 구현할 때,
  - 가중치가 최소인 edge에 연결된 두 node의 각 집합을 찾아서 동일한지 비교하고 (find)
  - 동일하지 않다면 합치는 과정이 필요하다. (union)
- Union-find을 제공하는 자료구조를 이용해야하는 것이다.
- **각 집합을 하나의 트리**로 표현한다.
  - 누가 root이고 누가 누구의 부모이든 상관없다.
  - root 노드의 부모는 자기자신이 되도록 한다.
  - 이진트리도 아니다.
  - 다만 **상향식 트리** (트리의 각 노드는 자식노드가 아닌 부모 노드의 주소를 가짐)
- 모든 트리를 하나의 배열로 표현가능!
  - 간단하다. 배열에 부모가 누구인지 넣으면 된다.
  - 상향식 트리라서 가능한 것이다.

### Find
- 자신이 속한 트리의 root를 찾아서 동일한지 비교하면 된다.
- 시간복잡도 : $O$(트리의 높이)
  - 최악의 경우 $O(V)$

### Union
- 한 트리의 root를 다른 트리 root의 자식 노드로 만들면 된다.
- 시간복잡도 : root 노드를 찾은 이후에는 $O(1)$

### Weighted Union
- 두 집합을 union할 때 작은 트리의 루트를 큰 트리의 루트의 자식으로 만든다.
- 그러면 트리의 높이가 무작정 높아지지 않기에 find를 할 경우 속도가 빨라진다.
- 각 트리의 크기를 count하고 있어야 한다.

### Path Compression
- root를 찾아가는 과정이 길수도 있으니까 중간과정의 노드들을 스킵하고 바로 root쪽이랑 연결되도록 해보자

```python
# parent는 list이고 해당 index 노드의 부모를 갖고 있다
def find_parent(parent, x):
  if parent[x] != x:
    parent[x] = find_parent(parent, parent[x])
  return parent[x]
```

### Weighted Union with Path Compression (WUPC)
- M번의 union-find 연산의 총 시간복잡도는 $O(N+M\log* N)$
  - $N$은 원소의 개수
  - $\log* N$ : 로그를 연속으로 몇 번 취하면 N이 1이되는지
    - $\log* 1=0,\;\log* 2=1,\;...\log* 65536=4,...$
- 거의 선형시간 알고리즘이다. 즉, 한 번의 Find 혹은 Union이 $O(1)$의 시간이 걸린다.
- 그래서 Kruskal 알고리즘의 시간복잡도는 edge를 정렬하는 시간이 dominant하다.

```python
# Kruskal 알고리즘

def find_parent(parent, x):
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    # 여기서는 Weighted Union을 사용하지는 않음
    # 수가 작을수록 root로 만드는 기준만 사용
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

v, e = map(int, input().split())
parent = [0] * (v+1) # 부모 테이블 초기화
edges = []
result = 0
for i in range(1, v+1):
    parent[i] = i
for _ in range(e):
    a, b, cost = map(int, input().split())
    edges.append((cost, a, b))

# edge값에 따라 정렬
edges.sort()

for edge in edges:
    cost, a, b = edge
    if find_parent(parent, a) != find_parent(parent, b):
        union_parent(parent, a, b)
        result += cost

print(result)
```

## Prim의 알고리즘
- 임의의 노드를 출발노드로 선택
- 출발 노드를 포함하는 트리를 점점 키워감
- 매 단계에서 이미 트리에 포함된 노드와 포함되지 않은 노드를 연결하는 edge들 중 가장 가중치가 작은 edge 선택
- 구현에서 핵심은
  - 포함되지 않은 나머지 edge들 중에서 가중치가 가장 작은 edge를 찾는 것

### 왜 Prim의 알고리즘이 MST를 찾는가?
- Prim의 알고리즘의 임의의 한 단계를 생각해보자.
- A를 현재까지 알고리즘이 선택한 edge의 집합이라고 하고 A를 포함하는 MST가 존재한다고 가정하자.
- 이제 아직 연결되지 않은 노드와 연결하는 edge들 중에서 가장 가중치가 작은 edge를 연결
- 이는 cut을 cross하고 A에 대해서 safe하므로 다시 MST의 부분집합이 된다. (by 안전한 edge 찾기 결론)

### 가중치가 최소인 edge 찾기
- $V_A$ : 이미 트리에 포함된 노드들
- $V_A$에 아직 속하지 않은 각 노드 v에 대해서 다음과 같은 값을 유지
  - $key(v)$ : 이미 $V_A$에 속한 노드와 자신을 연결하는 edge들 중 가중치가 최소인 edge $(u,v)$의 가중치
  - $\pi(v)$ : 그 edge의 끝점 $u$
- 그러면 가중치가 최소인 edge를 찾는 대신 key값이 최소인 노드를 찾으면 된다. 그리고 연결!
- 그리고 key값, pi값을 갱신해준다. (시간복잡도 : $O(V)$)
  - 이 때, 모든 값을 갱신할 필요는 없고
  - 방금 추가된 node와 연결된 노드들의 key값, pi값만 갱신하면 되는 것이다.
- 위의 과정을 반복하면 Prim의 알고리즘! (시간복잡도 : $O(V^2)$)

### key값이 최소인 노드 찾기
- key값을 하나씩 찾으면 $O(V)$ 시간이 걸린다.
- 근데 최소 우선순위 큐를 사용하면 key값이 최소인 노드를 찾는 시간이 $O(\log V)$이다.

### Prim의 알고리즘 : 시간복잡도
- 이진힙을 사용하여 우선순위 큐를 구현한 경우
- while loop:
  - $V$번의 extract-min 연산 : $O(V \log V)$
  - $E$번의 decrease-key 연산 : $O(E \log V)$
- 따라서 시간복잡도 : $O(V \log V + E \log V)=O(E \log V)$
  - $E$이 더 큰 경우가 대부분
- 우선순위 큐를 사용하지 않고 단순하게 구현할 경우 : $O(V^2)$

```python
mygraph = {
    'a' : {'b':7, 'd':5},
    'b' : {'a':7, 'c':8, 'd':9,},
    'c' : {'b':8},
    'd' : {'a':5, 'b':9}
}

from heapdict import heapdict 

def prim(graph, start):
    mst = list()
    # keys, pi, result 초기화
    keys, pi, result = heapdict(), dict(), 0
    for node in graph.keys():
        keys[node] = float('inf')
        pi[node] = None
    keys[start], pi[start] = 0, start

    while keys:
        # extract-min 연산
        # 우선순위 큐를 통해서 쉽게 진행
        current_node, current_key = keys.popitem()
        result += current_key
        # mst를 구해야하는 경우
        mst.append([pi[current_node], current_node, current_key])
        # decrease-key 연산
        for adjacent, weight in graph[current_node].items():
            if adjacent in keys and weight < keys[adjacent]:
                keys[adjacent] = weight
                pi[adjacent] =  current_node
    
    return result
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
