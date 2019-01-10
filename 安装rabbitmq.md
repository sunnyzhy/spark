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

## 启用插件
```
# rabbitmq-plugins enable rabbitmq_management
```

## 重启服务
```
# service rabbitmq-server restart
```

## 添加帐号:name 密码:passwd
```
# rabbitmqctl add_user name passwd
```

## 赋予其administrator角色
```
# rabbitmqctl set_user_tags name administrator
```

## 设置权限
```
# rabbitmqctl set_permissions -p / name ".*" ".*" ".*"
```
