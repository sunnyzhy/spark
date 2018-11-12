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
