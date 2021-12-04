# [알고리즘] 이진검색트리(BST)


이진검색트리 + python

<!--more-->

## Dynamic set
- 여러 개의 키를 저장
- 다음과 같은 연산들을 지원하는 자료구조
  - `INSERT` : 새로운 키 삽입
  - `SEARCH` : 키 탐색
  - `DELETE` : 키 삭제
- 위를 구현하는데 다양한 방법들 존재
  - 정렬된 혹은 정렬되지 않은 배열, 연결 리스트를 사용하면 위의 연산중에 적어도 하나는 $O(n)$
  - 트리 계열
  - 해쉬 계열

## 검색트리
- Dynamic set을 트리의 형태로 구현
- 일반적으로 `SEARCH`, `INSERT`, `DELETE` 연산이 트리의 높이에 비례하는 시간복잡도를 가짐

## 이진검색트리 (BST)
- 이진 트리이면서
- 각 노드에 하나의 키를 저장
- 각 노드 `v`에 대해서 그 노드의 왼쪽 subtree에 있는 키들은 `key[v]`보다 작거나 같고, 오른쪽 subtree에 있는 값은 크거나 같다.
- 각종 연산의 시간복잡도 $O(h)$, 최악의 경우는 트리의 높이가 $h=O(n)$이 될 수도 있다.
- 균형잡힌 트리(ex. 레드-블랙 트리)를 만들면 높이를 $O(\log_2 n)$

```python
class Node:
  def __init__(self, data):
    self.data = data
    self.left = None
    self.right = None

class BinarySearchTree:
  def __init__(self):
    self.root = None
```

### 최소값, 최대값
- 해당하는 최소값은 왼쪽자식이 없겠구나
- root에서 왼쪽으로만 내려가서 더 이상 내려갈 곳이 없을 때
- 최대값은 반대!
- 시간복잡도 : $O(h)$

### Successor
- 노드 `x`의 successor란 `key[x]`보다 크면서 가장 작은 키를 가진 노드
- 모든 키들이 서로 다르다고 가정
- 3 가지 경우 존재
  - 노드 x의 오른쪽 subtree가 존재할 경우, 오른쪽 subtree의 최소값
  - 오른쪽 subtree가 없는 경우, 어떤 노드 y의 왼쪽 subtree의 최대값이 x가 되는 그런 노드 y가 x의 successor
  - 그런 노드 y가 존재하지 않을 경우 successor가 없음 (x가 최대값)
- 시간복잡도 : $O(h)$

### Predecessor
- 노드 `x`의 successor란 `key[x]`보다 작으면서 가장 작은 키를 가진 노드

### SEARCH
```python
class BinarySearchTree:
  ...
  def find(self, key):
    return self._find_value(self.root, key)
  def _find_value(self, root, key):
    if root is None or root.data == key:
      return root is not None
    elif key < root.data:
      return self._find_value(root.left, key)
    else:
      return self._find_value(root.right, key)
```
- 시간복잡도 : $O(h)$
  - 여기서 $h$는 트리의 높이

### INSERT
- x, y 두 포인터를 이용 또는 재귀

```python
class BinarySearchTree:
  ...
  def insert(self, data):
    self.root = self._insert_value(self.root, data)
    return self.root is not None

  def _insert_value(self, node, data):
    if node is None:
      node = Node(data)
    else:
      if data <= node.data:
        node.left = self._insert_value(node,left, data)
      else:
        node.right = self._insert_value(node.right, data)
      return node
```
- 시간복잡도 : $O(h)$

### DELETE
- 삭제하려는 노드의 자식노드가 없는 경우 : 그냥 삭제
- 삭제하려는 노드의 자식노드가 1개인 경우 : 해당 자식노드를 나의 노드 위치로 보내면 된다
- 삭제하려는 노드의 자식노드가 2개인 경우
  - 삭제하려는 노드의 successor를 찾는다
  - 그 successor를 삭제하려는 노드로 옮긴다
  - 그리고 successor는 최대 1개의 자식노드를 가지니까 이를 successor로 옮긴다

```python
class BinarySearchTree:
  ...
  def delete(self, key):
      self.root, deleted = self._delete_value(self.root, key)
      return deleted

  def _delete_value(self, node, key):
      if node is None:
          return node, False
      deleted = False
      # 해당 노드가 삭제할 노드일 경우
      if key == node.data:
          deleted = True
          # 삭제할 노드가 자식이 두개일 경우
          if node.left and node.right:
              # 오른쪽 서브 트리에서 가장 왼쪽에 있는 노드를 찾고 교체
              parent, child = node, node.right
              while child.left is not None:
                  parent, child = child, child.left
              child.left = node.left
              if parent != node:
                  parent.left = child.right
                  child.right = node.right
              node = child
          # 자식 노드가 하나일 경우 해당 노드와 교체
          elif node.left or node.right:
              node = node.left or node.right
          # 자식 노드가 없을 경우 그냥 삭제
          else:
              node = None
      elif key < node.data:
          node.left, deleted = self._delete_value(node.left, key)
      else:
          node.right, deleted = self._delete_value(node.right, key)
      return node, deleted
```
- 시간복잡도 : $O(h)$

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
