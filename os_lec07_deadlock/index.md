# [OS] Deadlock


Deadlock에 대해 알아보자.

<!--more-->
## Deadlock
- Deadlock state
  - 프로세스가 **발생 가능성이 없는** 이벤트(or 자원)를 기다리는 경우: 프로세스가 deadlock 상태에 있다고 한다
- starvation과 다른 점
  - starvation은 발생가능성이 없는게 아니다
  - CPU(프로세서)를 기다리는 것이다 (ready state에서 대기)

그렇다면 Deadlock이 발생하는 상황을 생각해보자. 
- 2개의 프로세스($P1,P2$)와 2개의 자원($R1,R2$)이 있다.
- 1. $P1$이 $R2$를 요청한다.
- 2. $P2$가 $R1$을 요청한다.
- 3. $P1$이 $R1$을 요청한다.
- 4. $P2$가 $R2$를 요청한다.

위와 같은 상황이 발생하면 4번에서 deadlock 상태가 되버린다. $P1$은 $R1$을 받아서 일을 끝내고 자원을 반납해야하고 $P2$는 $R2$만 받으면 일을 끝난다. 근데 서로 물고 있어서 끝날 수가 없는 상태가 되는 것이다.

## 자원의 분류
### 선점 가능 여부에 따른 분류
- Preemptible resources
  - 선점 당한 후, 돌아와도 문제가 발생하지 않는 자원
  - ex) processor, memory
    - processor는 context-switching이 발생할 떄, save와 restore
- Non-preemptible resources
  - 선점 당하면, 이후 진행에 문제가 발생하는 자원
  - rollback, restart 등 특별한 동작이 필요
  - ex) disk drive
### 할당 단위에 따른 분류
- Total allocation resources
  - 자원 전체를 프로세스에게 할당
  - ex) processor, disk drive
- Partitioned allocation resources
  - 하나의 자원을 여러 조작으로 나누어, 여러 프로세스들에게 할당
  - ex) memory
### 동시 사용 가능 여부에 따른 분류
- Exclusive allocation resources
  - 한 순간에 한 프로세스만 사용 가능한 자원
  - ex) processor, memory, disk drive
- Shared allocation resource
  - 여러 프로세스가 동시에 사용 가능한 자원
  - ex) program, shared data
    - word 창 여러개 띄워서 쓰는 경우처럼
### 재사용 가능 여부에 따른 분류
- Serially-reusable resources
  - 시스템 내에 항상 존재하는 자원
  - 사용이 끝나면, 다른 프로세스가 사용 가능
  - ex) processor, memory, disk drive, program
- Consumable resources
  - 한 프로세스가 사용한 후에 사라지는 자원
  - ex) signal, message

## Deadlock 발생 필요 조건
아래의 4가지가 성립해야 deadlock이 발생한다.
- Exclusive use of resources
- Non-preemptible resources
- Hold and wait (Partial allocation)
  - 자원을 하나 hold하고 다른 자원 요청
- Circular wait

이제 Deadlock 해결방법에 대해 알아보자. 크게 3가지로 나누어서 생각할 수 있다.

## Deadlock 해결 방법
### Prevention
- 4가지의 deadlock 발생 필요 조건 중 하나을 제거
- deadlock이 절대 발생하지 않지만 심각한 자원 낭비가 발생하거나 비현실적인 대안밖에 없다
#### 모든 자원을 공유허용
- Exclusive use of resources 조건 제거
- 현실적으로 불가능
#### 모든 자원에 대해 선점 허용
- Non-preemptible resources 조건 제거
- 현실적으로 불가능
#### 필요 자원 한번에 모두 할당
- hold and wait 조건 제거
- 자원 낭비 발생
- 무한 대기 발생 가능
#### Circular wait 조건 제거
- 자원들에게 순서를 부여해서 순서의 증가 방향으로만 자원 요청 가능하게 하면 된다
- 자원 낭비 발생

### Avoidance
- 시스템의 상태를 감시하여서 deadlock 상태가 될 가능성이 있는 자원 할당 요청을 보류
- 시스템을 항상 safe state로 유지
  - safe state
    - 모든 프로세스가 정상적 종료 가능한 상태
    - safe sequence가 존재
  - unsafe state
    - deadlock 상태가 될 가능성이 있음 (반드시 발생한다는 의미는 아님)
#### 가정
- 프로세스의 수가 고정됨
- 자원의 종류와 수가 고정됨
- 프로세스가 요구하는 자원 및 최대 수량을 알고 있음
- 프로세스는 자원을 사용 후 반드시 반납
- 위의 가정도 사실 not practical 하다

#### avoidance 알고리즘
- Dijkstra's algorithm 
  - 예시를 통해 이해해보자
  - 1가지 resource가 10개 있다, 3개의 프로세스가 있다
  - 각 프로세스의 리소스 관련 (max.claim, cur.alloc, additional need) 은
    - p1: (3, 1, 2), p2: (9, 5, 4), p3: (5, 2, 3)
  - 남아 있는 리소스는 2개이고 이를 이용하여 실행 종료 순서를 고려하면
  - p1 -> p3 -> p2 : safe sequence가 존재한다, 따라서 safe state라고 할 수 있다
  - 이런 safe sequence를 구할 수 없는 상황이라면 deadlock이 발생할 수도 있다
- 위의 알고리즘을 확장한 (여러 종류의 자원 고려) 것이 Habermann's algorithm 이고 원리는 같다

#### 단점
- 항상 시스템을 감시해야 한다
- safe state 유지를 위해 사용되지 않는 자원이 존재
- 가정들이 not practical

### Detection and Recovery
- deadlock 방지를 위한 사전 작업을 하지 않는다
- Resource Allocation Graph (RAG)를 사용하여 주기적으로 deadlock 발생을 확인하고 해결한다

#### Resource Allocation Graph (Detection)
- directed, bipartite graph
- edge는 프로세스와 리소스 사이에만 존재
- Graph reduction
  - 주어진 RAG에서 edge를 하나씩 지워가는 방법
  - completely reduced
    - 모든 edge가 제거 됨
    - deadlock에 빠진 프로세스가 없음
  - irreducible
    - 지울 수 없는 edge가 존재
    - 하나 이상의 프로세스가 deadlock 상태

#### Graph Reduction
- unblocked process
  - 필요한 자원을 모두 할당 받을 수 있는 프로세스 (아래 식이 의미하는 바)

The process $P_i$ is unblocked if it satisfies
$$\forall j, \| (P_i, R_j) \| \le t_j - \sum_{\text{all k}} \| (R_j,P_k) \|$$

- graph reduction procedure
  - 1. unblocked process에 연결된 모든 edge를 제거
  - 2. 더이상 unblocked process가 없을 때까지 1번 반복

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec07_01.png?raw=true"  width="500">
</center>

#### Recovery
- 방법
  - process termination
    - deadlock 상태에 있는 프로세스를 종료시킴
    - 강제 종료된 프로세스는 이후 재시작 됨
  - resource preemption
    - deadlock 상태 해결 위해 선점할 자원 선택
    - 선정된 자원을 가지고 있는 프로세스에서 자원을 빼았음

위의 방법들은 각각 여러가지 기준을 통해서 종료한 프로세스와 선점할 자원을 선택한다

- checkpoint-restart method
  - 프로세스의 수행 중 특점 지점마다 context를 저장
  - 강제 종료 후, 가장 최근의 check-point에서 재시작 (rollback)

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
