# 官网
http://hive.apache.org/downloads.html

# 解压
```
# cd /usr/local/hive

# tar -zxvf apache-hive-2.3.4-bin.tar.gz
```

# 配置环境变量
```
# vim /etc/profile
export HIVE_HOME=/usr/local/hive/apache-hive-2.3.4-bin
export HIVE_CONF_DIR=${HIVE_HOME}/conf
export CLASS_PATH=$CLASS_PATH:${HIVE_HOME}/lib
export PATH=$PATH:${HIVE_HOME}/bin

# source /etc/profile
```

# 新建hdfs目录
```
# hadoop fs -mkdir -p /user/hive/warehouse

# hadoop fs -chmod 777 /user/hive/warehouse

# hadoop fs -mkdir -p /tmp/hive

# hadoop fs -chmod 777 /tmp/hive
```

# 配置hive-site.xml
```
# cd /usr/local/hive/apache-hive-2.3.4-bin/conf

# cp hive-default.xml.template hive-site.xml
```
1. 把${system:java.io.tmpdir}替换为hive的临时目录/usr/local/hive/apache-hive-2.3.4-bin/tmp

```
  <property>
    <name>hive.exec.local.scratchdir</name>
    <value>/usr/local/hive/apache-hive-2.3.4-bin/tmp/${system:user.name}</value>
    <description>Local scratch space for Hive jobs</description>
  </property>
  <property>
    <name>hive.downloaded.resources.dir</name>
    <value>/usr/local/hive/apache-hive-2.3.4-bin/tmp/${hive.session.id}_resources</value>
    <description>Temporary local directory for added resources in the remote file system.</description>
  </property>
  <property>
    <name>hive.querylog.location</name>
    <value>/usr/local/hive/apache-hive-2.3.4-bin/tmp/${system:user.name}</value>
    <description>Location of Hive run time structured log file</description>
  </property>
  <property>
    <name>hive.server2.logging.operation.log.location</name>
    <value>/usr/local/hive/apache-hive-2.3.4-bin/tmp/${system:user.name}/operation_logs</value>
    <description>Top level directory where operation logs are stored if logging functionality is enabled</description>
  </property>
```

2. 把${system:user.name}替换为root

```
  <property>
    <name>hive.exec.local.scratchdir</name>
    <value>${system:java.io.tmpdir}/root</value>
    <description>Local scratch space for Hive jobs</description>
  </property>
  <property>
    <name>hive.querylog.location</name>
    <value>${system:java.io.tmpdir}/root</value>
    <description>Location of Hive run time structured log file</description>
  </property>
  <property>
    <name>hive.server2.logging.operation.log.location</name>
    <value>${system:java.io.tmpdir}/root/operation_logs</value>
    <description>Top level directory where operation logs are stored if logging functionality is enabled</description>
  </property>
```

