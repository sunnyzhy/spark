# 官网
http://hadoop.apache.org/

# 安装依赖
- ssh
- jdk

# 解压
```
# cd /usr/local/hadoop
# tar -zxvf hadoop-2.9.0.tar.gz
```

# 配置环境变量
```
# vim /etc/profile
export HADOOP_HOME=/usr/local/hadoop/hadoop-2.9.0
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/bin

# source /etc/profile

# hadoop version
Hadoop 2.9.0
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r 756ebc8394e473ac25feac05fa493f6d612e6c50
Compiled by arsuresh on 2017-11-13T23:15Z
Compiled with protoc 2.5.0
From source with checksum 0a76a9a32a5257331741f8d5932f183
This command was run using /usr/local/hadoop/hadoop-2.9.0/share/hadoop/common/hadoop-common-2.9.0.jar
```

# 在hadoop目录下创建子目录
```
# cd hadoop-2.9.0
# mkdir -p tmp/dfs/{data,name}
```
    创建目录结构的语法：{tmp,hdfs/{data,name}}

# 配置core-site.xml
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim core-site.xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://spark1:9000</value>
  </property>

  <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/usr/local/hadoop/hadoop-2.9.0/tmp</value>
  </property>
</configuration>
```
    fs.default.name的值是hdfs://master:9000

# 配置hdfs-site.xml
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim hdfs-site.xml
<configuration>
  <property>
   <name>dfs.namenode.name.dir</name>
   <value>file:/usr/local/hadoop/hadoop-2.9.0/tmp/dfs/name</value>
  </property>

  <property>
   <name>dfs.datanode.data.dir</name>
   <value>file:/usr/local/hadoop/hadoop-2.9.0/tmp/dfs/data</value>
  </property>

  <property>
   <name>dfs.replication</name>
   <value>2</value>
  </property>
</configuration>
```

# 配置mapred-site.xml
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# cp mapred-site.xml.template mapred-site.xml

# vim mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

# 配置yarn-site.xml
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim yarn-site.xml
<configuration>
  <property>
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
  </property>
  <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>spark1</value>
  </property>
</configuration>
```

# 配置hadoop-env.sh
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim hadoop-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151

# source hadoop-env.sh
```

# 配置yarn-env.sh
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim yarn-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151

# source yarn-env.sh
```

# 配置mapred-env.sh
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim mapred-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151

# source mapred-env.sh
```

# 配置Slaves
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim slaves
spark2
```

# 向各节点分发Hadoop程序
```
# cd /usr/local/hadoop

# scp -r hadoop-2.9.0 root@spark2:/usr/local/hadoop/hadoop-2.9.0
```

# 重启
```
# shutdown -r 0
```

# 格式化NameNode
```
# cd /usr/local/hadoop/hadoop-2.9.0/bin

# ./hdfs namenode -format
18/01/30 14:41:20 INFO namenode.NameNode: STARTUP_MSG: 
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = localhost/127.0.0.1
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 2.9.0
...
STARTUP_MSG:   java = 1.8.0_151
************************************************************/
...
18/01/30 14:41:24 INFO common.Storage: Storage directory /usr/local/hadoop/hadoop-2.9.0/tmp/dfs/name has been 
successfully formatted.
...
18/01/30 14:41:25 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at localhost/127.0.0.1
************************************************************/
```

# 启动HDFS
```
# cd /usr/local/hadoop/hadoop-2.9.0/sbin

# ./start-dfs.sh

# jps
13315 Jps
11878 DataNode
11753 NameNode
12079 SecondaryNameNode
```
    spark1上面运行的守护进程有：NameNode、SecondaryNameNode和DataNode

```
# jps
23976 Jps
23759 DataNode
```
    spark2上面运行的守护进程有：DataNode

# 启动YARN
```
# cd /usr/local/hadoop/hadoop-2.9.0/sbin

# ./start-yarn.sh

# jps
13315 Jps
13970 NodeManager
11878 DataNode
11753 NameNode
14121 ResourceManager
12079 SecondaryNameNode
```
    此时在spark1上运行的守护进程有：NameNode、SecondaryNameNode、DataNode、NodeManager和ResourceManager

```
# jps
23976 Jps
25163 NodeManager
23759 DataNode
```
    此时在spark2上运行的守护进程有：DataNode和NodeManager
    
# 验证
- 在浏览器的地址栏中输入http://spark1:50070

- Live Nodes 	:	2 (Decommissioned: 0)，节点数为2就说明hadoop集群启动成功

- 点开Live Nodes就可以看到集群spark1、spark2的监控信息

# 测试HDFS
- 创建目录
```
# hadoop fs -mkdir -p /user/hadoop/test

# hadoop fs -ls /
Found 1 items
drwxr-xr-x   - root supergroup          0 2018-01-30 16:04 /user

# hadoop fs -ls /user
Found 1 items
drwxr-xr-x   - root supergroup          0 2018-01-30 16:41 /user/hadoop

# hadoop fs -ls /user/hadoop
Found 1 items
drwxr-xr-x   - root supergroup          0 2018-01-30 16:41 /user/hadoop/test
```

- 上传文件
```
# hadoop fs -put /usr/local/hadoop/hadoop-2.9.0/etc/hadoop/core-site.xml /user/hadoop/test

# hadoop fs -ls /user/hadoop/test
Found 1 items
-rw-r--r--   1 root supergroup        988 2018-01-30 16:09 /user/hadoop/test/core-site.xml
```

- 查看文件
```
# hadoop fs -cat /user/hadoop/test/core-site.xml
```

- 删除目录下的所有文件
```
# hadoop fs -rm -r /user/hadoop/test
Deleted /user/hadoop/test
```

- 删除目录
```
# hadoop fs -rm -r /user/hadoop/test
Deleted /user/hadoop/test
```
    -r：如果指定的目录下面有文件，就删除该目录下的所有文件，但是不删除该目录；如果指定的目录下面没有文件，就删除该目录。
    可以在hadoop的web界面查看HDFS，http://spark1:50070 -> Utilites -> Browse the filesystem

# 测试mapred
```
# hadoop fs -mkdir -p /user/hadoop/test

# hadoop jar /usr/local/hadoop/hadoop-2.9.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.9.0.jar wordcount /user/hadoop/test /user/hadoop/result

# hadoop fs -cat /user/hadoop/result/part-r-00000

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
