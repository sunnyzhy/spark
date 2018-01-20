# 下载
下载地址http://spark.apache.org/

# 解压
```
# cd /usr/local/spark

# tar -zxvf spark-2.2.1-bin-hadoop2.7.tgz
```

# 修改主机名
```
# vim /etc/hosts
127.0.0.1   spark1 ocalhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

# systemctl restart network
```

# 配置系统变量
```
# vim /etc/profile
export SPARK_HOME=/usr/local/spark/spark-2.2.1-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

# source /etc/profile
```

# 配置slaves
```
# cd /usr/local/spark/spark-2.2.1-bin-hadoop2.7/conf

# cp slaves.template slaves

# vim slaves
spark1
spark2
```

# 配置spark-env.sh
```
# cp spark-env.sh.template spark-env.sh

# vim spark-env.sh
export JAVA_HOME=/usr/local/jdk
export SPARK_MASTER_IP=spark1
export SPARK_MASTER_PORT=7077
export SPARK_WORKER_CORES=1
export SPARK_WORKER_INSTANCES=1
export SPARK_WORKER_MEMORY=512M

# source spark-env.sh
```

# 向各节点分发spark程序
```
# cd /usr/local/spark

# scp -r spark-2.2.1-bin-hadoop2.7 root@spark2:/usr/local/spark/spark-2.2.1-bin-hadoop2.7
```

# 启动Spark
```
# cd /usr/local/spark/spark-2.2.1-bin-hadoop2.7/sbin

# ./start-all.sh
starting org.apache.spark.deploy.master.Master, logging to /usr/local/spark/spark-2.2.1-bin-hadoop2.7/logs/spark-root-org.apache.spark.deploy.master.Master-1-spark1.out
spark1: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark/spark-2.2.1-bin-hadoop2.7/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-spark1.out
spark2: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark/spark-2.2.1-bin-hadoop2.7/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-spark2.out
```

# 验证
此时在spark1上面运行的进程有：Worker和Master
```
# jps
2921 Master
2988 Worker
3087 Jps
```

在spark2上面运行的进程有只有Worker
```
# jps
5537 Worker
5620 Jps
```

查看spark1节点网络情况，8080和7077端口处于侦听状态
```
# netstat -nlt
tcp6       0      0 :::8080                 :::*                    LISTEN
tcp6       0      0 127.0.0.1:7077          :::*                    LISTEN
```

在浏览器中输入 http://spark1:8080，可以进入Spark集群状态页面
```
    URL: spark://spark1:7077
    REST URL: spark://spark1:6066 (cluster mode)
    Alive Workers: 2
    Cores in use: 1 Total, 0 Used
    Memory in use: 512.0 MB Total, 0.0 B Used
    Applications: 0 Running, 0 Completed
    Drivers: 0 Running, 0 Completed
    Status: ALIVE
```
显示以下内容就说明spark集群启动成功：

1).Alive Workers: 2  
2).Workers列表包含spark1、spark2的地址

# 验证客户端连接
```
# cd /usr/local/spark/spark-2.2.1-bin-hadoop2.7/bin
# ./spark-shell --master spark://spark1:7077 --executor-memory 500m
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 2.2.1
      /_/
         
Using Scala version 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_151)
Type in expressions to have them evaluated.
Type :help for more information.

scala> 
```

# Spark的日志配置
```
# cd /usr/local/spark/spark-2.2.1-bin-hadoop2.7/conf

# cp log4j.properties.template log4j.properties

# vim log4j.properties
log4j.rootCategory=INFO, console
log4j.rootCategory=WARN, console
log4j.rootCategory=DEBUG, console
```
