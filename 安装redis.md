# yum安装
~~~
# yum -y install redis
~~~

# 下载安装
redis官网: https://redis.io/

下载到 /usr/local 目录下：
~~~
# cd /usr/local

# tar -xzvf redis-5.0.5.tar.gz

# cd redis-5.0.5

# make

# cd src

# make install PREFIX=/usr/local/redis

# make test
You need tcl 8.5 or newer in order to run the Redis test
make: *** [test] Error 1

# yum -y install tcl

# cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis/bin

# vim /usr/local/redis/bin/redis.conf
bind 0.0.0.0 # 允许远程访问
daemonize yes # 设置为后台进程
~~~

# 配置开机自启动
```
# cp /usr/local/redis-5.0.5/utils/redis_init_script /etc/init.d/redis

# vim /etc/init.d/redis
REDISPORT=6379
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/usr/local/redis/bin/redis.conf"

# chkconfig --add redis
```

# 配置环境变量
```
# vim /etc/profile
export REDIS_HOME=/usr/local/redis
export PATH=$PATH:${REDIS_HOME}/bin

# source /etc/profile
```

# 启动、停止
~~~
# service redis start

# service redis stop
~~~

# 链接redis服务端
~~~
# redis-cli -h 192.168.x.x -p 6379
192.168.x.x:6379>
~~~

# 测试
~~~
192.168.x.x:6379> set name zhy
OK
192.168.x.x:6379> get name
"zhy"
~~~
