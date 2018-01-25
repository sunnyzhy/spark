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
# cd /usr/local/hadoop/hadoop-2.7.5/name/current

# vim VERSION
clusterID=CID-eb45aa78-5e50-45cb-b6c0-06aa9cd4c476

# cd /usr/local/hadoop/hadoop-2.7.5/data/current

# vim VERSION
clusterID=CID-eb45aa78-5e50-45cb-b6c0-06aa9cd4c476 //复制name的clusterID
```
- 第二种情况，重启系统之后，DataNode不启动
```
# cd /usr/local/hadoop/hadoop-2.7.5/sbin

# ./hadoop-daemon.sh start datanode
```

# NodeManager启动失败
```
# cd /usr/local/hadoop/hadoop-2.7.5/sbin

# ./yarn-daemon.sh start nodemanager
```

# WARN org.apache.hadoop.hdfs.server.datanode.DataNode: IOException in offerService
java.net.ConnectException: Call From localhost/127.0.0.1 to spark1:9000 failed on connection exception: java.net.ConnectException: Connection refused;
# WARN org.apache.hadoop.hdfs.server.datanode.DataNode: Problem connecting to server: spark1/192.168.253.107:9000
```
# systemctl stop firewalld

# systemctl disable firewalld
```

# WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-java classes where applicable
- 第一种情况，libc.so.6: version `GLIBC_2.14' not found
因为系统的gblci版本太低，需要安装2.14以上的版本：
下载glibc2.14，http://www.gnu.org/software/libc/
```
# tar -zxvf /..目录/glibc-2.14.tar.gz -C /usr/local/glibc

# cd /usr/local/glibc

# ./glibc-2.14/configure --prefix=/usr/local/glibc  //glibc不允许在源码目录树下编译，必须在其他目录configure和build，这样一旦出错将整个目录移除即可

# make

# make install
```

注意：
安装过程中，如果出现Can't open configuration file /usr/local/glibc/etc/ld.so.conf: No such file or directory
```
# cp /etc/ld.so.c* /usr/local/glibc/etc

# ln -sf /usr/local/glibc/lib/libc-2.14.so /lib64/libc.so.6 //建立软链指向glibc-2.14
```

误删libc.so.6解决方法：
```
# LD_PRELOAD=/lib64/libc-2.12.so ln -s /lib64/libc-2.12.so libc.so.6

# strings /lib64/libc.so.6 | grep GLIBC //显示GLIBC_2.14，说明安装成功
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_PRIVATE
```

- 第二种情况，在调用spark-shell时，提示Failed to load native-hadoop with error: java.lang.UnsatisfiedLinkError: no hadoop in java.library.path
```
# cd /usr/local/spark/spark-2.0.1-bin-hadoop2.6/conf

# cp spark-env.sh.template spark-env.sh

# vim spark-env.sh
export JAVA_LIBRARY_PATH=$JAVA_LIBRARY_PATH:$HADOOP_HOME/lib/native
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HADOOP_HOME/lib/native
export SPARK_YARN_USER_ENV="JAVA_LIBRARY_PATH=$JAVA_LIBRARY_PATH,LD_LIBRARY_PATH=$LD_LIBRARY_PATH"

# source spark-env.sh
```
