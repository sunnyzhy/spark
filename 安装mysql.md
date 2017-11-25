# 官网
https://dev.mysql.com/downloads/repo/yum/

# 安装
~~~
# mkdir -p /usr/local/mysql
# yum -y localinstall mysql57-community-release-el7-11.noarch.rpm
# yum search mysql
# yum -y install mysql-community-server.x86_64 mysql-community-client.x86_64

~~~

#### 下载mysql
https://dev.mysql.com/downloads/mysql/
~~~
Select Operating System:
Linux - Generic

Select OS Version:
Linux - Generic(glibc 2.12)(x86,64-bit)

下载 Compressed TAR Archive
~~~

#### 解压
~~~javascript
# tar -zxvf mysql-5.7.19-linux-glibc2.12-x86_64.tar.gz -C /usr/local
~~~

#### 重命名
~~~javascript
# mv mysql-5.7.19-linux-glibc2.12-x86_64 mysql
~~~

#### 安装
~~~javascript
# cd mysql
# adduser mysql
# chown mysql:mysql -R /usr/local/mysql
# ./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
2017-08-29T07:13:30.353666Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2017-08-29T07:13:30.560072Z 0 [Warning] InnoDB: New log files created, LSN=45790
2017-08-29T07:13:30.674065Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2017-08-29T07:13:30.738373Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 8fb33268-8c89-11e7-8abc-000c29942c33.
2017-08-29T07:13:30.741119Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2017-08-29T07:13:30.743215Z 1 [Note] A temporary password is generated for root@localhost: 3+MMsYZ+,VYw
~~~

~~~
安装完成，出现如上信息，将随机生成的登录密码记录下来，密码在最后一行的末尾(3+MMsYZ+,VYw)
~~~

修改mysqld节点，如果不存在client节点就添加client节点
~~~javascript
# vim /etc/my.cnf
~~~
~~~
[mysqld]
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/mysql.sock
[client]
socket=/usr/local/mysql/mysql.sock
~~~

#### 开启mysql服务
~~~javascript
# ./support-files/mysql.server start
~~~

#### 将mysql进程放入系统进程中
~~~javascript
# cp support-files/mysql.server /etc/init.d/mysqld
~~~

#### 为mysql创建软链接
~~~javascript
# ln -s /usr/local/mysql/bin/mysql /usr/bin
~~~

#### 重新启动mysql服务
~~~javascript
# service mysqld restart
~~~

#### 使用随机密码登录mysql数据库，修改密码并设置远程访问
~~~javascript
# mysql -u root -p
Enter password: 3+MMsYZ+,VYw
mysql> 
~~~

#### 为root用户设置新密码
~~~javascript
mysql> use mysql;
mysql> alter user 'root'@'localhost' identified by 'root';
~~~

#### 设置允许远程连接数据库
~~~javascript
mysql> update user set user.Host='%' where user.User='root';
mysql> flush privileges;
~~~

#### 题外
~~~javascript
mysql> grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
~~~

~~~
'root'@'%' identified by 'root'，允许任何IP地址（%）的电脑用帐户（root）和密码（root）来访问MySQL Server
'root'@'192.168.0.1'，%号可以用IP地址替换，意思是仅允许指定的IP地址访问MySQL Server
~~~

#### 启动、停止、重启mysql服务
~~~javascript
# service mysqld start
# service mysqld stop
# service mysqld restart
~~~
