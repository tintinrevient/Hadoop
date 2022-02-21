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

## References
