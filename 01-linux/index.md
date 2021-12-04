# [Linux] 리눅스 명령어


생활코딩 리눅스 명령어 정리

<!--more-->

### file & directory
- `pwd` : 현재 경로 확인
- `ls` : 현재 위치에 있는 파일, 디렉토리들 확인
  - `ls -l` : 파일, 디렉토리들 정보도 같이 확인 가능
  - `ls -a` : 감춰진 것들도 확인 가능
  - `ls -al` : 위 두개를 동시에 확인
- `mkdir 디렉토리이름` : 디렉토리 만들기
- `cd 특정위치` : 특정위치로 이동 (절대경로, 상대경로 모두 사용가능)
- `rm` : 파일 삭제
  - `rm -r 디렉토리` : directory를 `rm`으로 삭제가 안되고 이 명령어 사용
  - `rmdir` : directory를 삭제
- `touch` : 파일 만들때 쓰는듯

### help & man
- `명령어 --help` : 해당 명령어에 대한 설명이 나옴
- `man 명령어` : 더 자세히 설명이 나오는듯  

### 파일이동, 복사
- `cp 복사당할파일위치 복사할파일위치` : 파일복사
  - 예시) `cp /home/a.txt /home/move/b.txt`
- `mv 이동당할파일위치 이동할파일위치` : 파일이동
  - 파일이름 바꾸때도 `mv` 쓰면 되겠구나

### sudo
- `sudo` : super user do
- 각각의 사용자마다 권한을 다르게 지정되어있는데 일반적인 사용자의 경우 `sudo`를 통해 강한 권한

### package manager
- apt라는 package manager를 사용해보자
- `apt-get update` : 이 명령어를 통해 최신상태 소프트웨어 목록을 다운받는다
- `apt-cache search 원하는소프트웨어` : 위에서 다운받은 목록에서 원하는 소프트웨어와 관련한 내용을 보여준다
- `apt-get install 원하는소프트웨어` : 해당 소프트웨어 다운
- `apt-get upgrade 원하는소프트웨어` : 해당 소프트웨어 업그레이드
  - `apt-get upgrade` 그냥 이렇게 하면 전부 다 업그레이드
- `apt-get remove 원하는소프트웨어` : 해당 소프트웨어 삭제
  
### file download : wget
- `wget 다운받을주소` : 파일다운로드
- `wget -O 저장하고싶은이름 다운받을주소` : 원하는 이름으로 파일이 저장된다

### why CLI?
- GUI보다 메모리도 덜 먹고 편함
- 연속적으로 명령이 가능!
  - 예를 들어 `mkdir minsoo; cd minsoo`
  - `;` 세미콜론은 그냥 편의상 명령어를 구분하는 역할
  - 근데 주의! 앞의 명령어가 오류가 나도 뒤의 명령어가 진행된다

### pipeline
- `cat sample.txt` : sample.txt 파일을 보여준다
- `grep 특정단어 특정파일` : 특정파일에 특정단어가 들어가있는 행을 보여준다.
- pipeline 예시 (`|` 사용)
  - `ls --help | grep sort` 

### IO Redirection
- `>`
  - `ls -l > result.txt` : 앞의 내용(standard output)을 result.txt로 redirection해서 파일을 만든다.
  - 근데 이때 `>`는 `1>`을 의미한다. 이는 standard output을 redirection할때 사용하는 것이다.
  - 근데 `rm result.txt`이 에러가 나는 명령어라고 가정하면 이는 standard error이고 이를 rediection하기 위해서는 `rm result.txt 2> error.txt` 해야한다.
- `cat` 
  - `cat result.txt` 와 `cat < result.txt`는 똑같은 결과는 낸다. 다른점은?
  - 전자의 경우는 result.txt를 Command-line Argument로 받은 것이고 후자는 Standard input으로 받은 것!
- `>>`
  - 기존에 result.txt 파일이 있다고 하면 예를 들어, `ls -al >> result.txt` 하면 `ls -al`의 결과가 result.txt에 append된다.

### Directory structure
- `/bin` : user binaries, 우리가 사용하는 명령어들
- `/sbin` : system binaries
- `/etc` : configuration files, 설정
- `/var` : variable files
- `/home` : home directories, user들의 file
- 기타등등

### file find : locate, find, whereis
- `locate 원하는파일` : 원하는 파일 찾을 수 있다
- `find 원하는파일` : 원하는 파일 찾을 수 있다
  - 더 다양한 사용법이 존재
- `whereis` : 주로 명령어, binary 파일 위치 찾을 때 쓰는듯
- `which` : 사용하는 명령어의 위치 알려줌

### background execute
- 어떤 프로그램을 사용하다가 `Ctrl+Z`를 누르면 해당 프로그램이 background로 간다
- 그리고 `jobs` 라는 명령어를 치면 background에 돌아가는 프로그램을 보여준다
- 원하는 background에 있는 프로그램에 돌아가려면 `fg %해당프로그림의번호` 명령어를 이용하면 된다
- `&`
  - 시간이 오래걸리는 프로그램이 있으면 `~명령어~ &`를 통해서 바로 background로 보내고 나는 다른 일을 하면 된다

### daemon
- `ls, rm ...` 과 같은 프로그램은 필요할 때만 사용
- 그렇다면 항상 켜져있도록하는 프로그램도 있을 것!
  - ex) server
- 이를 daemon이라고 한다
- `/etc/init.d` : 여기에 daemon 파일들이 있다
  - `service 프로그램 start`, `service 프로그램 stop`
- 컴퓨터 시작할 때 자동으로 실행되도록 하고 싶으면 `/etc/rc3.d`에 link를 만들면 된다

### CRON
- CRON : 정기적으로 명령을 실행시켜주는 도구
- 예를 들어,
  - 사용자가 server로 메일을 보내면 바로 완료되었다고 뜨지만 server에서는 해당내용을 체크만 하고 cron을 통해서 체크된 내용을 정기적으로 진행한다
- `crontab`
  - `crontab -e` 명령어를 치면 에디터가 열리고 거기에 명령어를 만들어주면 된다

### startup scipt
- shell을 시작할 때, 특정 명령어 실행되도록 하는 것
- 에디터로 `.bashrc` 파일을 열어보면 이미 만들어져있다
  - 여기에 원하는 명령어를 추가하면 된다
  - 예를 들어, `alias l='ls -al'` 이렇게 별명을 붙여주기가 가능하는데 이런거는 미리 만들어두면 편하게 이용할 수 있을 것이다

### Mult user
- `id`
  - `id` 명령어를 치면 지금 user에 대한 정보 보여준다
- `who`
  - `who` 명령어를 치면 누가 접속했는지 보여준다

### Root user
- 리눅스에는 크게 두 가지 user
  - super(root) user
  - user
- `sudo` 명령어로 user가 root user처럼 명령할 수 있다
  - 하지만 아무나 사용은 못하고 이를 사용할 수 있도록 명령어를 추가로 해야한다
- `su 사용자이름` 명령어를 통해 user를 바꿀 수 있다

### Add user
- `useradd -m 이름` : 해당 이름 user가 만들어진다
- `passwd 이름` : 해당 이름에서 사용한 비밀번호 지정

### Permission
- file, directory에 대해 user의 권한을 지정
  - read, write, excute 에 대한 권한을 지정
- `-rw-r--r-- 1 root root 485 Jul  2 12:38 result.txt` : `ls -l result.txt` 라는 명령어를 친 결과
  - `-rw-r--r--` 에서 맨 앞은 type(file or directory), 뒷부분은 access mode를 의미
  - `rw-r--r--` : 여기에서 `rw-`는 owner의 권한, `r--`는 그룹의 권한, `r--`는 나머지들의 권한
  - `root root` 에서 앞부분은 owner, 뒤부분은 group을 의미
- directory에서 read를 안에있는 파일을 읽을수있는지, write는 안에있는 파일을 수정할수있는지, execute는 해당 directory에 들어갈수있는지
- `chmod`
  - `r` : read, `w` : write, `x` : excute
  - 위에서 `chomod o-r result.txt`하면 나머지들은 read도 못한다 추가는 `chomod o+r result.txt` 이런식으로 가능
    - `u`, `g`, `o`, `a` 에 `+,-,=` 연산자와 mode 이용
  - Octal modes도 있다 예를들어 `chomod 111 result.txt` 처럼 owner, group, other 모두 한번에 `1`(excute only)이 되도록 한다

### Reference
- (생활코드)[https://www.youtube.com/playlist?list=PLuHgQVnccGMBT57a9dvEtd6OuWpugF9SH]
