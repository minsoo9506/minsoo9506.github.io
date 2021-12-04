# [알고리즘] 정렬 알고리즘 (2)


Merge, Quick sort + python

<!--more-->

- 두 가지 방법 모두 *분할정복법* 의 전략을 사용한다.
  - 해결하고자 하는 문제를 작은 크기의 **동일한** 문제들로 분할
  - 각각의 작은 문제를 순환적으로 해결
  - 작은 문제의 해를 합하여(merge) 원래 문제에 대한 해를 구함

## Merge sort (병합정렬)
- 데이터가 저장된 배열을 절반으로 나눔 (divide)
- 각각을 순환적으로 정렬 (recursively sort)
- 정렬된 두 개의 배열을 합쳐서 전체를 정렬 (merge)

계속 나누다보면 길이가 1인 배열이 남을 것이다. 이를 merge할 때, sort를 한다. 이때, 두 배열을 앞에서부터 서로 비교를 하면서 작은 원소를 뽑아서 임시배열에 넣으면 된다. 이 과정에서 임시배열을 위한 별도의 저장 공간이 필요하다.

```python
data = [5,2,3,1,4]

def merge_sort(data):
    def sort(start, end):
        if start < end:
            mid = (start+end) // 2
            sort(start, mid)
            sort(mid+1, end)
            merge(start, mid, end)
        else:
          return

    def merge(start, mid, end):
        # 임시 배열이 필요
        # 임시 배열에 두 배열의 원소들을 비교하면서
        # 넣어주고 다 넣으면 data를 tmp로 수정
        tmp = [0] * len(data)
        i, j = start, mid+1
        k = start

        # 정렬된 두 배열을 merge하는 과정
        while i <= mid and j <= end:
            if data[i] < data[j]:
                tmp[k] = data[i]
                i += 1
                k += 1
            else:
                tmp[k] = data[j]
                j += 1
                k += 1
        
        # 만약에 merge하는 두 배열중에 한쪽이 다 끝나면
        # 나머지 한쪽 배열의 원소들은 그대로 tmp에 넣으면 되니까
        while i <= mid:
            tmp[k] = data[i]
            i += 1
            k += 1
        while j <= end:
            tmp[k] = data[j]
            j += 1
            k += 1

        # tmp에 mergesort된 배열로 data를 수정
        for i in range(start, end+1):
            data[i] = tmp[i] 

    return sort(0, len(data)-1)


merge_sort(data)
print(data)
```

- 시간복잡도 
  - $T(n)$ : n개의 data를 정렬하는데 걸리는 시간
    - if n=1, $T(n)=0$
    - otherwise,  $T(n) = T(n/2) + T(n/2) + n$
    - 여기서 $n$은 정렬된 두 배열의 원소들을 하나씩 비교하면서 merge하는 과정
  - 최종적으로 $O(n \log n)$
    - 그림을 그리면서 고민해보면 이진트리처럼 모양이 나온다. 각 depth마다 연산량이 $n$이기 때문에!

## Quick sort (퀵정렬)
- 분할정복법
  - 분할
    - 배열의 원소중 하나를 pivot으로 선정한다.
    - [pivot보다 작은 원소들], [pivot보다 큰 원소들] 두 부분으로 나눈다.
  - 정복 : 각 부분을 순환적으로 정렬한다.
  - 합병 : 해당사항이 없다.

```python
data = [5,2,3,1,4]

def quick_sort(data):
    def sort(start, end):
        if start < end:
            q = partition(start, end)
            sort(start, q-1)
            sort(q+1, end)
        else:
            return

    def partition(start, end):
        # 맨 뒤에 있는 원소를 pivot으로 지정
        pivot = data[end]
        # i는 pivot보다 작은원소들중 마지막 원소의 위치
        i = start - 1
        # j은 pivot과 비교하는 원소의 위치
        # 처음부터 pivot전까지 순서대로 쭉 간다
        for j in range(start, end):
            # 원소가 pivot보다 작으면 i++ 하고 i와 j위치 swap
            # 그러면 i는 작은원소들중 마지막 원소의 위치 유지
            # i이하의 원소들은 다 pivot보다 작은 원소들
            # 원소가 pivot보다 크면 nothing
            if data[j] < pivot:
                i += 1
                data[i], data[j] = data[j], data[i]
        # pivot 앞에 위치한 원소까지 다하면 
        # i+1의 위치의 원소와 pivot을 swap하면 정렬완료!
        data[i+1], data[end] = data[end], data[i+1]
        # pivot의 위치 return
        return i+1

    return sort(data, 0, len(data)-1)

quick_sort(data)
print(data)
```

- 시간복잡도
  - 최악의 경우 $O(n^2)$
    - 항상 한 쪽은 0개 다른 쪽은 n-1개로 분할되는 경우
    - pivot의 값이 어떤 값 걸리냐에 따라 달라지겠다. pivot값이 전체에서 최소, 최대값인 경우 $O(n^2)$이 되는 것이다.
  - 최선의 경우 $O(n\log n)$
    - 항상 절반으로 분할이 되는 경우

일반적으로 quick sort가 선호되는 이유는 무엇일까.

- 항상 한쪽이 적어도 1/9 이상이 되도록 분할된다면 가정해보자.
  - [그림 영상 40분 참고](https://www.youtube.com/watch?v=hq4dpyuX4Uw&list=PL52K_8WQO5oUuH06MLOrah4h05TZ4n38l&index=11&t=1606s)
  - 제일 오래 걸리는 경우는 계속 분할이 0.9만큼 되는 경우이므로 $(\frac{9}{10})^k * n = 1$ 이라고 할 수 있다.
  - 따라서 $k=\log_{10/9}n \approx \log n$ 이라고 할 수 있다.
  - 1:9만 되더라도 $O(n\log n)$이 되는 것이다.

실제로 극단적으로 최악의 경우가 되는 경우는 많지 않으니까 quick sort가 일반적으로 좋은 성능을 보인다고 할 수 있다. 실제로 quick sort의 평균시간복잡도를 수학적으로 계산하면 $O(n\log_2 n)$이 나온다.

- pivot의 선택
  - 첫번째 값이나 마지막 값을 pivot으로 선택
    - 좋은 방법은 아니다.
    - 이미 정렬된 데이터 혹은 거꾸로 정렬된 데이터가 들어오는 경우 최악의 경우가 된다.
  - Median of three
    - 중간값을 pivot으로 선택
    - 정렬된 데이터가 들어와도 최악의 경우가 발생하지 않는다.
    - 그렇다고 최악의 경우에 해당하는 시간복잡도가 달라지는 것은 아니다.
  - Randomized Quicksort
    - pivot을 랜덤하게 선택
    - 최악의 경우가 되는 경우는 확률적으로 거의 없지만 없다고는 할 수 없다.

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현

