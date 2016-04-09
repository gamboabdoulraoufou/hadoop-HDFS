## hadoop-HDFS

This post will cover the following points:
- Basic HDFS architecture, design goal and performance envelope 
- Read Write process
- Tuning consideration, perfomance, impact of tuning and impact of robusteness
- Moving data to from HDFS

**HDFS concepts**  
Hadoop File System was developed using distributed file system design. It is run on commodity hardware. Unlike other distributed systems, HDFS is highly faulttolerant and designed using low-cost hardware.  

HDFS holds very large amount of data and provides easier access. To store such huge data, the files are stored across multiple machines. These files are stored in redundant fashion to rescue the system from possible data losses in case of failure. HDFS also makes applications available to parallel processing

![java default](https://raw.github.com/mbonaci/mbo-spark/master/resources/java-default.PNG)

***NameNode***  
A master server that manages the file system namespace and regulates access to files by client.

***DataNode***  
- Manage storage
- Serving read/write requests from client
- Bloc creation, deletion, replication based on instruction from NameNode

**Read/Write process**  
***Read process***
- Client gets DataNode list from NameNode
- Read from replicat closest to reader

***Write process*** 
- client request to create file
- NameNode contacted one the bloc of data is accumulated
- NameNode response with a list of DataNodes
- The first DataNode receives data, writes to local and forwards to second DataNode
- The NameNode commits files creation

**HDFS Tuning**
***Parameters***
***Bloc size***
***NameNode, DataNode, system/dfs parameters***


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
