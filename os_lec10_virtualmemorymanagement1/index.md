# [OS] Virtual Memory Management (1)


virtual memory를 다루는 방법들을 알아보자

<!--more-->
이전에 virtual memory에 대해서 공부했었다. 그렇다면 이제 virtual memory를 관리하는 방법에 대해 알아보자. 왜 virtual memory를 잘 관리해야할까? 당연히 시스템 성능을 높이기 위해서다. vitual memory는 상당히 유용한 방법이지만 이를 잘 관리해야 유용한 방법이 된다. 따라서 다양한 최적화기법을 이번 챕터에서 공부하고자 한다.

먼저 관리를 잘하는지 평가하기 위해서는 cost model이 필요하다. virtual memory에서 주로 사용하는 cost model은 page fault에 관한 것이다.
- page fault frequency
- page fault rate ($F(w)$)
    - page reference string: 프로세스 수행 중 참조한 페이지 번호 순서
      - $w = r_1 r_2...r_T$, $r_i$: 페이지 번호
    - $F(w)=\frac{\text{num of page faults during } w}{\|w\|}$

cost model을 page fault rate로 정했다면 이를 최소화하기 위해 노력해야한다. 이제 어떤 방법들이 있는지 알아보자.

## Hardware component
Hardware적인 방법을 알아보자. 먼저 Address translation device가 있다. 이는 이전 Lec09에서 본 TLB같은 것들이다. 그 다음으로는 Bit Vectors에 대해 알아보자. Bit Vectors는 page 사용상황에 대한 정보를 기록하는 것이다. 두 가지 종류가 있다.
- Reference bits (used bits)
  - 메모리에 적대된 각각의 page가 최근에 참조 되었는지를 표시
  - 이를 통해 최근에 참조된 page들을 확인할 수 있다
  - 프로세스에 의해 참조되면 해당 page의 Ref.bit를 1로 설정하고 이를 계속 유지하는게 아니라 주기적으로 모든 Ref.bit를 0으로 초기화
- Update bits (write bits, dirty bits)
  - page가 메모리에 적대된 후, 프로세스에 의해 수정되었는지를 표시
  - 이를 통해 해당 page에 대한 write-back to swap device를 해야하는지 판단할 수 있다
  - 주기적으로 초기화하지 않고 메모리에서 나올때 0으로 초기화 (write-back하면서)

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec10_01.png?raw=true"  width="400">
</center>

## Software component
software적으로 virtual memory 성능 향상을 위한 관리 기법들에 대해 알아보자.
- Allocation strategy
- Fetch strategy
- Placement strategy
- Replacement strategy
- Cleaning strategy
- Load control strategy

### Allocation strategy
각 프로세스에게 메모리를 얼마만큼, 어떻게 줄 것인가?
- Fixed allocation (고정할당)
  - 프로세스의 실행동안 고정된 크기의 메모리 할당
- Variable allocation (가변할당)
  - 프로세스의 실행동안 할당하는 메모리의 크기가 유동적
  - 프로세스 실행에 필요한 메모리 양을 예측해야 함 (어려움)

너무 큰 메모리를 할당하면 메모리가 낭비되고 너무 적은 메모리를 할당하면 page fault rate가 올라가서 시스템성능이 떨어진다.

### Fetch strategy
특정 page를 메모리에 언제 적재할 것인가?
- Demand fetch (demand paging)
  - 프로세스가 참조하는 페이지들만 적재
  - page fault overhead
- Anticipatory fetch (pre-paging)
  - 참조될 가능이 높은 page 예측
  - 가까운 미래에 참조될 가능성이 높은 page를 미리 적재
  - 예측 성공 시, page fault overhead가 없음 (어려움, prediction overhead)

일반적으로 대부분의 시스템은 Demand fetch를 사용한다고 한다.

### Placement strategy
page/segment를 어디에 적재할 것인가?
- paging system에는 불필요
- sementation system에서는
  - first-fit, best-fit, worst-fit, next-fit

### Replacement strategy
새로운 page를 어떤 page와 교체할 것인가? (빈 page frame이 없는 경우) 이 부분은 뒤에서 좀 더 자세히 볼 예정이다.
- Fixed allocation을 위한 기법
  - MIN, Random, FIFO, LRU, LFU, NUR, Clock, Second chance algorithm
- Variable allocation을 위한 기법
  - VMIN, WE, PFF algorithm

### Cleaning strategy
변경된 page를 언제 write-back 할 것인가?
- Demand cleaning
  - 해당 page에 메모리에서 내려올 떄 write-back
- Anticipatory cleaning (pre-cleaning)
  - 더 이상 변경될 가능성이 없다고 판단 할 떄, 미리 write-back (어려움, prediction overhead)
  - page 교체 시 발생하는 write-back 시간 절약
  - 근데 write-back 이후, page 내용이 또 수정되면 overhead

여기서도 대부분의 시스템은 Demand cleaning 기법을 사용한다고 한다.

### Load control strategy
시스템의 multi-programming degree를 조절하는 방법이다. 적정 수준의 multi-programming degree를 유지해야 한다.
- if 저부하 상태
  - 시스템 자원 낭비 & 성능 저하
- if 고부하 상태
  - 자원에 대한 경쟁이 심해져서 성능 저하
  - Thrashing(스레싱) 현상 발생: 과도한 page fault발생하는 현상

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
