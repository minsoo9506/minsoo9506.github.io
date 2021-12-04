# [알고리즘] 힙의 응용


우선순위 큐 + python

<!--more-->

## 힙의 응용 : 우선순위 큐

- 우선순위 큐도 최대, 최소 우선순위 큐가 존재한다.
  - FIFO queue
- 다음의 두 가지 연산을 지원하는 자료구조이다.
  - `INSERT` : 새로운 원소를 삽입
  - `EXTRACT_MAX(MIN)` : 최대(최소)값을 삭제하고 반환

### `INSERT`
- heap에 새로운 원소를 넣는 과정
- 맨 마지막 위치에 원소를 넣고 원소의 부모와 값을 비교해가면서 완성된 heap을 만들어간다.

```python
data = ['no use',1,5,6,4,2,3]
n = len(data)-1
# max heap 생성
for i in range(n, 0, -1):
    max_heapify(data, i, n)

def insert(data, original_heap_size, new_data):
    heap_size = original_heap_size + 1
    data[heap_size] = new_data
    i = heap_size
    # i >1 : i가 root가 아니고
    # data[i//2] < data[i] : new_data가 부모보다 큰 경우
    while i > 1 and data[i//2] < data[i]:
        data[i//2], data[i] = data[i], data[i//2]
        i //= 2
```
- 시간복잡도
  - $O(log_2 n)$

### `EXTRACT_MAX()`
- 1. root값이 max이므로 root의 값을 뽑기 & 삭제
- 2. 맨 뒤에 있는 원소를 root로 보내기
- 3. heapify
- 위에서 맨 뒤에 있는 원소를 root로 보내면 다른 영역에서 heap이 무너지지 않는다. 즉, 전체에 대해 heapify할 상태가 된 것이다.  heapify를 하면 문제가 해결되는 것이다.

```python
data = ['no use',1,5,6,4,2,3]
n = len(data)-1
# max heap 생성
for i in range(n, 0, -1):
    max_heapify(data, i, n)

def extract_max(data, heap_size):
    if heap_size < 1:
        return
    # heap의 root는 max
    max_val = data[1]
    # 맨 마지막원소를 root로 보내기
    data[1] = data[heap_size]
    # root값을 뺐으니까
    heap_size -= 1
    # root의 subtree들은 heap인 상태므로 heapify
    max_heapify(data, 1, heap_size)
    return max_val
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
