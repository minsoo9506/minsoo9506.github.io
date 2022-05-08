# [Airflow] Advanced Concepts in Airflow


Airflow에 대한 좀 더 advanced한 내용을 공부해보자.

<!--more-->
## Remove repetive patterns with SubDags
반복적인 task의 경우 좀 더 깔끔한 패턴을 만들기 위해 subdags라는 개념을 이용할 수 있다. 이전에 봤던 예시인 `task_1 >> [task_2, task_3] >> task_4` 에서 `task_2`, `task_3`를 묶어보자.

`SubDagOperator`는 인자로 subdag을 return하는 함수가 필요하다. 이를 위해 일단 `dags` 폴더에 `subdags`라는 폴더를 만든다. 그리고 그 폴더에 subdag_parallel_dag.py 파일을 만들고 함수를 만들어보자. 해당함수는 dag을 return하는 함수여야 한다.

```python
from airflow import DAG

def subdag_parallel_dag(parent_dag_id, child_dag_id, default_args):
    with DAG(dag_id=f'{parent_dag_id}.{child_dag_id}', default_args=default_args) as dag:

        task_2 = BashOperator(
            task_id='task_2',
            bash_command='sleep 3'
        )

        task_3 = BashOperator(
            task_ud='task_3',
            bash_command='sleep 3'
        )

    return dag
```

그리고 이 함수를 import해서 아래와 같이 DAG를 만들어준다.

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.subdag import SubDagOperator

from subdags.subdag_parallel_dag import subdag_parallel_dag
from datetime import datetime

default_args = {
    'start_date': datetime(2022, 4, 23)
}

with DAG(
    'parallel_subdag',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 3'
    )
    
    processing = SubDagOperator(
        task_id='processing_tasks',
        subdag=subdag_parallel_dag('parallel_subdag', 'processing_tasks', default_args)
    )

    task_4 = BashOperator(
        task_id='task_4',
        bash_command='sleep 3'
    )

    task_1 >> processing >> task_4
```

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/airflow03.png?raw=true"  width="500">
</center>

이렇게 subdag으로 task를 묶을 수 있다. 하지만 subdag을 사용하는 것을 추천하지는 않는다고 한다. 이유는 Deadlock, complexity, subdag은 sequential excutor를 default로 사용 이라고 한다.

## SubDags가 아닌 TaskGroup를 사용해보자
```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.utils.task_group import TaskGroup

from subdags.subdag_parallel_dag import subdag_parallel_dag
from datetime import datetime

default_args = {
    'start_date': datetime(2022, 4, 23)
}

with DAG(
    'parallel_subdag',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 3'
    )

    with TaskGroup('processing_tasks') as processing_tasks:
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

    task_1 >> processing_tasks >> task_4
```
`TaskGroup`을 사용하면 따로 함수를 만들 필요도 없어서 편하다.

## Share data with XCom
XCom을 이용하여 task끼리 data를 주고받을 수 있다. Airflow에서 어떤 database를 쓰냐에 따라 data의 크기도 정해져 있다. 다만, 작은 양의 data를 사용하기를 추천한다. task를 통해 생성된 값은 key, value 형태로 보존된다.

예시를 통해 이해해보자. 머신러닝 모델을 3개 만들어보고 가장 성능이 좋은 모델을 선택하는 pipeline을 만들고 싶다. 아래 코드에서는 `processing_tasks`에서 모델을 훈련시키고 성능점수를 `choose_model`에서 확인하고 있다.

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator
from airflow.operators.task_group import TaskGroup

from random import uniform
from datetime import datetime

default_args = {
    'start_date': datetime(2022, 5, 1)
}

def _training_model(ti):
    acc = uniform(0.1, 10.0)
    print(f'model\'s acc = {acc}')
    # database에 push
    ti.xcom_push(key='model_acc', value=acc)

def _choose_best_model(ti):
    print('choose best model')
    # database에서 pull
    accs = ti.xcom_pull(key='model_acc', task_ids=[
        'processing_tasks.training_model_a',
        'processing_tasks.training_model_b',
        'processing_tasks.training_model_c',
    ])
    # webserver나 terminal에서 확인 할 수 있다
    print(accs)

with DAG(
    'xcom_dag',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:


    downloading_data = BashOperator(
        task_id='downloading_data',
        bash_command='sleep 3'
    )

    with TaskGroup('processing_tasks') as processing_tasks:
        training_model_a = PythonOperator(
            task_id='training_model_a',
            python_callable=_training_model
        )

        training_model_b = PythonOperator(
            task_id='training_model_b',
            python_callable=_training_model
        )

        training_model_c = PythonOperator(
            task_id='training_model_c',
            python_callable=_training_model
        )

    choose_model = PythonOperator(
        task_id='task_4',
        python_callable=_choose_best_model
    )

    downloading_data >> processing_tasks >> choose_model
```

webserver에서 Admin -> xcom 에서 결과를 확인할 수 있다. 로그를 보면 `accs`가 print된 것을 확인할 수 있다.

## 특정 condition에 따라 실행할 task 선택하기
아래 코드예시처럼 return 값에 다음에 진행할 task_id를 넣어주면 된다.
```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator, BranchPythonOperator
from airflow.operators.dummy import DummyOperator
from airflow.utils.task_group import TaskGroup

from random import uniform
from datetime import datetime

default_args = {
    'start_date': datetime(2022, 5, 1)
}

def _training_model(ti):
    acc = uniform(0.1, 10.0)
    print(f'model\'s acc = {acc}')
    # database에 push
    ti.xcom_push(key='model_acc', value=acc)

def _choose_best_model(ti):
    print('choose best model')
    # database에서 pull
    accs = ti.xcom_pull(key='model_acc', task_ids=[
        'processing_tasks.training_model_a',
        'processing_tasks.training_model_b',
        'processing_tasks.training_model_c',
    ])
    # 조건에 맞는 task를 return하게 한다
    for acc in accs:
        if acc > 5:
            return 'accurate' # ['accurate', 'inaccurate'] 이런식으로 여러개도 가능
    return 'inaccurate'

with DAG(
    'xcom_dag',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:


    downloading_data = BashOperator(
        task_id='downloading_data',
        bash_command='sleep 3',
        # default로 push를 하기 때문에 이런식으로 못하게 할 수 있다
        # False안하고 xcom을 보면 key, value에 아무것도 없음을 알 수 있다
        do_xcom_push=False
    )

    with TaskGroup('processing_tasks') as processing_tasks:
        training_model_a = PythonOperator(
            task_id='training_model_a',
            python_callable=_training_model
        )

        training_model_b = PythonOperator(
            task_id='training_model_b',
            python_callable=_training_model
        )

        training_model_c = PythonOperator(
            task_id='training_model_c',
            python_callable=_training_model
        )

    choose_model = BranchPythonOperator(
        task_id='task_4',
        python_callable=_choose_best_model
    )

    accurate = DummyOperator(
        task_id='accurate'
    )
    inaccurate = DummyOperator(
        task_id='inaccurate'
    )

    downloading_data >> processing_tasks >> choose_model
    choose_model >> [accurate, inaccurate]
```

## Trigger Rules
task가 trigger되는 default값을 바꿔서 특정 조건이 되면 trigger되도록 (사용자가 원하는대로) 할 수 있다. task마다 `trigger_rule`을 통해서 규칙을 정해주면 된다.

```python
from airflow import DAG
from airflow.operators.bash import BashOperator

from datetime import datetime

default_args = {
    'start_date': datetime(2022, 5, 1)
}

with DAG(
    'trigger_rule',
    schedule_interval='@daily',
    default_args=default_args,
    catchup=False
) as dag:
    task_1 = BashOperator(
        task_id='task_1',
        bash_command='exit 1',
        do_xcom_push=False
    )
    task_2 = BashOperator(
        task_id='task_2',
        bash_command='exit 0',
        do_xcom_push=False
    )
    task_3 = BashOperator(
        task_id='task_3',
        bash_command='exit 0',
        do_xcom_push=False,
        # 이렇게 지정가능
        trigger_rule='one_failed'
    )

    [task_1, task_2] >> task_3
```

## Reference
- [udemy: the complete hands on course to master apache airflow](https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow)
