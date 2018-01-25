# 官网
http://hadoop.apache.org/

# 解压
```
# cd /usr/local/hadoop
# tar -zxvf hadoop-2.7.5.tar.gz
```

# 在hadoop目录下创建子目录
```
# cd hadoop-2.7.5
# mkdir tmp
# mkdir name
# mkdir data
```

# 配置hadoop-env.sh
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# vim hadoop-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151
export PATH=$PATH:/usr/local/hadoop/hadoop-2.7.5/bin

# source hadoop-env.sh

# hadoop version
Hadoop 2.7.5
Subversion https://shv@git-wip-us.apache.org/repos/asf/hadoop.git -r 18065c2b6806ed4aa6a3187d77cbe21bb3dba075
Compiled by kshvachk on 2017-12-16T01:06Z
Compiled with protoc 2.5.0
From source with checksum 9f118f95f47043332d51891e37f736e9
This command was run using /usr/local/hadoop/hadoop-2.7.5/share/hadoop/common/hadoop-common-2.7.5.jar
```
# 配置日志级别
```
# export HADOOP_ROOT_LOGGER=DEBUG,console
# export HADOOP_ROOT_LOGGER=INFO,console
```

# 配置yarn-env.sh
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# vim yarn-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151

# source yarn-env.sh
```

# 配置core-site.xml
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# vim core-site.xml
<configuration>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://spark1:9000</value>
  </property>

  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://spark1:9000</value>
  </property>

  <property>
    <name>io.file.buffer.size</name>
    <value>131072</value>
  </property>

  <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/usr/local/hadoop/hadoop-2.7.5/tmp</value>
    <description>A base for other temporary directories.</description>
  </property>

  <property>
    <name>hadoop.proxyuser.hduser.hosts</name>
    <value>*</value>
  </property>

  <property>
    <name>hadoop.proxyuser.hduser.groups</name>
    <value>*</value>
  </property>
</configuration>
```
    fs.default.name的值是hdfs://master:9000

# 配置hdfs-site.xml
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# vim hdfs-site.xml
<configuration>
  <property>
   <name>dfs.namenode.secondary.http-address</name>
   <value>spark1:9001</value>
  </property>

  <property>
   <name>dfs.namenode.name.dir</name>
   <value>file:/usr/local/hadoop/hadoop-2.7.5/name</value>
  </property>

  <property>
   <name>dfs.datanode.data.dir</name>
   <value>file:/usr/local/hadoop/hadoop-2.7.5/data</value>
  </property>

  <property>
   <name>dfs.replication</name>
   <value>2</value>
  </property>

  <property>
   <name>dfs.webhdfs.enabled</name>
   <value>true</value>
  </property>
</configuration>
```

# 配置mapred-site.xml
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# cp mapred-site.xml.template mapred-site.xml

# vim mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>

  <property>
    <name>mapreduce.jobhistory.address</name>
    <value>spark1:10020</value>
  </property>

  <property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>spark1:19888</value>
  </property>
</configuration>
```

# 配置yarn-site.xml
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# vim yarn-site.xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>

  <property>
    <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>

  <property>
    <name>yarn.resourcemanager.address</name>
    <value>spark1:8032</value>
  </property>

  <property>
    <name>yarn.resourcemanager.scheduler.address</name>
    <value>spark1:8030</value>
  </property>

  <property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>spark1:8031</value>
  </property>

  <property>
    <name>yarn.resourcemanager.admin.address</name>
    <value>spark1:8033</value>
  </property>

  <property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>spark1:8088</value>
  </property>
</configuration>
```

# 配置Slaves
```
# cd /usr/local/hadoop/hadoop-2.7.5/etc/hadoop

# vim slaves
spark2
```

# 向各节点分发Hadoop程序
```
# cd /usr/local/hadoop

# scp -r hadoop-2.7.5 root@spark2:/usr/local/hadoop/hadoop-2.7.5
```

# 格式化NameNode
```
# cd /usr/local/hadoop/hadoop-2.7.5

# ./bin/hdfs namenode -format
2018-01-25 14:08:46,208 INFO  [main] namenode.NameNode (LogAdapter.java:info(47)) - STARTUP_MSG: 
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = spark1/192.168.253.107
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 2.7.5
...
STARTUP_MSG:   java = 1.8.0_151
************************************************************/
2018-01-25 14:08:46,328 INFO  [main] namenode.NameNode (LogAdapter.java:info(47)) - registered UNIX signal handlers for [TERM, HUP, INT]
2018-01-25 14:08:46,355 INFO  [main] namenode.NameNode (NameNode.java:createNameNode(1441)) - createNameNode [-format]
...
2018-01-25 14:08:49,857 INFO  [main] common.Storage (NNStorage.java:format(568)) - Storage directory /tmp/hadoop-root/dfs/name has been successfully formatted.
...
2018-01-25 14:08:50,232 INFO  [Thread-1] namenode.NameNode (LogAdapter.java:info(47)) - SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at spark1/192.168.253.107
************************************************************/
```

# 启动HDFS
```
# cd /usr/local/hadoop/hadoop-2.7.5/sbin

# ./start-dfs.sh

# jps
13315 Jps
11878 DataNode
11753 NameNode
12079 SecondaryNameNode
```
    spark1上面运行的进程有：NameNode、SecondaryNameNode和DataNode

```
# jps
3976 Jps
3759 DataNode
```
    spark2上面运行的进程有：DataNode

# 启动YARN
```
# cd /usr/local/hadoop/hadoop-2.7.5/sbin

# ./start-yarn.sh

# jps
14449 Jps
13970 NodeManager
11878 DataNode
11753 NameNode
14121 ResourceManager
12079 SecondaryNameNode
```
    此时在spark1上运行的进程有：NameNode、SecondaryNameNode、DataNode、NodeManager和ResourceManager

```
# jps
25245 Jps
25163 NodeManager
24910 DataNode
```
    此时在spark2上运行的进程有：DataNode和NodeManager

# 配置环境变量
``
# vim /etc/profile
export HADOOP_HOME=/usr/local/hadoop/hadoop-2.7.5
export PATH=.:$HADOOP_HOME/bin:$JAVA_HOME/bin:$PATH

# source /etc/profile
```

# 查看
```
# cd /usr/local/hadoop/hadoop-2.7.5

# cd lib/native

# file ./libhadoop.so.1.0.0
./libhadoop.so.1.0.0: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, BuildID[sha1]=612c3e78dc66f30ab64ef032524d92022eebe34e, not stripped
```
    ELF 64-bit LSB，说明是64位
 
# 验证
- 在浏览器的地址栏中输入http://spark1:50070
- Live Nodes 	:	2 (Decommissioned: 0)，节点数为2就说明hadoop集群启动成功
- 点开Live Nodes就可以看到集群spark1、spark2的监控信息
```
# hadoop checknative
2016-11-14 10:07:38,227 WARN  [main] bzip2.Bzip2Factory (Bzip2Factory.java:isNativeBzip2Loaded(73)) - Failed to load/initialize native-bzip2 library system-native, will use pure-Java version
2016-11-14 10:07:38,231 INFO  [main] zlib.ZlibFactory (ZlibFactory.java:<clinit>(49)) - Successfully loaded & initialized native-zlib library
Native library checking:
hadoop:  true /usr/local/hadoop/hadoop-2.6.0/lib/native/libhadoop.so.1.0.0
zlib:    true /lib64/libz.so.1
snappy:  true /usr/lib64/libsnappy.so.1
lz4:     true revision:99
bzip2:   false 
openssl: false Cannot load libcrypto.so (libcrypto.so: cannot open shared object file: No such file or directory)!
解决方法：
1.bzip2:   false
# yum install bzip2 //安装bzip2
2.openssl: false Cannot load libcrypto.so (libcrypto.so: cannot open shared object file: No such file or directory)!
# cd /usr/lib64/
# ln -s libcrypto.so.1.0.1e libcrypto.so

# 上传数据到HDFS中
```
# cd /usr/local/hadoop/hadoop-2.7.5

# hadoop fs -mkdir -p /user/hadoop/testdata --http://spark1:50070/dfshealth.jsp，打开Browse the filesystem，就可以看到新建的目录，其中user就HDFS的系统目录

# hadoop fs -put /usr/local/hadoop/hadoop-2.7.5/etc/hadoop/core-site.xml /user/hadoop/testdata  --把hadoop配置文件core-site.xml上传到HDFS中，打开Browse the filesystem，就会在testdata看到core-site.xml
或者
# ./bin/hdfs dfs -mkdir -p /user/hadoop/testdata

# ./bin/hdfs dfs -put /usr/local/hadoop/hadoop-2.7.5/etc/hadoop/core-site.xml /user/hadoop/testdata

# ./bin/hdfs dfs -ls /user/hadoop/testdata --查看文件系统
Found 1 items
-rw-r--r--   2 root supergroup       1363 2016-11-12 12:07 /user/hadoop/testdata/core-site.xml
```

# 测试mapred
```
# cd /usr/local/hadoop/hadoop-2.6.0

# hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar wordcount /user/hadoop/testdata /user/hadoop/result

# hadoop fs -cat /user/hadoop/result/part-r-00000  --结果在result下面的part-r-00000中
或者
# ./bin/hdfs dfs -cat /user/hadoop/result/part-r-00000 

"AS	1
"License");	1
(the	1
-->	2
2.0	1
<!--	2
</configuration>	1
</property>	6
<?xml	1
<?xml-stylesheet	1
<configuration>	1
<description>A	1
<name>fs.default.name</name>	1
<name>fs.defaultFS</name>	1
<name>hadoop.proxyuser.hduser.groups</name>	1
<name>hadoop.proxyuser.hduser.hosts</name>	1
<name>hadoop.tmp.dir</name>	1
<name>io.file.buffer.size</name>	1
<property>	6
<value>*</value>	2
<value>131072</value>	1
<value>file:/usr/local/hadoop/hadoop-2.6.0/tmp</value>	1
<value>hdfs://hadoop1:9000</value>	2
ANY	1
Apache	1
BASIS,	1
CONDITIONS	1
IS"	1
KIND,	1
LICENSE	1
License	3
License,	1
License.	2
Licensed	1
OF	1
OR	1
Put	1
See	2
Unless	1
Version	1
WARRANTIES	1
WITHOUT	1
You	1
a	1
accompanying	1
agreed	1
an	1
and	1
applicable	1
at	1
base	1
by	1
compliance	1
copy	1
directories.</description>	1
distributed	2
either	1
encoding="UTF-8"?>	1
except	1
express	1
file	1
file.	2
for	2
governing	1
href="configuration.xsl"?>	1
http://www.apache.org/licenses/LICENSE-2.0	1
implied.	1
in	3
is	1
language	1
law	1
limitations	1
may	2
not	1
obtain	1
of	1
on	1
or	2
other	1
overrides	1
permissions	1
property	1
required	1
site-specific	1
software	1
specific	1
temporary	1
the	7
this	2
to	1
type="text/xsl"	1
under	3
use	1
version="1.0"	1
with	1
writing,	1
you	1
```
