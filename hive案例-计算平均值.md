# 创建数据源文件
```
# touch /usr/local/txt/avg.log
# vim /usr/local/txt/avg.log
1 1
2 2
3 3
4 4
5 5
6 6
7 7
8 8
9 9
10 10
```
**字符之间用空格分割**

# 创建表
```
hive> create table avg(id int,age int) row format delimited fields terminated by ' ';

hive> desc avg;
OK
id                  	int                 	                    
age                 	int                 	                    
Time taken: 0.06 seconds, Fetched: 2 row(s)
```

# 装载数据
```
hive> load data local inpath '/usr/local/txt/avg.log' overwrite into table avg;

hive> select * from avg;
OK
1	1
2	2
3	3
4	4
5	5
6	6
7	7
8	8
9	9
10	10
Time taken: 0.163 seconds, Fetched: 10 row(s)
```

# 计算平均值
```
hive> select avg(age) from avg;
OK
5.5

hive> select s/c from (select sum(age) s, count(1) c from avg) a;
OK
5.5
```
