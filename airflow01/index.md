# [Airflow] 설치


Airflow를 공부하기 위해 vitualBox를 이용하여 세팅해보자.
<!--more-->

## 설치
vitualBox를 이용해서 진행한다. vitualBox를 설치하고 강의자가 제공하는 file을 이용하여 가상환경을 세팅한다. 그리고 vscode에서 ssh로 접근한다.

### ssh 연결
1. vscode extension에서 remote-ssh를 설치한다.
2. `cmd+p`를 치고 `>remote-ssh`라고 치고 `Add New SSH Host...`를 클릭한다.
3. `ssh -p 2222 airflow@localhost`라고 친다.
4. 그리고 첫번째 ssh config file을 클릭한다.
5. 연결이 잘 되었다면 `cmd+p`를 치면 localhost가 뜰 것이고 이를 클릭한다. 그러면 접속이 될 것이다.

### airflow 설치
python virtual environment를 이용하여 airflow를 설치한다. 이미 강의자가 잘 세팅을 했고 이를 이용하기만 하면 된다.

```bash
python3 -m venv sandbox
source sandbox/bin/activate # 이러면 sandbox가 활성화된다

pip install wheel

pip3 install apache-airflow==2.1.0 --constraint https://gist.githubusercontent.com/marclamberti/742efaef5b2d94f44666b0aec020be7c/raw/21c88601337250b6fd93f1adceb55282fb07b7ed/constraint.txt

airflow db init # 필요한 파일들 생성
```

그러면 `airflow` 폴더가 만들어져있을 것이다. 그리고 `airflow webserver`라고 치면 localhost:8080에 들어가면 web이 띄워져 있다. 지금은 사용자를 생성하지 않은 상태이므로 사용자를 생성해보자.

```bash
airflow users create -u admin -p admin -f minsoo -l song -r Admin -e admin@airflow.com
```

이제 web에 로그인할 수 있다. 처음에는 디폴트 예시 DAG들이 있을 것이다.

## Reference
- [udemy: the complete hands on course to master apache airflow](https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow)
