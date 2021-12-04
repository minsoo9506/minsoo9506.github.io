# [알고리즘] Heap sort


Heap sort + python

<!--more-->

## Heap sort (힙 정렬)
- 최악의 경우 시간복잡도 $O(n\log_2 n)$
- 추가 배열 불필요
- 이진 힙 (binary heap) 자료구조를 사용

## Binary tree (이진 트리)
- 트리인데 각 노드가 최대 2명의 자식을 갖는 트리
  - 자식은 왼쪽자식, 오른쪽자식으로 구분
- full binary tree : 모든 레벨에 노드들이 꽉 차있는 형태
- complete binary tree : 마지막 레벨을 제외하면 완전히 꽉 차있고, 마지막 레벨에는 가장 오른쪽부터 연속된 몇 개의 노드가 비어있을 수 있다.

## Heap의 정의
- heap은
  - complete binary tree
  - heap property를 만족
    - max heap property : 부모는 자식보다 크거나 같다
    - min heap property : 부모는 자식보다 작거나 같다
- 동일한 데이터를 가진 힙은 유일하지 않다. 여러개가 존재할 수 있다.

### Heap의 표현
- 힙은 일차원 배열로 표현가능!
- `A[1,2,...,n]` 의 배열이 있을 때 (index를 1부터 시작)
  - root node : `A[1]`
  - `A[i]`의 부모 = `A[i/2]` (python에서는 `A[i//2]` 의미)
  - `A[i]`의 왼쪽 자식 = `A[2i]`
  - `A[i]`의 오른쪽 자식 = `A[2i+1]`

### 기본 연산 : max-heapify
- 현재 상황
  - root만이 heap property를 만족 안한다. 즉, 자식중에 더 큰 수가 존재한다.
  - **단, 왼쪽, 오른쪽 subtree(root 기준 왼쪽, 오른쪽 자식이 root가 되는 tree)는 heap인 상태**
- 이런 경우 부모의 값이 더 작은 경우 자식과 swap한다. 이를 반복하면 전체 트리가 heap이 된다. 

```python
# recursive
def max_heapify(A, i):
    left = 2*i
    right = 2*i + 1
    # 자식이 없는 경우
    if (left > len(A)-1) and (right > len(A)-1):
        return
    else:
        # i의 자식 중에 큰 값 찾기
        # 2*i+1 > len(A)-1 : 왼쪽 자식만 있거나 
        # A[2*i] >= A[2*i+1] : 왼쪽 자식이 오른쪽 자식보다 크거나 같은 경우
        if right > len(A)-1 or A[left] >= A[right]:
            k = left
        else:
            k = right

        # 부모(i)보다 자식(k)이 더 작으면 swap
        if A[i] >= A[k]:
            return
        else:
            A[i], A[k] = A[k], A[i]
        max_heapify(A, k)

# index 1 아래의 subtree들은 이미 heap이라고 가정하고 진행한 것
max_heapify(data, 1)
print(data)
```

## 정렬할 배열을 힙으로 만들기
배열을 사용하는데 이를 complete binary tree의 형태로 이해하자. heap정렬을 하기위해서는 먼저 tree를 heap으로 만들어야한다.
- 이를 위해서 tree에서 작은 subtree들부터 (아래부터) heapify를 진행한다.
- 점점 위로 올라가면서 (heap이 된 tree들을 포함하는 더 큰 tree)들을 heapify하면서 전체 tree(배열)을 heap으로 만든다.
- 수학적으로 계산하면 heap으로 만드는 시간복잡도는 $O(n)$이다.

### 정렬
heap은 정렬된 상태라고 할 수 없다. 다만 확실한 것은 root가 최대값이라는 것이다. 이 특징을 이용하여 heap sort를 진행할 것이다.
- 주어진 데이터로 heap을 만든다.
- heap에서 최대값(root값)을 가장 마지막 값과 바꾼다.
  - 이 때 heap의 크기가 1 줄어든 것으로 간주
  - 즉, 마지막값은 heap의 일부가 아닌 것
- root node에 대해서 heapify(1)한다.
- 위의 과정을 반복하면서 정렬할 수 있다. (계속해서 최대값을 구하는 과정이므로)

```python
# pseudo code
HEAPSORT(A)
  BUILD-MAX-HEAP(A)               : O(n)
  for i <- heap_size downto 2 do  : n-1 times
    exchange A[1] <-> A[i]        : O(1)
    heap_size <- heap_size - 1    : O(1)
    MAX-HEAPIFY(A,1)              : O(log n)
```

```python
# max-heap sort
data = ['no use',1,5,6,4,2,3]

def max_heapify(A, i):
    left = 2*i
    right = 2*i + 1
    if (left > len(A)-1) and (right > len(A)-1):
        return
    else:
        if right > len(A)-1 or A[left] >= A[right]:
            k = left
        else:
            k = right
        if A[i] >= A[k]:
            return
        else:
            A[i], A[k] = A[k], A[i]
        max_heapify(A, k)

def heap_sort(data, n):
    # max heap 생성
    for i in range(n, 0, -1):
        max_heapify(data, i, n)
    
    # sort
    for j in range(n, 0, -1):
        # heap에서 root가 max이므로
        # 맨뒤로 보낸다
        data[1], data[j] = data[j], data[1]
        # heap size(n)을 하나씩 줄인다
        n -= 1
        max_heapify(data, 1, n)

heap_sort(data, len(data)-1)
print(data)
```
- 시간복잡도
  - $O(n\log_2 n)$

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현

