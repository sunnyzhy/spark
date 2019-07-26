# 查询
语法
```
ls path [watch]

ls -s path [watch]
```
[watch] 添加一个 watch（监视器），如果该节点发生变化，watch 可以使客户端得到通知。watch 只能被触发一次。如果要一直获得znode的创建和删除的通知，那么就需要不断的在znode上开启观察模式。如果在该 path 下创建节点，会产生 NodeChildrenChanged 事件；如果在该 path 下删除节点，会产生 NodeDeleted 事件。

使用方法
```
[zk: localhost:2181(CONNECTED) 0] ls /
[zk_lock, zookeeper]

[zk: localhost:2181(CONNECTED) 1] ls -s /zk_lock
[_c_f966001c-0608-40a0-b6c2-06c0262ed92f-lock-0000000000]cZxid = 0xef
ctime = Fri Jul 26 14:40:54 CST 2019
mZxid = 0xef
mtime = Fri Jul 26 14:40:54 CST 2019
pZxid = 0xfb
cversion = 9
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
```
每个 znode 的状态信息包含以下内容：
- czxid，创建（create）该 znode 的 zxid
- mzxid，最后一次修改（modify）该 znode 的 zxid
- pzxid，最后一次修改该 znode 子节点的 zxid
- ctime，创建该 znode 的时间
- mtime，最后一次修改该 znode 的时间
- dataVersion，该节点内容的版本，每次修改内容，版本都会增加
- cversion，该节点子节点的版本
- aclVersion，该节点的 ACL 版本
- ephemeralOwner，如果该节点是临时节点（ephemeral node），会列出该节点所在客户端的 session id；如果不是临时节点，该值为 0
- dataLength，该节点存储的数据长度
- numChildren，该节点子节点的个数

# 检查状态
语法
```
stat path [watch]
```
[watch] 添加一个 watch（监视器），如果节点内容发生改变，会产生 NodeDataChanged 事件；如果删除节点，会产生 NodeDeleted 事件。

使用方法
```
[zk: localhost:2181(CONNECTED) 2] stat /zk_lock
cZxid = 0xef
ctime = Fri Jul 26 14:40:54 CST 2019
mZxid = 0xef
mtime = Fri Jul 26 14:40:54 CST 2019
pZxid = 0xfb
cversion = 9
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
```
stat 比 ls 少了子节点的数组列表。

# 创建节点
语法
```
create [-s] [-e] path data acl
```

- -s 创建有序节点

如果在创建znode时，我们使用排序标志的话，ZooKeeper会在我们指定的znode名字后面增加一个数字。我们继续加入相同名字的znode时，这个数字会不断增加。这个序号的计数器是由这些排序znode的父节点来维护的。

- -e 创建临时节点

znode有两种类型：ephemeral和persistent。在创建znode时，我们指定znode的类型，并且在之后不会再被修改。当创建znode的客户端的session结束后，ephemeral类型的znode将被删除。persistent类型的znode在创建以后，就与客户端没什么联系了，除非主动去删除它，否则他会一直存在。Ephemeral znode没有任何子节点。

使用方法：

普通节点
```
[zk: localhost:2181(CONNECTED) 6] create /node hello
Created /node

[zk: localhost:2181(CONNECTED) 7] create /node/node1 world
Created /node/node1
```

有序节点
```
[zk: localhost:2181(CONNECTED) 8] create -s /node hello
Created /node0000000010

[zk: localhost:2181(CONNECTED) 9] create -s /node/node1 world
Created /node/node10000000001
```

临时节点
```
[zk: localhost:2181(CONNECTED) 11] create -e /temp hello
Created /temp
```
重启 zkCli，/temp 节点已经被删除了。

# 获取节点信息
语法
```
get path [watch]
```
[watch] 添加一个 watch（监视器），如果节点内容发生改变，会产生 NodeDataChanged 事件；如果删除节点，会产生 NodeDeleted 事件。

使用方法
```
[zk: localhost:2181(CONNECTED) 17] get /node
hello
```

# 修改节点
语法
```
set path data [version]
```

使用方法
```
[zk: localhost:2181(CONNECTED) 18] set /node helloo

[zk: localhost:2181(CONNECTED) 19] get /node
helloo
```

# 删除节点
语法
```
delete path [version]
```
调用delete和set操作时，如果指定znode版本号，需要与当前的版本号匹配。如果版本号不匹配，操作将会失败。

使用方法
```
[zk: localhost:2181(CONNECTED) 29] create /node hello
Created /node

[zk: localhost:2181(CONNECTED) 30] delete /node
```

如果要删除的节点有子节点，不能删除
```
[zk: localhost:2181(CONNECTED) 37] create /node
Created /node

[zk: localhost:2181(CONNECTED) 38] create /node/node1
Created /node/node1

[zk: localhost:2181(CONNECTED) 39] delete /node
Node not empty: /node
```

# 删除节点
语法
```
deleteall path
```
删除节点及子节点。

使用方法
```
[zk: localhost:2181(CONNECTED) 47] create /node
Created /node

[zk: localhost:2181(CONNECTED) 48] create /node/node1
Created /node/node1

[zk: localhost:2181(CONNECTED) 49] deleteall /node
```

# 历史记录
语法
```
history
```

使用方法
```
[zk: localhost:2181(CONNECTED) 50] history
40 - create /node
41 - delete /node/node1
42 - delete /node
43 - create /node
44 - create /node/node1
45 - rmr /node
46 - deleteall /node
47 - create /node
48 - create /node/node1
49 - deleteall /node
50 - history
```

# 重复之前的命令
语法
```
redo cmdno
```
根据 cmdno 重复之前的命令。

使用方法
```
[zk: localhost:2181(CONNECTED) 51] redo 40
Created /node
```

# 是否输出 watch 事件
语法
```
printwatches on|off
```

使用方法
```
[zk: localhost:2181(CONNECTED) 52] printwatches on

[zk: localhost:2181(CONNECTED) 53]  printwatches off
```

# 关闭连接
语法
```
close
```

使用方法
```
[zk: localhost:2181(CONNECTED) 55] close

[zk: localhost:2181(CLOSED) 56] ls /
Not connected
```

# 连接远程的 zk 服务
语法
```
connect host:port
```

使用方法
```
[zk: localhost:2181(CONNECTED) 0] connect 192.168.0.10:2181
```

# 退出连接
语法
```
quit
```

使用方法
```
[zk: 192.168.0.10:2181(CONNECTED) 1] quit
```

# 强制同步
语法
```
sync path
```
sync方法会强制客户端所连接的服务器状态与leader的状态同步，这样在读取 path 的值就是最新的值了。
