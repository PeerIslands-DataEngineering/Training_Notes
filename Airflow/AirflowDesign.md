
# ✅ Apache Airflow Advanced Tutorial

Includes:

1. **DAG Design Best Practices**
2. **Dynamic DAGs**
3. **Parameterized DAGs**
4. **Monitoring and Troubleshooting**

---

## 🧩 1. DAG Design Best Practices

Designing DAGs well ensures readability, performance, and maintainability.

### 🔑 Key Principles

| Principle                            | Explanation                                                                                |
| ------------------------------------ | ------------------------------------------------------------------------------------------ |
| **Idempotency**                      | DAG runs should be repeatable with the same result.                                        |
| **Small tasks**                      | Prefer small, focused tasks (e.g., extract, transform, load) instead of one large task.    |
| **Task dependencies only for logic** | Use dependencies to define execution logic, not data flow.                                 |
| **Avoid top-level code**             | DAG files are parsed many times by the scheduler — avoid placing heavy logic at top-level. |
| **Group tasks logically**            | Use TaskGroups to improve UI readability.                                                  |
| **Retries and timeouts**             | Always configure `retries`, `retry_delay`, and `execution_timeout`.                        |
| **No external I/O in DAG file**      | Avoid DB connections or file reads at DAG definition time.                                 |

### ✅ Example Best-Practice DAG Skeleton

```python
from airflow import DAG
from airflow.operators.empty import EmptyOperator
from airflow.operators.bash import BashOperator
from datetime import datetime, timedelta

default_args = {
    "owner": "arulanandha",
    "retries": 1,
    "retry_delay": timedelta(minutes=5),
}

with DAG(
    dag_id="best_practice_dag",
    default_args=default_args,
    schedule_interval="@daily",
    start_date=datetime(2025, 7, 1),
    catchup=False,
    description="Best practices applied",
    tags=["example"],
) as dag:

    start = EmptyOperator(task_id="start")

    extract = BashOperator(
        task_id="extract",
        bash_command="echo 'Extracting...'",
    )

    transform = BashOperator(
        task_id="transform",
        bash_command="echo 'Transforming...'",
    )

    load = BashOperator(
        task_id="load",
        bash_command="echo 'Loading...'",
    )

    end = EmptyOperator(task_id="end")

    start >> extract >> transform >> load >> end
```

---

## 🧬 2. Dynamic DAGs

Dynamic DAGs are created programmatically to reduce repetition and improve maintainability.

---

### 🔁 Use Case

You need to create 10 DAGs to ingest data from 10 sources. Instead of writing 10 separate files, you generate them in a loop.

### ✅ Example: Creating Multiple DAGs Programmatically

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.utils.dates import days_ago

def generate_dag(dag_id, source):
    with DAG(
        dag_id=dag_id,
        schedule_interval="@daily",
        start_date=days_ago(1),
        catchup=False,
    ) as dag:
        ingest = BashOperator(
            task_id="ingest_data",
            bash_command=f"echo 'Ingesting from {source}'"
        )
        return dag

# Dynamically create DAGs
for source in ['mysql', 'postgres', 'mongodb']:
    dag_id = f"ingest_{source}_dag"
    globals()[dag_id] = generate_dag(dag_id, source)
```

> ⚠️ Use `globals()` carefully — it registers DAGs with the scheduler.

---

## 🧰 3. Parameterized DAGs (Using DAG Configuration)

Parameterized DAGs allow input via the UI or API at runtime (a **manual run**).

---

### 🔑 When to Use:

* Different inputs for the same logic
* Different datasets, dates, configurations

---

### ✅ Example: Accessing Config Parameters in PythonOperator

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def process_data(**context):
    dag_run_conf = context['dag_run'].conf
    dataset = dag_run_conf.get('dataset', 'default_dataset')
    print(f"Processing dataset: {dataset}")

with DAG(
    dag_id='parameterized_dag',
    start_date=datetime(2025, 7, 1),
    schedule_interval=None,  # Trigger manually
    catchup=False
) as dag:

    task = PythonOperator(
        task_id='run_with_param',
        python_callable=process_data,
        provide_context=True,
    )
```

### 🧪 Triggering with Parameters (from UI or API)

```json
{
  "dataset": "sales_2025_q1"
}
```

> From the UI: Go to DAG → Trigger DAG → Add JSON conf.

---

## 🖥️ 4. Monitoring and Troubleshooting

Airflow provides rich tools for tracking DAG execution and handling errors.

---

### 🔍 Monitoring Tools in Airflow UI

| Tool           | Description                                                                    |
| -------------- | ------------------------------------------------------------------------------ |
| **Tree View**  | Shows DAG execution over time with task status (green = success, red = failed) |
| **Graph View** | Shows the task graph and its status                                            |
| **Gantt View** | Shows task duration and overlap                                                |
| **Task Logs**  | View stdout, stderr, and exception logs for each task                          |
| **Code View**  | View DAG source code in the UI                                                 |

---

### ⚠️ Common Issues and Solutions

#### 1. Task Fails with Exception

* **What to do**: Go to Graph View → Click Task → View Log
* **Check**: Stack trace, environment variables, DAG file logic

#### 2. DAG Not Showing in UI

* Check the DAG folder is configured correctly
* DAG file has syntax error
* `dag_id` not globally registered
* File does not end with `.py`

#### 3. Task Stuck in Queued

* Scheduler not running?
* Worker slot not available?
* Dependencies not satisfied?

#### 4. Catchup Causing Huge Backfill

* Add `catchup=False` in DAG definition

---

### 🧠 Best Practices for Monitoring

| Best Practice                     | Benefit                     |
| --------------------------------- | --------------------------- |
| Enable `email_on_failure`         | Get notified if task fails  |
| Use `max_active_runs` in DAG      | Prevent DAG flooding        |
| Add `execution_timeout` per task  | Detect and kill stuck tasks |
| Tag your DAGs                     | Helps filtering in UI       |
| Log external API responses/errors | Easier to debug issues      |

---

### ✅ Example: Adding Monitoring Settings

```python
default_args = {
    'owner': 'arulanandha',
    'email': ['alerts@company.com'],
    'email_on_failure': True,
    'retries': 2,
    'retry_delay': timedelta(minutes=10),
    'execution_timeout': timedelta(minutes=30),
}
```
