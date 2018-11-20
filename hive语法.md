# 官网
https://cwiki.apache.org/confluence/display/Hive/Home#Home-UserDocumentation

# 基本操作
1. 查看数据库列表：show databases;
2. 查看数据表列表：show tables;
3. 查询表数据：select
4. 查看表结构：desc [tableName];
5. 创建数据库：create database [databaseName];

# 建表语句
```
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name    -- (Note: TEMPORARY available in Hive 0.14.0 and later)
  [(col_name data_type [COMMENT col_comment], ... [constraint_specification])]
  [COMMENT table_comment]
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
  [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
  [SKEWED BY (col_name, col_name, ...)                  -- (Note: Available in Hive 0.10.0 and later)]
     ON ((col_value, col_value, ...), (col_value, col_value, ...), ...)
     [STORED AS DIRECTORIES]
  [
   [ROW FORMAT row_format] 
   [STORED AS file_format]
     | STORED BY 'storage.handler.class.name' [WITH SERDEPROPERTIES (...)]  -- (Note: Available in Hive 0.6.0 and later)
  ]
  [LOCATION hdfs_path]
  [TBLPROPERTIES (property_name=property_value, ...)]   -- (Note: Available in Hive 0.6.0 and later)
  [AS select_statement];   -- (Note: Available in Hive 0.5.0 and later; not supported for external tables)
 
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
  LIKE existing_table_or_view_name
  [LOCATION hdfs_path];
 
data_type
  : primitive_type
  | array_type
  | map_type
  | struct_type
  | union_type  -- (Note: Available in Hive 0.7.0 and later)
 
primitive_type
  : TINYINT
  | SMALLINT
  | INT
  | BIGINT
  | BOOLEAN
  | FLOAT
  | DOUBLE
  | DOUBLE PRECISION -- (Note: Available in Hive 2.2.0 and later)
  | STRING
  | BINARY      -- (Note: Available in Hive 0.8.0 and later)
  | TIMESTAMP   -- (Note: Available in Hive 0.8.0 and later)
  | DECIMAL     -- (Note: Available in Hive 0.11.0 and later)
  | DECIMAL(precision, scale)  -- (Note: Available in Hive 0.13.0 and later)
  | DATE        -- (Note: Available in Hive 0.12.0 and later)
  | VARCHAR     -- (Note: Available in Hive 0.12.0 and later)
  | CHAR        -- (Note: Available in Hive 0.13.0 and later)
 
array_type
  : ARRAY < data_type >
 
map_type
  : MAP < primitive_type, data_type >
 
struct_type
  : STRUCT < col_name : data_type [COMMENT col_comment], ...>
 
union_type
   : UNIONTYPE < data_type, data_type, ... >  -- (Note: Available in Hive 0.7.0 and later)
 
row_format
  : DELIMITED [FIELDS TERMINATED BY char [ESCAPED BY char]] [COLLECTION ITEMS TERMINATED BY char]
        [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char]
        [NULL DEFINED AS char]   -- (Note: Available in Hive 0.13 and later)
  | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]
 
file_format:
  : SEQUENCEFILE
  | TEXTFILE    -- (Default, depending on hive.default.fileformat configuration)
  | RCFILE      -- (Note: Available in Hive 0.6.0 and later)
  | ORC         -- (Note: Available in Hive 0.11.0 and later)
  | PARQUET     -- (Note: Available in Hive 0.13.0 and later)
  | AVRO        -- (Note: Available in Hive 0.14.0 and later)
  | JSONFILE    -- (Note: Available in Hive 4.0.0 and later)
  | INPUTFORMAT input_format_classname OUTPUTFORMAT output_format_classname
 
constraint_specification:
  : [, PRIMARY KEY (col_name, ...) DISABLE NOVALIDATE ]
    [, CONSTRAINT constraint_name FOREIGN KEY (col_name, ...) REFERENCES table_name(col_name, ...) DISABLE NOVALIDATE 
```

# 描述
- PATITIONED BY： 用于指定分区字段，每指定一个字段就表示基于该字段创建一个文件夹，如示例在一级目录中存在多个dt分区文件夹，在每个dt文件夹下又存在基于country分区的文件夹；
    这里需要注意PARTITIONED BY后的字段不能在建表语句中出现，其中的字段会自动添加到表结构中；
- CLUSTERED BY：兼具合并和排序的功能，合并指的是用于指定map端将数据处理之后传输到reduce端的数据合并方式，而排序指的是每个reduce中数据的排序方式方式，其只能为降序排序；
- SORTED BY：指定在map端将数据传输到reduce端时每个reduce中数据的排序方式，默认是按照升序排序的。需要注意的是SORTED BY只保证每个reduce中的数据是按照指定字段排序的，而不保证reduce之间的数据是有序的；
- ROW FORMAT DELIMITED：用于指定加载数据时数据的分隔方式。LIENS TERMINATED BY指定每一行数据以什么字符结尾；FIELDS TERMINATED BY指定了每个字段之间使用'\001'进行分隔；COLLECTION ITEMS TERMINATED BY指定字段数据为集合时其内部每个元素之间的分隔方式为'\002'；MAP KEYS TERMINATED BY指定集合元素如果为MAP时，其键值对的分隔方式为'\003'；
- STORED AS: 用于指定加载方式，有TEXTFILE和SEQUENCEFILE两种值，TEXTFILE表示以纯文本形式加载，SEQUENCEFILE则表示以压缩文件形式加载；
- LOACTION：用于指定初始数据的加载地址，该地址为HDFS文件系统中的文件。

# 加载数据
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


# 导出数据
```
hive> insert overwrite local directory '/usr/local/txt/' select * from student;
```
```
overwrite后可接local关键字，如果有该关键字，那么后面的路径则表示本地路径，否则表示HDFS文件系统上的路径。
```
# 删除分区
```
hive> alter table t1 drop if exists partition (partition_field='value');
```

# 删除表
```
hive> drop table if exists t1;
```

# 在MySQL中查看hive数据表
```
SELECT * FROM hive.TBLS;
```
# 在hdfs中查看hive数据库
http://127.0.0.1:50070/explorer.html#/user/hive/warehouse
