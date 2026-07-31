# Project 04: Unified Data Processing with Apache Spark

## Project Overview

Apache Spark is a high-performance, in-memory distributed compute engine. This project covers setting up PySpark, reading data from HDFS, executing Dataframe queries, performing SQL transformations, and comparing memory-centric processing against MapReduce.

---

## Learning Objectives

Upon completion of this project, you will be able to:
1. Install PySpark and configure Python integration with Hadoop.
2. Initialize SparkSession and SparkContext objects.
3. Perform distributed data transformations using Resilient Distributed Datasets (RDDs) and DataFrames.
4. Execute Spark SQL queries against structured and unstructured datasets in HDFS.
5. Save Spark query results back to HDFS storage.

---

## Prerequisites

- Active Hadoop HDFS cluster (see **Project 01**).
- Python 3.8+ installed on workstation.
- PySpark package installed via pip.

---

## Step-by-Step Implementation Guide

### Step 1: Install PySpark

Open Command Prompt and install PySpark via pip:
```cmd
pip install pyspark findspark
```

---

### Step 2: Write Spark Analytics Script

Create a Python script named `spark_analytics.py`:

```python
import sys
from pyspark.sql import SparkSession
from pyspark.sql.functions import explode, split, lower, col, count

def main():
    # Initialize SparkSession connected to local master / YARN
    spark = SparkSession.builder \
        .appName("HDFS-Spark-Analytics") \
        .config("spark.master", "local[*]") \
        .getOrCreate()

    print("Spark Session Successfully Initialized")

    # Read dataset directly from HDFS
    hdfs_path = "hdfs://localhost:9000/user/student/data/sample_dataset.txt"
    lines_df = spark.read.text(hdfs_path)

    # Transform: Tokenize lines into individual words
    words_df = lines_df.select(
        explode(
            split(lower(col("value")), "\\s+")
        ).alias("word")
    ).filter(col("word") != "")

    # Group and Count
    word_counts = words_df.groupBy("word").agg(count("word").alias("frequency"))
    word_counts.show(10, truncate=False)

    # Save output to HDFS
    output_path = "hdfs://localhost:9000/output/spark_wordcounts"
    word_counts.write.mode("overwrite").csv(output_path)
    print(f"Results written to HDFS path: {output_path}")

    spark.stop()

if __name__ == "__main__":
    main()
```

---

### Step 3: Run Spark Application

Execute the PySpark application:
```cmd
python spark_analytics.py
```

---

### Step 4: Verify Output in HDFS

List generated output CSV files in HDFS:
```cmd
hdfs dfs -ls /output/spark_wordcounts
hdfs dfs -cat /output/spark_wordcounts/part-*.csv
```

---

## Troubleshooting Guide

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| `JavaGateway process exited before sending its port number` | Incompatible `JAVA_HOME` path or missing Java. | Ensure `JAVA_HOME=C:\JDK17` is set in environment. |
| `Py4JJavaError: An error occurred while calling z:org.apache.spark.api.python.PythonRDD.collectAndServe` | Cannot connect to HDFS URI. | Verify NameNode is running at `hdfs://localhost:9000`. |
