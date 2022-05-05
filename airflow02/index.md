# [Airflow] 기본 구조, CLI 명령어


Airflow의 기본적인 구조와 CLI 명령어를 정리
<!--more-->
## 구조
- webserver
- scheduler
  - 설정한 시간에 맞춰 DAG를 실행할 수 있게 해줌
- worker(excecutor)
  - 실제 작업을 실행하는 주체
- metaDB
  - 작업관련 데이터들이 저장됨

## 명령어
### `db`
- `airflow db init`
  - 처음 시작할 때만 사용해야함
  - airflow에 필요한 file, data들을 생성
- `airflow db upgrade`
- `airflow db reset`

### `webserver`
- `airflow webserver`
  - webserver를 실행

### `dag`
- `airflow dags list`
  - 아래처럼 dag에 대한 정보를 보여준다'
```
dag_id                             | filepath                          | owner   | paused
===================================+===================================+=========+=======
example_bash_operator              | /home/airflow/sandbox/lib/python3 | airflow | True  
                                   | .8/site-packages/airflow/example_ |         |       
                                   | dags/example_bash_operator.py     |         |      
```

### `task`
- `airflow tasks list dag이름`
  - 해당 dag의 task의 이름을 보여준다

## Reference
- [udemy: the complete hands on course to master apache airflow](https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow)
