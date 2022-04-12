# [OS] Process Management


Process에 대한 개념 정리

<!--more-->
## 프로세스의 개념
- 작업(Job) / 프로그램 (Program)
  - 실행 할 프로그램 + 데이터
  - 즉, 실행을 요청하기 전 상태
- 프로세스 (Process)
  - 실행을 위해 시스템(커널)에 등록된 작업

### 프로세스의 정의
- 실행중인 프로그램
  - 커널에 등록되고 커널의 관리하에 있는 작업
  - 각종 자원들을 요청하고 할당 받을 수 있는 개체
  - PCB를 할당 받은 개체
  - 능동적인 개체 (실행 중에 자원들을 요구, 할당, 반납하며 진행)

## PCB
- Process Control Block (PCB)
  - os가 프로세스 관리에 필요한 정보 저장
  - 프로세스 생성 시, 생성된다
- PCB가 관리하는 정보 (os마다 조금씩 다름)
  - PID (Process Identification Number)
  - 스케줄링 정보
  - 프로세스 상태
  - 메모리 관리 정보
  - 입출력 상태 정보
  - 문맥 저장 영역
  - 계정 정보

## 프로세스 상태 변화
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec03_01.png?raw=true"  width="500">
</center>

### Create State
- 작업(Job)을 커널에 등록
- PCB 할당 및 프로세스 생성
- 이후에 memory가 할당되면 ready로 아니면 suspended ready

### Ready State
- 프로세서 이외의 자원을 할당받은 상태
  - 프로세서 할당 대기, 즉시 실행 가능 상태
- Dispatch (Schedule)
  - ready -> running

### Running State
- 프로세서와 필요한 자원을 모두 할당 받은 상태
- Preemption (Time run out)
  - 프로세서를 뺏기는 것 (eg. time-out, priority changes)
  - running에서 ready로
- Block (Sleep)
  - I/O 같은 자원 할당 요청이 필요한 해서 asleep상태로 가는 것
  - running -> asleep

### Blocked/Asleep State
- 프로세서 외에 다른 자원을 기다리는 상태
  - Wake-up
    - Asleep -> ready

### Suspended State
- 메모리를 할당받지 못한(빼앗긴) 상태
  - memory image를 swap device (eg. 하드디스크)
에 보관
  - 커널 또는 사용자에 의해 발생
- swap-out(suspended), swap-in(resume)

### Terminated/Zombie State
- 프로세스 수행이 끝난 상태
- 모든 자원 반납 후, 커널 애에 일부 PCB 정보만 남아있는 상태

## 인터럽트
- unexpected and external events

### 인터럽트 처리 과정
- 먼저 $P_i$라는 프로세스가 running 상태라고 하자
- 커널에 의해 interrupt가 들어오면 processor는 해당 프로세스를 멈추고 context saving을 한다
- 그리고 interrupt handler를 통해서 이를 어찌할지 결정하고
- interrupt service를 통해 이를 해결한다
- 마지막으로 context restoring을 하여 $P_i$를 진행하다
  - 다만 여기서 항상 $P_i$가 진행되는 것은 아니고 다른 ready상태였던 프로그램이 진행될 수도 있다

## Context Switching
- Context: 프로세스와 관련한 정보들
  - cpu register context는 cpu register에 저장
  - code & data, stack, PCB는 memory에 저장
- Context saving: 현재 프로세스의 register context를 저장하는 작업
- Context restoring: Register context를 프로세스로 복구하는 작업

위의 두가지 과정을 Context switching (process switching) 이라고 한다.

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
