# 安装erlang
https://github.com/rabbitmq/erlang-rpm

## 配置yum源
```
# vim /etc/yum.repos.d/rabbitmq-erlang.repo
[rabbitmq-erlang]
name=rabbitmq-erlang
baseurl=https://dl.bintray.com/rabbitmq-erlang/rpm/erlang/21/el/7
gpgcheck=1
gpgkey=https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc
repo_gpgcheck=0
enabled=1
```

## 安装
```
# yum clean all

# yum makecache
```

# 安装rabbitmq
http://www.rabbitmq.com/download.html

## 安装
```
# wget https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.10/rabbitmq-server-3.7.10-1.el7.noarch.rpm

# yum -y install rabbitmq-server-3.7.10-1.el7.noarch.rpm
```

## 启动
```
# service rabbitmq-server start
```

## 查看状态
```
# rabbitmqctl status
```

## 开启网页管理界面
```
# rabbitmq-plugins enable rabbitmq_management

# rabbitmqctl list_users
Listing users ...
user	tags
guest	[administrator]

# rabbitmqctl change_password guest admin
Changing password for user "guest" ...

# service rabbitmq-server restart
```

## 登录管理界面
http://localhost:15672/
用户名：guest
密码：amdin

    15762 是web管理的监控端口，5762是rabbitMQ的监控端口

## 重启服务
```
# service rabbitmq-server restart
```

# 用户管理
## 新增一个用户
```
# rabbitmqctl  add_user  Username  Password
```

## 删除一个用户
```
# rabbitmqctl  delete_user  Username
```

## 修改用户的密码
```
# rabbitmqctl  change_password  Username  Newpassword
```

## 查看当前用户列表
```
# rabbitmqctl  list_users
```

# 用户角色

1. 超级管理员(administrator)

可登陆管理控制台(启用management plugin的情况下)，可查看所有的信息，并且可以对用户，策略(policy)进行操作。

2. 监控者(monitoring)

可登陆管理控制台(启用management plugin的情况下)，同时可以查看rabbitmq节点的相关信息(进程数，内存使用情况，磁盘使用情况等)

3. 策略制定者(policymaker)

可登陆管理控制台(启用management plugin的情况下), 同时可以对policy进行管理。但无法查看节点的相关信息(上图红框标识的部分)。

与administrator的对比，administrator能看到这些内容

4. 普通管理者(management)

仅可登陆管理控制台(启用management plugin的情况下)，无法看到节点信息，也无法对策略进行管理。

5. 其他

无法登陆管理控制台，通常就是普通的生产者和消费者。

了解了这些后，就可以根据需要给不同的用户设置不同的角色，以便按需管理。

设置用户角色的命令为：
```
# rabbitmqctl  set_user_tags  User  Tag
```
User为用户名， Tag为角色名(对应于上面的administrator，monitoring，policymaker，management，或其他自定义名称)。

也可以给同一用户设置多个角色，例如
```
# rabbitmqctl  set_user_tags  hncscwc  monitoring  policymaker
```

# 用户权限
## 设置用户权限
```
# rabbitmqctl  set_permissions  -p  VHostPath  User  ConfP  WriteP  ReadP
```

## 查看(指定hostpath)所有用户的权限信息
```
# rabbitmqctl  list_permissions  [-p  VHostPath]
```

## 查看指定用户的权限信息
```
# rabbitmqctl  list_user_permissions  User
```

##  清除用户的权限信息
```
# rabbitmqctl  clear_permissions  [-p VHostPath]  User
```

# 常见问题
## reply-code=530, reply-text=NOT_ALLOWED - access to vhost '/' refused for user user_admin
问题原因：用户admin没有赋予访问权限
```
# rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```

**也可以在管理后台中的Admin选项卡中添加用户和授权。**
