# [알고리즘] Hash


Hash 개념 + python

<!--more-->

## Hash table
- 해쉬테이블은 dynamic set을 구현하는 효과적인 방법의 하나
  - 적절한 가정하에서 평균 탐색, 삽입, 삭제시간 $O(1)$
  - 보통 최악의 경우 $O(n)$
- 해쉬함수(hash function) h를 사용하여 key k를 T[h(k)]에 저장
  - key k가 h(k)로 해슁되었다고 말함
  - 즉 각 key에 대한 해쉬함수값을 그 key를 저장할 배열 인덱스로 사용

### 해쉬 함수의 예
- h(k) = k % m
  - 즉, key를 하나의 자연수로 해석한 후 테이블의 크기 m으로 나눈 나머지
  - 항상 0 ~ m-1 사이의 정수가 됨

## 충돌 collision
- 근데 배열의 크기가 데이터의 수보다 작은 경우는? 충돌이 발생할 수 밖에 없다. 즉, 다른 key값을 가지지만 같은 인덱스를 가질 수 있는 것이다.
- 서로 다른 두 key a와 b에 대해서 h(a) = h(b) 인 상황
- 대처 방법
  - chaining과 open addressing

### Chaining에 의한 충돌 해결
- 동일한 장소로 해슁된 모든 key들을 하나의 연결리스트로 저장
- key의 삽입
  - 연결리스트 T[h(k)]의 맨 앞에 삽입 : $O(1)$
  - 중복된 key가 들어올 수 있고 중복 저장이 허용되지 않는다면 삽입시 리스트를 검색해야 함 : 이 경우 시간복잡도는 리스트의 길이에 비례
- key의 검색
  - 리스트 T[h(k)]에서 순차검색
  - 시간복잡도는 key가 저장된 리스트의 길이에 비례
- key의 삭제
  - 리스트 T[h(k)]로부터 key를 검색 후 삭제
  - 일단 key를 검색해서 찾은 후에는 $O(1)$시간에 삭제 가능
- 최악의 경우는
  - 모든 key가 하나의 해싱값을 가지는 경우
  - 길이가 n인 하나의 연결리스트가 만들어짐
  - 따라서 최악의 경우 탐색시간은 $O(n)$+해쉬함수 계산시간
- **평균시간복잡도는 key들이 여러 슬롯에 얼마나 잘 분배되느냐에 의해서 결정된다!**

### SUHA (simple uniform hashing assumption)
- 각각의 key가 모든 슬롯들에 균등한 확률로 독립적으로 해슁된다는 가정
  - hash 함수는 deterministic하므로 현실에서는 불가능
- Load factor $\alpha = n/m$
  - n : 테이블에 저장될 key의 개수
  - m : 해쉬테이블의 크기, 즉, 연결리스트의 개수
  - 각 슬롯에 저장된 key의 평균 개수
- 연결리스트 T[j]의 길이를 $n_j$라고 하면 $E[n_j]=\alpha$
- 만약 $n=O(m)$이면 평균검색시간은 $O(1)$

### Open addressing에 의한 충돌 해결
- 모든 key를 해쉬 테이블 자체에 저장
- 테이블의 각 슬롯에는 1개의 key만 저장
- 충돌 해결 기법
  - Linear probing
  - Quadratic probing
  - Double hashing

### Open addressing - Linear probing
- 들어가야할 슬롯에 이미 데이터가 있다면 순서대로 $h(k)+1, h(k)+2 ...$ 검사하여 처음으로 빈 슬롯에 저장
- 단점
  - cluster가 생성되면 점점 더 커지는 현상이 생김
- 이에 대한 보완방법
  - Quadratic probing
    - 충돌 발생시 $h(k)+1^2,\;h(k)+2^2...$
  - Double hashing
    - 서로 다른 두 해쉬함수 h1,h2를 이용
    - h1(k) = k mod m, h2(k) = h1(k) + (k mod 어떤 수)
    - 즉, 겹쳤을 때 건너뛰는 정도가 key값마다 달라지는 것

### Open addressing - key의 삭제
- 단순히 key를 삭제할 경우 문제 발생
  - dynamic set같은 경우 데이터의 추가,삭제가 빈번한다
  - 그렇다면 삭제가 많이 발생한 경우 중간에 빈칸이 많이 생기고
  - 이는 search연산을 진행할 때 쓸데없는 시간을 소비하게 한다
- 그래서 데이터를 삭제한 뒤에 뒤에 있는 데이터를 가져온다 (같은 해쉬값을 갖는 데이터들만)

## 좋은 해쉬 함수란?
- 현실에서는 key들이 랜덤하지 않음
- 만약 key들의 통계적 분포에 대해 알고 있다면 이를 이용해서 해쉬 함수를 고안하는 것이 가능하겠지만 현실적으로 어려움
- key들이 어떤 특정한 패턴을 가지더라도 해쉬함수값이 불규칙적이 되도록 하는게 바람직
  - 해쉬함수값이 key의 특정 부분에 의해서만 결정되지 않아야한다
  - 예를 들어, 학번 : 2014xxxx 같은 패턴이 있다

## 해쉬 함수
- Division 기법
  - h(k)= k mod m
  - 장점 : 빠르다.
  - 단점 : 어떤 m값에 대해서는 해쉬함수값이 key값의 특정 부분에 의해서 결정되는 경우가 있다.
- Multiplication 기법
  - 0에서 1사이의 상수 A를 선택
  - kA의 소수부분만을 택한다.
  - 소수 부분에 m을 곱한 후 소수점 아래를 버린다.

## 파이썬에서 hash
- 파이썬은 Open addressing 방법을 사용
- 파이썬에서 해시 테이블로 구현된 자료형 : 딕셔너리

```python
import hashlib
hash_obj = hashlib.sha256()
hash_obj.update(b'hello world')
hex_dig = hash_obj.hexdigest()
print(hex_dig) 
# 결과
# b94d27b9934d3e08a52e52d7da7dabfac484efe37a5380ee9088f7ace2efcde9
```

```python
# 해시맵, 개별체이닝 구현
class ListNode:
      def __init__(self, key=None, value=None):
    self.key = key
    self.value = value
    self.next = next

import collections

class HashMap:
    def __init__(self):
        self.size = 1000
        self.table = collections.defaultdict(ListNode)

    def put(self, key: int, value: int) -> None:
        index = key % self.size # division 기법
        
        # index에 노드가 없는 경우
        # defaultdict는 index가 없으면 바로 생성하기에
        # if self.table[index] is None: 이렇게 하면 무조건 True
        if self.table[index].value is None:
            self.table[index] = ListNode(key, value)
            return

        # 노드가 있는 경우
        p = self.table[index]
        while p:
            if p.key == key:
                p.value = value
                return
            if p.next is None:
                return
            p = p.next
        p.next = ListNode(key, value)

    def get(self, key: int) -> int:
        index = key % self.size
        # 해당하는 index가 비어있는 경우
        if self.table[index].value is None:
            return -1
        # 해당하는 index에서 값을 찾는다
        p = self.table[index]
        while p: 
            if p.key == key:
                return p.value
            p = p.next
        return -1

    def remove(self, key: int) -> None:
        index = key % self.size
        # 해당하는 index가 비어있는 경우
        if self.table[index].value is None:
            return
        # index의 첫번째 노드일 때 삭제처리
        p = self.table[index]
        if p.key == key:
            # if self.table[index].value is None: 이부분이 에러나지 않도록하기 위해
            self.table[index] = ListNode() if p.next is None else p.next
            return
        # 연결 리스트 노드 삭제
        prev = p
        while p:
            if p.key == key:
                prev.next = p.next
                return
            prev, p = p, p.next
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
