# yum安装
~~~
# yum -y install redis
~~~

# 下载安装
redis官网: https://redis.io/

下载到/usr/local目录下：
~~~
# tar -xzvf redis-5.0.5.tar.gz

# cd redis-5.0.5

# make

# cd src

# make install PREFIX=/usr/local/redis

# make test
You need tcl 8.5 or newer in order to run the Redis test
make: *** [test] Error 1

# yum -y install tcl

# cp ../redis.conf /usr/local/redis/bin

# vim /usr/local/redis/bin/redis.conf
bind 0.0.0.0
daemonize yes
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

# 启动
~~~
# cd /usr/local/bin

# ./redis-server /etc/redis.conf

# ps -ef | grep redis
root     14561     1  0 17:25 ?        00:00:00 ./redis-server 127.0.0.1:6379 
root     14568  3106  0 17:26 pts/1    00:00:00 grep redis
~~~

# 允许远程访问
    若要支持远程访问调试，除了需要开放服务器端口号6379，还需要将配置文件中的bind 127.0.0.1注释掉
~~~
# vim /etc/redis.conf
# bind 127.0.0.1
~~~

# 重启
~~~
# /etc/init.d/redis restart
Stopping redis-server:                                     [  OK  ]
Starting redis-server:                                     [  OK  ]
~~~

# 启动、停止
~~~
# /etc/init.d/redis start

# /etc/init.d/redis stop
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
