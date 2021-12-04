# [알고리즘] Recursion의 응용


미로찾기, n queens problem + python

<!--more-->

## 미로찾기
### Recursive Thinking
- 현재 위치에서 출구까지 가는 경로가 있으려면
  - 현재 위치가 출구이거나 혹은
  - 이웃한 셀들 중 하나에서 현재 위치를 지나지 않고 출구까지 가는 경로가 있거나

```python
from collections import deque

n, m = map(int, input().split())
maze = []
for i in range(n):
    maze.append(list(map(int, input())))

def findMaze(x: int, y: int):
    on_path = 0 # visited이며 아직 출구로 가는 경로가 될 가능성 존재
    wall = 1
    blocked = 2 # visited이며 출구로 가는 경로가 안됨
    path = 3    # 출구로 가는 경로

    # maze의 크기를 벗어나는 경우
    if x < 0 or y < 0 or x >=n or y >= m:
        return False
    # 출구로 가는 경로가 될 가능성이 없는 경우 제외
    elif maze[x][y] != on_path:
        return False
    # 길을 찾은 경우
    elif x == n-1 & y == m-1:
        maze[x][y] = path
        return True
    # recursion
    else:
        maze[x][y] = path
        if findMaze(x-1, y) or findMaze(x, y+1) or\
            findMaze(x+1, y) or findMaze(x, y-1):
            return True
        maze[x][y] = blocked # 위에서 True가 아니면 경로에 포함이 안되니까
        return False

print(maze)
print(findMaze(0,0))
print(maze)
```

## Counting Cells in a Blob
- 입력으로 흑백 binary 이미지가 주어진다.
- 각 픽셀은 background pixel이거나 혹은 image pixel
- 서로 연결된 image pixel들의 집합을 blob이라고 부른다. (대각방향도 포함)

```python
n, m = map(int, input().split())
image = []
for i in range(n):
    image.append(list(map(int, input())))

def count_cell(x: int, y: int) -> int:
    background_color = 0
    image_color = 1
    counted_color = 2

    if x < 0 or y < 0 or x >= n or y >= m:
        return 0
    elif image[x][y] != image_color:
        return 0
    else:
        image[x][y] = counted_color
        return 1 + count_cell(x-1, y) + count_cell(x-1, y+1) +\
            count_cell(x, y+1) + count_cell(x+1, y+1) +\
                count_cell(x+1, y) + count_cell(x+1, y-1)+\
                    count_cell(x, y-1)+count_cell(x-1, y-1)

print(image)
print(count_cell(1,1))
print(image)
```

## n queens problem
- 체스에서 여왕은 모든 길로 갈 수 있다.
- N*N 체스판에 N개의 Queen을 놓고 싶다. 그런데 서로를 공격하지 않게 올려놓고 싶다. 총 몇 가지 경우의 수가 있을까?
- 모든 경우의 수를 고려하면 O(N^N) 조합이 존재한다. 이를 더 빠르게 해야겠구나!
- 그래서 *Backtracking* 기법
  - 조합의 숫자를 셀 때, 모든 조합이 아닌 **특정조건에 한정**해서 조합을 카운트하는 개념
  - 일반적으로 DFS(깊이우선탐색)을 통하여 구현 (recursion이나 stack사용할 수 있겠구나)

## 상태공간트리
- 찾는 해를 포함하는 트리
- 즉, 해가 트리의 어떤 한 노드(노드가 갖고있는 값)에 해당한다.
- 이 문제를 풀기 위해 상태공간 트리의 모든 노드를 탐색해야 하는 것은 아니다.
  - 깊이 내려가지 전에 이미 infeasible한 경우가 있기 때문에

```python
# 매개변수 level은 현재 노드의 위치(깊이)

# 전역변수 배열 cols를 이용하여
# cols[i] = j 
# 으로 여왕이  (i행, j열) 에 있음을 표시한다.

# level == N 이면 모든 말이 놓였다는 의미 -> 성공!

n = int(input())

class Queen:
    def __init__(self, n: int):
        self.n = n
        self.cols = [-1]*n
        self.counts = 0

    def search(self, level: int):
        # 현재 level이 n이라는 것은 
        # 0~n-1 까지 다 여왕을 놓았다는 의미
        if level == self.n:
            self.counts += 1
            print(self.cols)
            return self.counts
        else:
            for i in range(self.n):
                self.cols[level] = i
                if self._is_possible(level):
                    self.search(level+1)

    def _is_possible(self, level: int):
        # 0~level-1 까지의 여왕들과 만나는지 확인해야한다.
        # 행은 이미 다르므로
        # 같은 열 or 대각선에 있는지 확인이 필요하다.
        for i in range(level):
            # 같은 열에 있는지 확인
            if self.cols[i] == self.cols[level]:
                return False
            # 대각선에 있는지 확인
            elif abs(i-level) == abs(self.cols[i]-self.cols[level]):
                return False
        return True

q = Queen(n)
print(q.search(0))
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
