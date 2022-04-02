# [OS] Virtual Memory


사용자 프로그램을 메모리에 연속하지 않게 할당하는 과정에 대해 알아보자.

<!--more-->
memory allocation을 하는 방법중에서 non-continuos memory allocation 방법을 알아보자.

## Virtual memory
사용자 프로그램을 여러 개의 block으로 분할하는 방법이다. 그리고 해당 프로그램 실행 시, 필요한 block들만 메모리에 적재한다. 나머지 block들은 swap device에 있다. 이러한 기법들 3가지 알아보자.
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

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec08_01.png?raw=true"  width="500">
</center>

## Virtual storage methods
가상메모리를 이용하는 방법들에 대해 알아보자.

### Paging system
Paging system은 프로그램을 같은 크기의 block으로 분할하는 방법이다. 여기서 분할된 프로그램 block을 *Page*라고 한다. 이 방법에서는 메모리도 Page와 동일한 크기로 분할하는데 이 분할영역을 *Page Frame*이라고 한다. 실제 window에서 성능옵션 -> 고급 에 가보면 페이징에 관련한 내용을 확인할 수 있다.
- 특징
  - 논리적 분할이 아니고 크기에 따른 분할 방법이다
  - simple
  - external fragmentation이 발생하지 않는다 (internal은 발생 가능: 분할을 하다보면 남는 경우가 존재)

Address mapping 할 차례임 https://www.youtube.com/watch?v=mTFYeZwPj0s&list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN&index=28
9분

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
