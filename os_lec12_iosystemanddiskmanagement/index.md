# [OS] I/O system and Disk Management


입출력 시스템과 디스크 관리에 대해 알아보자.

<!--more-->
## I/O Mechanism
I/O 에서는 크게 두 가지 방식이 있다.
- Processor controlled memory access
  - pooling
  - interrupt
- direct memory access

### Pooling
- processor가 주기적으로 I/O 장치들을 순환하면서 상태를 확인
- 장점
  - simple
  - I/O 장치가 빠르고 데이터 전송이 잦은 경우 효율적
- 단점
  - processor의 부담이 큼 (특히 I/O device가 느린 경우)

### Interrupt
- I/O 장치가 작업을 완료한 후, 자신의 상태를 processor에게 전달
- interrupt 발생 시, processor는 데이터 전송 수행
- 장점
  - pooling 대비 low overhead
  - 불규칙적인 요청 처리에 적합
- 단점
  - interrupt handling overhead

Processor controlled memory access 방법은 processor가 모든 데이터 전송을 처리해야하는 단점이 존재한다. 전송하는 모든 과정에 참여해야 하기에 overhead가 있다.

### Direct memory access (DMA)
- processor는 I/O 장치와 memory 사이의 데이터 전송을 시작/종료에만 관여
- processor가 명령 & 관련정보 들을 DMA 제어기에 보내고 DMA가 I/O 작업을 진행하고 끝나면 processor에게 알린다

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec12_01.png?raw=true"  width="400">
</center>

## I/O services of OS
I/O performance를 올리기 위해 OS가 제공하는 방법들을 알아보자.

### I/O scheduling
- 입출력 요청에 대한 처리 순서 결정

### Error handling
- 입출력 중 발생하는 오류 처리

### Buffering
- I/O 장치와 program 사이에 전송되는 데이터를 buffer에 임시저장
- 전송속도(or 처리단위) 차이 문제를 해결

### Caching
- 자주 사용하는 데이터를 미리 복사해 둠
- cache hit 시 I/O를 생략 할 수 있음

### Spooling
- 한 I/O 장치에 여러 program이 요청을 보낼 시, 출력이 섞이지 않도록 하는 기법
  - 각 program에 대응하는 disk file에 기록 (spooling)
- 예를 들어, 프린트에 여러 개의 요청이 쌓이면 섞이지 않도록 해야함

## Disk Scheduling
Disk access 요청들의 처리 순서를 결정하고 disk system의 성능을 향상시키기 위해서 스케쥴링을 잘해야 한다. 이 과정에 있어서 고려해야하는 주요 기준은 Throughput (단위 시간당 처리량), mean response time (평균 응답 시간), predictability (응답 시간의 예측성, 요청이 무기한 연기되지 않도록 방지) 이라고 할 수 있다. 아래는 방법론들이다.
- Optimizing seek time (head 이동)
  - FCFS
  - SSTF
  - Scan scheduling
  - C-Scan scheduling
  - Look scheduling
- Optimizing rotational delay (돌아서 필요한 sector가 head로)
  - SLTF
- SPTF (Shortest Positioning Time First)

### First Come First Service (FCFS)
- 요청이 도착한 순서에 따라 처리
- simple, 공평한 처리
- 최적 성능 달성에 대한 고려가 없음
- disk access 부하가 적은 경우에 적합

### Shortest Seek Time First (SSTF)
- 현재 head 위치에서 가장 까까운 요청 먼저 처리
- throughput은 mean response time은 낮은 장점
- 하지만 predictability가 낮고 starvation 현상이 발생가능
- 따라서 일괄처리 시스템에 적합하다

### Scan scheduling
- 현재 head의 진행방향에서 head와 가장 가까운 요청 먼저 처리
- 진행방향 기준 마지막 cylinder 도착 후, 반대 방향으로 진행
- SSTF의 starcation 문제 해결
- Throughput 및 mean response time 우수
- 진행 방향 반대쪽 끝의 요청들의 응답시간이 길어진다

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec12_02.png?raw=true"  width="400">
</center>

### C-Scan scheduling
- scan과 유사하지만 head가 미리 정해진 방향으로만 이동
- 즉, 진행방향으로 가다가 끝 cylinder에 도착하면 그 반대 cylinder로 이동하여 같은 진행방향으로 진행
- scan에 비해 균등한 기회제공

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec12_03.png?raw=true"  width="400">
</center>

### Look scheduling
- scan(C-scan)에서 현재 진행 방향에 요청이 없으면 방향 전환

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec12_04.png?raw=true"  width="400">
</center>

### Shortest latency time first (SLTF)
- fixed head dist 시스템에 사용
  - 각 track마다 head가 있는 disk, head의 이동이 없음
- sector queuing algorithm
  - head 아래 도착한 sector의 queue에 있는 요청을 먼저 처리함
- moving head disk의 경우는 같은 cylinder에 도착하면 queue에 있는 여러 개의 요청을 모두 처리

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec12_05.png?raw=true"  width="400">
</center>

### Shortest Positioning Time First (SPTF)
- positioning time = seek time + rotational delay
- positioning time이 가장 작은 요청 먼저 처리
- throughput 높고, mean response time은 낮다는 장점
- 가장 안쪽과 바깥쪽 cylinder의 요청에 대해 starvation 발생 가능

## RAID Architecture
- RAID (Redundant Array of Inexpensive Disks)
  - 여러 개의 물리 disk를 하나의 논리 disk로 사용
  - disk system의 성능 향상을 위해 사용
 
### RAID 0
- disk striping
  - block을 일정한 크기로 나누어 각 disk에 나누어 저장
- 모든 disk에 입출력 부하 균등 분배, parallel access, performance 향상
- 한 disk에서 장애시 데이터 손실 발생 (low reliability)

### RAID 1
- Disk mirroring
  - 동일한 데이터를 mirroring disk에 중복 저장
- 최소 2개의 disk로 구성되어야 함 (입출력은 둘 중 어느 disk에서 가능)
- 한 disk에서 장애가 생겨도 데이터를 중복 저장해서 괜찮다 (high reliability)
- 가용 disk 용량 = (전체 disk 용량 / 2)

### RAID 3
- RAID 0 + parity disk
  - byte 단위 분할 저장
  - 모든 disk에 입출력 부하 균등 분배
  - parity라는 disk를 통해 나머지는 관리하는 느낌 (hadoop master node처럼)
- 한 disk에 장애 발생 시, parity 정보를 이용하여 복구
- write 시 parity 계산 필요

### RAID 4
- RAID 3와 유사, 단 block 단위로 분산저장

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
