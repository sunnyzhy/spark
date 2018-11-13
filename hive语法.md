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

```
说明：id和name直接是TAB键，不是空格，因为在上面创建表的语句中用了terminated  by  '\t'所以这个文本里id和name的分割必须是用TAB键，还有就是行与行之间不能有空行，否则下面执行load，会把NULL存入表内，该文件要使用unix格式，如果是在windows上用txt文本编辑器编辑后在上载到服务器上，需要用工具将windows格式转为unix格式，例如可以使用Notepad++来转换。
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
