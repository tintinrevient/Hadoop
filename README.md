# Hadoop

## Overview

## Word Count

1. Prepare the local file `docs` as below and place it under the folder `input`:
```bash
Mary had a little lamb
its fleece was white as snow
and everywhere that Mary went
the lamb was sure to go
```

2. Execute the following command:
```bash
hadoop jar /usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount file:///home/shu/input file:///home/shu/output

2022-02-21 15:33:22,800 INFO impl.YarnClientImpl: Submitted application application_1645453867051_0001
2022-02-21 15:33:22,868 INFO mapreduce.Job: The url to track the job: http://playground:8088/proxy/application_1645453867051_0001/
2022-02-21 15:33:22,869 INFO mapreduce.Job: Running job: job_1645453867051_0001
2022-02-21 15:33:32,057 INFO mapreduce.Job: Job job_1645453867051_0001 running in uber mode : false
2022-02-21 15:33:32,059 INFO mapreduce.Job:  map 0% reduce 0%
2022-02-21 15:33:38,170 INFO mapreduce.Job:  map 100% reduce 0%
2022-02-21 15:33:48,285 INFO mapreduce.Job:  map 100% reduce 100%
2022-02-21 15:33:48,306 INFO mapreduce.Job: Job job_1645453867051_0001 completed successfully
```

3. The result is as below:
```
cat output/part-r-00000

Mary    2
a       1
and     1
as      1
everywhere      1
fleece  1
go      1
had     1
its     1
lamb    2
little  1
snow    1
sure    1
that    1
the     1
to      1
was     2
went    1
white   1
```

4. The Java source code is as below, compared to [Hive](https://github.com/tintinrevient/Hive#word-count) and [Spark](https://github.com/tintinrevient/Spark#word-count):
```java
import java.io.IOException;
import java.util.*;
        
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.conf.*;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.*;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
        
public class WordCount {
        
 public static class Map extends Mapper<LongWritable, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
        
    public void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        String line = value.toString();
        StringTokenizer tokenizer = new StringTokenizer(line);
        while (tokenizer.hasMoreTokens()) {
            word.set(tokenizer.nextToken());
            context.write(word, one);
        }
    }
 } 
        
 public static class Reduce extends Reducer<Text, IntWritable, Text, IntWritable> {

    public void reduce(Text key, Iterable<IntWritable> values, Context context) 
      throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
            sum += val.get();
        }
        context.write(key, new IntWritable(sum));
    }
 }
        
 public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
        
    Job job = new Job(conf, "wordcount");
    
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
        
    job.setMapperClass(Map.class);
    job.setReducerClass(Reduce.class);
        
    job.setInputFormatClass(TextInputFormat.class);
    job.setOutputFormatClass(TextOutputFormat.class);
        
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
        
    job.waitForCompletion(true);
 }
        
}
```

## Installation

1. Update `.bash_profile`:
```bash
export HADOOP_HOME=/home/user/hadoop-3.3.1
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
````

2. Update the configurations in `etc/hadoop` with [core-site.xml](config/core-site.xml), [hdfs-site.xml](config/hdfs-site.xml), [mapred-site.xml](config/mapred-site.xml), [yarn-site.xml](config/yarn-site.xml) and [hadoop-env.sh](config/hadoop-env.sh).


3. Test whether `yarn` or `local` mapreduce example works:

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar pi 2 10

2022-07-16 03:32:42,191 INFO input.FileInputFormat: Total input files to process : 2
2022-07-16 03:32:42,239 INFO mapreduce.JobSubmitter: number of splits:2
2022-07-16 03:32:42,398 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1657935129711_0001
2022-07-16 03:32:42,398 INFO mapreduce.JobSubmitter: Executing with tokens: []
2022-07-16 03:32:42,585 INFO conf.Configuration: resource-types.xml not found
2022-07-16 03:32:42,585 INFO resource.ResourceUtils: Unable to find 'resource-types.xml'.
2022-07-16 03:32:43,055 INFO impl.YarnClientImpl: Submitted application application_1657935129711_0001
2022-07-16 03:32:43,112 INFO mapreduce.Job: The url to track the job: http://nezumikozo.local:8088/proxy/application_1657935129711_0001/
2022-07-16 03:32:43,113 INFO mapreduce.Job: Running job: job_1657935129711_0001
2022-07-16 03:32:52,320 INFO mapreduce.Job: Job job_1657935129711_0001 running in uber mode : false
2022-07-16 03:32:52,321 INFO mapreduce.Job:  map 0% reduce 0%
2022-07-16 03:32:59,473 INFO mapreduce.Job:  map 100% reduce 0%
2022-07-16 03:33:05,532 INFO mapreduce.Job:  map 100% reduce 100%
2022-07-16 03:33:05,545 INFO mapreduce.Job: Job job_1657935129711_0001 completed successfully
2022-07-16 03:33:05,663 INFO mapreduce.Job: Counters: 50
	File System Counters
		FILE: Number of bytes read=50
		FILE: Number of bytes written=819918
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=532
		HDFS: Number of bytes written=215
		HDFS: Number of read operations=13
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=3
		HDFS: Number of bytes read erasure-coded=0
	Job Counters
		Launched map tasks=2
		Launched reduce tasks=1
		Data-local map tasks=2
		Total time spent by all maps in occupied slots (ms)=10948
		Total time spent by all reduces in occupied slots (ms)=3075
		Total time spent by all map tasks (ms)=10948
		Total time spent by all reduce tasks (ms)=3075
		Total vcore-milliseconds taken by all map tasks=10948
		Total vcore-milliseconds taken by all reduce tasks=3075
		Total megabyte-milliseconds taken by all map tasks=11210752
		Total megabyte-milliseconds taken by all reduce tasks=3148800
	Map-Reduce Framework
		Map input records=2
		Map output records=4
		Map output bytes=36
		Map output materialized bytes=56
		Input split bytes=296
		Combine input records=0
		Combine output records=0
		Reduce input groups=2
		Reduce shuffle bytes=56
		Reduce input records=4
		Reduce output records=0
		Spilled Records=8
		Shuffled Maps =2
		Failed Shuffles=0
		Merged Map outputs=2
		GC time elapsed (ms)=227
		CPU time spent (ms)=0
		Physical memory (bytes) snapshot=0
		Virtual memory (bytes) snapshot=0
		Total committed heap usage (bytes)=772276224
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters
		Bytes Read=236
	File Output Format Counters
		Bytes Written=97
Job Finished in 24.013 seconds
Estimated value of Pi is 3.80000000000000000000
```

## References
* https://hadoop.apache.org/docs/r3.3.1/hadoop-project-dist/hadoop-common/SingleCluster.html
* https://stackoverflow.com/questions/36843680/resources-are-low-on-nn-after-deactivation-of-safemode-and-added-space
* https://stackoverflow.com/questions/54801232/hdfs-dfs-name-dir-is-out-of-free-space
* https://stackoverflow.com/questions/29131449/why-does-hadoop-report-unhealthy-node-local-dirs-and-log-dirs-are-bad
