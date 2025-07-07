

## 🔷 1. Spark Cluster Manager 

A **Cluster Manager** is responsible for managing resources and scheduling jobs in a distributed Spark environment.

### Common Cluster Managers:

| Cluster Manager | Description                          |
| --------------- | ------------------------------------ |
| **Standalone**  | Spark’s own built-in cluster manager |
| **YARN**        | Hadoop ecosystem resource manager    |
| **Mesos**       | General-purpose cluster manager      |
| **Kubernetes**  | Container-based cluster manager      |

---

## 🔷 2. Spark Cluster Components

| Component           | Description                                                             |
| ------------------- | ----------------------------------------------------------------------- |
| **Driver**          | Runs `main()` function, creates SparkSession, and manages job execution |
| **Cluster Manager** | Allocates resources for the application                                 |
| **Executors**       | Worker processes that run tasks and store data                          |

---

## 🔷 3. Cluster Modes in Spark

| Mode                   | Description                            |
| ---------------------- | -------------------------------------- |
| **Local**              | All in a single JVM (testing/dev)      |
| **Standalone Cluster** | One master, multiple workers           |
| **YARN Client Mode**   | Driver runs on client (user’s machine) |
| **YARN Cluster Mode**  | Driver runs inside the cluster         |
| **Kubernetes**         | Spark pods run in a Kubernetes cluster |

---

## 🔷 4. Key Cluster Configuration Parameters

### ➤ Application Level

```python
SparkSession.builder \
  .appName("MyApp") \
  .master("yarn") \
  .config("spark.executor.memory", "4g") \
  .config("spark.driver.memory", "2g") \
  .config("spark.executor.cores", "2") \
  .config("spark.executor.instances", "5") \
  .getOrCreate()
```

---

## 🔷 5. Driver Configuration

### Role:

* Coordinates job scheduling.
* Collects results from executors.
* Maintains DAG, stages, tasks.

### Key Configs:

| Config                       | Description                            | Example |
| ---------------------------- | -------------------------------------- | ------- |
| `spark.driver.memory`        | Memory for driver process              | `"2g"`  |
| `spark.driver.cores`         | Cores used by driver (Standalone/YARN) | `"1"`   |
| `spark.driver.maxResultSize` | Max size of result from executors      | `"1g"`  |

#### YAML-style Configuration Example (for `spark-defaults.conf`)

```properties
spark.driver.memory 2g
spark.driver.cores 1
spark.driver.maxResultSize 1g
```

---

## 🔷 6. Executor Configuration

### Role:

* Perform actual task execution.
* Hold block of data in memory or disk.
* One JVM per executor.

### Key Configs:

| Config                     | Description                             | Example |
| -------------------------- | --------------------------------------- | ------- |
| `spark.executor.instances` | Number of executors                     | `"10"`  |
| `spark.executor.memory`    | RAM per executor                        | `"4g"`  |
| `spark.executor.cores`     | CPU cores per executor                  | `"2"`   |
| `spark.memory.fraction`    | Fraction of executor memory for caching | `0.6`   |

#### Memory Allocation Formula:

```
Executor Total Memory = spark.executor.memory
Usable Memory for Storage/Cache = spark.memory.fraction * executor.memory
```

### Tip:

Use more executors with fewer cores (recommended for parallelism).

---

## 🔷 7. Advanced Resource Management

### Configs:

| Config                                     | Description            |
| ------------------------------------------ | ---------------------- |
| `spark.dynamicAllocation.enabled`          | Auto-scale executors   |
| `spark.dynamicAllocation.minExecutors`     | Min executors          |
| `spark.dynamicAllocation.maxExecutors`     | Max executors          |
| `spark.dynamicAllocation.initialExecutors` | Initial executor count |

---

## 🔷 8. Cluster Manager Examples

### ✳ Standalone Mode

Start master and workers:

```bash
./sbin/start-master.sh
./sbin/start-worker.sh spark://<master-url>:7077
```

Submit job:

```bash
spark-submit \
  --master spark://<master-url>:7077 \
  --executor-memory 4g \
  --total-executor-cores 8 \
  app.py
```

---

### ✳ YARN Mode

```bash
spark-submit \
  --master yarn \
  --deploy-mode cluster \
  --executor-memory 4g \
  --executor-cores 2 \
  --num-executors 5 \
  app.py
```

---

### ✳ Kubernetes Mode

```bash
spark-submit \
  --master k8s://https://<k8s-master>:6443 \
  --deploy-mode cluster \
  --name spark-k8s \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=5 \
  --conf spark.kubernetes.container.image=<image> \
  local:///opt/spark/examples/jars/spark-examples_2.12-3.1.2.jar
```

---

## 🔷 9. Spark UI and Monitoring

Access Spark Web UI (typically):

* Standalone: `http://<master>:4040`
* YARN: ResourceManager UI
* Kubernetes: via pod logs or Grafana/Prometheus setup

Spark UI helps track:

* Stages, tasks
* Executor memory/cpu usage
* Shuffle read/write
* DAG visualization

---

## 🔷 10. Performance Tuning Tips

* Prefer more executors with fewer cores for better parallelism.
* Avoid under-utilization: ensure CPU and memory are not idle.
* Use **dynamic allocation** to optimize resource use.
* Tune **shuffle partitions** with `spark.sql.shuffle.partitions`.
* Use **broadcast joins** for small dimension tables.

---

## 🔷 11. Useful Spark Submit Options

| Option              | Description           |
| ------------------- | --------------------- |
| `--master`          | Cluster manager       |
| `--deploy-mode`     | client or cluster     |
| `--executor-memory` | Memory per executor   |
| `--executor-cores`  | Cores per executor    |
| `--num-executors`   | Total executors       |
| `--driver-memory`   | Driver memory         |
| `--conf`            | Set any configuration |

---

## 🔷 12. Real-World Example

> **Problem:** Load 100 GB data, process with Spark on YARN, minimize latency.

### Suggested Configuration:

```python
--executor-memory 8g \
--executor-cores 3 \
--num-executors 20 \
--driver-memory 4g \
--conf spark.sql.shuffle.partitions=200
```

* Total Cores: 3 x 20 = 60
* Memory: 8 GB x 20 = 160 GB
* Optimize shuffle partitions for joins/aggregations

---
