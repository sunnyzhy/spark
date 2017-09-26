#### yum安装

~~~javascript
# yum -y install redis
~~~

#### 下载安装

##### 下载redis
https://redis.io/download

##### 安装
~~~javascript
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
~~~javascript
# yum -y install tcl
~~~

##### 配置redis

~~~javascript
# cp redis.conf /etc/
# vi /etc/redis.conf
~~~

~~~
修改 daemonize yes
~~~

##### 启动redis

~~~javascript
# cd /usr/local/bin
# ./redis-server /etc/redis.conf
# ps -ef | grep redis
root     14561     1  0 17:25 ?        00:00:00 ./redis-server 127.0.0.1:6379 
root     14568  3106  0 17:26 pts/1    00:00:00 grep redis
~~~

### 使用yum安装方便些，以下步骤均为yum安装环境

#### 允许远程访问redis
若要支持远程访问调试，除了需要开放服务器端口号6379，还需要将配置文件中的bind 127.0.0.1注释掉

~~~javascript
# vi /etc/redis.conf
# bind 127.0.0.1
~~~

#### 重启redis

~~~javascript
# /etc/init.d/redis restart
Stopping redis-server:                                     [  OK  ]
Starting redis-server:                                     [  OK  ]
~~~

#### 启动、停止redis

~~~javascript
# /etc/init.d/redis start
# /etc/init.d/redis stop
~~~

#### 打开另外一个终端

##### 链接redis服务端

~~~javascript
# redis-cli -h 192.168.x.x -p 6379
192.168.x.x:6379>
~~~

##### 测试

~~~javascript
192.168.x.x:6379> set name zhy
OK
192.168.x.x:6379> get name
"zhy"
~~~