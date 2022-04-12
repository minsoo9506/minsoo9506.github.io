# [OS] File System


File System에 대해 알아보자

<!--more-->
## Disk System
### Disk Pack
먼저 Disk Pack에 대해 알아보자. Disk Pack은 데이터를 영구적으로 저장할 수 있는 비휘발성 장치를 의미한다. 그렇다면 Disk Pack의 구성요소에 대해 알아보자.
- Platter
  - 양면에 자성물질을 입힌 원형 금속판
  - 데이터의 기록/판독이 가능한 기록매체
- Sector
  - 데이터 기록/핀독의 물리적 단위
- Track
  - Platter 한 면에서 중심으로 같은 거리에 있는 sector들의 집합
- Cylinder
  - 같은 반지름을 갖는 track들의 집합
  - 여러 개의 platter를 관통하는 집합
- Surface
  - platter의 윗면과 아랫면

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec11_01.png?raw=true"  width="300">
</center>

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec11_02.png?raw=true"  width="300">
</center>

### Disk drive

Disk drive는 Disk Pack에 데이터를 기록/판독할 수 있도록 구정된 장치이다. 구성요소를 알아보자.
- Head
  - 디스크 표면에 데이터를 기록/판독
- Arm
  - Head를 고정/지탱
- Positioner (boom)
  - Arm을 지탱
  - Head를 원하는 track으로 이동
- Spindle
  - Disk pack을 고정 (회전축)
  - RPM (Revolutions Per Minute): 분당 회전 수

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec11_03.png?raw=true"  width="300">
</center>

### Disk address
Disk Pack에 있는 데이터를 기록/판독하기 위해서는 Disk address를 알아야한다. 먼저 Physical disk address는 cylinder number, surface number, sector number 이 3가지를 통해 해당하는 sector로 이동할 수 있다. 하지만 OS는 Logical disk address만 알고 사용한다. Disk system 데이터 전체를 block들의 나열로 취급한다. 따라서 os는 block number만 다루고 이를 disk driver가 physical address로 변환하여 disk에 접근하게 된다.

Disk system에서 Data에 Access하는 시간은 아래와 같이 크게 3가지로 구분한다. (실행순서도)
1. Seek time
    - 디스크 head를 필요한 cylinder로 이동하는 시간
2. Rotational delay
    - 필요한 sector가 head 위치로 도착하는 시간
3. Data transmission time
    - 해당 sector를 읽어서 기록/전송하는 시간

## File System
- File system의 구성
  - File: 보조 기억 장치에 저장된 연관된 정보들의 집합
  - Directory structure: 시스템 내 파일들의 정보를 구성 및 제공
  - Partitions: Directory들의 집합을 논리적/물리적으로 구분

## Directory Structure
### Flat Directory Structure
- File system에 directory가 하나만 존재하는 경우이다. 옛날 mp3 생각하면 될 것 같다.
- 문제
  - file naming
  - file protection
  - file management
  - 다중 사용자 환경에서 문제가 더욱 커진다

### 2-Level Directory Structure
- 사용자마다 하나의 directory 배정
- 문제
  - flat과 거의 유사

### Hierarchinal Directory Structure
- tree 형태의 계층적 directory 사용 가능
- 사용자가 하부 directory 생성/관리 가능
- 대부분의 OS가 사용

### Acyclic Graph Directory Structure
- Hierachinal directory structure의 확장
- directory안에 shared directory, shared file를 담을 수 있다. 이는 link의 개념을 사용했다고 할 수 있다. 

### General Graph Directory Structure
- cycle을 허용
- 하지만 infinite loop 가 생성되지 않도록 조심해야 한다

## File Protection
File에 대한 부적절한 접근을 방지하는 것은 특히 다중 사용자 시스템에서 더 필요하다. 접근 제어가 필요한 연산들은 주로 read, write, execute, append이다. 그렇다면 파일을 보호하는 방법들은 어떤게 있을까?

- File Protection mechanism
  - password 기법
    - 모든 file과 접근제어연산 마다 pw를 부여하기 현실적으로 어렵다
    - 따라서 몇 가지 중요한 file에만 사용
  - access matrix 기법

### Access matrix
범위(domain)와 개체(object) 사이의 접근 권한을 명시한 것이다.
- object: 접근 대상 (file , device...)
- domain: 접근 권한의 집합
- access right: <object-name, right>

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/os_Lec11_04.png?raw=true"  width="500">
</center>

그렇다면 이런 access matrix를 실제로 어떻게 구현하는지에 대해 알아보자.
- Access List
  - Access matrix의 열을 list로 표현
  - object 생성 시, 각 domain에 대한 권한 부여
  - object 접근 시, 권한을 검사
  - 실제 os에서 많이 사용됨 `ls- l` 명령어 치면 나오는 것처럼
- Capability List
  - Access matrix의 행을 list로 표현
  - 각 domain별로 obejct와 관련 접근권한을 나열
  - 프로세스가 권한을 제시하고 시스템이 검증을 승인

Access list의 경우 object별 권한 관리가 용이하다. 하지만 모든 접근마다 권한을 검사해야하는 하기 때문에 느려질 수도 있다. 반면에 Capability list는 list내 object들에 대한 접근에 유리하지만 object별 권한관리가 어렵다. 그래서 많은 OS에서 이를 모두 이용한다. 
1. object에 대한 첫 접근 $\rightarrow$ access list 탐색
2. 접근이 허용된다면 capability를 생성하여 프로세스에게 전달
    - 따라서 이후 접근시에는 권한검사가 필요없이 프로세스가 capability를 제시하고 접근
3. 마지막 접근 후에 capability를 삭제

## File system implementation
이번에는 file 저장을 위한 디스크 공간 할당 방법을 알아보자.
- Allocation
  - Continuous
  - Discontinuous (linked, indexed)

### Continuous Allocation
- 한 file을 디스크의 연속된 block에 저장
- 장점
  - 효율적인 file 접근
- 단점
  - 새로운 file을 위한 공간 확보가 어려움
  - external fragmentation
  - file 공간 크기 결정이 어려움, 나중에 파일이 커지게 된다면?

### Linked Allocation
- file이 저장된 block들을 linked list로 연결
- directory는 각 file에 대한 첫번째 block에 대한 포인터를 가짐
- 장점
  - 순처적 접근에는 효율적
  - no external fragmentation
- 단점
  - 직접 접근에 비효율적
  - 포인터 저장을 위한 공간이 필요, 포인터를 사용자가 건드릴수도 있음

### Indexed Allocation
- file이 저장된 block의 정보(pointer)를 index block에 모아둔다
- 장점
  - 직접 접근에는 효율적
- 단점
  - 순차적 접근에는 비효율적
  - file당 index block을 유지해야 되서 space overhead가 발생할 수 있다
  
## Free space management
빈 공간을 다루는 방법을 알아보자.
- Bit vector
- Linked list
- Grouping
- Counting

### Bit vector
- 시스템 내 모든 block들에 대한 사용여부를 1 bit flag로 표시
- bit vector 전체를 메모리에 보관해야 한다
### Linked list
- 빈 block을 linked list로 연결
- 비효율적 (공간, 직접접근)
### Grouping
- n개의 빈 block을 그룹으로 묶고, 그룹 단위로 linked list로 연결
- 연속된 빈 block을 쉽게 찾을 수 있음
### Counting
- 연속된 빈 block들 중에서 첫번째 block의 주소와 연속된 block의 수를 table로 유지
- continuous allocation 시스템에 유리한 기법
- 예시: (0번위치, 6개), (13번위치,10개)

## Reference
- [HPC Lab. KOREATECH 운영체제 강의](https://www.youtube.com/playlist?list=PLBrGAFAIyf5rby7QylRc6JxU5lzQ9c4tN)
