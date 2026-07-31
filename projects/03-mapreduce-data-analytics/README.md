# Project 03: Distributed Analytics with Apache Hadoop MapReduce

## Project Overview

MapReduce is the core parallel data processing framework in Apache Hadoop. This project guides you through designing, writing, compiling, packaging, and executing MapReduce applications on YARN for large-scale data analytics.

---

## Learning Objectives

Upon completion of this project, you will be able to:
1. Understand the MapReduce programming model (Map, Shuffle & Sort, Reduce).
2. Develop custom Mapper and Reducer classes in Java.
3. Configure MapReduce Job drivers with input and output formats.
4. Execute MapReduce jobs managed by YARN ResourceManager and NodeManager.
5. Retrieve and analyze MapReduce execution metrics and logs.

---

## Prerequisites

- Active Hadoop 3.5.0 cluster with YARN enabled (see **Project 01**).
- OpenJDK 17 configured in environment.
- Text editor or Java IDE.

---

## Step-by-Step Implementation Guide

### Step 1: Write Custom MapReduce Application Source Code

Create a directory named `WordCountApp` and write `WordCount.java`:

```java
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```

---

### Step 2: Compile Java Source and Create JAR Package

1. Set Hadoop Classpath environment in Command Prompt:
   ```cmd
   for /f "tokens=*" %i in ('hadoop classpath') do set HADOOP_CLASSPATH=%i
   ```
2. Compile `WordCount.java`:
   ```cmd
   javac -classpath "%HADOOP_CLASSPATH%" -d . WordCount.java
   ```
3. Create JAR archive:
   ```cmd
   jar -cvf wordcount.jar *.class
   ```

---

### Step 3: Prepare Input Data in HDFS

1. Create input directory in HDFS:
   ```cmd
   hdfs dfs -mkdir -p /input/wordcount
   ```
2. Upload input text files:
   ```cmd
   hdfs dfs -put sample_dataset.txt /input/wordcount/
   ```

---

### Step 4: Execute MapReduce Job on YARN

1. Remove any previous output directory in HDFS:
   ```cmd
   hdfs dfs -rm -r /output/wordcount
   ```
2. Submit job to YARN:
   ```cmd
   yarn jar wordcount.jar WordCount /input/wordcount /output/wordcount
   ```

---

### Step 5: Inspect Job Results and Performance

1. View execution status on YARN Web UI: http://localhost:8088
2. Inspect output files in HDFS:
   ```cmd
   hdfs dfs -ls /output/wordcount
   ```
3. View word count statistics output:
   ```cmd
   hdfs dfs -cat /output/wordcount/part-r-00000
   ```

---

## Troubleshooting Guide

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| `Output directory ... already exists` | MapReduce safety mechanism prevents overwriting existing output paths. | Delete output folder: `hdfs dfs -rm -r /output/wordcount`. |
| `ClassNotFoundException` | Compiled class file not included in the JAR root. | Package JAR using `jar -cvf wordcount.jar *.class`. |
| `Job failed with state FAILED` | Memory limit exceeded or YARN container launch failure. | Enable `mapreduce.job.ubertask.enable = true` in `mapred-site.xml`. |
