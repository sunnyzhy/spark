# 官网
http://hbase.apache.org/

# 解压
```
# cd /usr/local/hbase

# tar -zxvf hbase-2.1.1-bin.tar.gz
```

# 配置环境变量
```
# vim /etc/profile
export HBASE_HOME=/usr/local/hbase/hbase-2.1.1
export PATH=$PATH:${HBASE_HOME}/bin

# source /etc/profile
```

# 配置hbase-site.xml
```
# cd /usr/local/hbase/hbase-2.1.1

# mkdir temp

# mkdir pids

# cd /usr/local/hbase/hbase-2.1.1/conf

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
   <value>/usr/local/hbase/hbase-2.1.1/tmp</value>
  </property>

  <property>
   <name>hbase.cluster.distributed</name>
   <value>true</value>
  </property>
</configuration>
```

# 修改regionservers文件
```
# cd /usr/local/hbase/hbase-2.1.1/conf

# vim regionservers
localhost
```

# 配置hbase-env.sh
```
# cd /usr/local/hbase/hbase-2.1.1/conf

# vim hbase-env.sh
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_151
export HBASE_MANAGES_ZK=false
# export HADOOP_HOME=/usr/local/hadoop/hadoop-2.9.0
# export HBASE_HOME=/usr/local/hbase/hbase-2.1.1
# export HBASE_CLASSPATH=/usr/local/hadoop/hadoop-2.9.0/etc/hadoop
# export HBASE_PID_DIR=/usr/local/hbase/hbase-2.1.1/pids
```
**如果HBASE_MANAGES_ZK=true，说明hbase使用自带的zookeeper。**

# 启动
启动Hbase之前，先启动hadoop和zookkeeper（如果HBASE_MANAGES_ZK=false）
```
# cd /usr/local/hbase/hbase-2.1.1/bin

# ./start-hbase.sh
```

# 验证
http://192.168.197.107:16030/
