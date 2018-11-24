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
```

# Permission denied: user=anonymous, access=WRITE, inode="/user/***":root:supergroup:drwxr-xr-x

- **问题原因**

权限的问题，连接Hive时，如果没有使用认证用户操作，就默认使用hive-default.xml文件中的anonymous用户提交任务，所以在HDFS上创建文件目录时的用户就变成了anonymous；

- **解决方法**

```
# hadoop fs -chown -R anonymous:supergroup /user
# hadoop fs -chown -R anonymous:supergroup /tmp
``
