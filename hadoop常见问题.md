# Cannot find configuration directory: /etc/hadoop
WARN  [main] util.NativeCodeLoader (NativeCodeLoader.java:<clinit>(62)) - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable]
Error: Cannot find configuration directory: /etc/hadoop
Error: Cannot find configuration directory: /etc/hadoop
解决方法，把127.0.0.1修改为本地IP地址：
```
# vim /etc/hosts
#127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.253.107   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.253.107 spark1
192.168.253.108 spark2

# systemctl restart network
```

# NameNode启动失败
- 第一种情况，HDFS把namenode的格式化信息存在了系统的tmp目录下，该目录每次开机都会被清空，因此每次重新启动机器，都需要重新格式化HDFS。
- 第二种情况，Failed to start namenode.java.io.IOException: There appears to be a gap in the edit log.  We expected txid 1, but got txid 197.
```
# hadoop namenode -recover
```
    选 c 

# DataNode启动失败
- 第一种情况，name和data的clusterID不一致
```
# cd /usr/local/hadoop/hadoop-2.9.0/name/current

# vim VERSION
clusterID=CID-eb45aa78-5e50-45cb-b6c0-06aa9cd4c476

# cd /usr/local/hadoop/hadoop-2.9.0/data/current

# vim VERSION
clusterID=CID-eb45aa78-5e50-45cb-b6c0-06aa9cd4c476 //复制name的clusterID
```
- 第二种情况，重启系统之后，DataNode不启动
```
# cd /usr/local/hadoop/hadoop-2.9.0/sbin

# ./hadoop-daemon.sh start datanode
```

# NodeManager启动失败
```
# cd /usr/local/hadoop/hadoop-2.9.0/sbin

# ./yarn-daemon.sh start nodemanager
```

# WARN org.apache.hadoop.hdfs.server.datanode.DataNode: IOException in offerService java.net.ConnectException: Call From localhost/127.0.0.1 to spark1:9000 failed on connection exception: java.net.ConnectException: Connection refused;
# WARN org.apache.hadoop.hdfs.server.datanode.DataNode: Problem connecting to server: spark1/192.168.253.107:9000
```
# systemctl stop firewalld

# systemctl disable firewalld
```

# Yarn application has already ended! It might have been killed or unable to launch application master
配置yarn-site.xml，以下2个配置二选一即可
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim yarn-site.xml
<!--虚拟内存设置是否生效,若实际虚拟内存大于设置值 ,spark 以client模式运行可能会报错,"Yarn application has already ended! It might have been killed or unable to l"-->
<property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
</property>

<!--配置虚拟内存/物理内存的值，默认为2.1,物理内存默认应该是1g，所以虚拟内存是2.1g-->
<property>
    <name>yarn.nodemanager.vmem-pmem-ratio</name>
    <value>4</value>
</property>
```

# DEBUG util.NativeCodeLoader: Failed to load native-hadoop with error: java.lang.UnsatisfiedLinkError: no hadoop in java.library.path
# WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
添加配置HADOOP_OPTS
```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim hadoop-env.sh
export HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib/native"

# source hadoop-env.sh
```

# Exception in thread "main" org.apache.hadoop.security.AccessControlException: Permission denied: user=Administrator, access=WRITE, inode="/spark/global":root:supergroup:drwxr-xr-x
```
# hadoop fs -chmod 777 /user/hadoop/test
```
