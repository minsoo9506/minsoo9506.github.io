# [OS] Process Synchronization and Mutual Exclusion


Process Synchronization and Mutual Exclusion 에 대한 개념 정리 (semaphore)

<!--more-->
## Process Synchronization and Mutual Exclusion
- 여러 개의 프로세스들이 존재할 때, 공유자원 또는 데이터가 있을 때 문제가 발생할 수 있다.
- 이런 경우를 대비하기 위해 동기화를 한다.
  - 동기화: 프로세스들이 서로 동작을 맞추는 것, 정보를 공유해서 문제가 발행하지 않도록 하는 것
- Mutual exclusion
  - 둘 이상의 프로세스가 동시에 critical section에 진입하는 것을 막는 것
    - critical section: 공유 데이터를 접근하는 코드 영역

### Requirements for ME
- Mutual exlcusion
  - critical section에 프로세스가 있으면, 다른 프로세스의 진입을 금지
- Progress
  - critical section 안에 있는 프로세스 외에는 다른 프로세스가 critical section에 진입하는 것을 방해하면 안됨
- Bounded waiting
  - 프로세스의 critical section 진입은 유한시간 내에 허용되어야 함

Mutual Exclusino Solutions에 대한 solution들이 많다. 그중에서 os supported SW solution중 하나인 semaphore에 대해 알아보자.

## Semaphore
- $S$: 음이 아닌 정수형 변수
- 초기화 연산 $P(),V()$로만 접근 가능
- 종류
  - binary semaphore: 상호배제나 프로세스 동기화의 목적
  - counting semaphore: producer-consumer 문제 등을 해결하기 위해 사용

여기서는 상호배제의 경우만 알아보자.

- $P(S)$ 연산
  - if $S( > 0)$
  - then $S \leftarrow S - 1$
  - else wait on the queue $Q_s$
- $V(S)$ 연산
  - if any waiting processes on $Q_s$
  - then wakeup one of them
  - else $S \leftarrow S + 1$

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec06_01.png?raw=true"  width="500">
</center>

- $P_i$가 먼저 들어와서 $P(\text{active})$ 후 critical section에 들어간다.
- 다음에 $P_j$가 들어왔는데 active가 0이라서 ready queue로 들어가서 대기한다.
- $P_i$가 끝나면 $V(\text{active})$ 연산을 진행해서 queue에 있는 $P_j$를 wake up 하고 active=1이 된다.
- $P_j$는 $P(\text{active})$연산을 진행하고 critival section에 들어간다.

위와 같은 과정을 통해 프로세스가 충돌하지 않도록, mutual exclusion 하도록 만드는 것이다.

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
