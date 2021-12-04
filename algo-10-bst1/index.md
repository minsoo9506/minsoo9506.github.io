# [알고리즘] 트리와 이진트리


트리와 이진트리의 개념 + python

<!--more-->

## 트리 Tree
- 계층적인 구조를 표현
- 노드(node)들과 노드들을 연결하는 링크(link or edge)들로 구성
- 맨 위의 노드를 루트 (root)
- 부모노드, 자식노드
- 형제관계
  - 부모가 동일한 노드들을 형제 관계라고 부름
- leaf 노드
  - 자식이 없는 노드들
- 조상-자손 관계
  - 부모-자식 관계를 확장한 것
- 부트리 subtree
  - 트리의 일부분인 트리
- 레벨
  - 트리의 깊이를 의미 
  - level1이 root 위치를 의미
- 높이

## 트리의 기본적인 성질
- 노드가 N개인 트리는 항상 N-1개의 링크를 가진다.
- 트리는 루트에서 어떤 노드로 가는 경로는 유일하다. (단 노드를 반복방문하지 않는다는 가정하에서)

## 이진트리 Binary Tree
- 각 노드는 최대 2개의 자식을 가진다.
- 자식이 한 명이여도 왼쪽, 오른쪽 자식인지 구분

### 이진트리의 표현
- 연결구조 표현
  - 각 노드에 하나의 데이터 필드와 왼쪽자식, 오른쪽 자식, 부모노드의 주소를 저장
  - 부모노드의 주소는 반드시 필요한 경우가 아니면 보통 생략

### 이진트리의 순회 (traversal)
- 순회 : 이진트리의 모든 노드를 방문하는 일
- 중순위(inorder) 순회
  - 1. left subtree를 inorder 순회
  - 2. root를 순회
  - 3. right subtree를 inorder 순회
  - recursive 하다
- 선순위(preorder) 순회
- 후순위(postorder) 순회
- 레벨오더(level-order) 순회
  - 레벨 순으로 방문
  - 동일 레벨에서는 왼쪽에서 오른쪽 순서로
  - 큐를 이용하여 구현

```python
# 전위(Pre-Order) 순회 : NLR
def preorder(node):
  if node is None:
    return
  print(node.val)
  preorder(node.left)
  preorder(node.right)

# 중의(In-Order) 순회 : LNR
def inorder(node):
  if node is None:
    return
  inorder(node.left)
  print(node.val)
  inorder(node.right)
  
# 후위(Post-Order) 순회 : LRN
def postorder(node):
  if node in None:
    return
  postorder(node.left)
  postorder(node.right)
  print(node.val)
```

```python
# level-order 순회
# pseudo code
visit the root
enque root into Q
while Q is not empty do
    v = dequeue(Q)
    visit children of v
    enqueue children of v into Q
end
```
## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
