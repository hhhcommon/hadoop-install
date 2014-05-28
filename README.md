CDH-5.0.1 [Apache Hadoop 2.3.0]
=============================
####Prerequisites
- Supported Operating Systems: RedhatEL,Ubuntu,Debian,CentOS,SLES,OracleLinux->64-bit
- Supported JDK Versions: >= jdk-1.7.0_25_
- Supported Internet Protocol: CDH requires IPv4. IPv6 is not supported.
- SSH configuration:SSH should be configured

####Installing CDH 5 on a Single Linux Node in Pseudo-distributed Mode

For development purpose, Apache Hadoop and CDH 5 components can be deployed
on a single Linux node in pseudo-distributed mode.
In pseudo-distributed mode, Hadoop processing is distributed over all of the
cores/processors on a single machine. Hadoop writes all files to the
Hadoop Distributed File System (HDFS), and all services and daemons communicate
over local TCP sockets for inter-process communication.

###STEP-1
```
Download CDH tarball from cloudera
$ wget http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.3.0-cdh5.0.1.tar.gz
$ cd /opt
$ tar -xzf ~/hadoop-2.3.0-cdh5.0.1.tar.gz
$ cd hadoop-2.3.0-cdh5.0.1
```
 
_Edit ~/.bashrc_

```
export HADOOP_HOME=/opt/hadoop-2.3.0-cdh5.0.1
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop
```
#####Refresh bash profile `$ bash`

_Edit config files_
 - core-site.xml
 - hdfs-site.xml
 - mapred-site.xml
 - yarn-site.xml
 - hadoop-env.sh
 - yarn-env.sh
 - mapred-env.sh
 
    OR

```
$ git clone https://github.com/mehikmat/hadoop-install.git
$ cp -R hadoop-install/etc/hadoop/* $HADOOP_HOME/etc/hadoop/
```


###STEP-2

Create dirs,user, and set Java Home for all users

```
$ git clone https://github.com/mehikmat/hadoop-install.git
$ cd hadoop-install/users-and-dirs

Set Java for all users
$ ./java.sh

Create users(if you want use separate users)
$ ./users.sh

Create required directories
$ ./dirs.sh
```
##STEP-3

_Create HDFS dirs_
```
Create the history directory and set permissions and owner
$ sudo -u hdfs hadoop fs -mkdir -p /user/history
$ sudo -u hdfs hadoop fs -chmod -R 1777 /user/history
$ sudo -u hdfs hadoop fs -chown mapred:hadoop /user/history

$ sudo -u hdfs hadoop fs -mkdir /tmp
$ sudo -u hdfs hadoop fs -chmod -R 1777 /tmp
```

##STEP-4

Format HDFS
```
$ sudo -u hdfs bin/hdfs namenode -format
```

##STEP-5
_Start HDFS and YARN services_
```
$ sudo -u hdfs sbin/start-dfs.sh
$ sudo -u yarn sbin/start-yarn.sh
```

###Utilities:
- $HADOOP_HOME/bin/hadoop  :>>>   For basic hadoop operations
- $HADOOP_HOME/bin/yarn   :>>>   For YARN related operations
- $HADOOP_HOME/bin/mapred :>>>   For MapReduce realted operations
- $HADOOP_HOME/bin/hdfs  :>>>    For HDFS related operations

###Demoen Utilities:
- $HADOOP_HOME/sbin/start-yarn.sh;stop-yarn.sh
- $HADOOP_HOME/sbin/start-dfs.sh;stop-dfs.sh 
- $HADOOP_HOME/sbin/start-all.sh;stop-all.sh    
- $HADOOP_HOME/sbin/mr-jobhistory-daemon.sh start historyserver


##STEP-6
Check installation using `jps`

For master:
```
$ jps
6539 ResourceManager
6451 DataNode
8701 Jps
6895 JobHistoryServer
6234 NameNode
6765 NodeManager

For slaves:
$ jps
8014 NodeManager
7858 DataNode
9868 Jps

```

If these services are not up, check the logs in `logs` directory to identify the issue.

###Web interfaces

- NameNode:>         http://master:50070/dfshealth.jsp
- ResourceManager:>  http://master:8088/cluster
- JobHistoryServer:> http://master:19888/jobhistory

###Hadoop Native Code Loading

If you see  WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Then you have to re-compile hadoop native code manually and then put libhadoop-*.so in classpath
```
$ cd $HADOOP_HOME/src/hadoop-dist
$ mvn package -Pdist,native -Dskiptests -Dtar

You should see the newly-built library in:
$ hadoop-dist/target/hadoop-2.3.0-cdh5.1.0/lib/native

Put following lines in hadoop-env.sh file
export HADOOP_OPTS="$HADOOP_OPTS -Djava.library.path=$HADOOP_HOME/lib/"
export HADOOP_COMMON_LIB_NATIVE_DIR="path/to/native"
```
####References:
- http://www.cloudera.com/content/cloudera-content/cloudera-docs/CDH5/latest/CDH5-Installation-Guide/cdh5ig_yarn_cluster_deploy.html
- http://raseshmori.wordpress.com/2012/10/14/install-hadoop-nextgen-yarn-multi-node-cluster/
- https://www.digitalocean.com/community/articles/how-to-install-hadoop-on-ubuntu-13-10