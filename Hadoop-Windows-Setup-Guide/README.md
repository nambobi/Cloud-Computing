# Complete Guide: Installing Apache Hadoop 3.5.0 on Windows (HDFS & YARN)

This step-by-step guide walks you through installing, configuring, running, and verifying **Apache Hadoop 3.5.0** with **HDFS** and **YARN** on Microsoft Windows 10/11.

---

## 📋 Table of Contents
1. [Prerequisites & System Requirements](#1-prerequisites--system-requirements)
2. [Step 1: Install & Configure Java 17](#step-1-install--configure-java-17)
3. [Step 2: Extract Apache Hadoop 3.5.0](#step-2-extract-apache-hadoop-350)
4. [Step 3: Install Windows Native Binaries (winutils)](#step-3-install-windows-native-binaries-winutils)
5. [Step 4: Configure Hadoop XML & Environment Files](#step-4-configure-hadoop-xml--environment-files)
6. [Step 5: Format the HDFS NameNode](#step-5-format-the-hdfs-namenode)
7. [Step 6: Start HDFS and YARN Services](#step-6-start-hdfs-and-yarn-services)
8. [Step 7: Verification & Testing](#step-7-verification--testing)
9. [Step 8: Troubleshooting & Common Issues](#step-8-troubleshooting--common-issues)

---

## 1. Prerequisites & System Requirements

- **Operating System**: Windows 10 or Windows 11 (64-bit).
- **Disk Space**: At least 5 GB of free storage.
- **Downloaded Files Needed**:
  1. **Apache Hadoop 3.5.0 Archive**: `hadoop-3.5.0.tar.gz` (Download from official Apache Hadoop mirrors).
  2. **Java 17 Development Kit (JDK 17)**: OpenJDK 17 (e.g. Eclipse Temurin 17).
     > ⚠️ **Important Note on Java Version**: Apache Hadoop 3.5.0 is compiled targeting Java 17 (class file version 61.0). Java 8 (1.8) will **not** work and will result in `UnsupportedClassVersionError`. You **must** use JDK 17.
  3. **Windows Native Binaries (`winutils.exe`)**: Hadoop 3.x native binaries for Windows (`winutils.exe`, `hadoop.dll`, `hdfs.dll`, `yarn.dll`).

---

## Step 1: Install & Configure Java 17

1. Download **OpenJDK 17** zip archive (e.g., from [Adoptium Temurin 17](https://adoptium.net/)).
2. Extract the downloaded zip contents directly into `C:\JDK17`.
   - Your Java executable path should be: `C:\JDK17\bin\java.exe`.
3. Set **Environment Variables** in Windows:
   - Open Command Prompt or PowerShell as Administrator/User and set:
     ```cmd
     setx JAVA_HOME "C:\JDK17"
     ```
   - Add Java `bin` to your `PATH`:
     Add `C:\JDK17\bin` to your System or User `PATH` environment variable.
4. **Verify Java Installation**:
   Open a new Command Prompt window and run:
   ```cmd
   java -version
   ```
   **Expected Output:**
   ```text
   openjdk version "17.0.20" 2026-07-21
   OpenJDK Runtime Environment Temurin-17.0.20+8 (build 17.0.20+8)
   OpenJDK 64-Bit Server VM Temurin-17.0.20+8 (build 17.0.20+8, mixed mode)
   ```

---

## Step 2: Extract Apache Hadoop 3.5.0

1. Locate `hadoop-3.5.0.tar.gz` in your Downloads folder.
2. Extract the archive directly into `C:\`.
   - Using PowerShell:
     ```powershell
     tar -xzf "C:\Users\%USERNAME%\Downloads\hadoop-3.5.0.tar.gz" -C "C:\"
     ```
   - The resulting installation folder should be: `C:\hadoop-3.5.0`.
3. Set **Environment Variables** for Hadoop:
   - Open Command Prompt and run:
     ```cmd
     setx HADOOP_HOME "C:\hadoop-3.5.0"
     ```
   - Add `%HADOOP_HOME%\bin` and `%HADOOP_HOME%\sbin` to your `PATH` environment variable:
     ```cmd
     setx PATH "C:\JDK17\bin;C:\hadoop-3.5.0\bin;C:\hadoop-3.5.0\sbin;%PATH%"
     ```

---

## Step 3: Install Windows Native Binaries (winutils)

Apache Hadoop natively relies on POSIX permissions via Windows API wrappers (`winutils.exe` and `hadoop.dll`). Official releases do not bundle Windows binaries by default.

1. Download compatible Hadoop 3.x native Windows binaries:
   - `winutils.exe`
   - `hadoop.dll`
   - `hdfs.dll`
   - `yarn.dll`
   *(Source repository: [cdarlint/winutils](https://github.com/cdarlint/winutils))*.
2. Copy all four files (`winutils.exe`, `hadoop.dll`, `hdfs.dll`, `yarn.dll`) into:
   `C:\hadoop-3.5.0\bin\`
3. Verify `winutils.exe` execution:
   ```cmd
   C:\hadoop-3.5.0\bin\winutils.exe
   ```
   If it prints usage instructions for `chmod`, `chown`, `ls`, etc., it is installed correctly.

---

## Step 4: Configure Hadoop XML & Environment Files

All configuration files are located in `C:\hadoop-3.5.0\etc\hadoop\`.

### 1. Create HDFS Data Directories
Create the local directories where HDFS will store NameNode metadata and DataNode blocks:
```powershell
New-Item -ItemType Directory -Force -Path "C:\hadoop-3.5.0\data\namenode"
New-Item -ItemType Directory -Force -Path "C:\hadoop-3.5.0\data\datanode"
```

### 2. Configure `hadoop-env.cmd`
Open `C:\hadoop-3.5.0\etc\hadoop\hadoop-env.cmd` and set:
```cmd
set JAVA_HOME=C:\JDK17
```

### 3. Configure `core-site.xml`
Open `C:\hadoop-3.5.0\etc\hadoop\core-site.xml` and add inside `<configuration>`:
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

### 4. Configure `hdfs-site.xml`
Open `C:\hadoop-3.5.0\etc\hadoop\hdfs-site.xml` and add inside `<configuration>`:
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

### 5. Configure `mapred-site.xml`
Open `C:\hadoop-3.5.0\etc\hadoop\mapred-site.xml` and add inside `<configuration>`:
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

### 6. Configure `yarn-site.xml`
Open `C:\hadoop-3.5.0\etc\hadoop\yarn-site.xml` and add inside `<configuration>`:
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

## Step 5: Format the HDFS NameNode

Before starting HDFS for the first time, you **must** format the NameNode metadata directory.

Open Command Prompt and run:
```cmd
hdfs namenode -format
```
**Success Indicator**: Look for the log line near the bottom:
`INFO common.Storage: Storage directory C:\hadoop-3.5.0\data\namenode has been successfully formatted.`

---

## Step 6: Start HDFS and YARN Services

You can start services either using startup scripts or individual commands in separate terminal windows.

### Method A: Start Scripts (Recommended for Command Prompt)
Navigate to `C:\hadoop-3.5.0\sbin\` and run:
1. Start HDFS (NameNode & DataNode):
   ```cmd
   start-dfs.cmd
   ```
2. Start YARN (ResourceManager & NodeManager):
   ```cmd
   start-yarn.cmd
   ```

### Method B: Manual Commands (Separate Windows)
- Window 1 (NameNode): `hdfs namenode`
- Window 2 (DataNode): `hdfs datanode`
- Window 3 (ResourceManager): `yarn resourcemanager`
- Window 4 (NodeManager): `yarn nodemanager`

---

## Step 7: Verification & Testing

### 1. Check Web UIs in Browser
- **HDFS NameNode UI**: [http://localhost:9870](http://localhost:9870)
- **HDFS DataNode UI**: [http://localhost:9864](http://localhost:9864)
- **YARN ResourceManager UI**: [http://localhost:8088](http://localhost:8088)

### 2. Test HDFS Command Line
Run the following commands in Command Prompt:
```cmd
:: Create a directory inside HDFS
hdfs dfs -mkdir -p /user/test

:: List contents of HDFS root directory
hdfs dfs -ls -R /
```

### 3. Run Sample MapReduce Job on YARN
Run the standard Pi calculation example:
```cmd
yarn jar C:\hadoop-3.5.0\share\hadoop\mapreduce\hadoop-mapreduce-examples-3.5.0.jar pi 2 5
```
Check status on the YARN Web UI at `http://localhost:8088/cluster`.

---

## Step 8: Troubleshooting & Common Issues

| Error Message / Symptom | Cause | Solution |
| :--- | :--- | :--- |
| `UnsupportedClassVersionError (61.0)` | Running Hadoop 3.5.0 with Java 8 (1.8) instead of Java 17. | Install JDK 17, update `JAVA_HOME=C:\JDK17`, and update `hadoop-env.cmd`. |
| `Could not locate executable null\bin\winutils.exe` | Missing `winutils.exe` in `%HADOOP_HOME%\bin`. | Download `winutils.exe` for Hadoop 3.x and place it in `C:\hadoop-3.5.0\bin`. |
| `CreateSymbolicLink error (1314): A required privilege is not held` | Windows limits symlink creation for non-admin accounts when YARN launches container processes. | 1. Enable `mapreduce.job.ubertask.enable = true` in `mapred-site.xml`. <br> 2. Turn ON **Developer Mode** in Windows: `Settings -> Privacy & Security -> For developers -> Developer Mode -> ON`. |
| DataNode fails to connect to NameNode | Old metadata conflict or duplicate cluster IDs. | Delete contents of `C:\hadoop-3.5.0\data\namenode` and `C:\hadoop-3.5.0\data\datanode`, then re-run `hdfs namenode -format`. |
