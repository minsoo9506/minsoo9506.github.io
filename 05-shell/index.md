# [Linux Shell] Shell Script 명령어 정리


Shell Script 명령어 정리

<!--more-->

## Shell?

- 사용자가 입력한 명령어를 해석해서 kernel에 전달
- 현재 쓰고 있는 쉘을 확인하려면 `echo $SHELL`로 확인하면 된다.

## Bash Shell과 변수

### Shell의 변수

- 변수선언
  - `변수이름=변수`
  - `minsoo=kind`
- 변수 확인
  - `echo $변수이름`
- 변수 삭제
  - `unset 변수이름`

### Shell의 환경변수

- 환경변수: 동작되는 프로그램에 영형을 주는 변수
- 환경변수선언
  - `export 변수이름=변수`
  - 환경변수는 주로 대문자를 사용한다.
- 시스템의 환경변수는 `env` 명령어로 확인이 가능하다.

## Bash Shell의 기능들

### Quoting Rule

- Shell에서 특별한 의미가 있는 문자들이 있다. (`*`, `[]` 등등)
  - `*`: means "match zero or more characters"
  - `?`: matches a single character
    - `201?.txt` will match 2017.txt or 2018.txt, but not 2017-01.txt
  - `[...]`: matches any one of the characters inside the square brackets
    - `201[78].txt` matches 2017.txt or 2018.txt, but not 2016.txt
  - `{...}`: matches any of the comma-separated patterns inside the curly brackets
    - `{_.txt, _.csv}` matches any file whose name ends with .txt or .csv
- `\`를 통해서 위의 메타문자들이 바로 뒤에서 문자 그대로 사용되도록 만들 수 있다.
- `""` 내의 메타문자들의 의미를 제거한다. 단, `$`, ` ` ``은 제외
- `''` 내의 메타문자들의 의미를 제거한다.

### Nesting commands

- 명령어의 실행 결과를 치환하여 명령을 실행
- 두가지의 방법이 있는데 `$(명령어)`, `` `명령어` `` 를 이용한다.
  - 예를 들어
    - `echo "Today is $(date)"`
    - `` echo "Today is `date`" ``

### Alias

- 명령어를 다른 이름으로 사용, 복잡한지만 자주 쓰는 명령어는 편하게 만들어 놓을 수 있을 것이다.
- `alias` 명령어를 실행하면 지금 저장된 alias들을 볼 수 있다.
- alias 등록
  - `alias 원하는줄임말=명령어`
  - 근데 명령어에 띄어쓰기가 있으면 당연히 따옴표들로 감싸줘야한다.
- alias 해제
  - `alias 줄임말`
- 근데 shell 끄면 alias는 날라간다. 계속 사용하고 싶으면 `.bashrc` 같은 파일에 저장해야 한다.

### Redirection

- 일반적으로 키보드를 통해서 내용을 입력하면 프로그램이 이를 받고 결과를 터미널을 통해 보여준다.
- 하지만 때로는 이를 redirect하고 싶을 경우가 존재한다.

| communication channels |     redirection character     |                    의미                    |
| :--------------------: | :---------------------------: | :----------------------------------------: |
|         STDIN          |          `0<`, `0<<`          |   입력을 키보드가 아닌 파일을 통해 받음    |
|         STDOUT         | `1>` (덮어쓰기), `1>>` (추가) |   표준 출력을 터미널이 아닌 파일로 출력    |
|         STDERR         |          `2>`, `2>>`          | 표준 에러 출력을 터미널이 아닌 파일로 출력 |

- 위에서 숫자 0,1 은 생략이 가능하고 주로 생략해서 사용한다.

### Pipeline

- 명령의 실행결과를 다음 명영의 입력으로 전달
- `명령어1 | 명령어2 | 명령어3`

## Bash Shell Script

- 리눅스 명령어를 모아놓은 ASCII Text 파일
- `#`으로 시작하는 것은 comment, `#!/bin/bash`을 통해서 스크립트를 실행할 sub shell를 지정하여 `/bin/bash` bash shell script를 실행하는 것이다.
- shell script 실행
  - `파일.sh`를 만들고 해당 위치에서 `./bash 파일.sh`명령어로 실행할 수 있다.
    - 단, 실행권한이 없는 경우 `chmod +x 파일.sh`로 실행권한을 바꾸고 진행한다.
  - 또는 `bash 파일.sh`로 실행시킬 수도 있다.
    - 이때는 실행권한이 없어도 된다.
  - `#!/bin/bash`문이 파일에 없어도 실행된지만 sub shell을 지정하는 것이다.
- 예시

```bash
#!/bin/bash
echo "-----"
date
echo "-----"
```

## Positional Parameters

- 예시

```bash
#!/usr/bash
echo $0
echo $1
echo $2
echo $@
echo "There are" $# "arguments"
```

- `bash pos.sh one two` 치면 argument들이 들어가는 것이다.
  - `$0`는 shell script의 이름
- `$@`는 전체 argument, `$#`는 argument의 갯수
- 실행결과

```
pos.sh
one
two
one two
There are 2 arguments
```

## Input, Ouput

### echo

- 옵션으로 `-e`를 사용하면 escape문자를 사용한다. `\n`, `\t` 같은 문자가 기능을 한다.

### read

- `read 옵션 변수명`명령어를 통해서 사용자에게 직접 변수에 해당하는 값을 입력받는다.
- 예시
  - `echo -n "your name: " ; read name` 명령어를 입력하면
    - `-n`옵션은 줄바꿈을 하지 않는다는 것
  - `your name: `에 커서가 있고 `name`변수에 해당하는 값을 넣으면 된다.

### printf

- format에 맞춰서 출력할 수 있다.
- 예시
  - `today`라는 변수에 `birthday`라는 값이 있다고 가정하면
  - `printf "Today is %d" $today`라고 치면 `Today is birthday`가 나온다.

## Branching

### exit

- 실행된 프로그램이 종료된 상태를 전달
- `$?`에 직전에 실행한 명령어의 종료상태를 갖고 있다. `echo $?`하면 볼 수 있는 것이다.
  - 0: 성공하고 종료
  - 1-255: 실패로 종료
    - 2: syntax error
    - 126: 명령을 실행할 수 없음
    - 127: 명령(파일)이 존재하지 않음

### test

- 비교연산자, 명령어 실행결과를 true 또는 false 로 보여준다.
- 사용방법은 2가지 인데 `test 비교구문`, `[ 비교구문 ]` 이런식으로 사용한다. (띄어쓰기를 꼭 넣어야함)
- 예시
  - `x`가 10이라는 값을 가진다고 하면
  - `test $x -gt 5`라는 것은 `x`가 5보다 크냐는 것이므로 true이다. `echo $?`를 치면 0이라는 값이 나온다.
  - 위와 똑같은 역할을 하는 것이 `[ $x -gt 5 ]` 명령어이다.

### if-then

- 사용형태

```bash
if 조건
then
  명령어1
else
  명령어2
fi
```

- 예시

```bash
x="Queen"
if [ $x == "King" ]; then
    echo "$x is a King"
else
    echo "$x is not a King"
fi
```

### case

- if랑 비슷하지만 조건이 많을 때 사용하면 될듯
- 예시

```bash
case $1 in
  Monday|Tuesday|Wednesday|Thursday|Friday)
  echo "It is a Weekday!";;
  Saturday|Sunday)
  echo "It is a Weekend!";;
  *)
  echo "Not a day!";;
esac
```

## Loop

### while , until

- while은 명령어가 성공하는 동안 반복, until은 성공할 때까지 반복
- while 예시

```bash
x=1
while [ $x -le 3 ];
do
  echo $x
  ((x+=1))
done
```

### for

- 예시

```bash
for x in {1..5..2}
do
  echo $x
done
```

```bash
for book in books/*
do
  echo $book
done
```

```bash
for book in $(ls books/ | grep -i 'air')
do
  echo $book
done
```

## Reference

- 따배셸, 셸 프로그래밍 시리즈

