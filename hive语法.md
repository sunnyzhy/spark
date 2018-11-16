# 基本操作
1. 查看数据库列表：show databases;
2. 查看数据表列表：show tables;
3. 查询表数据：select
4. 查看表结构：desc [tableName];
5. 创建数据库：create database [databaseName];

# 语法
```
LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1,partcol2=val2 ...)]
```
# 描述
-    filepath 可以是：
1).        相对路径，如project/data1

2).        绝对路径，如/user/hive/project/data1

3).        完整的URL，如hdfs://namenode:9000/user/hive/project/data1
-    目标可以是一个表或是一个分区。如果目标表是分区表，必须指定是要加载到哪个分区。
-    filepath 可以是一个文件，也可以是一个目录(会将目录下的所有文件都加载)。
-    如果命令中带LOCAL，表示：
--        load命令从本地文件系统中加载数据，可以是相对路径，也可以是绝对路径。对于本地文件系统，也可以使用完整的URL，如file:///user/hive/project/data1
--        load命令会根据指定的本地文件系统中的filepath复制文件到目标文件系统，然后再移到对应的表
-    如果命令中没有LOCAL，表示从HDFS加载文件，filepath可以使用完整的URL方式，或者使用fs.default.name定义的值
-    命令带OVERWRITE时加载数据之前会先清空目标表或分区中的内容，否则就是追加的方式。

# 新建数据库
```
hive> create database hive_test;
```

# 创建数据表
```
hive> use hive_test;

hive> create table student(id int,name string) row format delimited fields  terminated by '\t';
```

# 将文件数据写入表中
```
# touch /usr/local/txt/student.txt

# vim /usr/local/txt/student.txt
001     a
002     b
003     c
004     d
005     e

hive> load data local inpath '/usr/local/txt/student.txt' into table hive_test.student;
```

# 查看是否写入成功
```
hive> select * from student;
OK
1	a
2	b
3	c
4	d
5	e
```

# 在MySQL中查看hive数据表
```
SELECT * FROM hive.TBLS;
```
# 在hdfs中查看hive数据库
http://127.0.0.1:50070/explorer.html#/user/hive/warehouse
