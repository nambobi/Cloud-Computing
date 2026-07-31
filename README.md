# Cloud Computing: Hands-On Projects and Practical Guides

Welcome to the Cloud Computing laboratory repository. This repository serves as a comprehensive resource for students learning cloud computing concepts, distributed storage systems, cluster resource management, parallel data processing, and containerization.

Each project module in this repository contains a dedicated guide providing theoretical background, architecture overview, prerequisites, step-by-step implementation procedures, verification steps, and troubleshooting notes.

---

## Repository Structure

```text
Cloud-Computing/
|-- README.md
`-- projects/
    |-- 01-hadoop-windows-setup/
    |   |-- README.md
    |   `-- config-files/
    |       |-- core-site.xml
    |       |-- hdfs-site.xml
    |       |-- mapred-site.xml
    |       `-- yarn-site.xml
    |-- 02-hdfs-data-operations/
    |   `-- README.md
    |-- 03-mapreduce-data-analytics/
    |   `-- README.md
    |-- 04-apache-spark-analytics/
    |   `-- README.md
    `-- 05-docker-containerization/
        `-- README.md
```

---

## Project Index and Curriculum

The curriculum is structured into five progressive hands-on projects designed to build foundational and advanced competencies in cloud systems engineering:

| Project ID | Project Title | Key Technologies | Description |
| :--- | :--- | :--- | :--- |
| **[Project 01](projects/01-hadoop-windows-setup/README.md)** | **Apache Hadoop 3.5.0 Windows Setup** | Java 17, Hadoop 3.5.0, Winutils, HDFS, YARN | Complete setup and configuration of a single-node Hadoop cluster on Windows, including HDFS NameNode formatting and YARN daemon management. |
| **[Project 02](projects/02-hdfs-data-operations/README.md)** | **Distributed Data Operations with HDFS** | HDFS CLI, NameNode, DataNode, Block Storage | Practical file operations, block inspection, directory management, replication factor adjustment, and dataset ingestion into HDFS. |
| **[Project 03](projects/03-mapreduce-data-analytics/README.md)** | **Distributed Analytics with MapReduce** | Java, Hadoop MapReduce, YARN, MapReduce API | Designing, compiling, and executing parallel MapReduce algorithms for large-scale text analytics and data processing. |
| **[Project 04](projects/04-apache-spark-analytics/README.md)** | **Unified Data Processing with Apache Spark** | PySpark, Spark SQL, RDDs, DataFrames | Building high-performance memory-centric data pipelines using Apache Spark integrated with HDFS storage. |
| **[Project 05](projects/05-docker-containerization/README.md)** | **Cloud Microservices with Docker** | Docker, Docker Compose, Microservices, Containers | Containerizing cloud services, managing container networks, configuring multi-container microservices applications, and state persistence. |

---

## How to Use This Repository

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/nambobi/Cloud-Computing.git
   cd Cloud-Computing
   ```
2. **Navigate to a Project Module**:
   Enter any project directory under `projects/` to access the specific project documentation and source code templates.
3. **Follow the Implementation Guides**:
   Execute the step-by-step instructions outlined in each module's `README.md` file.

---

## Prerequisites and Environment Setup

Before starting the projects, ensure your workstation meets the following minimum specifications:

- **Operating System**: Windows 10/11 (64-bit), Linux (Ubuntu 20.04/22.04 LTS), or macOS.
- **Memory**: Minimum 8 GB RAM (16 GB recommended).
- **Storage**: At least 20 GB free disk space.
- **Software Dependencies**:
  - OpenJDK 17 or Java 11.
  - Git command line interface.
  - Python 3.8+ (for PySpark modules).
  - Docker Desktop (for containerization modules).

---

## License and Contribution

This repository is maintained for educational purposes. Students are encouraged to fork the repository, complete the projects, and submit their implementation code as instructed by the course coordinator.
