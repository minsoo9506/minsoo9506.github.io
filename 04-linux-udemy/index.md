# [Linux] 리눅스 명령어 정리


리눅스 명령어 정리

<!--more-->

### 명령어 기초

- `date`
  - 현재 시간 보여줌
- `ncal`
  - 달력출력
- `command -options arguments`
  - arguments (parameter): `echo minsoo`에서 `minsoo`같이 명령어에 넣어주는 값을 의미
  - options: 명령어의 옵션을 의미하고 dash(`-`)로 prefixed 되어 있다. 대소문자도 유의해야한다.
  - option을 여러개 한번에 사용할 수 있다. `ncal -3h`처럼 묶어서도 사용할 수 있다.
  - option을 풀네임으로 쓰려면 dash를 두 번 써야한다. `--options`
  - option을 사용할 때, argument가 필요한 경우가 있다. 이때는 option 뒤에 작성하면된다. `ncal -A 1`와 같이 할 수도 있고 공백없이 `ncal -A1`처럼 작성할 수도 있다.

### 도움말 확인

- `man 명령어`를 통해서 명령어에 대한 설명을 확인할 수 있다. unix계열이 제공하는 메뉴얼이라고 생각할 수 있다.

### `type`, `which`, `help`

- 명령어에는 4가지의 종류가 있다.
  - executable program (compiled binary files usually stored in `/bin`, `/usr/bin` ...)
  - built-in shell command
  - shell function
  - alias
- `type 명령어`
  - 해당 명령어의 타입을 알 수 있다.
  - `type clear`라고 치면 `clear is hashed (/usr/bin/clear)`라고 나온다.
- `which 명령어`
  - 해당 명령어의 위치를 알 수 있다.
- `help`
  - `man cd`를 치면 메뉴얼이 없다고 나온다. built-in 명령어들이 그렇다. 그럴 때는 `help`를 이용하면 된다. `help 명령어`를 통해서 도움을 받자.

### 파일 시스템 탐색

- 리눅스에는 root directory가 있다. 모든 파일, 디렉토리가 들어있는 가장 최상단 디렉토리이고 경로(이름)는 `/`이다.
  - 이 안에 이름이 `root`인 디렉토리가 있는데 이를 의미한 것이 아니다.
- `home` 디렉토리에는 user들과 관련한 디렉토리들이 있다. `~`은 `home`을 나타내는 경로이다.
- `pwd`
  - 현재 working directory (경로) 를 보여준다.
- `ls`
  - 디렉토리에 있는 파일, 디렉토리들을 보여준다.
  - `ls 경로`를 통해서 특정 경로에 있는 것들도 볼 수 있다.
  - `-l` 옵션은 파일, 디렉토리에 대해 더 긴 설명을 보여준다. (권한, 용량 등등)
  - `-a` 옵션은 `.`으로 시작하는 파일까지도 보여준다.
- `cd 경로`
  - 해당 경로(디렉토리)로 이동한다.
- `.`은 현재 디렉토리를 의미하고 `..`은 부모 디렉토리를 의미한다.
- 절대경로는 root directory를 의미하는 `/`으로 시작한다.

### 파일 및 폴더 생성

- `touch 파일이름`
  - 파일 생성시에 사용하는 명령어
  - 당연히 한번에 여러개도 만들 수 있다.
  - `man touch`해서 설명을 보면 `update the access and modification times of each FILE to the current time. A FILE argument that does not exist is created empty.` 라고 되어 있다.
- `file 파일이름`
  - 파일의 이름에 있는 확장자를 통해서 해당 파일의 종류가 결정되는 것이 아니다.
  - 해당 명령어를 통해서 어떤 종류의 파일인지 파악할 수 있다. 특정 파일이름에 있는 확장자를 바꿔도 `file` 명령어로는 원래대로 나온다.
- `mkdir 디렉토리이름`
  - 디렉토리를 만드는 명령어
  - `mkdir 디렉토리1 디렉토리2` 이런식으로 여러개의 디렉토리를 만들 수도 있다.
  - 중첩된 디렉토리를 만들기 위해 `mkdir 강아지/푸들`이라고 하면 강아지 디렉토리가 존재하지 않는 경우 에러가 발생한다. `mkdir -p 강아지/푸들`이라고 하면 강이지 디렉토리를 만들고 그 안에 푸들 디렉토리를 만들어준다.

### 삭제, 복사, 이동

- `rm`
  - 파일, 디렉토리를 삭제하는 명령어 (옵션을 사용하지 않으면 파일만 삭제가능)
  - 휴지통으로 보내는게 아니라 바로 영구적으로 삭제한다.
  - 옵션 `-d`는 빈 디렉토리를 삭제하는 옵션이다. 디렉토리에 파일이나 디렉토리가 있다면 `-r` 옵션을 사용한다.
  - `-i` 옵션은 삭제 명령어에 해당하는 모든 파일, 디렉토리를 지우려고 할 때마다 유저에게 물어본다. y,n 로 답하면 된다.
- `mv 시작점 도착점`
  - 시작점을 도착점으로 옮기는 명령어
  - 먼저 파일을 옮길 때는 `mv 파일경로 옮길디렉토리경로`를 하면 되고 파일을 여러개 한번에 옮길 수도 있다. 디렉토리를 옮길 때도 동일하게 진행하면 된다.
  - 그런데 `도착점`이 존재하지 않으면 파일, 디렉토리의 이름이 바뀐다. 즉, `mv`는 이름을 바꾸는데에도 사용되는 것이다.
- `cp 시작점 도착점`

  - 복사하는 명령어
  - `mv`처럼 하위에 파일, 디렉토리가 있다면 `-r` 옵션을 이용해서 복사한다.

### 단축키

- `ctrl + l`: `clear` 명령어랑 같다.
- `ctrl + a(e)`: 명령어의 맨 앞(뒤)으로 이동한다.
- `alt + f(b)`: 단어를 하나씩 건너 뛰면서 앞(뒤)으로 이동한다.
- `alt + t`: 지금 단어와 앞에 위치한 단어의 위치를 바꾼다.
- `ctrl + k(u)`: 현재부터 뒤(앞)에 있는 명령어들 삭제한다.
- `alt + d`: 커서의 위치를 기준으로 현재 단어의 마지막까지 삭제한다.

### 파일 작업

- `cat 파일`
  - 파일을 출력
- `less 파일`
  - 긴 파일이 있을 때, 한번에 보여주는게 아니라 조금씩 나눠서 보여준다.
- `head`
  - 파일의 상위 몇줄을 보여주는 명령어
  - `head -n 5 minsoo.txt`하면 파일의 상위 5줄을 보여준다.
- `tail`
  - `head`와 반대
  - `tail -f 파일`은 파일 맨 끝에 내용이 추가되는 것을 실시간으로 계속 업데이트해서 보여준다. 내용이 추가되는 로그파일을 모니터링하기에 좋은 옵션이다.
- `wc`
  - 특정 파일의 줄 수, 단어 수 등을 알 수 있는 명령어
  - `wc 파일`을 치면 3개의 수가 나오는데 순서대로 줄수, 단어수, 바이트를 의미한다. 각각을 보고 싶으면 옵션 `-l`, `-w`, `c`를 사용하면 된다.
- `sort`
  - 각 line을 사전순 정렬해서 보여주는 명령어
  - 파일을 바꾸는 것이 아니고 출력만 하는 것이다.
  - 기본적으로 사전순이기 때문에 숫자를 정렬하는 경우, 숫자의 크기대로 정렬하고 싶으면 `-n` 옵션을 사용해야 한다.
  - 해당 파일이 table형태인 경우 특정 열을 기준으로 정렬할 수도 있다. 예를 들어, `sort -k2 minsoo.txt`라고 하면 2번째 컬럼을 기준으로 정렬된 결과를 출력한다.

### 리다이렉트

- redirect output symbol `>`을 이용하여 stdout을 stdin으로 만들 수 있다. (표준출력의 리다이렉션)
  - 이는 명령어와 파일을 연결시켜준다고 이해할 수 있다.
  - 예를 들어 `ls -al > ls.txt`하면 `ls -al`에 의해 출력된 결과를 `ls.txt`로 만든다.
  - 새로운 파일을 만들 수 있고 원래 있는 파일이면 덮어쓴다.
  - 기존 파일에 내용을 추가하고 싶으면 `>>`을 사용해야 한다.
- 그렇게 자주 쓰이지는 않지만 표준입력의 리다이렉션은 `<`으로 할 수 있다.
  - 예를 들어 `cat < minsoo.txt`
- stderr의 리다이렉션은 `2>`으로 한다. (에러를 리다이렉션하는 것)
  - 예들 들어 `sort tmp.tcxt 2> err.txt`
  - 숫자 2인 이유는 stdin이 0, stdout이 1이기 때문이다. 각각 숫자를 생략하고 사용했을 뿐이다.
  - `2>>`를 하면 존재하는 파일에 내용을 추가한다.
- `ls -l > output.txt 2> output.txt`와 같이 한번에 사용할 수도 있다. 이런 경우 에러없으면 없는대로 있으면 있는대로 출력물이 저장된다.

### 파이프

- `|`를 이용하여 여러 개의 명령어를 이어서 사용할 수 있다.
  - 명령어의 output을 다른 명령어의 input으로 연결시켜준다.
  - `ls -al | wc` 같이 사용할 수 있다.
- `tr`
  - stdin의 글자를 수정할 수 있는 명령어
  - 예를 들어, `cat ls.txt | tr minsoo Minsoo`라고 하면 `cat ls.txt`의 output에 있는 `minsoo`를 `Minsoo`로 다 바꿔준다.
- `tee`
  - 파이프에서 중간 결과를 파일로 저장하고 다음 명령어로 넘겨주기도 하는 역할을 한다.
  - 예를 들어, `cat ls.txt ls.txt | tee ls2.txt | wc -w`에서 `cat ls.txt ls.txt`의 output을 `ls2.txt`로 저장하고 `wc -w`의 input으로 넘겨준다.

### 확장

- `*`
  - 원하는 패턴에 맞는 파일, 디렉토리를 한번에 사용하게 해준다.
  - `ls *.txt`는 해당 위치의 txt파일 모두를 보여준다. 여기서 `*`는 특별한 기능을 하는 것이다.
- `?`
  - 하나의 문자 패턴
  - `ls picture?.png`는 `picture`뒤에 딱 한 글자로 이루어진 png파일들을 보여준다.
  - `*`는 글자수와 상관없고 `?`는 한글자만 해당한다.
  - 두글자 이상 하고 싶으면 `?`를 `??`와 같이 여러 개 사용하면 된다.
- `[]`
  - 글자의 범위를 지정할 수 있다.
  - `ls file[1-3]`는 `file1`, `file2`, `file3` 세 개의 파일을 찾아서 있으면 보여준다.
- `[^]`
  - 특정 문자를 제외하는 것을 의미한다.
  - 예를 들어, `ls [^a]*.txt`는 `a`로 시작하는 txt파일을 제외하고 보여준다.
- `{}`
  - `touch page{1,2,3}.txt`를 통해서 `page1.txt`, `page2.txt`, `page3.txt`를 만들 수 있다.
  - `{1..31}`처럼 `..`으로 해당 범위를 한번에 표현할 수도 있다.
- `$((수학식))`
  - `echo $((10+10))`를 하면 20이 나온다.
- `""`, `''`
  - `echo hello {1,2,3}`를 하면 `hello 1 2 3`가 나온다. `echo "hello {1,2,3}"`는 `hello {1,2,3}`가 그대로 나온다. 이처럼 `""`안에 있는 문자를 그대로 나오게 한다.
  - 하지만 예외도 있다. `$`, `\`, ```같은 경우 이들을 문자 그대로 판단하지 않는다.
  - 이들을 문자 그대로 사용하고 싶으면 `''`을 사용하면 된다.
- `$(명령어)`
  - 다른 명령어의 output을 보여주는 명령어
  - `echo "today is $(date)"`같이 사용할 수 있다.
  - `echo today is `date``도 동일한 기능을 한다.

### 찾기

- `locate`
  - linux ubunto에는 설치되어 있지 않다.
  - `sudo apt install mlocate`로 설치해야 한다.
  - 내장 데이터베이스 파일을 이용해서 파일에 대한 정보를 갖고 있다. 그래서 파일을 찾거나 할 때, 빠르고 쉽게 할 수 있다.
  - `minsoo`라는 이름의 파일을 찾고 싶으면 `locate minsoo`라고 하면 된다. 그러면 경로를 보여준다.
  - 근데 데이터베이스가 바로바로 업데이트되는 것은 아니다.
- `find`
  - 현재 위치안에 있는 모든 파일, 디렉토리에서 원하는 파일을 찾는 명령어
  - `find`를 치면 해당 디렉토리에 있는 모든 파일과 디렉토리를 보여준다.
  - 원하는 경로를 `find 경로`로 지정할 수 있다.
  - `find -type d`는 디렉토리로 제한하고 `f`는 파일만 찾아서 보여준다.
  - `find -name "*.txt"`처럼 큰따옴표를 이용하여 원하는 파일만 찾아볼 수도 있다. 정확하게 일치하는 파일만 찾아서 보여준다. 해당 단어가 들어간 파일을 찾거나 하는게 아니다.
  - `find -size +1G`는 1G이상의 파일을 찾아준다. 이처럼 파일의 크기를 이용하여 찾을 수도 있다. 이하를 찾고 싶으면 `-`를 이용하면 된다.
  - `find -user 주인이름`으로 특정 유저(주인)이름을 갖고 있는 사람
  - `find -name "minsoo*" -or -name "song*"`, `find -type -f -not -name "*.csv"` 과 같이 logical operator를 이용할 수도 있다.
  - `find -exec 명령어 '{}' ';'`를 통해서 `find`를 통해 찾은 파일들에 명령어들을 실행한다.
    - 예를 들어 `find -type f -exec ls -al '{}' ';'`와 같이 사용할 수 있다.
    - `{}`에 `find`로 찾은 파일들이 하나씩 들어간다고 이해할 수 있다.
    - 또다른 예시는 `find ! -type f -empty -exec cp '{}' '{}_copy' ';'`이다. 해당 명령어를 실행하면 비어있는 파일들의 복사본인 `파일_copy`이라는 파일이 생성된다.
- `xargs`
  - 여러개의 argument를 특정 명령어로 넘겨주는 명령어
  - 위에서는 `exec`를 사용하였지만 더 간단하게 `xargs`를 사용할 수 있다. 다소 차이점이 있다면 `exec`에서는 여러 개의 파일을 하나씩 실행하였다면 `xargs`는 한번에 넘긴다. 그래서 터미널창에 보이는 결과가 조금 달라질 수 있다.
  - `find -type f | xargs ls -al`처럼 파이프를 이용하여 많이 사용된다.
- Time
  - mtime은 파일 내용 최종 수정시간을 의미한다. (modification)
    - `ls -l`에서 나오는 시간은 파일 내용 최종 수정시간(mtime)을 의미한다.
  - ctime은 파일의 이름, 경로 등의 최종 수정시간을 의미한다. (change)
    - `ls -lc`를 통해서 확인할 수 있다.
  - atime은 파일에 최종 접근시간을 의미한다. (access)
    - `ls -lu`를 통해서 확인할 수 있다.
  - `find -mmin +30`은 30분 이전에 modification된 파일을 보여준다.
    - `cmin`, `amin` 도 당연히 있다.

### `grep`

- `grep PATTERN FILE`을 통해서 파일에 있는 해당 패턴(단어)를 찾는 명령어
- `grep "minsoo" minsoo.txt`을 하면 `minsoo`가 빨간색으로 강조되고 해당 단어가 있는 행들을 함께 보여준다. 해당 패턴(단어)가 들어있는 모든 단어들을 찾는다. 해당 단어 근처의 내용도 같이 보여주는 것이다. 대소문자 상관없이 하려면 `-i`를 추가한다.
- 위에는 해당 패턴(단어)가 들어있는 모든 단어를 보여주는데 정확한 단어를 지정할 수도 있다. `grep -w "minsoo" minsoo.txt`에서 `-w`를 통해 정확히 `minsoo` 단어가 있는 행을 보여주는 것이다.
- `grep -r "minsoo"`을 통해서 특정 파일이 아니라 디렉토리에서 재귀적으로 찾게 해준다.
- `grep -c "minsoo" minsoo.txt`처럼 `-c`를 이용하여 해당 패턴이 몇 번 나왔는지 확인 할 수 있다.
- 정규표현식을 이용해서 원하는 패턴을 찾을 수 있다.
  - 몇가지 예시를 보자.
  - `grep "min[sj]oo"`처럼 하면 s,j 모두 찾는다.
  - `grep ")$" example.txt`에서 `$`는 행 끝을 의미하기에 문장에서 `)`가 마지막인 경우를 찾는다.
- `man grep | grep "regular" -w`처럼 파이프를 만들어서 자주 사용한다.

### 권한 기초

- 유닉스, 유닉스계열은 multiuser 시스템이다.
- 다른 사용자의 파일을 수정할 수 없다.
  - `cd ~`이면 현재 사용자의 home 디렉토리로 간다. 내 컴퓨터의 경우 `/home/minsoo`이다. 상위 경로로 가서 다른 사용자들의 파일, 디렉토리를 볼 수 있으나 수정은 불가능하다.
- 파일 소유자
  - `ls -l` 명령어의 경우 예를 들어 아래와 같은 결과가 나온다.
  - 아래에서 첫번째로 나오는 `minsoo`는 해당 파일(디렉토리)의 owner이다.
  - 두번째로 나오는 `minsoo`는 group이다. 해당 group에서 여러 명의 user가 속할 수 있다. group은 같은 수준의 권한을 공유한다.

```bash
drwxr-xr-x  2 minsoo minsoo       4096  5월 21 11:06 Desktop
drwxr-xr-x  2 minsoo minsoo       4096  5월 21 11:06 Documents
drwxr-xr-x  4 minsoo minsoo       4096  7월 15 16:54 Downloads
```

- 파일 속성
  - 위 결과에서 `drwxr-xr-x` 같은 경우 각 문자들은 해당 파일(디렉토리)가 갖고 있는 특성(권한관련)을 보여준다.
  - 맨 앞 `d`는 디렉토리를 의미한다.
    - `-`: 파일, `l`: symbolic link
  - `rwx`는 각각 read, write, execute 권한을 의미한다.
    - 파일이 `r`이 없으면 당연히 읽을 수 없고 디렉토리에도 없으면 `ls` 명령어가 먹지 않는다.
    - 파일이 `w`이 없으면 해당 파일을 수정할 수 없고 디렉토리에서는 이동, 이름변경, 삭제 등이 불가능하다.
    - 파일이 `x`가 없으면 파일을 실행할 수 없다. 디텍토리에서는 `cd` 명령어를 통해서 들어가는게 불가능하다.
  - 그리고 `rwx`가 3번 반복되는데 순서대로 owner, group, everyone else 들에게 허용된 권한을 의미한다.

### permission 변경하기

- `chmod`
  - who
    - `u`: user (owner of the file)
    - `g`: group
    - `o`: others
    - `a`: all of the above
  - what
    - `-`: permission 제거
    - `+`: permission 주기
    - `=`: set a permision and remove others
  - which
    - `r`, `w`, `x`
  - 예를 들어, `chmod g+w file.txt`의 경우 `file.txt`에 대해 group에 write 권한을 준다는 것이다.
- `su`
  - 다른 사용자로 바꿀 때 사용
  - 예를 들어, 지금 `song`이라는 사용자로 터미널을 사용하고 있는데 `minsoo`로 사용자를 바꾸고 싶은 경우 `su - minsoo`라고 치면 된다. 그러면 password를 치라고 나오는데 치면 사용자 전환이 이뤄진다.
  - `exit`를 치면 원래 사용자로 돌아간다.
- `sudo`
  - linux에는 root라 불리는 super user가 있다. (ubuntu는 root user를 default로 잠군다)- 잠겨있어도 우리는 `sudo` 명령어를 통해서 root user로서 명령어를 사용할 수 있다.
- `chown`
  - owner, group owner를 바꾸는 명령어
  - `sudo chown minsoo file.txt`라고 하면 `minsoo`로 owner를 바꾼다.
  - `sudo chown :minsoo file.txt`라고 하면 group owner를 바꾼다.
- group
  - `groups 유저`를 통해 유저와 같은 group에 누가 있는지 확인할 수 있다.
  - `addgroup 그룹이름`을 통해서 그룹을 만들 수 있다.
    - 그리고 `adduser 유저 그룹`을 통해서 유저를 추가한다.

### 환경

- `printenv`를 실행하면 key, value 형태의 환경변수들이 나온다. 이들이 환경을 구성하는 것이다.
- parameter expansion
  - `$`를 통해서 위에서 나오는 환경변수들에 접근 할 수 있다. 예를 들어, `echo $USER`라고 치면 `minsoo`라고 나온다.
- 우리가 원하는 (사용자 정의) 변수를 만들기도 쉽다. `변수=값`이면 된다. `number=123`하고 `echo $number`하면 `123`이 나온다. 이는 사용자 정의 변수이기 때문에 `printenv`에는 나오지 않는다. 이는 shell 변수라고도 할 수 있다. 해당 shell이 닫히면 날라간다.
  - 여기서 해당 변수를 저장하고 싶으면 `export number`하면 `printenv`에 나온다. (not 휘발)
- startup files
  - shell는 global startup file을 읽고 login한 해당 유저의 startup file을 읽는다.
  - login session
    - `/etc/profile`: global config for all users
    - `~/.bash_profile`: user's personal config file
    - `~/.bash_login`: 위의 `.bash_profile`이 없는 경우 이 파일을 읽는다
    - `~/.profile`: 위의 두 파일이 없는 경우 이 파일을 읽는다.
  - non-login session
    - `~/.bashrc`: user마다 setting에 관한 내용이 담겨있다.
- `alias`
  - 원하는 명령어를 `alias ll='ls -al`같이 다른 이름으로 지정할 수 있다.
  - `.bashrc`에 원하는 alias를 만들어 놓으면 편하다.
- `PATH`
  - `echo $PATH`하면 아래와 같이 결과가 나온다.
  - 예를 들어, `ls` 같은 명령어를 실행하면 아래의 `PATH`에 있는 경로를 찾아서 해당하는 파일(프로그램)을 실행한다.
  - `:`으로 여러 경로들이 구분되어 있다.
  - `PATH`를 추가하고 싶으면 `PATH="추가경로:$PATH"` 이런 식으로 하면 된다.

```
PATH=/home/minsoo/.pyenv/shims:/home/minsoo/.pyenv/bin:/home/minsoo/.pyenv/bin:/home/linuxbrew/.linuxbrew/bin:/home/linuxbrew/.linuxbrew/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

### cron

- `crontab -e` 처음치면 에디터를 선택하라고 나온다. 원하는 것을 선택하면 된다.
- cron syntax
  - `a b c d e command`
  - `a`: minute, `b`: hour, `c`: day, `d`: month, `e`: day of week
  - 위의 `a b c d e`마다 `command`를 실행한다.
  - 예를 들어, `30 * * * * command`는 매일 시간이 30분이 되면 명령어를 실행한다. 30분 마다가 아니라 at minute 30 이라는 의미이다.

## Reference

- (udemy) Linux Command Line 부트캠프: 리눅스 초보자부터 고수까지
- (youtube) https://www.youtube.com/watch?v=ZtqBQ68cfJc

