
## 🔷 1. What is PySpark Streaming?

**PySpark Streaming** is a scalable, high-throughput, fault-tolerant stream processing system built on top of **Spark Core**. It allows you to process **real-time data streams** using the same code base and Spark APIs used for batch processing.

PySpark Streaming processes data in **micro-batches**, where data is divided into small batches (e.g., every 2 seconds) and processed using Spark.

---

## 🔷 2. Use Cases

* Real-time fraud detection in banking
* Live dashboard analytics (e.g., Twitter, IoT sensors)
* Log processing systems (e.g., Apache Kafka logs)
* Stream ETL pipelines (e.g., Kafka to Data Lake)

---

## 🔷 3. Architecture

```
             +---------------------+
             |  Data Source (e.g.  |
             |  Kafka, socket, etc)|
             +---------------------+
                      ↓
             +---------------------+
             | Spark Streaming App |
             | (DStream / Struct. )|
             +---------------------+
                      ↓
             +---------------------+
             |  Data Sink (e.g. DB,|
             |  Kafka, File, etc)  |
             +---------------------+
```

---

## 🔷 4. Spark Streaming vs Structured Streaming

| Feature         | Spark Streaming      | Structured Streaming       |
| --------------- | -------------------- | -------------------------- |
| API             | DStreams (RDD-based) | DataFrame/Dataset-based    |
| Latency         | Higher (\~2s+)       | Lower (ms to sec)          |
| Fault-tolerance | Limited              | Exactly-once semantics     |
| Integrations    | Older (deprecated)   | Supports Kafka, Files, etc |

> ✅ **Recommendation**: Use **Structured Streaming** for new projects.

---

## 🔷 5. Structured Streaming Basics

Structured Streaming treats real-time data as an **unbounded table**, where new data is appended continuously.

### 💡 Basic Workflow:

1. Read streaming data using `readStream`
2. Apply transformations using DataFrame API
3. Write results using `writeStream`

---

## 🔷 6. Example: Read from Socket, Word Count

### ▶️ Start a socket server:

```bash
# On terminal
nc -lk 9999
```

### ▶️ PySpark Code:

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import explode, split

# Create Spark Session
spark = SparkSession.builder \
    .appName("StructuredNetworkWordCount") \
    .getOrCreate()

# Read from socket (localhost:9999)
lines = spark.readStream \
    .format("socket") \
    .option("host", "localhost") \
    .option("port", 9999) \
    .load()

# Split lines into words
words = lines.select(
    explode(split(lines.value, " ")).alias("word")
)

# Count occurrences
wordCounts = words.groupBy("word").count()

# Output to console
query = wordCounts.writeStream \
    .outputMode("complete") \
    .format("console") \
    .start()

query.awaitTermination()
```

---

## 🔷 7. Output Modes in Structured Streaming

| Mode     | Description                                                            |
| -------- | ---------------------------------------------------------------------- |
| append   | Only new rows are written in each trigger                              |
| complete | Entire result table is output in each trigger (e.g., for aggregations) |
| update   | Only updated rows are written since the last trigger                   |

---

## 🔷 8. File-Based Streaming

Watch a folder and process new CSV files:

```python
df = spark.readStream \
    .option("header", "true") \
    .schema("name STRING, age INT") \
    .csv("path/to/folder")

df.writeStream \
    .outputMode("append") \
    .format("console") \
    .start() \
    .awaitTermination()
```

Place CSV files into that folder while this runs.

---

## 🔷 9. Kafka-Based Streaming Example

Requires Kafka setup and `spark-sql-kafka` package.

```python
df = spark.readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "test-topic") \
    .load()

# Convert Kafka bytes to string
df_parsed = df.selectExpr("CAST(value AS STRING)")

df_parsed.writeStream \
    .outputMode("append") \
    .format("console") \
    .start() \
    .awaitTermination()
```

---

## 🔷 10. Watermarking and Event Time (Handling Late Data)

```python
from pyspark.sql.functions import window

df = spark.readStream \
    .schema(schema) \
    .option("maxFilesPerTrigger", 1) \
    .json("path/to/data")

aggregated = df \
    .withWatermark("eventTime", "10 minutes") \
    .groupBy(window("eventTime", "5 minutes")) \
    .count()

aggregated.writeStream \
    .outputMode("append") \
    .format("console") \
    .start()
```

**Watermark** helps discard late events after a threshold.

---

## 🔷 11. Checkpointing

Used to store state & resume on failure.

```python
query = df.writeStream \
    .format("parquet") \
    .option("checkpointLocation", "/tmp/checkpoints/stream1") \
    .option("path", "/tmp/output") \
    .start()
```

---



## 🔷 12. Aggregations in Streaming

```python
from pyspark.sql.functions import window

windowedCounts = words \
    .groupBy(window(words.timestamp, "10 minutes", "5 minutes"), words.word) \
    .count()
```

---

## 🔷 13. Real-world Architecture Example

```
Kafka → Spark Structured Streaming → Transform (filter, agg) → Delta Table or Kafka
```

* Kafka: real-time log ingestion
* Spark Streaming: processing pipeline
* Sink: data lake or re-publish to topic

---
