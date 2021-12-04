# [알고리즘] Sort in linear time


Counting, Radix sort + python

<!--more-->

## Counting sort
- 조건
  - n개의 정수를 정렬
  - 단, 모든 정수는 0에서 k사이의 정수 (사전지식)
  - k가 주로 너무 크지 않은 경우 사용한다.
- 방법
  - 길이 k인 배열에 각 정수의 개수를 count한다.
  - 그 count개수를 기준으로 하나씩 뽑아내면 정렬한 결과가 나온다.
  - 예를 들어, `[4,1,2,1]` 데이터가 있는 경우 count 배열은 `[0,2,1,0,1]`이 된다. 그러면 1은 2번, 2를 1번, 4를 1번로 하여 print하면 되는 것이다.

근데 위에서 문제는
- 우리가 정렬하는 데이터들은 다른 데이터들과 함께 묶여있는 경우가 많다.
- 예를 들어, 학생정보가 있는 데이터를 학생의 시험점수로 정렬하고자 하는 경우
- 그래서 윈래의 데이터 reference를 갖고 정렬하는 과정이 필요하다.

이를 위한 해결방법은
- 누적을 이용하는 것이다.
- 예를 들어, `[4,1,2,1]` 데이터가 있는 경우 count 배열은 `[0,2,3,3,4]`이 된다. 각 원소의 개수를 누적합으로 표현한다.
- 그리고 원래 데이터배열 `[4,1,2,1]` 뒤에서부터 하나씩 빼온다.
  - 맨 뒤에 있는 1이 뽑힌다.
    - 이렇게 맨 뒤에서부터 뽑으면 stable 정렬이 가능하다.
    - stable 정렬 알고리즘 : 입력에 동일한 값이 있을때 입력에 먼저 나오는 값이 (정렬된)출력에서도 먼저 나온다.
  - count배열에서 1이하의 값은 2개이므로
  - sort배열의 2번째 위치에 해당 값을 넣는다. (new sort배열 필요, index를 1부터 시작)
  - count배열에서 1의 개수에서 1을 뺀다.
- 이런식으로 반복을 하면 정렬된 배열을 얻을 수 있다.

```python
data = [1,5,6,4,2,3]
# 데이터수는 n
# 데이터는 0부터 k의 값을 가진다.
def counting_sort(data, n=None, k=None):
    if n is None:
        n = len(data)
    if k is None:
        k = max(data)
    sort_list = [0]*(n+1) # index를 1부터 하기 위해
    count_list = [0]*(k+1)
    # data 원소별 개수 count
    for ele in data:
        count_list[ele] += 1
    # 누적합
    for i in range(1,len(count_list)):
        count_list[i] = count_list[i-1] + count_list[i]
    # 정렬
    # data의 맨 뒤부터 시작 -> stable 정렬이 가능
    for i in range(n-1, -1, -1):
        sort_list[count_list[data[i]]] = data[i]
        count_list[data[i]] -= 1

    return sort_list[1:] # index를 1부터 사용했으니까

print(counting_sort(data, len(data), max(data)))
```
- 시간복잡도
  - $O(n+k)\;\text{or}\;O(n)\;\text{if}\;k=O(n)$
  - $k$가 클 경우 비실용적

## Radix sort
- n개의 d자리 정수들
  - 정수가 꼭 아니여도 된다 ex. d자리 문자열
- 가장 낮은 자리수부터 정렬
- stable 정렬을 이용해야한다.
  - 예를 들어, [77,66,69] 이라는 배열이 있다.
  - 먼저 일의 자리에 따라 정렬하면 [66,77,69] 가 되고
  - 다음 십의 자리에 따라 정렬하면 [66,69,77]이 된다.
  - 여기서 66과 69는 십의 자리가 같지만 제대로 정렬이 되는 이유는
  - 이전에 일의 자리에서 정렬한 순서를 그대로 유지했지 때문이다. (stable)
- stable 정렬에 counting sort이용하면 되겠다.
  - 이런 경우 시간복잡도 $O(d(n+k))$
  - 그런데 $d$가 상수고 $k=O(n)$이면 $O(n)$

```python
data = [13,22,16,4,12,23]

def counting_sort_for_radix(data, digit):
    n = len(data)
    sort_list = [0]*(n+1) 
    count_list = [0]*10

    for i in data:
        idx = i // digit
        count_list[idx % 10] += 1

    for i in range(1,10):
        count_list[i] = count_list[i-1] + count_list[i]

    for i in range(n-1, -1, -1):
        idx = data[i] // digit
        sort_list[count_list[idx % 10]] = data[i]
        count_list[idx % 10] -= 1

    return sort_list[1:]

def radix_sort(arr):
    max_val = max(arr)
    digit = 1
    while max_val // digit > 0:
        arr = counting_sort_for_radix(arr, digit)
        digit *= 10

    return arr

print(radix_sort(data))
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
