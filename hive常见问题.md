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
