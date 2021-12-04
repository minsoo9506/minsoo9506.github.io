# [알고리즘] 멱집합, 조합


멱집합, 조합 + python

<!--more-->

## 멱집합(powerset)
- 임의의 data에 대해 모든 부분집합을 의미한다.

이를 recursion으로 해결해보고자 한다.
- `{a,b,c,d}`의 모든 부분집합을 나열하려면
  - `{c,d}`의 모든 부분집합에 `{a}`를 추가한 집합들을 나열한다.
  - `{c,d}`의 모든 부분집합에 `{a,b}`를 추가한 집합들을 나열한다.
- 이런 과정에서 recursion으로 진행하는 것이다.

이를 실제로 구현하기 위해서 어떤 식으로 접근해야할지 고민해보자.
- 위에서 `{c,d}`를 집합 S라고 하자. 이는 **k번째부터 마지막 원소까지 연속된 원소들**이다.
- `{a}`, `{a,b}`는 **처음부터 k-1번째 원소들 중의 일부**이다. 이를 P집합이라 하자.
  - 즉, S의 멱집합을 구한 후 각각에 집합 P를 합집합하면 되는 것이다.
- 이를 위해서 **k라는 매개변수가 필요함**을 알 수 있다.
- 그렇다면 집합 P은 어떻게 표현해야할까?
  - `include`라는 list를 만들어서 `True`면 포함, `False`면 빼면 된다.
  - 이를 통해 k-1번째까지 원소들의 집합들을 만들 수 있을 것이다.

상태공간트리의 형태로 이해해도 좋다. (개인적으로 이게 더 편했다)
- 왼쪽 자식 노드는 k번째 data를 제외
- 오른쪽 자식 노드는 k번재 data를 포함하여서 내려간다.
  - 제일 왼쪽 마지막 노드는 공집합, 오른쪽 마지막 노드는 모든 원소가 포함된 집합

```python
data = ['a','b','c','d']

# 전역변수
n = len(data)
include = [0]*n

# 모든 멱집합을 print하는 함수
def powerset(k: int):
    tmp = []
    if k == n:
        for i in range(n):
            # include가 1인 경우만 print
            if include[i] == 1:
                tmp.append(data[i])
        print(tmp)
        return
    # k번재 원소 제외
    include[k] = 0
    powerset(k+1)
    # k번째 원소 포함
    include[k] = 1
    powerset(k+1)

powerset(0)
```

## 순열(permutation)
- `{a,b,c,d}`의 모든 순열은?
  - `{b,c,d}`의 모든 순열에 `a`를 추가
  - `{a,c,d}`의 모든 순열에 `b`를 추가
  - `{a,b,d}`의 모든 순열에 `c`를 추가
  - `{a,b,c}`의 모든 순열에 `d`를 추가

이런식으로 recursion을 진행하는 것이다.

- 여기서도 매개변수 `k`를 사용하자.
- `k`부터 나머지 원소의 순열에 `0 ~ k-1`까지 원소는 추가해주는 원리이다.

```python
data = ['a','b','c']

def perm(k: int):
    if k == len(data)-1:
        print(data)
        return
    else:
        for i in range(k, len(data)):
            data[k], data[i] = data[i], data[k]
            perm(k+1)
            data[i], data[k] = data[k], data[i]

perm(0)
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
