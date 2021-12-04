# [알고리즘] 정렬 알고리즘 (1)


Selection, Bubble, Insertion sort + python

<!--more-->

## Selection sort
- 순서대로 하나씩 비교하면서 가장 큰 값을 맨 뒤로 보낸다.

```python
data = [5,2,3,1,4]

def selection_sort(data):
    n = len(data)
    # for 루프는 n-1번 반복
    for last in range(n-1, 0, -1): 
        max_val = data[last]
        # 가장 큰 수를 찾기 위해  last번 반복
        for k in range(0, last):        
            if max_val < data[k]: 
                data[last], data[k] = data[k], data[last]

selection_sort(data)
print(data)
```
- 시간복잡도
  - $(n-1)+(n-2)+...+1=O(n^2)$
  - 최악, 최선, 평균의 경우 모두 동일

## Bubble sort
- 두 개씩 비교해서 큰 값을 뒤로 보낸다.
- 이를 반복하면 가장 큰 값이 맨 뒤로 간다.

```python
data = [5,2,3,1,4]

def bubble_sort(data):
    n = len(data)
    # for 루프는 n-1번 반복
    for last in range(n-1, 0, -1): 
        # for 루프는 last번 반복
        for k in range(0, last):
            if data[k] > data[k+1]: 
                data[k], data[k+1] = data[k+1], data[k]

bubble_sort(data)
print(data)
```
- 시간복잡도
  - $(n-1)+(n-2)+...+1=O(n^2)$
  - 최악, 최선, 평균의 경우 모두 동일

## Insertion sort (삽입정렬)
예를 들어 [2,4,3,1]을 정렬한다고 생각해보자.
- [2]는 하나이므로 정렬된 상태
- 4을 [2]의 원소와 비교하여 2뒤에 삽입 : [2,4]
- 3을 [2,4]의 원소와 비교하여 2뒤, 4앞에 삽입 : [2,3,4]
- 1을 [2,3,4]의 원소와 비교하여 2앞에 삽입 : [1,2,3,4]

근데 여기서 삽입을 위해 이미 정렬된 배열의 원소들과 비교할 때
- 뒤에서부터, 즉 큰 값들부터 비교하는게 더 빠르다.
- 앞에서부터(작은 값부터) 비교하면 맨 뒤까지 가야하지만
- 뒤에서부터(큰 값부터) 내려오다가 삽입값보다 작은 값을 만나면 멈추고 삽입할 수 있기 때문이다.

```python
data = [5,2,3,1,4]

def insert_sort(data):
    n = len(data)
    # for 루프는 n-1번 반복
    for i in range(1,n): 
        insert_val = data[i]
        # 최악의 경우 i-1번 반복
        while i > 0 and data[i-1] > insert_val:
            data[i] = data[i-1]
            i -= 1
        data[i] = insert_val

insert_sort(data)
print(data)
```
- 시간복잡도
  - 최악 : $(n-1)+(n-2)+...+1=O(n^2)$
  - 최선 : $O(n)$

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
