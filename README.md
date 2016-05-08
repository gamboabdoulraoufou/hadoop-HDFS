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

**HDFS Security: Enabling ACLs**
HDFS supports POSIX Access Control Lists (ACLs), as well as the traditional POSIX permissions model already supported. ACLs control access of HDFS files by providing a way to set different permissions for specific named users or named groups.

```sh
# Edit hdfs-site.xml file
sudo nano /home/hadoop/hadoop-install/share/hadoop/templates/conf/hdfs-site.xml

# Enable ACL by adding the content bellow
<property>
  <name>dfs.namenode.acls.enabled</name>
  <value>true</value>
  <description>use posix to secure file and repository access</description>
</property>

```

To set and get file access control lists (ACLs), use the file system shell commands, setfacl and getfacl.

getfacl
```sh
hdfs dfs -getfacl [-R] <path>

<!-- COMMAND OPTIONS
<path>: Path to the file or directory for which ACLs should be listed.
-R: Use this option to recursively list ACLs for all files and directories.
-->
```

Examples:
```sh
<!-- To list all ACLs for the file located at /user/hdfs/file -->
hdfs dfs -getfacl /user/hdfs/file
```

```sh
<!-- To recursively list ACLs for /user/hdfs/file -->
hdfs dfs -getfacl -R /user/hdfs/file
setfacl
hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]
```

<!-- COMMAND OPTIONS
<path>: Path to the file or directory for which ACLs should be set.
-R: Use this option to recursively list ACLs for all files and directories.
-b: Revoke all permissions except the base ACLs for user, groups and others.
-k: Remove the default ACL.
-m: Add new permissions to the ACL with this option. Does not affect existing permissions.
-x: Remove only the ACL specified.
<acl_spec>: Comma-separated list of ACL permissions.
--set: Use this option to completely replace the existing ACL for the path specified. 
       Previous ACL entries will no longer apply.
-->
Examples:
```sh
<!-- To give user ben read & write permission over /user/hdfs/file -->
hdfs dfs -setfacl -m user:ben:rw- /user/hdfs/file

<!-- To remove user alice's ACL entry for /user/hdfs/file -->
hdfs dfs -setfacl -x user:alice /user/hdfs/file

<!-- To give user hadoop read & write access, and group or others read-only access -->
hdfs dfs -setfacl --set user::rw-,user:hadoop:rw-,group::r--,other::r-- /user/hdfs/file
```

**Add user**
```sh
# Log to hadoop user
sudo su - hadoop

# Create user
sudo adduser abdoul # set passwork 1234

# Create user home directory in HDFS
hadoop dfs –mkdir /user/abdoul/

# Change the ownership of user home directory in HDFS
hadoop dfs –chown –R abdoul:abdoul /user/abdoul

# Change ACL of user folder
hadoop fs -chmod -R 750 /user/abdoul

```
