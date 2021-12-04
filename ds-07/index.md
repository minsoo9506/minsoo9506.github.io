# [자료구조] 트리


비선형적인 자료구조 트리에 대해 정리

<!--more-->

## 트리
- 그래프 vs 트리
  - 트리는 순환 구조를 갖지 않는 그래프
  - 트리에서 부모 노드는 단 하나, 루트도 하나

## 이진 트리
- 가장 널리 사용되는 트리는 이진트리와 이진탐색트리(BST)
- 일반적으로 트리라고 하면 대부분 이진트리를 의미
- 이진트리는 노드의 차수가 2이하인 트리
- 이진트리 표현법
  - 배열, 리스트
  - 노드와 링크로 직접 class 구현

## 트리순회
- 그래프 순회의 한 형태로 트리 자료구조에서 각 노드를 정확히 한 번 방문하는 과정
- 종류
  - pre-order
  - in-order
  - post-order

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

## 이진 탐색 트리 (Binary Search Tree)
- 여기서 탐색이란 **정렬되었다는 것을 의미**한다.
- 노드의 왼쪽에는 작은 값, 오른쪽에는 같거나 큰 값이 위치한다.
- 장점은? 탐색이 $O(\log n)$

```python
# BST
# search
def search(self, key):
  if self.size == 0:
    return None
  v = self.root
  while v != None:
    if v.key == key :
      return v
    elif v.key < key:
      v = v.right
    else:
      v = v.left
```

## 균형 이진 탐색 트리 (Balanced BST)
- 트리의 높이가 너무 높아지지 않도록 조정
- rotation 회전을 통해 조정
- 종류
  - AVL tree
  - Red-Black
  - (2,3,4) tree
  - splay tree

## Python 예시
- [Link](https://github.com/minsoo9506/DS-AL-study)
- 이진트리 최대깊이
  - `deque`를 이용하여 bfs
- 이진 트리의 직경
  - dfs
- 가장 긴 동일값의 경로
  - dfs
- 이진트리반전
  - 재귀, 큐, 스택
- 두 이진 트리 병합
  - 재귀
- 이진 트리 직렬화
  - bfs
- 균형 이진 트리
- 정렬된 배열의 이진 탐색 트리 변환
- 이진 탐색 트리를 더 큰 수 합계 트리로
- 이진 탐색 트리 합의 범위

## Reference
- 파이썬 알고리즘 인터뷰 (박상길 지음)
- [신찬수, 한국외대, 컴퓨터전자시스템공학부, 자료구조 2020-1](https://www.youtube.com/playlist?list=PLsMufJgu5933ZkBCHS7bQTx0bncjwi4PK)
