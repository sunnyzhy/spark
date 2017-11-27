# yum安装
~~~
# yum -y install redis
~~~

# 下载安装
- redis官网
https://redis.io/download

- 安装
~~~
# mkdir redis
# cd redis
# tar -xzvf redis-4.0.1.tar.gz
# cd redis-4.0.1
# make
# cd src
# make install
# make test
You need tcl 8.5 or newer in order to run the Redis test
make: *** [test] Error 1
~~~

如果报错，就说明需要安装tcl
~~~
# yum -y install tcl
~~~

# 配置
~~~
# cp redis.conf /etc/
# vim /etc/redis.conf
daemonize yes
~~~

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
