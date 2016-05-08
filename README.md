## hadoop-HDFS

This post will cover the following points:
- Basic HDFS architecture, design goal and performance envelope 
- Read Write process
- Tuning consideration, perfomance, impact of tuning and impact of robusteness
- HDFS access API
- Command

**HDFS concepts**  
Hadoop File System was developed using distributed file system design. It is run on commodity hardware. Unlike other distributed systems, HDFS is highly faulttolerant and designed using low-cost hardware.  

HDFS holds very large amount of data and provides easier access. To store such huge data, the files are stored across multiple machines. These files are stored in redundant fashion to rescue the system from possible data losses in case of failure. HDFS also makes applications available to parallel processing

![java default](https://raw.github.com/mbonaci/mbo-spark/master/resources/java-default.PNG)

_NameNode_  
A master server that manages the file system namespace and regulates access to files by client.

_DataNode_  
- Manage storage
- Serving read/write requests from client
- Bloc creation, deletion, replication based on instruction from NameNode

**Read/Write process**  
_Read process_
- Client gets DataNode list from NameNode
- Read from replicat closest to reader

_Write process_   
- client request to create file
- NameNode contacted one the bloc of data is accumulated
- NameNode response with a list of DataNodes
- The first DataNode receives data, writes to local and forwards to second DataNode
- The NameNode commits files creation

**HDFS Tuning**  
_Parameters_
Parameters can be changed from hdfs-site.xml file or GUI interface for some commercial distribution.
_Bloc size_
By default, block size is 64MB. Can be changed based on workloads. The parameter that this changes is dfs.blocksize or dfs.block.size.
_Replication_
By default, the replication is set to 3. The parameters that this change is dfs.replication.
_Other parameters_
Change number of threads per Node, maximum number of block by file.

**HDFS access**
- Command line invoked via bin/hdfs
- Native Java API
- C API
- WebHDFS Rest API: HTTP Get, Put, Delete operations

**Command**
```sh
# Create test directory
hadoop fs -mkdir /user/test

# Look at the content of user folder
hadoop fs -ls /user

# Create file in local disk
echo "Hello world" > sample.txt

# Copy file into HDFS
hadoop fs -put sample.txt /user/test

hadoop fsck
hadoop fsadmin -report

```

**HDFS Security**
```sh
# Edit hdfs-site.xml file
sudo nano /home/hadoop/hadoop-install/share/hadoop/templates/conf/hdfs-site.xml

# Enable ACL by adding the content bellow
<property>
    <name> dfs.namenode.acls.enabled</name>
    <value>true</value>
    <description>use posix to secure file and repository access</description>
  </property>

```

**Add user**
```sh
# Create user
sudo adduser abdoul # set passwork 1234

# Create user home directory in HDFS
hadoop dfs –mkdir /user/abdoul/

# Change the ownership of user home directory in HDFS
hadoop dfs –chown –R abdoul:abdoul /user/abdoul

```
