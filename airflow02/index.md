# [Airflow] 기본 CLI 명령어


Airflow의 기본적인 CLI 명령어를 정리
<!--more-->

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
