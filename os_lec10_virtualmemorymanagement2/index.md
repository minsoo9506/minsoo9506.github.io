# [OS] Virtual Memory Management (2)


virtual memory를 다루는 방법들을 알아보자

<!--more-->
새로운 page를 어떤 page와 교체할 것인가? (빈 page frame이 없는 경우) 에 대한 전략들을 알아보자.
- Fixed allocation을 위한 기법
  - MIN, Random, FIFO, LRU, LFU, NUR, Clock, Second chance algorithm
- Variable allocation을 위한 기법
  - VMIN, WS, PFF algorithm

## Fixed allocation
### MIN Algorithm
- 앞으로 가장 오랫동안 참조되지 않을 page를 교체하는 방법
- minimize page fault frequency (optimal solution) 할 수 있는 것이 증명되었다
- 하지만 page reference string (참조되는 순서) 을 미리 알고 있어야 한다
- 따라서 실현 불가능한 기법

### Random Alogorithm
- 무작위로 교체할 page 선택

### FIFO Algorithm
- page가 적재된지 가장 오래된 page를 교체
- 적재된 시간을 기억하고 있어야 한다
- locality에 대한 고려가 없어서 자주 사용되는 page도 교체될 수도 있다
- FIFO anomaly
  - 더 많은 page frame을 할당받았음에도 page fault의 수가 증가하는 경우가 있다

### LRU (Least Recently Used) Algorithm
- 가장 오랫동안 참조되지 않은 page를 교체
- page 참조할때 마다 시간을 기록해야 한다
- locality에 기반을 둔 방법으로 min algorithm에 근접한 성능을 보여줘서 실제로 가장 많이 활용된다고 한다
- 단점
  - 참조할때 마다 시간을 기록해야한다 (overhead), 다만 정확한 시간대신 순서를 기록하는 등의 방법을 이용할 수도 있다
  - Loop 실행에 필요한 크기보다 작은 수의 page frame이 할당된 경우, page fault 수가 급격히 증가한다
    - 예를 들어, loop 실행으로 page 1,2,3,4가 있고 할당된 page frame이 3개이면 계속 page fault가 발생
    - 이는 Allocation 기법에서 해결해야 한다

아래의 표에서 Time=6에서 이제 page5가 메모리에 적재되어야 한다. 따라서 LRU의 정책에 따라 가장 오랫동안 참조되지 않는 page2와 바꾸면서 page fault가 일어난다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec10_02.png?raw=true"  width="500">
</center>

### LFU (Least Frequently Used) Algorithm
- 가장 참조 횟수가 적은 page를 교체
- 따라서 page 참조할때 마다 참조 횟수를 누적시켜야함
- locality 활용하고 LRU 대비 적은 overhead (시간이 아니라 횟수누적만 하면 되니까)
- 단점
  - 최근 적재되어서 참조횟수는 적지만 곧 다시 참조될 가능성이 높은 page가 교체될 가능성이 있다
  - 참조누적횟수도 overhead가 없는 것은 아니다

### Clock Algorithm
- reference bit 를 사용한다, 다만 주기적인 초기화는 없다
- page frame들을 순차적으로 가리키는 pointer(시계바늘같은 것)를 사용하여 교체될 page를 결정한다
- pointer를 돌리면서 교체 page를 결정한다
  - 현재 가리키고 있는 page의 reference bit 를 확인한다
  - $r=0$인 경우, 교체 page로 결정
  - $r=1$인 경우, reference bit 초기화 후 pointer를 이동한다
- 위와 같은 방법으로 진행하면 먼저 적재된 page가 교체될 가능성이 높아진다 (FIFO처럼)
  
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec10_03.png?raw=true"  width="300">
</center>

아래 표에서 time=5가 되면 e가 들어와야하므로 pointer가 돌기 시작한다. 지금 모두 ref가 1이므로 0으로 초기화하면서 pointer가 이동한다. 한 바퀴 다 돌고 다시 frame0으로 왔더니 ref가 0이므로 a를 내리고 e를 적재한다. 그리고 ref를 1로 바꾼다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec10_04.png?raw=true"  width="500">
</center>

### Second Change Algorith
- Clock algorithm과 유사하나 update bit 도 함께 고려한다.
- (ref bit, update bit) 를 확인
  - (0,0): 교체 page로 결정
  - (0,1): (0,0)으로 바꿈 & write-back list 에 추가 후 이동
  - (1,0): (0,0)으로 바꾸고 이동
  - (1,1): (0,1)으로 바꾸고 이동

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec10_05.png?raw=true"  width="500">
</center>

## Variable allocation
variable allocation의 경우 page fault만으로 성능평가를 하는게 아니라 다른 지표도 함께 봐야한다. 예를 들어, 할당받는 page frame의 수가 달라지므로 할당받는 평균 page frame의 수를 추가적인 성능 지표로 생각할 수 있다. 동일한 page fault가 발생한다면 할당받는 page frame이 적을수록 더 좋은 알고리즘이라고 할 수 있을 것이다.
### Working Set Algorithm
- Working set
  - process가 특정 시점에 자주 참조하는 page들의 집합
  - 최근 일정시간 $\[ t - \Delta, t \]$ 동안  참조된 page들의 집합
  - Window size ($\Delta$)는 고정한다
  - 당연히 시간에 따라 변한다
- Working set을 항상 메모리에 유지한다
  - page fault rate 감소, 시스템 성능 향상
- locality를 고려하는 것
- working set이 달라지는 시점에 page frame의 할당수도 달라질 수 있다
- 적재되는 page가 없어도 메모리를 반납하는 page가 있을 수 있다
- 새로 적재되는 page가 있더라도 교체되는 page가 없을 수 있다
- 단점
  - working set management overhead
  - page fault가 없어도 set를 지속적으로 관리해야한다

time=1 일 때, set은 {4,3,0,2}이고 다음 time이 되면 적재될 page가 없어도 set가 {3,0,2}이기 때문에 4가 메모리에서 빠진다. 그래서 할당된 page frame의 수도 3으로 줄어든다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec10_06.png?raw=true"  width="500">
</center>

### PFF (Page Fault Frequency) Algorithm
- Residence set size를 page fault rate에 따라 결정한다
  - Low page fault rate: process에게 할당된 page frame 수를 감소
  - High page fault rate: process에게 할당된 page frame 수를 증가
  - 이는 지난 page fault와 현재 page fault 사이의 시간이 특정 값보다 큰지 작은지로 판단
- Resident set 갱신 및 메모리 할당은 WS알고리즘과 다르게 page fault가 발생할때만 수행한다

### Variable MIN Algorithm
- Variable allocation 기반 교체 기법 중 optimal algorithm
  - 평균 메모리 할당량과 page fault 발생 횟수 모두 고려
- page reference string을 미리 알고 있어야해서 실현 불가능한 기법
- 과정
  - page $i$가 $t$시간에 참조되면 $(t,t+\Delta \]$ 사이에 다시 참조되는지 확인
  - 참조 된다면, page $i$를 유지
  - 참조 안된다면, page $i$를 메모리에서 내림

## Other Consideration
### Page size
아래 표와 같은 특징이 있다.

|Small page size|Laarge page size|
|---|---|
|internal fragement 감소|internal fragement 증가|
|IO 시간 증가|IO 시간 감소|
|Locality 향상|Locality 저하|
|page fault 증가|page fault 감소|

그런데 요즘은 CPU가 발전되고 memory size도 많이 커졌다. 따라서 IO의 시간(disk에서 memory또는cpu 사이의 IO)이 감소할수록 좋고 상대적으로 page fault가 성능을 좌지우지하는 경향이 강해져서 page size가 커지는 경향이 있다.

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
