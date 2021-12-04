# [알고리즘] 동적계획법


Dynamic Programming + Python

<!--more-->

### Fibonacci 예시
```python
def fib(n):
    if n==1 or n==2:
        return 1
    else:
        return fib(n-2) + fib(n-1)
```
- 많은 계산이 중복된다.

## Memoization
- 중복되는 계산을 caching함으로써 중복계산을 피한다.
```python
def fib(n):
    if n==1 or n==2:
        return 1
    elif f[n] > -1: # 배열 f가 -1로 초기화되어 있음
        return f[n] 
    else:
        f[n] = fib(n-2) + fib(n-1)
        return f[n]
```

## Dynamic Programming
- bottom-up 방식으로 중복계산을 피한다.
```python
def fib(n):
    f[1], f[2] = 1, 1
    for i in range(3, n+1):
        f[n] = f[n-1] + f[n-2]
    return f[n]
```

### Memoization vs Dynamic Programming
- 순환식의 값을 계산하는 기법들이다.
- 둘 다 동적계획법의 일종으로 보기도 한다.
- Memoization은 top-down방식, 실제로 필요한 subproblem만 푼다. 기본적으로 recursion으로 한다.
- Dynamic Programming은 bottom-up방식, recursion에 수반되는 overhead가 없다.

### 행렬 경로 문제
- n*n 행렬의 좌상단에서 우하단까지 이동한다.
- 단, 오른쪽이나 아래쪽 방향으로만 이동할 수 있다.
- 이때, 방문한 칸에 있는 정수들의 합이 최소가 되는 경우?
- 순환식
  - $L[i,j]$ : (1,1)에서 (i,j)까지 이르는 최소합
  - $L[i,j]=min(L[i-1,j], L[i,j-1])+m_{ij}$
  - 이를 그대로 recursive하게 진행하면 중복계산이 많다.

```python
# Memoization
def mat(i, j):
    if L[i][j] != -1:
        return L[i][j]
    elif i == 1:
        L[i][j] = mat(1, j-1) + m[i][j]
    elif j == 1:
        L[i][j] = mat(i-1, 1) + m[i][j]
    else:
        L[i][j] = min(mat(i-1, j), mat(i, j-1)) + m[i][j]
    return L[i][j]

# Bottom-up
def mat():
    for i in range(1, n+1):
        for j in range(1, n+1):
            if i==1 & j==1:
                L[i][j] = m[1][1]
            elif i==1:
                L[i][j] = m[i][j] + L[i][j-1]
            elif j==1:
                L[i][j] = m[i][j] + L[i-1][1]
            else:
                L[i][j] = m[i][j] + min(L[i-1][j], L[i][j-1])
    
    return L[n][n]
```

## 동적계획법
- 일반적으로 **최적화문제(최소, 최대값)** 혹은 **카운팅(counting)문제**에 적용된다.
- 주어진 문제에 대한 **순환식**을 정의 -> 순환식을 memoization 혹은 bottom-up 방식으로 푼다.
  - 순환식을 잘 만드는것이 핵심포인트!
  - **최적해의 일부분이 그부분에 대한 최적해인가?** 질문해보자 ex) 행렬 경로 문제 참고
  - 순환식은 optimal substructure를 표현
- subproblem을 풀어서 원래 문제를 푸는 방식
  - 분할정복법과 공통점이 있겠구나
  - 분할정복법에서는 분할된 문제들이 서로 disjoint하지만 동적계획법에서는 그렇지 않다.
  - 즉, **서로 overlapping하는 subproblem들을 해결함으로써 원래 문제를 해결하고자 한다.**

### Matrix-Chain 곱하기
- 행렬 $A\;:10*100,\;B\;:100*5,\;C\;:5*50$
- $(AB)C$ : 7500번의 곱셈이 필요
- $A(BC)$ : 75000번의 곱셈이 필요
- 곱하는 순서에 따라서 연산량이 다르다.
- 그렇다면 n개의 행렬곱 $A_1 A_2 ... A_n$을 계산하는 최적의 순서는?
- optimal substructure
  - 최종 결과는 앞부분의 곱과 뒷부분의 곱의 결과를 곱한 것! $(Z=A_a A_b)$
- 순환식
  - $m[i,j]:\;A_i A_{i+1} ...A_j$의 최소곱셈 횟수
  - $m[i,j]=min_{i \le k \le j-1} (m[i,k]+m[k+1,j] + p_{i-1} p_k p_j)$

## Reference
- 권오흠 교수님 알고리즘
  - Java예시를 Python으로 바꿔서 구현
