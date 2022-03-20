# [OS] Process Scheduling


Process scheduling에 대한 기본개념 정리

<!--more-->
여러개의 프로세스가 시스템에 존재하기 때문에 자원을 할당 할 프로세스를 잘 관리해야한다. 이번에는 이런 프로세스를 스케쥴링하는 방법을 알아보자.

## 스케줄링의 목적
- 시스템의 성능 향상
- 성능 지표
  - 응답시간
  - 작업 처리량
  - 자원 활용도

## 스케줄링의 단계
- Long-term scheduling
  - job scheduling: kernel에 등록할 작업 결정
- Mid-term scheduling
  - memory allocation
- Short-term scheduling
  - process scheduling
    - 프로세서를 할당할 프로세스를 결정
    - 가장 빈번하게 발생하기에 빨라야 함

## 스케줄링 정책
- Preemptive/Non-preemptive scheduling
  - Non-preemtive
    - 할당 받을 자원을 스스로 반납할 때까지 사용
  - Preemptive
    - 타의에 의해 자원을 빼앗길 수 있음
- Priority
  - Static priority
    - 프로세스 생성시 결정된 priority가 유지 됨
  - Dynamic priority
    - 프로세스의 상태 변화에 따라 priority가 변경

## 기본 스케줄링 알고리즘
### FCFS (First-Come-First-Service)
- Non-preemptive
- 도착시간 기준, 먼저 도착한 프로세스를 먼저 처리
- 대기시간이 길어질 수 있다, 긴 평균 응답시간
### RR (Round-Robin)
- Preemptive
- 도착시간 기준, 먼저 도착한 프로세스를 먼저 처리하지만 자원사용 제한시간이 있다
- 프로세스는 할당된 시간이 지나면 자원을 반납한다
- context switch overhead가 클 수 있다
### SPN (Shortest-Process-Next)
- Non-preemptive
- 실행시간 기준, 실행시간이 가장 작은 프로세스를 먼저 처리
- 평균 대기시간이 작고, 시스템 내 프로세스 수도 작다
- 실행시간을 예측하기 어렵다, Starvation(무한대기) 발생
### SRTN (Shortest Remaining Time Next)
- Preemptive
- 잔여 실행 시간이 더 적은 프로세스가 ready가 되면 선점된다
- SPN의 장점 극대화
- 실행시간을 예측하기 어렵고 잔여 실행을 계속 추척해야 한다
### HRRN (High-Response-Ratio-Next)
- SPN + Aging + Non-preemptive
- 프로세스이 대기시간(Aging)을 고려하여 기회를 제공한다
- SPN의 장점 + starvation 방지
- 실행시간을 예측하기 어렵다
### MLQ (Multi-level Queue)
- 작업(or 우선순위)별 별도의 ready queue를 가진다
  - 최초 배정된 queue를 바꿀 수 없다
  - 각각의 queue는 자신만의 스케줄링 기법 사용
- queue 사이에는 우선순위 기반의 스케줄링 사용
- 여러개의 queue로 인한 장점도 있지만 관리해야 하는 단점도 있다
- 우선순위가 낮은 queue는 starvation 현상이 발생 할 수도 있다
### MFQ (Multi-level Feedback Queue)
- 프로세스의 queue간 이동이 허용된 MLQ
- Feedback을 통해 우선 순위 조정
- 프로세스에 대한 사전 정보 없이 SPN ~ HRRN 기법의 효과를 볼 수 있다
- MFQ를 조금씩 변형해서 사용된다

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
