# [Airflow] Database and Executor


multiple task를 execute하는 방법과 관련한 내용을 공부해보자.

<!--more-->
- airflow.cfg를 바꿔서 적용하려면 webserver, scheduler를 재시작해야한다

### default configuration
```python
airflow config get-value core sql_alchemy_conn
```
위의 명령어를 치면 `sqlite:////home/airflow/airflow/airflow.db` 라고 나온다. 이는 현재 airflow에서 SQLite가 default db라고 이해할 수 있다.

```python
airflow config get-value core executor
```
위의 명령어를 치면 `SequentialExecutor` 라고 나온다.

이러한 default의 상황에서 아래 예시는 sequential하게 task1을 하고 task2, 3를 하고 task4를 하는 과정이다. task2, 3 중에서 누가 먼저 진행될지는 알 수 없다. 순차적으로 진행된다.

```python
from airflow import DAG
from airflow.operators.bash import BashOperator

from datetime import datetime

default_args = {
    'start_date': datetime(2022, 4, 23)
}

with DAG(
    'parallel_dag',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 3'
    )

    task_2 = BashOperator(
        task_id='task_2',
        bash_command='sleep 3'
    )

    task_3 = BashOperator(
        task_id='task_3',
        bash_command='sleep 3'
    )

    task_4 = BashOperator(
        task_id='task_4',
        bash_command='sleep 3'
    )

    task_1 >> [task_2, task_3] >> task_4
```

위의 내용들이 현재 airflow의 default configuration이라고 할 수 있다. 이제 목적에 맞게 이를 조금씩 수정하는 과정을 알아보자.

### Scaling with the Local Executor
- local executor
  - sequential executor와 다르게 multiple tasks를 parallel하게 execute할 수 있다

위의 예시에서 task2, 3를 parallel하게 진행해보자. 또한, 이번에는 sqlite가 아니라 PostgreSQL을 사용해보자. 이는 SQLite는 한번에 하나의 writer만이 가능하기에 동시에 task를 해야하는 local, celery executor를 사용하기에 적절하지 않기 때문이다.

```bash
sudo apt update
# 설치
sudo apt install postgresql
# 비밀번호 지정
sudo -u postgres psql
ALTER USER postgres PASSWORD 'postgres';
# 추가 패키지 설치
pip install 'apache-airflow[postgres]'
```

그리고 default configure를 바꾸기 위해 `airflow.cfg`에 들어가서 `sql_alchemy_conn`의 값을 바꿔준다.

```
sql_alchemy_conn = postgresql+psycopg2://postgres:postgres@localhost/postgres
# 원래는 아래값이 default
# sql_alchemy_conn = sqlite:////home/airflow/airflow/airflow.db
```

그리고 잘됐는지 확인하기 위해 `airflow db check`해서 확인해보자. 이제 metastore db가 postgres로 바뀌었다. 추가로 executor도 바꿔주고 websever, scheduler 모두 끄고 다시 db init 부터 시작한다.

```
executor = LocalExecutor
# executor = SequentialExecutor
```

```bash
airflow db init
airflow users create -u admin -p admin -r Admin -f admin -l admin -e admin@airflow.com
```

그리고 다시 webserver, scheduler를 키고 dag를 실행시키면 task2, 3가 정말 동시에 parallel하게 진행됨을 확인할 수 있다.

### Scale to the infinity with the Celery Executor
- celery executor
  - allows distributing the execution of task instances to multiple worker nodes

task는 queue(redis를 이용할 예정)에 있다가 실행될 때, node(worker)로 보내져서 실행된다. node(worker)의 수를 늘리면 더 많은 task를 할 수 있다. 또한 각 node(worker)마다 또한 여러 개의 task를 진행할 수 있고 이는 worker_concurruency를 통해서 조절 할 수 있다.

```bash
# celery 설치
pip install 'apache-airflow[celery]'
# redis server 설치
sudo install redis-server
# 설정 바꾸기
sudo vi /etc/redis/redis.conf # 여기서 supervised no 를 찾아서 no를 systemd로 바꾼다
# redis restart하고 잘되는지 확인
sudo systemctl restart redis.service
sudo systemctl status redis.service
```

redis가 잘 돌아가면 이제 airflow.cfg헤서 몇 가지 수정한다.
- executor를 `CeleryExecutor`로 바꾼다.
- `broker_url = redis://redis:6379/0`를 `broker_url = redis://localhost:6379/0`로 바꾼다.
- `result_backend = db+postgresql://postgres:airflow@postgres/airflow`를 `result_backend = postgresql+psycopg2://postgres:postgres@localhost/postgres`로 바꾼다.
  - `result_backend`는 task가 완료되면 관련한 metadata를 저장하는 곳을 의미

다음으로 redis와 airflow의 연결을 위해 `pip install 'apache-airflow[redis]'`으로 관련 패키지를 설치한다. 그리고 다른 bash창에서 `airflow celery flower`를 통해 worker들을 웹으로 확인해볼 수 있다. 그러면 지금은 worker가 없는 것을 확인할 수 있다. `airflow celery worker`로 worker를 추가하면 Flower웹에서 확인할 수 있다. 그리고 dag를 돌려서 확인해볼 수도 있다.

### Concurrency
airflow.cfg에서 `parallelism`을 찾아보면 airflow instance 전체에서 parallel하게 task가 동시에 최대 몇 개 까지 진행될 수 있는지 나와있다. `executor = LocalExecutor`였을 때 원래 task2, 3가 동시에 진행되었는데 `parallelism=1`로 지정하면 동시에 진행되지 않는다. 

`dag_concurrency`를 통해서 하나의 DAG에서 parallel하게 동시에 돌아갈 수 있는 task 수를 제한 할 수 있다. 이 값은 default이고 당연히 각 DAG에 각각 다르게 지정할 수 있다.

`max_active_runs_per_dag`은 maximum number of active DAG runs per DAG을 의미한다. 이 값도 DAG에 따라 다르게 지정할 수 있다.

이들을 통해 동시에 진행되는 DAG, Task를 조절할 수 있다.

## Reference
- [udemy: the complete hands on course to master apache airflow](https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow)
