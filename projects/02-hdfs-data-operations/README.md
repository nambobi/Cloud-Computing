# Project 02: Distributed Data Operations with HDFS

## Project Overview

The Hadoop Distributed File System (HDFS) is designed to store large files reliably across a cluster of machines. This project introduces hands-on HDFS file operations, directory creation, block management, file replication adjustments, file integrity verification (fsck), and data ingestion techniques.

---

## Learning Objectives

Upon completion of this project, you will be able to:
1. Master fundamental HDFS CLI commands (`mkdir`, `put`, `get`, `cat`, `ls`, `rm`, `du`).
2. Manage HDFS storage quotas and file permissions.
3. Dynamically adjust replication factors for individual files and directories.
4. Execute HDFS filesystem health checks using `hdfs fsck`.
5. Upload and process unstructured dataset files in HDFS.

---

## Prerequisites

- Active Hadoop 3.5.0 cluster running HDFS NameNode and DataNode (see **Project 01**).
- Command Line Interface (Command Prompt or PowerShell).

---

## Step-by-Step Implementation Guide

### Step 1: Initialize HDFS User Environment

1. Verify HDFS is accessible:
   ```cmd
   hdfs dfs -admin -report
   ```
2. Create standard HDFS user directory structure:
   ```cmd
   hdfs dfs -mkdir -p /user/student/data
   hdfs dfs -mkdir -p /user/student/output
   ```
3. List the created directories:
   ```cmd
   hdfs dfs -ls /user/student
   ```

---

### Step 2: Upload and Ingest Datasets into HDFS

1. Create a local sample text file named `sample_dataset.txt` on your workstation:
   ```cmd
   echo Cloud Computing Distributed Storage HDFS Lab > sample_dataset.txt
   echo Apache Hadoop Cluster Architecture >> sample_dataset.txt
   ```
2. Upload the file from local storage to HDFS:
   ```cmd
   hdfs dfs -put sample_dataset.txt /user/student/data/
   ```
3. Verify the file placement in HDFS:
   ```cmd
   hdfs dfs -ls /user/student/data/
   ```
4. Read the file contents directly from HDFS:
   ```cmd
   hdfs dfs -cat /user/student/data/sample_dataset.txt
   ```

---

### Step 3: Manage Replication Factor and Storage Metadata

1. Check current replication factor and file details:
   ```cmd
   hdfs dfs -ls /user/student/data/sample_dataset.txt
   ```
2. Adjust replication factor to 2 (or 1 for single-node development):
   ```cmd
   hdfs dfs -setrep -w 1 /user/student/data/sample_dataset.txt
   ```
3. Inspect disk space usage in HDFS:
   ```cmd
   hdfs dfs -du -h /user/student/
   ```

---

### Step 4: Download and Export Files from HDFS

1. Copy file from HDFS back to local storage:
   ```cmd
   hdfs dfs -get /user/student/data/sample_dataset.txt exported_dataset.txt
   ```
2. Verify local file contents:
   ```cmd
   type exported_dataset.txt
   ```

---

### Step 5: Filesystem Integrity Check (fsck)

1. Run the HDFS filesystem check utility:
   ```cmd
   hdfs fsck /user/student/data/ -files -blocks -locations
   ```
2. Review output report for healthy blocks, missing blocks, and block locations.

---

## Hands-On Student Exercises

1. **Exercise 1**: Create a folder `/user/student/logs/` in HDFS and copy two log files into it.
2. **Exercise 2**: Use `hdfs dfs -tail` to view the last 1 KB of a large file stored in HDFS.
3. **Exercise 3**: Set directory permission to read-only for group users using `hdfs dfs -chmod 755 /user/student/data/`.

---

## Troubleshooting Guide

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| `Call From ... to localhost:9000 failed on connection exception` | HDFS NameNode daemon is not running. | Execute `start-dfs.cmd` or `hdfs namenode` to start HDFS. |
| `Permission denied: user=student, access=WRITE` | HDFS user permissions restrict directory modification. | Run `hdfs dfs -chmod 777 <path>` or execute command as superuser. |
| `SafeModeException: NameNode is in safe mode` | NameNode entered protection mode on startup. | Force exit safe mode: `hdfs dfsadmin -safemode leave`. |
