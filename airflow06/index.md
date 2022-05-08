# [Airflow] Use plugin in Airflow


Airflow에서 plugin을 이용하여 사용반경을 넓혀보자.

<!--more-->
먼저 Elasticsearch를 설치해보자. 
```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

# 그리고 비밀번호 친다

echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

sudo apt update && sudo apt install elasticsearch

# 설치
pip install elasticsearch==7.10.1

# 시작
sudo systemctl start elasticsearch

# 잘 되는지 확인
curl -X GET 'http://localhost:9200'
```

## Plugin
- 사용자만의 own
    - operators를 커스텀
    - views를 통해서 원하는 ui 만들기
    - hooks을 통해서 서드파티를 이용가능

직접 원하는대로 python module을 만들어서 사용할 수 있다.

이제 Elasticsearch와 interact할 수 있는 plugin을 만들어보자. postgreSQL에서 Elasticsearch로 data를 보내는 hook과 operator를 아래와 같이 만들 수 있다.

먼저, plugins(이름을 잘 지켜야함)라는 폴더를 만들고 그 안에 elasticsearch_plugin이라는 폴더를 만든다. 그리고 그 폴더안에 hooks, operators라는 폴더를 만든다. 그리고 각 폴더에 `elastic_hook.py`, `postgres_to_elastic.py`라는 파일을 만든다. 두 파일 모두 Airflow에 있는 Base hook, operator를 상속받아서 class를 만들어 사용하는 코드를 담고 있다.

```python
from airflow.hooks.base import BaseHook
from elasticsearch import Elasticsearch

class ElasticHook(BaseHook):
    def __init__(
        self,
        conn_id='elasticsearch_default',
        *args,
        **kwargs
        ):
        super().__init__(*args, **kwargs)
        
        # BaseHook에 있는 함수
        conn = self.get_conntetion(conn_id)

        conn_config = {}
        hosts = []

        if conn.host:
            hosts = conn.host.split(',')
        if conn.port:
            conn_config['port'] = int(conn.port)
        if conn.login:
            conn_config['http_auth'] = (conn.login, conn.password)

        self.es = Elasticsearch(hosts, **conn_config)
        self.index = conn.schema

    def info(self):
        return self.es.info()

    def set_index(self, index):
        self.index = index

    def add_doc(self, index, doc_type, doc):
        self.set_index(index)
        res = self.es.index(index=index, doc_type=doc_type, body=doc)
        return res
```

```python
from airflow.models import BaseOperator
from airflow.providers.postgres.hooks.postgres import PostgresHook
from elasticsearch_plugin.hooks.elastic_hook import ElasticHook

from contextlib import closing
import json

class PostgresToElasticOperator(BaseOperator):
    def __init__(
        self,
        sql,
        index,
        postgres_conn_id='postgres_default',
        elastic_conn_id='elasticsearch_default',
        *args,
        **kwargs
        ):
        super(PostgresToElasticOperator, self).__init__(*args, **kwargs)

        self.sql = sql
        self.index = index
        self.postgres_conn_id = postgres_conn_id
        self.elastic_conn_id = elastic_conn_id

    def execute(self, context):
        es = ElasticHook(conn_id=self.elastic_conn_id)
        pg = PostgreHook(postgres_conn_id=self.postgres_conn_id)
        with closing(pg.get_conn()) as conn:
            with closing(conn.cursor()) as cur:
                cur.itersize = 1000
                cur.execute(self.sql)
                for row in cur:
                    doc = json.dumps(row, indent=2)
                    es.add_doc(index=self.index, doc_type='external', doc=doc)
```

그리고 dag을 만들어준다.
```python
from airflow import DAG
from elasticsearch_plugin.hooks.elastic_hook import ElasticHook
from elasticsearch_plugin.operators.postgres_to_elastic import PostgresToElasticOperator
from airflow.operators.python import PythonOperator
from datetime import datetime

default_args = {
    'start_date': datetime(2022, 5, 5)
}

def _print_es_info():
    hook = ElasticHook()
    print(hook.info())

with DAG(
    'elastcisearch_dag',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:

    print_es_info = PythonOperator(
        task_id=-'print_es_info',
        python_callable=_print_es_info
    )

    connections_to_es = PostgresToElasticOperator(
        task_id='connections_to_es',
        sql='SELECT * FROM connection',
        index='connections'
    )

    print_es_info >> connections_to_es
```

web에서 connection을 추가해준다.
<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/airflow04.png?raw=true"  width="500">
</center>

그리고 postgresql에 들어가서 user를 추가해준다.
```bash
sudo -u postgres psql
ALTER USER postgres PASSWORD 'postgres';
```

이제 dag을 실행시키고 아래 명령어를 통해 postgreSQL에서 Elasticsearch로 데이터를 보낸 것을 확인할 수 있다.

```bash
curl -X GET "http://localhost:9200/connections/_search" -H "Content -type: application/json" -d '{"query":{"match_all":{}}}'
```

## Reference
- [udemy: the complete hands on course to master apache airflow](https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow)
