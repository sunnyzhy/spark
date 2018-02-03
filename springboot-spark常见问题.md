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
配置spark.executor.memory属性
```
      val spark = SparkSession
      .builder()
      .appName("SparkWithSpring")
      .master("spark://192.168.253.107:7077")
      .config("spark.executor.memory", "512m")
      .getOrCreate();
```
