# [자료구조] 그래프


비선형적인 자료구조 그래프에 대해 정리

<!--more-->

## 그래프
- 탐색
  - BFS
    - 주로 큐로 구현
  - DFS
    - 주로 스택, 재귀로 구현
- 그래프를 표현하는 방법
  - 인접리스트
  - 인접행렬

```python
# 인접리스트
graph = {
    1 : [2,3,4],
    2 : [5],
    3 : [5],
    4 : [],
    5 : [6,7],
    6 : [],
    7 : [3],
}
# DFS (재귀)
def recursive_dfs(v, discovered=[]):
    discovered.append(v)
    for w in graph[v]:
        if not w in discovered:
            discovered = recursive_dfs(w, discovered)
    return discovered
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

## Python 예시
- [Link](https://github.com/minsoo9506/DS-AL-study)
- 섬의 갯수
  - dfs, 재귀
- 전화번호 문자 조합
  - dfs, 재귀
- 순열
  - 객체 복사는 `copy()`를 사용, 복잡한 리스트의 경우는 `copy.deepcopy()`로 처리
  - `itertools.permutations()`를 통해서 쉽게 permutation을 계산할 수 있다.
- 조합
  - `itertools.combinations()`
- 조합의 합
  - dfs, 재귀
- 부분 집합
  - dfs
- 일정 재구성
  - dfs

## Reference
- 파이썬 알고리즘 인터뷰 (박상길 지음)
