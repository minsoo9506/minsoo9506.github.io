# [OS] Memory Management


Memory Management에 대해 알아보자.

<!--more-->
## Background
### 메모리 계층구조
- 레지스터
- 캐시
- 메인 메모리
- 보조기억장치

레지스터쪽으로 갈수록 용량이 작고 비싸다. 레지스터와 캐시는 HW(CPU)가 관리하고 나머지 2개는 SW(OS)가 관리한다.

- block
  - 보조기억장치와 주기억장치 사이의 데이터 전송 단위
  - 보조기억장치에서 메인메모리로 1bit에 해당하는 데이터만 올려도 block단위(1~4kb)로 올라간다
- word
  - 메인메모리와 레지스터 사이의 데이터 전송 단위 (16~64bit)

### Address Binding
- 프로그램의 논리 주소를 실제 메모리의 물리 주소로 매핑하는 과정
- binding 시점에 따라 구분
  - complie time binding
  - load time binding
  - run time binding
    - 대부분의 OS가 사용하는 방법
    - Address binding을 수행시간(running state)까지 연기
    - HW(Memory Management Unit)의 도움이 필요

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec08_01.png?raw=true"  width="500">
</center>

## Memory allocation
### Continuous memory allocation
프로세스를 하나의 **연속된** 메모리 공간에 할당하는 정책이다. 이는 크게 두가지로 나눈다.
- Uni-programming
  - 하나의 프로세스만 메모리 상에 존재
- Multi-programming

#### Multi-programming
두 가지로 나눠서 생각할 수 있다.
- Fixed(Static) partition multi-programming
  - 메모리 공간을 고정된 크기로 미리 분할, 메모리 관리는 편함
  - 각 프로세스는 하나의 partition에 적재
  - start address, size를 통해서 partition 관리
  - 프로세스가 kernel이나 다른 partition을 침범하지 않도록 Boundary address를 통해 방지한다
  - 문제: Fragmentation (단편화)
    - Internal fragmentation: Partition 크기 $>$ Process 크기 
    - External fragmentation: (남은 메모리 크기 $>$ Process 크기)이지만 연속된 공간이 아니라 사용불가
    - 메모리가 낭비된다
- Variable(dynamic) partition multi-programming


### Non-continuous memory allocation

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
