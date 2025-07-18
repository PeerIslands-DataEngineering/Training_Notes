
# ✅ Apache Airflow Job Orchestration, Monitoring & Troubleshooting 

---

## 📦 1. What is Job Orchestration?

**Job orchestration** is the process of coordinating, scheduling, and managing multiple interdependent tasks to run in a defined order.

In Airflow, this is accomplished using:

* **DAGs** (Directed Acyclic Graphs)
* **Tasks and Operators**
* **Task Dependencies**
* **Schedules and Triggers**
* **Retries, SLAs, Alerts**

---

## 🔁 2. Orchestrating Jobs in Airflow

Let’s break down how to orchestrate data workflows using Airflow.

---

### ✅ 2.1 DAG Structure for Job Orchestration

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.email import EmailOperator
from airflow.operators.empty import EmptyOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'arulanandha',
    'retries': 2,
    'retry_delay': timedelta(minutes=5),
    'email_on_failure': True,
    'email': ['alerts@example.com'],
}

with DAG(
    dag_id='orchestrated_etl_pipeline',
    default_args=default_args,
    description='ETL job with orchestration and alerts',
    schedule_interval='@daily',
    start_date=datetime(2025, 7, 1),
    catchup=False,
    tags=['orchestration'],
) as dag:

    start = EmptyOperator(task_id='start')

    extract_data = BashOperator(
        task_id='extract_data',
        bash_command='echo "Extracting data..."'
    )

    transform_data = BashOperator(
        task_id='transform_data',
        bash_command='echo "Transforming data..."'
    )

    load_data = BashOperator(
        task_id='load_data',
        bash_command='echo "Loading data..."'
    )

    notify = EmailOperator(
        task_id='notify_success',
        to='team@example.com',
        subject='ETL Job Successful',
        html_content='<p>ETL pipeline executed successfully!</p>'
    )

    end = EmptyOperator(task_id='end')

    # Orchestration (task flow)
    start >> extract_data >> transform_data >> load_data >> notify >> end
```

---

### ✅ 2.2 Key Features to Use for Robust Orchestration

| Feature                  | Usage                                                  |
| ------------------------ | ------------------------------------------------------ |
| **Retries & Delays**     | Handles transient failures                             |
| **Task Timeouts**        | Avoids hanging/stuck tasks                             |
| **TaskGroup**            | Groups related tasks for clarity                       |
| **Branching**            | Executes conditional logic (e.g., if file exists)      |
| **SubDAGs** (deprecated) | Used earlier for modularity, now replaced by TaskGroup |

---

## 📊 3. Monitoring Airflow DAGs

Airflow offers **excellent monitoring** via its **Web UI**, **logs**, **metrics**, and **alerts**.

---

### 🖥️ 3.1 Airflow Web UI Views

| View              | Purpose                                                 |
| ----------------- | ------------------------------------------------------- |
| **Tree View**     | See task runs by DAG run over time (color-coded status) |
| **Graph View**    | See task dependencies with current status               |
| **Gantt View**    | Visualize timing and overlap of tasks                   |
| **Code View**     | See the source code of the DAG                          |
| **Logs**          | Access logs per task instance for debugging             |
| **Task Duration** | View how long tasks usually take over time              |

---

### 📘 3.2 Task States

| State          | Meaning                                   |
| -------------- | ----------------------------------------- |
| `success`      | Task completed successfully               |
| `failed`       | Task ran but encountered error            |
| `up_for_retry` | Failed and will be retried                |
| `skipped`      | Skipped due to branching or short-circuit |
| `queued`       | Waiting to be picked up by worker         |
| `running`      | Currently being executed                  |

---

### 📧 3.3 Email Alerts

Add in `default_args`:

```python
'email_on_failure': True,
'email_on_retry': False,
'email': ['alerts@example.com'],
```

Also ensure SMTP server is configured in `airflow.cfg`.

---

### 📈 3.4 Monitoring with Prometheus + Grafana (Advanced)

* Export Airflow metrics via [StatsD](https://airflow.apache.org/docs/apache-airflow/stable/logging-monitoring/metrics.html)
* Integrate with Prometheus using exporters
* Visualize via Grafana dashboards

---

## 🛠️ 4. Troubleshooting in Airflow

Airflow issues can occur at several levels: task, DAG, worker, scheduler.

---

### ⚠️ 4.1 DAG Not Showing in UI

| Issue                            | Fix                                                         |
| -------------------------------- | ----------------------------------------------------------- |
| Syntax error in DAG file         | Check logs at `$AIRFLOW_HOME/logs/scheduler/latest/*.log`   |
| File doesn't end with `.py`      | Rename file properly                                        |
| `dag_id` not registered globally | Ensure DAG is added to `globals()` if dynamically generated |
| Scheduler not running            | Start with `airflow scheduler`                              |

---

### ⛔ 4.2 Task Fails with Exception

Steps:

1. Open **Graph View** or **Tree View**
2. Click failed task → **View Log**
3. Inspect Python traceback or external service error

---

### 🕑 4.3 Task Never Starts (Stuck in `queued`)

| Cause               | Solution                                              |
| ------------------- | ----------------------------------------------------- |
| Workers not running | Check `airflow celery worker` or `airflow standalone` |
| Resources exhausted | Increase worker concurrency                           |
| DAG paused          | Unpause DAG in UI                                     |
| Dependency not met  | Check upstream task statuses                          |

---

### ⏳ 4.4 Task Takes Too Long or Hangs

Add `execution_timeout` to your task:

```python
transform_data = BashOperator(
    task_id='transform_data',
    bash_command='run_transform.sh',
    execution_timeout=timedelta(minutes=15)
)
```

---

### 🔁 4.5 DAG Runs Piling Up (Backfill Issues)

If `catchup=True`, DAG tries to "catch up" on all past schedules.

Fix: set `catchup=False`

```python
with DAG(
    ...,
    catchup=False
):
```

---

### 🧪 4.6 Debugging Custom PythonOperator

Use `provide_context=True` and print from the context:

```python
def debug_task(**kwargs):
    print("DAG run conf:", kwargs.get("dag_run").conf)
    print("Execution date:", kwargs.get("execution_date"))

task = PythonOperator(
    task_id="debug",
    python_callable=debug_task,
    provide_context=True
)
```

---

## ✅ Full Example: Monitored & Robust Orchestration

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.email import EmailOperator
from airflow.operators.empty import EmptyOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'arulanandha',
    'start_date': datetime(2025, 7, 1),
    'retries': 3,
    'retry_delay': timedelta(minutes=5),
    'email_on_failure': True,
    'email': ['dataops@example.com'],
}

with DAG(
    dag_id='robust_data_pipeline',
    default_args=default_args,
    schedule_interval='@daily',
    catchup=False,
    tags=['monitoring', 'orchestration'],
    description='Well-instrumented DAG with orchestration, retries, and alerts'
) as dag:

    start = EmptyOperator(task_id='start')

    extract = BashOperator(
        task_id='extract',
        bash_command='python3 /scripts/extract.py',
        execution_timeout=timedelta(minutes=10)
    )

    transform = BashOperator(
        task_id='transform',
        bash_command='python3 /scripts/transform.py',
        execution_timeout=timedelta(minutes=20)
    )

    load = BashOperator(
        task_id='load',
        bash_command='python3 /scripts/load.py',
        execution_timeout=timedelta(minutes=10)
    )

    notify_success = EmailOperator(
        task_id='notify_success',
        to='dataops@example.com',
        subject='DAG Success: robust_data_pipeline',
        html_content='<p>All tasks completed successfully.</p>'
    )

    end = EmptyOperator(task_id='end')

    start >> extract >> transform >> load >> notify_success >> end
```

---

## 📋 Summary Table

| Feature           | Description                                                 |
| ----------------- | ----------------------------------------------------------- |
| DAG Orchestration | Define task sequence, dependencies, retries, schedule       |
| Monitoring        | Use Tree, Graph, Gantt Views; logs; alerts; metrics         |
| Troubleshooting   | Handle issues like missing DAGs, stuck tasks, long runtimes |
| Alerting          | Use `EmailOperator`, `email_on_failure`, SLAs               |
| Timeout           | Use `execution_timeout` per task to kill hanging runs       |
