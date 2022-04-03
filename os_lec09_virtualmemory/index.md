# [OS] Virtual Memory


사용자 프로그램을 메모리에 연속하지 않게 할당하는 과정에 대해 알아보자.

<!--more-->
memory allocation을 하는 방법중에서 non-continuos memory allocation 방법을 알아보자.

## Virtual memory
사용자 프로그램을 여러 개의 block으로 분할하는 방법이다. 그리고 해당 프로그램 실행 시, **필요한 block들만 메모리에 적재**한다. **나머지 block들은 swap device**에 있다. 이러한 기법들 3가지 알아보자.
- paging system
- segmentation system
- hybrid system

먼저 non-continuous allocation의 경우 address mapping에 대해 알아보자. 이전에 continous의 경우와 비슷하게 가상주소와 실제주소가 있다.
- virtual address (=relative address)
  - logical address
  - 연속된 메모리 할당을 가정한 주소
- real address (=absolute, physical address)
  - 실제 메모리에 적재된 주소

사용자/프로세스는 address mapping을 통해 실행 프로그램 전체가 메모리에 연속적으로 적재되었다고 가정하고 실행할 수 있다. 그렇다면 구체적으로 어떤식으로 address mapping을 해서 프로그램이 실행되는지 알아보자. 간단한 방법중 하나인 block mapping에 대해 알아보자.

### Block mapping
- 사용자 프로그램을 block 단위로 분할/관리
- virtual address: $v=(b,d)$
  - $b$: 사용자 프로그램의 block number
  - $d$: offset in a memory block
- block map table (BMT) 를 통해서 address mapping 정보를 관리
  - 아래 그림에서 residence bit는 해당 block이 memory에 올라갔는지 여부를 체크
  - 그래서 block number를 확인하고 table에서 residence bit=1이면 real address로 가서 d를 더하면 main memory에 올라간 위치에 도달할 수 있는 것이다

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_01.png?raw=true"  width="500">
</center>

이제 가상메모리를 이용하는 방법들에 대해 알아보자.

## Paging system
Paging system은 프로그램을 같은 크기의 block으로 분할하는 방법이다. 여기서 분할된 프로그램 block을 *Page*라고 한다. 이 방법에서는 메모리도 Page와 동일한 크기로 분할하는데 이 분할영역을 *Page Frame*이라고 한다. 실제 window에서 성능옵션 -> 고급 에 가보면 페이징에 관련한 내용을 확인할 수 있다.
- 특징
  - 논리적 분할이 아니고 크기에 따른 분할 방법이다
  - simple
  - external fragmentation이 발생하지 않는다 (internal은 발생 가능: 분할을 하다보면 남는 경우가 존재)

그렇다면 이제 Paging system이 Address mapping을 하는 방법을 알아보자. virtual address는 $v=(p,d)$이고 각각 page number, displacement이다. Paging system에서는 PMT(page map table)을 통해 mapping을 한다. mapping하는 방법으로는 3가지가 있다.
- Direct mapping
- Associative mapping
- Hybrid direct/associative mapping

먼제 PMT는 아래와 같은 모습이다. 아래에서 secondary storage address는 swap device에서 page들의 위치를 의미한다.
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_02.png?raw=true"  width="500">
</center>

### Direct mapping
- 순서
  - 1. 해당 프로세스의 PMT가 저장되어 있는 주소 b에 접근
  - 2. 해당 PMT에서 page p에 대한 entry 찾음
  - 3. 찾은 entry의 residence bit 검사
    - 1. if 0, swap device에서 해당 page를 메모리에 적재하고 PMT를 갱신하고 3-2 수행 (이부분에서 overhead)
    - 2. if 1, 해당 entry에서 page frame 번호 p'를 확인
  - 4. p'와 d를 사용하여 실제 주소 r 형성
  - 5. 실제 주소 r로 주기억장치에 접근

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_03.png?raw=true"  width="500">
</center>

### Associative mapping

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_04.png?raw=true"  width="500">
</center>

### Hybrid direct/associative mapping
Hybrid direct/associative mapping은 direct와 associative 두 가지 방법을 모두 이용하는 방법이다.
- PMT: 메모리에 저장
  - direct mapping에서의 단점: 메모리 접근 횟수가 많아서(overhead) 성능저하, PMT를 위한 메모리 공간 필요
- TLB: PMT 중 일부 entry들(최근에 사용된 page들에 대한 entry)을 적재, 이는 locality를 활용하기 위함
  - Translation Look-aside Buffer
  - associative mapping에서 사용
  - PMT를 위한 전용기억장치(공간)로 swap device에서 page를 찾을 때, parallel search가 가능해서 엄청 빠르지만 비싸고 운영이 까다롭다

프로세스의 PMT가 TLB에 적재되어 있는지 확인한다.
- if TLB에 적재되어 있는 경우, residence bit를 검사하고 page frame 번호를 확인
- if TLB에 적재되어 있지 않은 경우, direct mapping으로 page frame 번호를 확인하고 해당 PMT entry를 TLB에 적재

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_05.png?raw=true"  width="500">
</center>

그렇다면 이제 memory management에 대해 알아보자. memory도 page와 같은 크기로 미리 분할(=page frame)하여 관리/사용한다. memory management를 하기 위해서 Frame table을 이용한다.
- Frame table

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_06.png?raw=true"  width="500">
</center>

또한 효율성을 위해 page sharing이라는 것도 할 수 있다. 여러 프로세스가 main memory에 있는 page frame을 공유하는 것이다. 물론 이 때, 문제가 발생하지 않도록 유의(page protection)해야한다.

## Segementation system
프로그램을 논리적 block으로 분할하는 방법이다. 따라서 block의 크기가 다 다를 수 있다.
- 특징
  - 메모리를 미리 분할하지 않음
  - segment sharing/protection이 용이함
  - address mapping 및 메모리 관리의 overhead가 큼
  - no internal fragmentation, external fragmentation은 발생가능

segmentation system에서도 address mapping은 비슷하다. virtual address v=(s,d)가 있고 SMT(segment map table)를 이용한다. mapping하는 방법은 paging system과 비슷하다.

- segment map table
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_07.png?raw=true"  width="500">
</center>

### Hybrid paging/segmentation system
- 프로그램 분할
  - 1. 논리 단위의 segment로 분할
  - 2. 각 segment를 고정된 크기의 page들로 분할
- page 단위로 메모리에 적재

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec09_08.png?raw=true"  width="500">
</center>

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
