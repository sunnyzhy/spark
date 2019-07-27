# 基本指令
## 查询
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

## 检查状态
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

## 创建节点
语法
```
create [-s] [-e] path data acl
```

- -s 创建有序节点

如果在创建znode时，我们使用排序标志的话，ZooKeeper会在我们指定的znode名字后面增加一个数字。我们继续加入相同名字的znode时，这个数字会不断增加。这个序号的计数器是由这些排序znode的父节点来维护的。

- -e 创建临时节点

znode有两种类型：ephemeral和persistent。在创建znode时，我们指定znode的类型，并且在之后不会再被修改。当创建znode的客户端的session结束后，ephemeral类型的znode将被删除。persistent类型的znode在创建以后，就与客户端没什么联系了，除非主动去删除它，否则他会一直存在。Ephemeral znode没有任何子节点。

- acl 在下面的 **ACL 操作** 中有详细介绍。

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

## 获取节点信息
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

## 修改节点
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

## 删除节点
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

## 删除节点
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

# 其他指令
## 历史记录
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

## 重复之前的命令
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

## 是否输出 watch 事件
语法
```
printwatches on|off
```

使用方法
```
[zk: localhost:2181(CONNECTED) 52] printwatches on

[zk: localhost:2181(CONNECTED) 53]  printwatches off
```

## 关闭连接
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

## 连接远程的 zk 服务
语法
```
connect host:port
```

使用方法
```
[zk: localhost:2181(CONNECTED) 0] connect 192.168.0.10:2181
```

## 退出连接
语法
```
quit
```

使用方法
```
[zk: 192.168.0.10:2181(CONNECTED) 1] quit
```

## 强制同步
语法
```
sync path
```
sync方法会强制客户端所连接的服务器状态与leader的状态同步，这样在读取 path 的值就是最新的值了。

# ACL 操作
一个znode中不仅包含了存储的数据，还有 ACL（Access Control List）。znode的创建时，可以给它设置一个ACL（Access Control List），来决定谁可以对znode做哪些操作。

ACL 具有以下特点：

1. ZooKeeper的权限控制是基于每个znode节点的，需要对每个节点设置权限

2. 每个znode支持设置多种权限控制方案和多个权限

3. 子节点不会继承父节点的权限，客户端无权访问某节点，但可能可以访问它的子节点

4. 所以任何一个客户端都可以通过exists 操作来获得任何znode的状态，从而得知znode是否真的存在。

## ACL 操作权限
|ACL 权限|ACL 简写|允许的操作|
|--|--|--|
|CREATE|c|创建子节点|
|READ|r|获取节点的数据和它的子节点|
|WRITE|w|设置节点的数据|
|DELETE|d|删除子节点 （仅下一级节点）|
|ADMIN|a|设置 ACL 权限|

## ACL 权限命令
|命令|语法|描述|
|--|--|--|
|getAcl|getAcl path|读取ACL权限|
|setAcl|setAcl path acl|设置ACL权限|
|addauth|addauth scheme auth|添加认证用户|
|create|create [-s] [-e] path data acl|创建节点时指明 ACL 权限|

## ACL 认证方式
ZooKeeper内置了一些权限控制方案，可以用以下方案为每个节点设置权限：

|方案|描述|
|--|--|
|world|只有一个用户：anyone，代表所有人（默认）|
|ip|使用IP地址认证|
|auth|使用已添加认证的用户认证|
|digest|使用"用户名:密码"方式认证|

ACL是由鉴权方式、鉴权方式的ID和一个许可（permession）的集合组成。例如，我们想通过一个ip地址为10.0.0.1的客户端访问一个znode。那么，我们需要为znode设置一个ACL，鉴权方式使用IP鉴权方式，鉴权方式的ID为10.0.0.1，只允许读权限。那么 ACL 的格式就是：ip:10.0.0.1:w

### world 认证
语法
```
setAcl <path> world:anyone:<acl>
```
默认情况下是 world 方法，任何人都拥有所有的权限。

使用方法
```
[zk: localhost:2181(CONNECTED) 60] getAcl /node
'world,'anyone
: cdrwa

[zk: localhost:2181(CONNECTED) 61] setAcl /node world:anyone:cdr

[zk: localhost:2181(CONNECTED) 62] set /node hello
Authentication is not valid : /node
```
把权限修改为 cdr 之后，就不能再修改节点的数据了。

### IP 认证
语法
```
setAcl <path> ip:<ip>:<acl>
```
<ip>：可以是具体IP也可以是IP/bit格式，即IP转换为二进制，匹配前bit位，如192.168.0.0/16匹配192.168.*.*

使用方法
```
[zk: localhost:2181(CONNECTED) 96] create /node hello
Created /node
  
[zk: localhost:2181(CONNECTED) 97] setAcl /node ip:192.168.0.11:cdrwa
```

使用192.168.0.11查询：
```
[zk: 192.168.0.10:2181(CONNECTED) 1] getAcl /node
'ip,'192.168.0.11
: cdrwa

[zk: 192.168.0.10:2181(CONNECTED) 2] get /node
hello
```

使用192.168.0.15查询：
```
[zk: 192.168.0.10:2181(CONNECTED) 1] get /node
org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /node
```

### auth 认证
语法
```
addauth digest <user>:<password>
  
setAcl <path> auth:<user>:<acl>
```

使用方法
```
[zk: localhost:2181(CONNECTED) 100] create /node hello
Created /node

[zk: localhost:2181(CONNECTED) 101] addauth digest admin:admin

[zk: localhost:2181(CONNECTED) 102] setAcl /node auth:admin:cdrwa

[zk: localhost:2181(CONNECTED) 103] getAcl /node
'digest,'admin:x1nq8J5GOJVPY6zgzhtTtA9izLc=
: cdrwa

[zk: localhost:2181(CONNECTED) 104] get /node
hello

[zk: localhost:2181(CONNECTED) 105] close

[zk: localhost:2181(CLOSED) 106] connect localhost:2181

[zk: localhost:2181(CONNECTED) 107] get /node
org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /node

[zk: localhost:2181(CONNECTED) 108] addauth digest admin:admin

[zk: localhost:2181(CONNECTED) 109] get /node
hello
```

### digest 认证
语法
```
setAcl <path> digest:<user>:<password>:<acl>
```

这里的密码是经过 SHA1 及 BASE64 处理的密文，在 **SHELL** 中可以通过以下命令计算：
```
echo -n <user>:<password> | openssl dgst -binary -sha1 | openssl base64
```

先来算一个密文密码：
```
# echo -n admin:admin | openssl dgst -binary -sha1 | openssl base64
x1nq8J5GOJVPY6zgzhtTtA9izLc=
```

使用方法
```
[zk: localhost:2181(CONNECTED) 121] setAcl /node digest:admin:x1nq8J5GOJVPY6zgzhtTtA9izLc=:cdrwa

[zk: localhost:2181(CONNECTED) 122] getAcl /node
'digest,'admin:x1nq8J5GOJVPY6zgzhtTtA9izLc=
: cdrwa

[zk: localhost:2181(CONNECTED) 123] get /node
hello

[zk: localhost:2181(CONNECTED) 124] close

[zk: localhost:2181(CLOSED) 125] connect localhost:2181

[zk: localhost:2181(CONNECTED) 126] get /node
org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /node

[zk: localhost:2181(CONNECTED) 127] addauth digest admin:admin

[zk: localhost:2181(CONNECTED) 128] get /node
hello
```

## 创建节点时指定 ACL
```
[zk: localhost:2181(CONNECTED) 130] addauth digest admin:admin

[zk: localhost:2181(CONNECTED) 131] create /node hello auth:admin:cdrwa
Created /node

[zk: localhost:2181(CONNECTED) 132] getAcl /node
'digest,'admin:x1nq8J5GOJVPY6zgzhtTtA9izLc=
: cdrwa

[zk: localhost:2181(CONNECTED) 133] get /node
hello

[zk: localhost:2181(CONNECTED) 134] close

[zk: localhost:2181(CLOSED) 135] connect localhost:2181

[zk: localhost:2181(CONNECTED) 136] get /node
org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /node

[zk: localhost:2181(CONNECTED) 137] addauth digest admin:admin

[zk: localhost:2181(CONNECTED) 138] get /node
hello
```

## zookeeper quota
zookeeper quota 机制支持节点个数（namespace）和空间大小（bytes）的设置。

zookeeper quota 保存在 /zookeeper/quota 节点下，可以设置该节点的 ACL 权限，以防其他人修改。

语法：
```
listquota path

setquota -n|-b val path

delquota [-n|-b] path
```

- -n 表示设置znode count限制，这里表示/node这个path下的znode count个数限制为3（包括/node本身）
- -b 表示设置znode数据的字节大小限制

使用方法：
```
[zk: localhost:2181(CONNECTED) 0] ls /zookeeper/quota
[]

[zk: localhost:2181(CONNECTED) 1] create /node
Created /node

[zk: localhost:2181(CONNECTED) 2] listquota /node
absolute path is /zookeeper/quota/node/zookeeper_limits
quota for /node does not exist.

[zk: localhost:2181(CONNECTED) 3] setquota -n 3 /node

[zk: localhost:2181(CONNECTED) 4] listquota /node
absolute path is /zookeeper/quota/node/zookeeper_limits
Output quota for /node count=3,bytes=-1
Output stat for /node count=1,bytes=0

[zk: localhost:2181(CONNECTED) 5] create /node/0 0
Created /node/0

[zk: localhost:2181(CONNECTED) 6] create /node/1 1
Created /node/1

[zk: localhost:2181(CONNECTED) 7] create /node/2 2
Created /node/2

[zk: localhost:2181(CONNECTED) 8] create /node/3 3
Created /node/3

[zk: localhost:2181(CONNECTED) 9] get /zookeeper/quota/node/zookeeper_limits
count=3,bytes=-1

[zk: localhost:2181(CONNECTED) 10] get /zookeeper/quota/node/zookeeper_stats
count=5,bytes=4

[zk: localhost:2181(CONNECTED) 11] delquota -n /node

[zk: localhost:2181(CONNECTED) 12] listquota /node
absolute path is /zookeeper/quota/node/zookeeper_limits
Output quota for /node count=-1,bytes=-1
Output stat for /node count=5,bytes=4
```

zookeeper的Quota机制是比较温和的，即使超限了，也只是在日志中报告一下，并不会限制Client的行为，Client可以继续操作znode。
