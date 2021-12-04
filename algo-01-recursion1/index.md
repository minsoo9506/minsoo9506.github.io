# [알고리즘] Recursion의 개념과 기본 예제들


Recursion(재귀)의 개념 + python

<!--more-->

## 순환이란?
- 자기 자신을 호출하는 함수
- 무한루프에 빠지지 않으려면?
  - Base case를 통해 **적어도 하나의 recursion에 빠지지 않는 경우가 존재**해야 한다.

```python
def func(n: int) -> int:
    # Base case
    if n == 0:
        return 0
    else:
        # recursive case
        return n + func(n-1)
```

## Recursive Thinking
- 수학함수 뿐만 아니라 다른 많은 문제들을 recursion으로 해결 할 수 있다.

```python
# 문자열의 길이 계산
def len_str(s: str) -> int:
    if len(s) == 1:
        return 1
    else:
        return 1 + len(s[1:])

# 문자열의 프린트
def print_str(s: str) -> str:
    if len(s) == 1:
        return s
    else:
        return s[0] + print_str(s[1:])

# 문자열을 뒤집어 프린트
def reverse_print_str(s: str) -> str:
    if len(s) == 1:
        return s
    else:
        return s[-1] + reverse_print_str(s[:-1])

# 2진수로 변환하여 출력
def print_as_binary(n: int) -> int:
    if n < 2:
        print(n)
    else:
        print_as_binary(n//2)
        print(n%2)

# 최대값 찾기
def find_max(n: int, nums: List[int]) -> int:
    if n == 1:
        return nums[0]
    else:
        return max(nums[n-1], find_max(n-1, nums))
```

## Recursion vs Iteration
- 모든 순환함수는 반복문으로 변경 가능
- 그 역도 성립
- 순환함수는 복잡한 알고리즘을 단순하고 알기 쉽게 표현하는 것을 가능하게 함

## 순환적 알고리즘 설계
- 암시적 매개변수를 **명시적 매개변수로 바꾸어라**

```python
# 순차 탐색 예시
# 여기서 index 0은 보통 생략 -> 즉, 암시적 매개변수
def search(data: List, target: int) -> int:
    for i in range(len(data)):
        if data[i] == target:
            return i
    return -1

# 매개변수의 명시화 : 순차탐색
def search(data: List, begin: int, target: int) -> int:
    if begin > len(data):
        return -1
    elif target == data[begin]:
        return begin
    else:
        return search(data, begin+1, target)

# 매개변수의 명시화 : 최대값 찾기
def findmax(data: List, begin: int):
    if begin == len(data):
        return -1
    else:
        return max(data[begin], findmax(data, begin+1))

# 매개변수의 명시화 : 이진탐색
def binarySearch(data: List,
                 begin: int,
                 end: int,
                 target: int) -> int:
    if begin > end:
        return -1
    else:
        mid = (begin+end) // 2
        if data[mid] == target:
            return mid
        elif data[mid] < target:
            return binarySearch(data, mid, end, target)
        else:
            return binarySearch(data, begin, mid, target)
```

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
