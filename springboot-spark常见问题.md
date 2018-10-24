#  java.io.IOException: Could not locate executable null\bin\winutils.exe in the Hadoop binaries.
- 下载winutils

https://github.com/srccodes/hadoop-common-2.2.0-bin

- 配置环境变量

1. 新建系统变量

变量名：HADOOP_HOME

变量值：D:\spark\hadoop-common-2.2.0

2. 在系统变量Path里添加%HADOOP_HOME%\bin;

3. 重启系统

# WARN TaskSchedulerImpl: Initial job has not accepted any resources; check your cluster UI to ensure that workers are registered and have sufficient resources
```
打开http://spark1:8080/，看到Status是Wait，分配的核是0，内存是参数 –executor-memory是1024m。

这是因为当前的集群的可用资源不能满足应用程序所请求的资源。

首先检查 /usr/local/spark/spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh 文件里面分给每个worker的executor的memory是多少。当应用程序中设置的参数超过这个数值的时候，应用程序就不会找这个worker进行工作，而是去找满足数值的worker。所以遇到这种情况基本是因为应用程序设置的参数超过了分配给worker的executor的memory的数值。此时，只要修改应用程序的executor.memory就可以了。
```
- 解决方法1

配置spark.executor.memory属性
```
      val spark = SparkSession
      .builder()
      .appName("SparkWithSpring")
      .master("spark://192.168.253.107:7077")
      .config("spark.executor.memory", "512m")
      .getOrCreate();
```

- 解决方法2

```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim yarn-site.xml
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>4096</value>
    </property>
    <property>
        <name>yarn.nodemanager.resource.cpu-vcore</name>
        <value>2</value>
    </property>
    <property>
        <name>yarn.scheduler.maximum-allocation-vcores</name>
        <value>2</value>
    </property>
```

# Unable to find encoder for type stored in a Dataset.
```
dataset.map -> dataset.rdd.map
```
