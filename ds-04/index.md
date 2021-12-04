# [자료구조] deque, 우선순위 큐


선형적인 자료구조 데크과 우선순위 큐에 대해 정리

<!--more-->

## 데크
- 양쪽 끝을 모두 추출할 수 있는, 큐를 일반화한 형태의 추상 자료형
- Python에서 제공하는 `collections.deque`는 이중 연결 리스트로 구현되어 있다.

## 우선순위 큐
- 우선순위가 높은 요소가 추출되는 자료형
- 최단 경로를 탐색하는 다익스트라 알고리즘, 힙 자료구조 와도 관련이 깊다.
- Python에서는 `heapq`를 사용
  - 최소 힙
  - 파이썬의 보통 리스트를 마치 최소 힙처럼 다룰 수 있도록 도와준다.
  - 최대 힙을 만들려면 각 값에 대한 우선 순위를 구한 후, `(우선 순위, 값)` 구조의 튜플(tuple)을 힙에 추가하거나 삭제

```python
import heapq
heap = []
heapq.heappush(heap, (-num, num))  # (우선 순위, 값)
```

## Python
- [Link](https://github.com/minsoo9506/DS-AL-study)
- 원형 데크 디자인
  - 직접 deque를 구현
- k개 정렬 리스트 병합
  - `heapq`

## Reference
- 파이썬 알고리즘 인터뷰 (박상길 지음)
