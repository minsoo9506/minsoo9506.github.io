# [자료구조] 힙


비선형적인 자료구조 힙에 대해 정리

<!--more-->

## 힙
- 트리 기반의 자료구조
- 우선순위 큐를 주로 힙으로 구현한다. 이전에도 `heapq`를 사용했었다. (근데 힙은 주로 배열로 구현)
- 여러 개의 값들 중에서 최댓값이나 최솟값을 빠르게 찾아내도록 만들어진 자료구조이다.
- 힙은 정렬된 구조는 아니다.
- 힙 트리에서는 중복된 값을 허용한다. (이진 탐색 트리에서는 중복된 값을 허용하지 않는다.)

## 힙의 종류
- 이진 힙
  - 자식이 둘인 힙
  - 힙을 애기할때는 주로 이진힙을 의미
- 최소 힙
  - 부모가 항상 자식보다 작거나 같다
- 최대 힙
  - 최소 힙과 반대

## 힙 구현
- 대개 트리의 배열 표현의 경우 index 계산을 편하게 하기 위해 index는 1부터 시작한다.
  - 이진힙에서 부모의 index를 계산할 때 본인 index를 2로 나눈 몫

```python
# 최소힙구현
class BinaryHeap:
    def __init__(self):
        self.items = [None]
    def __len__(self):
        return len(self.items) - 1

    # 삽입 : O(logn)
    # 반복 구조
    def _percolate_up(self):
        # 배열 가장 마지막에 먼저 삽입
        i = len(self)
        parent = i // 2
        # 부모가 더 큰 값을 가지는 경우 스왑
        while parent > 0:
            if self.items[i] < self.items[parent]:
                self.items[parent], self.itmes[i] = \
                    self.items[i], self.items[parent]
                i = parent
                parent = i // 2
                
    def insert(self, k):
        self.items.append(k)
        self._percolate_up()

    # 추출 : O(logn)
    # 재귀 구조
    def _percolate_down(self, idx):
        left = idx * 2
        right = idx * 2 + 1
        smallest = idx

        if left <= len(self) and self.items[left] < self.items[smallest]:
            smallest = left
        if right <= len(self) and self.items[right] < self.items[smallest]:
            smallest = right

        if smallest != idx:
            self.items[idx], self.items[smallest] = \
                self.items[smallest], self.items[idx]
            self._percolate_down(smallest)

    def extract(self):
        extracted = self.items[1]
        self.items[1] = self.items[len(self)]
        self.items.pop()
        self._percolate_down(1)
        return extracted
```

## Python 예시 
- [Link](https://github.com/minsoo9506/computer-science-study/tree/master/%5BData%20Structure%5D%20python%20algorithm%20interview)
- 배열의 K번째 큰 값 찾기
  - `heapq` 사용법
  - `heapq.heapify(my_list)` 를 통해 리스트를 heap으로 만든다.
  - `heapq.nlargest(k, my_list)`를 통해 리스트에서 큰 순서대로 k개 return한다.

## Reference
- 파이썬 알고리즘 인터뷰 (박상길 지음)
