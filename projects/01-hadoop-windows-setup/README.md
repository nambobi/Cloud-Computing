# Project 01: Apache Hadoop 3.5.0 Setup on Windows (HDFS and YARN)

## Project Overview

This project provides a comprehensive, step-by-step procedure for deploying and running a single-node Apache Hadoop 3.5.0 cluster on Microsoft Windows. It covers environment configuration, native Windows binary integration (`winutils`), cluster metadata formatting, daemon control for the Hadoop Distributed File System (HDFS) and Yet Another Resource Negotiator (YARN), operational testing, and troubleshooting common runtime errors.

---

## Learning Objectives

Upon completion of this project, you will be able to:
1. Configure system environment variables for Java 17 and Hadoop on Windows.
2. Integrate native Windows binary wrappers (`winutils.exe` and DLLs) required by Hadoop.
3. Edit core cluster XML files (`core-site.xml`, `hdfs-site.xml`, `mapred-site.xml`, `yarn-site.xml`) and environment scripts.
4. Format the HDFS NameNode metadata storage.
5. Initialize and manage HDFS (NameNode, DataNode) and YARN (ResourceManager, NodeManager) daemons.
6. Interact with the HDFS Command Line Interface (CLI) and access cluster web interfaces.

---

## Prerequisites and System Requirements

- **Operating System**: Windows 10 or Windows 11 (64-bit).
- **Storage**: Minimum 5 GB free space on drive `C:`.
- **Java Requirement**: OpenJDK 17 (e.g. Eclipse Temurin 17). 
  - *Note*: Apache Hadoop 3.5.0 is compiled targeting Java 17 (class version 61.0). Java 8 (1.8) is incompatible and will produce an `UnsupportedClassVersionError`.
- **Required Archives**:
  1. `hadoop-3.5.0.tar.gz` (Official Apache release).
  2. OpenJDK 17 zip archive.
  3. Hadoop 3.x Windows native binaries (`winutils.exe`, `hadoop.dll`, `hdfs.dll`, `yarn.dll`).

---

## Step-by-Step Implementation Guide

### Step 1: Install and Verify Java 17

1. Extract your OpenJDK 17 archive to `C:\JDK17`.
   - Executable path: `C:\JDK17\bin\java.exe`.
2. Configure System Environment Variables in Command Prompt:
   ```cmd
   setx JAVA_HOME "C:\JDK17"
   ```
3. Add `C:\JDK17\bin` to your System `PATH` variable.
4. Verify Java version:
   ```cmd
   java -version
   ```
   *Expected Output*: `openjdk version "17.0.x" ...`

---

### Step 2: Extract Apache Hadoop 3.5.0

1. Extract `hadoop-3.5.0.tar.gz` directly into `C:\`.
   - Using PowerShell:
     ```powershell
     tar -xzf "C:\Users\%USERNAME%\Downloads\hadoop-3.5.0.tar.gz" -C "C:\"
     ```
   - Target Directory: `C:\hadoop-3.5.0`.
2. Configure System Environment Variables:
   ```cmd
   setx HADOOP_HOME "C:\hadoop-3.5.0"
   setx PATH "C:\JDK17\bin;C:\hadoop-3.5.0\bin;C:\hadoop-3.5.0\sbin;%PATH%"
   ```

---

### Step 3: Install Windows Native Binaries (winutils)

1. Obtain the compatible 64-bit Hadoop 3.x Windows native binaries (`winutils.exe`, `hadoop.dll`, `hdfs.dll`, `yarn.dll`).
2. Copy all four files into `C:\hadoop-3.5.0\bin\`.
3. Verify `winutils.exe` execution:
   ```cmd
   C:\hadoop-3.5.0\bin\winutils.exe
   ```

---

### Step 4: Configure Cluster Environment and XML Files

All configuration files reside in `C:\hadoop-3.5.0\etc\hadoop\`.

#### 1. Create HDFS Storage Directories
```powershell
New-Item -ItemType Directory -Force -Path "C:\hadoop-3.5.0\data\namenode"
New-Item -ItemType Directory -Force -Path "C:\hadoop-3.5.0\data\datanode"
```

#### 2. Configure `hadoop-env.cmd`
Set explicit Java path in `C:\hadoop-3.5.0\etc\hadoop\hadoop-env.cmd`:
```cmd
set JAVA_HOME=C:\JDK17
```

#### 3. Configure `core-site.xml`
Insert into `<configuration>` in `C:\hadoop-3.5.0\etc\hadoop\core-site.xml`:
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

#### 4. Configure `hdfs-site.xml`
Insert into `<configuration>` in `C:\hadoop-3.5.0\etc\hadoop\hdfs-site.xml`:
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///C:/hadoop-3.5.0/data/namenode</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///C:/hadoop-3.5.0/data/datanode</value>
    </property>
</configuration>
```

#### 5. Configure `mapred-site.xml`
Insert into `<configuration>` in `C:\hadoop-3.5.0\etc\hadoop\mapred-site.xml`:
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.job.ubertask.enable</name>
        <value>true</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>%HADOOP_HOME%/share/hadoop/mapreduce/*;%HADOOP_HOME%/share/hadoop/mapreduce/lib/*;%HADOOP_HOME%/share/hadoop/common/*;%HADOOP_HOME%/share/hadoop/common/lib/*;%HADOOP_HOME%/share/hadoop/yarn/*;%HADOOP_HOME%/share/hadoop/yarn/lib/*;%HADOOP_HOME%/share/hadoop/hdfs/*;%HADOOP_HOME%/share/hadoop/hdfs/lib/*</value>
    </property>
</configuration>
```

#### 6. Configure `yarn-site.xml`
Insert into `<configuration>` in `C:\hadoop-3.5.0\etc\hadoop\yarn-site.xml`:
```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
```

---

### Step 5: Format the HDFS NameNode

Execute the format command to initialize cluster metadata:
```cmd
hdfs namenode -format
```
*Expected Log Message*: `Storage directory C:\hadoop-3.5.0\data\namenode has been successfully formatted.`

---

### Step 6: Start Cluster Daemons

Execute startup scripts from `C:\hadoop-3.5.0\sbin\`:
```cmd
start-dfs.cmd
start-yarn.cmd
```

---

## Verification and Testing

### 1. Web User Interfaces
- **HDFS NameNode UI**: http://localhost:9870
- **HDFS DataNode UI**: http://localhost:9864
- **YARN ResourceManager UI**: http://localhost:8088

### 2. File Operations via HDFS CLI
```cmd
hdfs dfs -mkdir -p /user/hadoop/test
hdfs dfs -ls -R /
```

### 3. Execute MapReduce Job on YARN
```cmd
yarn jar C:\hadoop-3.5.0\share\hadoop\mapreduce\hadoop-mapreduce-examples-3.5.0.jar pi 2 5
```

---

## Troubleshooting Guide

| Symptom / Error | Root Cause | Solution |
| :--- | :--- | :--- |
| `UnsupportedClassVersionError (61.0)` | Running Hadoop 3.5.0 with Java 8 (1.8). | Install JDK 17, update `JAVA_HOME=C:\JDK17`, and update `hadoop-env.cmd`. |
| `Could not locate executable null\bin\winutils.exe` | Missing `winutils.exe` in `%HADOOP_HOME%\bin`. | Place Hadoop 3.x `winutils.exe` and DLLs inside `C:\hadoop-3.5.0\bin`. |
| `CreateSymbolicLink error (1314)` | Windows user account lacks symbolic link privileges when YARN launches containers. | Set `mapreduce.job.ubertask.enable = true` in `mapred-site.xml` or enable Windows Developer Mode in Settings. |
| DataNode fails to start | Corrupted metadata or cluster ID mismatch. | Clear contents of `C:\hadoop-3.5.0\data\namenode` and `C:\hadoop-3.5.0\data\datanode`, then execute `hdfs namenode -format`. |
