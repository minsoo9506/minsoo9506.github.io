# [알고리즘] 정렬의 lower bound


정렬 알고리즘의 lower bound는?

<!--more-->

- comparison sort
  - 데이터들간의 상대적 크기관계만을 이용해서 정렬하는 알고리즘
  - 이전까지 배운 bubble, insertion, merge, quick, heap sort 등

- non-comparison sort
  - 정렬할 데이터에 대한 사전지식을 이용
  - 예) 100점만점 시험인 학생들의 점수(최대100점이라는 사전지식이 있는 것) : 90점대, 80점대.. 이렇게 분류한 뒤 정렬
  - Bucket sort
  - Radix sort

## 하한 (Lower Bound)
- 입력된 데이터를 한벅씩 다 보기위해서 최소 $O(n)$의 시간복잡도 필요
- merge, heap sort의 시간복잡도는 $O(n\log_2 n)$
- **어떤 comparison sort 알고리즘도 $O(n\log_2 n)$보다 나을수 없다.**

이에 대한 증명은 decision tree로 한다.
- n개의 data를 정렬하고자 한다.
- 그렇다면 가능한 경우의 수는? n!
- 이를 decision tree를 통해 그 과정을 나타내면 leaf가 n!개인 decision tree가 완성될 것이다.
- 따라서 $height \ge \log_2 n!$임을 알 수 있다.
  - height는 시간복잡도를 의미한다고 할 수 있다. 비교를 할 때 2개의 숫자중 큰것, 작은것으로 나누기 때문이다.
- 그런데 수학적인 증명을 통해 $\log_2 n! = O(n\log_2 n)$라고 한다.

## Reference
- 권오흠 교수님 알고리즘
