# [Airflow] Pipeline


Airflow pipeline을 만들어보자

<!--more-->
### DAG
- DAG
  - node들이 task이고 edge가 각 task의 dependency를 의미한다

### Operator
- operator
  - one task in data pipeline
  - 하나의 operator에는 하나의 task만 넣어라
  - 특히 dependency가 있는 task의 경우는 더 그래야한다

- 3 types of operator
  - Action operators: Execute an action
  - Transfer operators: Transfer data
  - Sensors: Wait for a condition to be met

## pipeline 만들기
만들고자 하는 pipeline은 다음과 같다.

```
creating_table >> is_api_available >> extracting_user >> processing_user >> storing_user
```

먼저 webserver와 scheduler를 활성화한다.

```bash
airflow webserver
airflow scheduler
```
### `creating_table` 
airflow를 설치하면 최소한의 패키지만 설치되기 때문에 추가적으로 패키지가 필요하면 설치해야 한다. 지금 만드려고 하는 db의 경우 sqlite를 이용할 것이기 때문에 sqlite operator가 필요하다. [링크](https://airflow.apache.org/docs/)에 가면 third-party와의 연결을 위한 package들이 있다. `pip install apache-airflow-providers-sqlite`으로 설치를 해준다.

파이썬 가상환경을 활성화해주면 

그리고 아래와 같이 `user_processing.py` 파일을 만들어준다.
```python
from airflow.models import DAG
from airflow.providers.sqlite.operators.sqlite import SqliteOperator
from datetime import datetime

# default로 넣고 싶은 인자들
default_args = {
    'start_date': datetime(2022, 4, 20)
}

with DAG(
    'user_processing', # dag id는 unique 해야한다: 'user_processing'
    schedule_interval='@daily', # start_date이후로 얼마나 자주 DAG가 run되야하는지
    default_args=default_args,
    catchup=False
    ) as dag:

    # define the task/operator
    creating_table = SqliteOperator(
        task_id='creating_table', # 하나의 pipeline에서 unique 해야함
        sqlite_conn_id='db_sqlite',
        sql='''
            CREATE TABLE users (
                firstname TEXT NOT NULL,
                lastname TEXT NOT NULL,
                country TEXT NOT NULL,
                username TEXT NOT NULL,
                password TEXT NOT NULL,
                email TEXT NOT NULL PRIMARY KEY
            );
            '''
    )
```

그리고 Admin -> Connections 에 가서 connection을 아래와 같이 추가해줘야 한다. conn_id는 위에서 정한 `db_sqlite`로 해줘야하고 `Host`는 아래와 같이 `airflow.db`가 있는 path를 해주면 된다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/airflow01.PNG?raw=true"  width="500">
</center>

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/airflow02.PNG?raw=true"  width="500">
</center>

- test
  - `airflow tasks test user_processing creating_table 2022-04-23`

### `is_api_available`
task이름 그래도 api가 작동하는지 확인하는 것이다. 기존 airflow의 example 데이터를 이용한다.

```python
from airflow.models import DAG
from airflow.providers.sqlite.operators.sqlite import SqliteOperator
from airflow.providers.http.sensors.http import HttpSensor

from datetime import datetime

# default로 넣고 싶은 인자들
default_args = {
    'start_date': datetime(2022, 4, 20)
}

with DAG(
    'user_processing', # dag_id는 unique 해야한다: 'user_processing'
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
    ) as dag:

    # define the task/operator

    creating_table = SqliteOperator(
        task_id='creating_table', # 하나의 pipeline에서 unique 해야함
        sqlite_conn_id='db_sqlite',
        sql='''
            CREATE TABLE users (
                firstname TEXT NOT NULL,
                lastname TEXT NOT NULL,
                country TEXT NOT NULL,
                username TEXT NOT NULL,
                password TEXT NOT NULL,
                email TEXT NOT NULL PRIMARY KEY
            );
            '''
    )

    is_api_available = HttpSensor(
        task_id='is_api_available',
        http_conn_id='user_api',
        endpoint='api/'
    )
```

- test
  - `airflow tasks test user_processing is_api_available 2022-04-23`

### `extracting_user`
airflow example 데이터를 extract하는 task를 생성한다.

```python
from airflow.models import DAG
from airflow.providers.sqlite.operators.sqlite import SqliteOperator
from airflow.providers.http.sensors.http import HttpSensor
from airflow.providers.http.operators.http import SimpleHttpOperator

from datetime import datetime
import json

# default로 넣고 싶은 인자들
default_args = {
    'start_date': datetime(2022, 4, 20)
}

with DAG(
    'user_processing', # dag_id는 unique 해야한다: 'user_processing'
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
    ) as dag:

    # define the task/operator

    creating_table = SqliteOperator(
        task_id='creating_table', # 하나의 pipeline에서 unique 해야함
        sqlite_conn_id='db_sqlite',
        sql='''
            CREATE TABLE users (
                firstname TEXT NOT NULL,
                lastname TEXT NOT NULL,
                country TEXT NOT NULL,
                username TEXT NOT NULL,
                password TEXT NOT NULL,
                email TEXT NOT NULL PRIMARY KEY
            );
            '''
    )

    is_api_available = HttpSensor(
        task_id='is_api_available',
        http_conn_id='user_api',
        endpoint='api/'
    )

    extracting_user = SimpleHttpOperator(
        task_id='extracting_user',
        http_conn_id='user_api',
        endpoint='api/',
        method='GET',
        response_filter=lambda response: json.loads(response.text),
        log_response=True
    )
```

- test
  - `airflow tasks test user_processing is_api_available 2022-04-23`

test를 진행하면 그 결과가 나온다. 아래는 GET을 통해 가져온 데이터를 의미한다.

```
[2022-04-23 06:35:15,589] {http.py:140} INFO - Sending 'GET' to url: https://randomuser.me/api/
[2022-04-23 06:35:16,104] {http.py:115} INFO - {"results":[{"gender":"female","name":{"title":"Mrs","first":"Lonne","last":"Schilstra"},"location":{"street":{"number":4722,"name":"Jaspis"},"city":"Zutphen","state":"Flevoland","country":"Netherlands","postcode":64338,"coordinates":{"latitude":"40.3466","longitude":"58.3229"},"timezone":{"offset":"+4:30","description":"Kabul"}},"email":"lonne.schilstra@example.com","login":{"uuid":"897a7df1-9964-4baf-9b3c-001f9cc815ef","username":"smallbear142","password":"chrysler","salt":"LysRP72T","md5":"92f39f731719df04767870a0cdcfb43d","sha1":"2cedb9b8ae797b56ddf40e8425db36df1f4866ee","sha256":"a1fd36358cf8d94e243ccfba37d9478b4b129bff367aa2ff218a4c975851ef11"},"dob":{"date":"1949-10-26T18:21:22.653Z","age":73},"registered":{"date":"2019-06-05T08:17:56.847Z","age":3},"phone":"(875)-139-9718","cell":"(524)-611-0968","id":{"name":"BSN","value":"47403055"},"picture":{"large":"https://randomuser.me/api/portraits/women/55.jpg","medium":"https://randomuser.me/api/portraits/med/women/55.jpg","thumbnail":"https://randomuser.me/api/portraits/thumb/women/55.jpg"},"nat":"NL"}],"info":{"seed":"ff9863d9e80ca539","results":1,"page":1,"version":"1.3"}}
[2022-04-23 06:35:16,123] {taskinstance.py:1184} INFO - Marking task as SUCCESS. dag_id=user_processing, task_id=extracting_user, execution_date=20220423T000000, start_date=20220423T060222, end_date=20220423T063516
```

### `processing_user`

```python
from airflow.models import DAG
from airflow.providers.sqlite.operators.sqlite import SqliteOperator
from airflow.providers.http.sensors.http import HttpSensor
from airflow.providers.http.operators.http import SimpleHttpOperator
from airflow.operators.python import PythonOperator

from datetime import datetime
import json
from pandas import json_normalize

# default로 넣고 싶은 인자들
default_args = {
    'start_date': datetime(2022, 4, 20)
}

# extracting_user 를 통해 얻은 결과를 이용
# airflow tasks test user_processing extracting_user 2022-04-23
def _processing_user(ti):
    # extracting_user task의 결과가 metastore에 저장이 되고 이 결과를 pull 한다
    users = ti.xcom_pull(task_ids=['extracting_user'])
    if not len(users) or 'results' not in users[0]:
        raise ValueError('User is empty')
    user = users[0]['results'][0]
    # json_normalize로 dict를 pd.DataFrame으로 변환
    processed_user = json_normalize({
        'first_name': user['name']['first'],
        'lastname': user['name']['last'],
        'country': user['location']['country'],
        'username': user['login']['username'],
        'password': user['login']['password'],
        'email': user['email']
    })
    processed_user.to_csv('/tmp/processed_user.csv', index=None, header=False)

with DAG(
    'user_processing', # dag_id는 unique 해야한다: 'user_processing'
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
    ) as dag:

    # define the task/operator

    creating_table = SqliteOperator(
        task_id='creating_table', # 하나의 pipeline에서 unique 해야함
        sqlite_conn_id='db_sqlite',
        sql='''
            CREATE TABLE users (
                firstname TEXT NOT NULL,
                lastname TEXT NOT NULL,
                country TEXT NOT NULL,
                username TEXT NOT NULL,
                password TEXT NOT NULL,
                email TEXT NOT NULL PRIMARY KEY
            );
            '''
    )

    is_api_available = HttpSensor(
        task_id='is_api_available',
        http_conn_id='user_api',
        endpoint='api/'
    )

    extracting_user = SimpleHttpOperator(
        task_id='extracting_user',
        http_conn_id='user_api',
        endpoint='api/',
        method='GET',
        response_filter=lambda response: json.loads(response.text),
        log_response=True
    )

    processing_user = PythonOperator(
        task_id='processing_user',
        python_callable=_processing_user
    )
```

- test
  - `airflow tasks test user_processing is_api_available 2022-04-23`

test를 진행하면 processed_user.csv가 생성된다. `cat /tmp/processed_user.csv`를 하면 아래와 같은 결과가 나온다.
```
Mercedes,Jimenez,Spain,goldenbutterfly721,darius,mercedes.jimenez@example.com
```

### `storing_user`
이제 `/home/airflow/airflow/airflow.db`에 user를 저장해보자. 처음에  `sqlite3 airflow.db`로 sqlite에 접속하고 `SELECT * FROM users;`하면 아무것도 없다가 나중에 `storing_user` task를 실행하면 그 유저가 들어간 것을 확인 할 수 있다.

### task 순서(dependency)정하기
`>>` 를 통해서 dependency를 알려준다.

```python
from airflow.models import DAG
from airflow.providers.sqlite.operators.sqlite import SqliteOperator
from airflow.providers.http.sensors.http import HttpSensor
from airflow.providers.http.operators.http import SimpleHttpOperator
from airflow.operators.python import PythonOperator
from airflow.operators.bash import BashOperator

from datetime import datetime
import json
from pandas import json_normalize

# default로 넣고 싶은 인자들
default_args = {
    'start_date': datetime(2022, 4, 20)
}

# extracting_user 를 통해 얻은 결과를 이용
# airflow tasks test user_processing extracting_user 2022-04-23
def _processing_user(ti):
    # extracting_user task의 결과가 metastore에 저장이 되고 이 결과를 pull 한다
    users = ti.xcom_pull(task_ids=['extracting_user'])
    if not len(users) or 'results' not in users[0]:
        raise ValueError('User is empty')
    user = users[0]['results'][0]
    # json_normalize로 dict를 pd.DataFrame으로 변환
    processed_user = json_normalize({
        'first_name': user['name']['first'],
        'lastname': user['name']['last'],
        'country': user['location']['country'],
        'username': user['login']['username'],
        'password': user['login']['password'],
        'email': user['email']
    })
    processed_user.to_csv('/tmp/processed_user.csv', index=None, header=False)

with DAG(
    'user_processing', # dag_id는 unique 해야한다: 'user_processing'
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
    ) as dag:

    # define the task/operator

    creating_table = SqliteOperator(
        task_id='creating_table', # 하나의 pipeline에서 unique 해야함
        sqlite_conn_id='db_sqlite',
        sql='''
            CREATE TABLE IF NOT EXISTS users (
                firstname TEXT NOT NULL,
                lastname TEXT NOT NULL,
                country TEXT NOT NULL,
                username TEXT NOT NULL,
                password TEXT NOT NULL,
                email TEXT NOT NULL PRIMARY KEY
            );
            '''
    )

    is_api_available = HttpSensor(
        task_id='is_api_available',
        http_conn_id='user_api',
        endpoint='api/'
    )

    extracting_user = SimpleHttpOperator(
        task_id='extracting_user',
        http_conn_id='user_api',
        endpoint='api/',
        method='GET',
        response_filter=lambda response: json.loads(response.text),
        log_response=True
    )

    processing_user = PythonOperator(
        task_id='processing_user',
        python_callable=_processing_user
    )

    storing_user = BashOperator(
        task_id='storing_user',
        bash_command='echo -e ".separator ","\n.import /tmp/processed_user.csv" | sqlite3 /home/airflow/airflow/airflow.db'
    )

    creating_table >> is_api_available >> extracting_user >> processing_user >> storing_user
```

## Reference
- [udemy: the complete hands on course to master apache airflow](https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow)
