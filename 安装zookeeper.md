# 官网
http://zookeeper.apache.org/

# 解压
```
# cd /usr/local/zookeeper

# tar -zxvf zookeeper-3.4.13.tar.gz
```

# 创建data和logs目录用于存储数据和日志
```
# cd /usr/local/zookeeper/zookeeper-3.4.13

# mkdir data

# mkdir logs
```

# 配置zoo.cfg
```
# cd /usr/local/zookeeper/zookeeper-3.4.13/conf

# vim zoo.cfg
tickTime=2000
dataDir=/usr/local/zookeeper/zookeeper-3.4.13/data
dataLogDir=/usr/local/zookeeper/zookeeper-3.4.13/logs
clientPort=2181
```

# 启动、停止、重启、查看当前节点状态
```
# cd /usr/local/zookeeper/zookeeper-3.4.13/bin

# ./zkServer.sh start

# ./zkServer.sh stop

# ./zkServer.sh restart

# ./zkServer.sh status
```
