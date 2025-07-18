

## 🧩 1. What are Airflow Connections?

Airflow **Connections** are **saved credentials/configurations** that allow tasks to **authenticate** to external systems (e.g., databases, cloud services, APIs).

Connections are:

* Stored in the **Airflow metadata DB**
* Used by **Hooks and Operators** to connect to systems
* Created via the **Airflow Web UI**, **CLI**, or **environment variables**

---

### 🔑 Common Connection Types

| Connection Type   | Usage Example               |
| ----------------- | --------------------------- |
| `Postgres`        | Connect to PostgreSQL DB    |
| `MySQL`           | Connect to MySQL DB         |
| `Http`            | API calls to web services   |
| `Databricks`      | Submit jobs to Databricks   |
| `S3`, `AzureBlob` | Interact with cloud storage |
| `Google Cloud`    | Interact with GCP           |

---

### 📌 Anatomy of a Connection

Each connection has:

* **Conn Id**: unique name (e.g., `databricks_default`)
* **Conn Type**: e.g., `databricks`, `http`, `postgres`, etc.
* **Host**: URL or IP
* **Login / Password**: authentication credentials
* **Schema**: e.g., database name
* **Extra**: JSON with service-specific values (like tokens, parameters)

---

## ⚙️ 2. What are Hooks in Airflow?

**Hooks** are **Python interfaces** to external systems.
They:

* Use the connection defined in Airflow
* Provide methods for interacting with the system (run SQL, send request, etc.)
* Are used internally by Operators or can be called manually in PythonOperator

---

### ✅ Common Hook Classes

| Hook Class       | Purpose                            |
| ---------------- | ---------------------------------- |
| `PostgresHook`   | Run SQL on Postgres                |
| `HttpHook`       | Make HTTP API requests             |
| `S3Hook`         | Work with AWS S3                   |
| `DatabricksHook` | Submit jobs, queries to Databricks |

---

## 🧪 3. How Airflow Uses Connections and Hooks

```python
from airflow.hooks.postgres_hook import PostgresHook

def query_postgres():
    hook = PostgresHook(postgres_conn_id='my_postgres_conn')
    result = hook.get_records("SELECT COUNT(*) FROM users")
    print(result)
```

Above:

* `'my_postgres_conn'` is the **Connection ID**
* `PostgresHook` uses this to **connect and query** the DB

---

## 🔗 4. Creating Airflow Connections

### 🖥️ A. Via Web UI

1. Go to **Admin > Connections**
2. Click **+ Add a new record**
3. Fill in:

   * **Conn Id**: `databricks_default`
   * **Conn Type**: `Databricks`
   * **Host**: `https://<your-databricks-instance>.cloud.databricks.com`
   * **Extra** (JSON):

     ```json
     {
       "token": "your-databricks-personal-access-token"
     }
     ```

---

### 💻 B. Via CLI

```bash
airflow connections add 'databricks_default' \
    --conn-type databricks \
    --conn-host 'https://<your-instance>.cloud.databricks.com' \
    --conn-extra '{"token":"your-token"}'
```

---

## 🧠 5. Airflow + Databricks Integration

You can connect to **Azure Databricks** or **AWS Databricks** using:

* `DatabricksHook`: internally used by operators
* `DatabricksSubmitRunOperator`: submits a job or notebook
* `DatabricksRunNowOperator`: triggers an existing job

---

### ✅ A. Using `DatabricksSubmitRunOperator`

```python
from airflow import DAG
from airflow.providers.databricks.operators.databricks import DatabricksSubmitRunOperator
from datetime import datetime

default_args = {
    "owner": "airflow",
    "start_date": datetime(2025, 7, 1),
}

with DAG(
    dag_id="databricks_submit_example",
    default_args=default_args,
    schedule_interval=None,
    catchup=False
) as dag:

    notebook_task = DatabricksSubmitRunOperator(
        task_id="run_databricks_notebook",
        databricks_conn_id="databricks_default",
        existing_cluster_id="your-cluster-id",
        notebook_task={
            'notebook_path': '/Users/you@example.com/my_notebook',
            'base_parameters': {
                'param1': 'value1',
                'param2': 'value2'
            }
        }
    )
```

---

### 🛠️ B. Using `DatabricksHook` Directly (Advanced)

```python
from airflow.providers.databricks.hooks.databricks import DatabricksHook

def run_databricks_job(**kwargs):
    hook = DatabricksHook(databricks_conn_id='databricks_default')
    job_config = {
        'existing_cluster_id': 'your-cluster-id',
        'notebook_task': {
            'notebook_path': '/Users/you@example.com/my_notebook',
            'base_parameters': {'param1': 'abc'}
        }
    }
    run_id = hook.submit_run(json=job_config)
    print(f"Run ID: {run_id}")

# Use inside a PythonOperator
from airflow.operators.python import PythonOperator

task = PythonOperator(
    task_id="hook_based_job_submit",
    python_callable=run_databricks_job,
    provide_context=True,
    dag=dag
)
```

---

## 🧾 Summary

| Concept                    | Description                                                              |
| -------------------------- | ------------------------------------------------------------------------ |
| **Connections**            | Store credentials (e.g., API keys, DB creds) in UI or CLI                |
| **Hooks**                  | Interface classes to external services using connections                 |
| **Databricks Integration** | Use `DatabricksHook`, `DatabricksSubmitRunOperator`, or `RunNowOperator` |
| **Airflow + Databricks**   | Submit notebooks/jobs to Databricks securely via Airflow                 |
| **Conn ID**                | Unique reference name (e.g., `databricks_default`)                       |

---

## ✅ Best Practices

| Practice                                                                | Why it helps                  |
| ----------------------------------------------------------------------- | ----------------------------- |
| Use Connection IDs instead of hardcoding credentials                    | Secures sensitive information |
| Name Conn IDs consistently (`databricks_default`, `azure_blob_storage`) | Easy to remember              |
| Use `Extra` JSON field for tokens and job config                        | Avoids clutter in code        |
| Prefer `DatabricksSubmitRunOperator` for new tasks                      | Clean and declarative         |

---
