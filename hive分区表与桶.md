# 分区表相关说明
- 分区表的一个分区对应hdfs上的一个目录
- 分区表包括静态分区表和动态分区表，根据分区会不会自动创建来区分
- 多级分区表，即创建的时候指定 PARTITIONED BY (event_month string,loc string)，根据顺序，级联创建 event_month=XXX/loc=XXX目录，其他和一级的分区表是一样的

# 创建分区表
```
hive> create table t3(
    > id int,
    > name string)
    > partitioned by (ptn string)
    > row format delimited
    > fields terminated by ',';

hive> desc t3;
OK
id                  	int                 	                    
name                	string              	                    
ptn                 	string              	                    
	 	 
# Partition Information	 	 
# col_name            	data_type           	comment             
	 	 
ptn                 	string     
```

# 创建数据文件
```
# touch /usr/local/txt/partition.log

# vim /usr/local/txt/partition.log
1,jim
2,kate
3,king
```

# 装载数据
```
hive> load data local inpath '/usr/local/txt/partition.log' overwrite into table t3 partition(ptn='2018-1-1');

hive> load data local inpath '/usr/local/txt/partition.log' overwrite into table t3 partition(ptn='2018-1-2');

hive> select * from t3;
OK
1	jim	2018-1-1
2	kate	2018-1-1
3	king	2018-1-1
1	jim	2018-1-2
2	kate	2018-1-2
3	king	2018-1-2

hive> select * from t3 where ptn='2018-1-1';
OK
1	jim	2018-1-1
2	kate	2018-1-1
3	king	2018-1-1
```

# 创建桶表
```
hive> create table t4(
    > id int,
    > name string)
    > clustered by (id) into 4 buckets
    > row format delimited
    > fields terminated by ',';

hive> desc t4;
OK
id                  	int                 	                    
name                	string       
```

# 创建一个没有划分桶的表
```
hive> create table t5(
    > id int,
    > name string)
    > row format delimited
    > fields terminated by ',';

hive> desc t5;
OK
id                  	int                 	                    
name                	string              	   

hive> load data local inpath '/usr/local/txt/bucket.log' overwrite into table t5;

hive> select * from t5;
OK
1	jim
2	kate
3	king
4	tom
```

# 往桶表中添加数据
```
hive> insert overwrite table t4 select * from t5;

# hadoop fs -ls /user/hive/warehouse/t4
Found 4 items
-rwxrwxrwx   2 root supergroup          6 2018-11-16 16:18 /user/hive/warehouse/t4/000000_0
-rwxrwxrwx   2 root supergroup          7 2018-11-16 16:18 /user/hive/warehouse/t4/000001_0
-rwxrwxrwx   2 root supergroup          7 2018-11-16 16:19 /user/hive/warehouse/t4/000002_0
-rwxrwxrwx   2 root supergroup          6 2018-11-16 16:19 /user/hive/warehouse/t4/000003_0

hive> select * from t4 tablesample(bucket 1 out of 4 on id);
OK
1	jim

hive> select * from t4 tablesample(bucket 3 out of 4 on id);
OK
3	king

hive> select * from t4 tablesample(bucket 3 out of 4 on rand());
OK
1	jim

hive> select * from t4 tablesample(3 rows);
OK
1	jim
2	kate
3	king
```

# 抽样语法
```
table_sample: TABLESAMPLE (BUCKET x OUT OF y [ON colname])

其中x是要抽样的桶编号，桶编号从1开始，colname表示抽样的列，y表示桶的数量。

下面的例子中，返回4个桶中的第2个桶中的行：
 select * from t4 tablesample(bucket 2 out of 4 on id);
```
