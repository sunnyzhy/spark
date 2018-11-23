# User: root is not allowed to impersonate anonymous报错

- **解决方法**

1. **修改hadoop的配置文件core-site.xml**

```
# cd /usr/local/hadoop/hadoop-2.9.0/etc/hadoop

# vim core-site.xml
  <property>
    <name>hadoop.proxyuser.root.hosts</name>
    <value>*</value>
  </property>

  <property>
    <name>hadoop.proxyuser.root.groups</name>
    <value>*</value>
  </property>
```

2. **重启hadoop**

- **问题原因**

hadoop.proxyuser.root.hosts配置项名称中root部分为报错User:* 中的用户名部分。因为root不允许，所以报错。

# Unable to move source hdfs:xx to destination xxx
**解决方法**

把$HADOOP_HOME/share/hadoop/tools/lib/hadoop-distcp-x.x.x.jar复制到hive的lib目录
```
# cp /usr/local/hadoop/hadoop-2.9.0/share/hadoop/tools/lib/hadoop-distcp-2.9.0.jar /usr/local/hive/apache-hive-2.3.4-bin/lib/hadoop-distcp-2.9.0.jar
``

# Permission denied: user=anonymous, access=WRITE, inode="...":root:supergroup:drwxr-xr-x
**解决方法**

```
# hadoop fs -chmod 777 /user/hive/warehouse
``
