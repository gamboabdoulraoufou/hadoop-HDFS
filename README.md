# hadoop-HDFS

## hadoop-Spark RDD

This post will cover the following points:
- what is RDD  
- how to manipulate RDD using _pyspark API_ (mapreduce example)
- Some RDD actions
- Caching data 
- Shared Variables
- Accumulator


**What is RDD (Resilient Distributed Dataset)**  
RDD is the data container, the way in Spark to store data like variable or object in programming.
Data can be read from local text file, HDFS or other source. 
  
Caracteristiques:
- Immutable: you cannot change just a section of RDD. But every time you transform one RDD, you create a new one.  
- Distributed: partition of data are divided accross machine
- Resilient: for every point in your calculations, Spark knows which are the partitions needed to recreate the partition in case it gets lost. And if that happens, then spark automatically figures out where it can start from to recompute what's the minimum amount of processing needed to recover the lost partition

**RDD manipulation**  
Go to your shell console and tape this command
```sh
pyspark
