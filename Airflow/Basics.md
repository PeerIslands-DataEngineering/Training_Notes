
## 🌟 1. Introduction to Apache Airflow

**Apache Airflow** is an open-source platform for authoring, scheduling, and monitoring workflows using Python.

It allows you to define your data pipelines as **DAGs** (Directed Acyclic Graphs) composed of **Tasks**. Airflow is commonly used for:

* ETL processes
* Machine Learning pipelines
* Data validation and reporting jobs
* Workflow automation

### ✨ Key Characteristics

| Feature                     | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| **Python as configuration** | Workflows are defined in Python scripts.                   |
| **Dynamic workflows**       | DAGs can be generated programmatically.                    |
| **Scheduler & Executor**    | Schedules and runs tasks based on time or external events. |
| **Extensible**              | Supports custom operators, sensors, hooks.                 |
| **Web UI**                  | Monitor, trigger, debug DAGs using a web interface.        |

---

## 🧭 2. DAGs (Directed Acyclic Graphs)

A **DAG** represents the structure of your pipeline. It defines how tasks run — in what order and with what schedule.

### 📌 DAG Structure

```python
from airflow import DAG
from airflow.utils.dates import days_ago
from datetime import timedelta

default_args = {
    'owner': 'arulanandha',
    'retries': 2,
    'retry_delay': timedelta(minutes=5),
}

dag = DAG(
    dag_id='sample_dag',
    default_args=default_args,
    description='A sample DAG',
    schedule_interval='@daily',  # or cron expression
    start_date=days_ago(1),
    catchup=False,
    tags=['demo'],
)
```

### 🔍 Key Parameters

| Parameter           | Description                                  |
| ------------------- | -------------------------------------------- |
| `dag_id`            | Unique identifier for the DAG                |
| `schedule_interval` | Cron or preset (`@hourly`, `@daily`, `None`) |
| `start_date`        | First date the DAG is eligible to run        |
| `catchup`           | Whether to catch up on missed intervals      |
| `default_args`      | Common parameters passed to tasks            |

---

## ⚙️ 3. Operators – The Workhorses of Airflow

**Operators** define what a single task actually does. Airflow provides a wide range of built-in operators.

---

### 🔧 3.1 BashOperator

Executes a Bash command or script.

```python
from airflow.operators.bash import BashOperator

print_date = BashOperator(
    task_id='print_date',
    bash_command='date',
    dag=dag
)
```

---

### 🐍 3.2 PythonOperator

Executes a Python function.

```python
from airflow.operators.python import PythonOperator

def say_hello():
    print("Hello, Airflow!")

greet_task = PythonOperator(
    task_id='greet',
    python_callable=say_hello,
    dag=dag
)
```

---

### 📧 3.3 EmailOperator

Sends an email. Requires SMTP configuration.

```python
from airflow.operators.email import EmailOperator

email_task = EmailOperator(
    task_id='send_email',
    to='user@example.com',
    subject='Airflow Alert',
    html_content='<h3>Your task has completed!</h3>',
    dag=dag
)
```

---

### 🧪 3.4 DummyOperator

Used for structuring or branching without doing actual work.

```python
from airflow.operators.empty import EmptyOperator

start = EmptyOperator(task_id='start', dag=dag)
end = EmptyOperator(task_id='end', dag=dag)
```

---

### 🔀 3.5 BranchPythonOperator

Decides which path to follow based on condition.

```python
from airflow.operators.python import BranchPythonOperator

def pick_branch():
    return 'branch_a' if True else 'branch_b'

branching = BranchPythonOperator(
    task_id='branching_task',
    python_callable=pick_branch,
    dag=dag
)
```

---

### 🔍 3.6 ShortCircuitOperator

Skips downstream tasks if a condition is not met.

```python
from airflow.operators.python import ShortCircuitOperator

def should_continue():
    return False  # All downstream tasks will be skipped

short_circuit = ShortCircuitOperator(
    task_id='check_condition',
    python_callable=should_continue,
    dag=dag
)
```

---

### 🧠 3.7 TriggerDagRunOperator

Triggers another DAG from a DAG.

```python
from airflow.operators.trigger_dagrun import TriggerDagRunOperator

trigger_another = TriggerDagRunOperator(
    task_id="trigger_other_dag",
    trigger_dag_id="other_dag_id",
    dag=dag
)
```

---

### 🗂️ 3.8 FileSensor (from Sensors)

Waits for a file to appear at a location.

```python
from airflow.sensors.filesystem import FileSensor

wait_for_file = FileSensor(
    task_id='wait_for_input_file',
    filepath='/tmp/my_input.csv',
    poke_interval=10,
    timeout=300,
    dag=dag
)
```

---

## 🔗 4. Task Dependencies

### 4.1 Using `>>` and `<<` (Bitshift Operators)

```python
start >> print_date >> greet_task >> email_task >> end
```

This implies:

```
start → print_date → greet_task → email_task → end
```

---

### 4.2 Using `set_upstream()` and `set_downstream()`

```python
greet_task.set_downstream(email_task)  # greet_task → email_task
email_task.set_upstream(greet_task)    # same as above
```

---

### 4.3 Complex DAG Dependencies

```python
start >> [print_date, greet_task]  # run in parallel
[print_date, greet_task] >> email_task >> end
```

Here:

* `print_date` and `greet_task` run concurrently
* Once both complete, `email_task` runs

---

### ✅ Full Example

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator
from airflow.operators.empty import EmptyOperator
from airflow.operators.email import EmailOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'arulanandha',
    'start_date': datetime(2025, 7, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=2),
}

with DAG(
    dag_id='detailed_example_dag',
    default_args=default_args,
    description='An end-to-end Airflow DAG example',
    schedule_interval='@daily',
    catchup=False
) as dag:

    start = EmptyOperator(task_id='start')

    print_date = BashOperator(
        task_id='print_date',
        bash_command='date'
    )

    def hello_world():
        print("Hello World from Airflow!")

    greet = PythonOperator(
        task_id='greet',
        python_callable=hello_world
    )

    list_files = BashOperator(
        task_id='list_files',
        bash_command='ls -l'
    )

    email = EmailOperator(
        task_id='send_email',
        to='your@email.com',
        subject='DAG Complete',
        html_content='<h3>All tasks completed successfully.</h3>'
    )

    end = EmptyOperator(task_id='end')

    # Set Dependencies
    start >> [print_date, greet]
    [print_date, greet] >> list_files >> email >> end
```

---

## 📚 Summary Table

| Component  | Description                                                           |
| ---------- | --------------------------------------------------------------------- |
| DAG        | Defines workflow structure, schedule, and metadata                    |
| Operator   | Defines what the task does (Bash, Python, Email, etc.)                |
| Task       | An instance of an operator                                            |
| Dependency | Order of execution using `>>`, `<<`, or `set_*stream()` methods       |
| Schedule   | Controls when the DAG is triggered (`@daily`, `@hourly`, cron string) |
| Start/End  | Typically defined using `EmptyOperator` for clarity in UI             |

---
