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

2. Execute the following statements in the Spark shell:
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
shu@playground:~$ cat output/part-r-00000 
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

## References
