# 官网
http://hbase.apache.org/

**我的hadoop版本是2.9.0，所以需要下载hbase-1.2.6。**

# 解压
```
# cd /usr/local/hbase

# tar -zxvf hbase-1.2.6-bin.tar.gz
```

# 配置环境变量
```
# vim /etc/profile
export HBASE_HOME=/usr/local/hbase/hbase-1.2.6
export PATH=$PATH:${HBASE_HOME}/bin

# source /etc/profile
```

# 配置hbase-site.xml
```
# cd /usr/local/hbase/hbase-1.2.6

# mkdir temp

# mkdir pids

# cd /usr/local/hbase/hbase-1.2.6/conf

# vim hbase-site.xml
<configuration>
 <property>
  <name>hbase.rootdir</name>
  <value>hdfs://spark1:9000/hbase</value>
  <description>The directory shared byregion servers.</description>
 </property>

 <property>
  <name>hbase.zookeeper.property.clientPort</name>
  <value>2181</value>
  <description>Property from ZooKeeper'sconfig zoo.cfg. The port at which the clients will connect. </description>
  </property>
 
  <property>
   <name>zookeeper.session.timeout</name>
   <value>120000</value>
  </property>

  <property>
   <name>hbase.zookeeper.quorum</name>
   <value>spark1</value>
  </property>

  <property>
   <name>hbase.tmp.dir</name>
   <value>/usr/local/hbase/hbase-1.2.6/tmp</value>
  </property>

  <property>
   <name>hbase.cluster.distributed</name>
   <value>true</value>
  </property>
</configuration>
```

# 修改regionservers文件
```
# cd /usr/local/hbase/hbase-1.2.6/conf

# vim regionservers
spark1
```

# 配置hbase-env.sh
```
# cd /usr/local/hbase/hbase-1.2.6/conf

# vim hbase-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151
export HBASE_MANAGES_ZK=false
# export HADOOP_HOME=/usr/local/hadoop/hadoop-2.9.0
# export HBASE_HOME=/usr/local/hbase/hbase-1.2.6
# export HBASE_CLASSPATH=/usr/local/hadoop/hadoop-2.9.0/etc/hadoop
# export HBASE_PID_DIR=/usr/local/hbase/hbase-1.2.6/pids
```
**如果HBASE_MANAGES_ZK=true，说明hbase使用自带的zookeeper。**

# 启动
启动Hbase之前，先启动hadoop和zookkeeper（如果HBASE_MANAGES_ZK=false）
```
# cd /usr/local/hbase/hbase-1.2.6/bin

# ./start-hbase.sh

# jps
6032 NodeManager
71920 HMaster
5203 NameNode
6229 ResourceManager
6503 DataNode
18844 QuorumPeerMain
72573 Jps
5709 SecondaryNameNode
70287 ZooKeeperMain
72046 HRegionServer
```

# 验证
访问WEB UI
master | regionServer
-- | --
http://spark1:16010 | http://spark1:16030
