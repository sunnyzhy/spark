# WARN yarn.Client: Neither spark.yarn.jars nor spark.yarn.archive is set, falling back to uploading libraries under SPARK_HOME.
- 问题描述
```
18/02/07 13:49:13 WARN yarn.Client: Neither spark.yarn.jars nor spark.yarn.archive is set, falling back to uploading libraries under SPARK_HOME.
18/02/07 13:49:28 INFO yarn.Client: Uploading resource file:/tmp/spark-bf8cc467-067b-4487-8ec5-e0634954e031/__spark_libs__3057205161304809210.zip -> hdfs://spark1:9000/user/root/.sparkStaging/application_1517910467343_0003/__spark_libs__3057205161304809210.zip
18/02/07 13:49:34 INFO yarn.Client: Uploading resource file:/tmp/spark-bf8cc467-067b-4487-8ec5-e0634954e031/__spark_conf__4399354941772472106.zip -> hdfs://spark1:9000/user/root/.sparkStaging/application_1517910467343_0003/__spark_conf__.zip
```
- 解决方法
```
# hadoop fs -mkdir -p /home/hadoop/spark_jars

# hadoop fs -put /usr/local/spark/spark-2.2.1-bin-hadoop2.7/jars/* /home/hadoop/spark_jars

# cd /usr/local/spark/spark-2.2.1-bin-hadoop2.7/conf

# cp spark-defaults.conf.template spark-defaults.conf

# vim spark-defaults.conf
spark.yarn.jars=hdfs://spark1:9000/usr/local/spark/spark-2.2.1-bin-hadoop2.7/jars/* /home/hadoop/spark_jars/
```

# java.lang.ClassCastException: cannot assign instance of scala.collection.immutable.List$SerializationProxy to field
1. 添加本地项目的jar文件
```
sc.addJar("../xxx.jar")
```
2. 把xxx.jar文件上传到 /usr/local/spark/spark-2.2.1-bin-hadoop2.7/jars 目录
