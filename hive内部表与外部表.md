# 内部表 VS 外部表
```
未被external修饰的是内部表（managed table），被external修饰的为外部表（external table）；
区别：
内部表数据由Hive自身管理，外部表数据由HDFS管理；
内部表数据存储的位置是hive.metastore.warehouse.dir（默认：/user/hive/warehouse），外部表数据的存储位置由自己制定；
删除内部表会直接删除元数据（metadata）及存储数据；删除外部表仅仅会删除元数据，HDFS上的文件并不会被删除；
对内部表的修改会将修改直接同步给元数据，而对外部表的表结构和分区进行修改，则需要修复（MSCK REPAIR TABLE table_name;）
```

# 创建内部表
```
hive> create table t1(
    > id int,
    > name string,
    > hobby array<string>,
    > add map<string,string>)
    > row format delimited
    > fields terminated by ','
    > collection items terminated by '-'
    > map keys terminated by ':';

hive> desc t1;
OK
id                  	int                 	                    
name                	string              	                    
hobby               	array<string>       	                    
add                 	map<string,string>  	

# hadoop fs -ls /user/hive/warehouse
Found 1 items
drwxrwxrwx   - root supergroup          0 2018-11-16 11:57 /user/hive/warehouse/t1
```

# 创建数据文件
```
# touch /usr/local/txt/data.log

# vim /usr/local/txt/data.log
1,xiaoming,book-TV-code,beijing:chaoyang-shagnhai:pudong 
2,lilei,book-code,nanjing:jiangning-taiwan:taibei
3,lihua,music-book,heilongjiang:haerbin
```

# 装载数据
```
hive> load data local inpath '/usr/local/txt/data.log' overwrite into table t1;

hive> select * from t1;
OK
1	xiaoming	["book","TV","code"]	{"beijing":"chaoyang","shagnhai":"pudong"}
2	lilei	["book","code"]	{"nanjing":"jiangning","taiwan":"taibei"}
3	lihua	["music","book"]	{"heilongjiang":"haerbin"}
Time taken: 2.666 seconds, Fetched: 3 row(s)

hive> desc formatted t1;
OK
# col_name            	data_type           	comment             
	 	 
id                  	int                 	                    
name                	string              	                    
hobby               	array<string>       	                    
add                 	map<string,string>  	                    
	 	 
# Detailed Table Information	 	 
Database:           	default             	 
Owner:              	root                	 
CreateTime:         	Fri Nov 16 11:07:51 CST 2018	 
LastAccessTime:     	UNKNOWN             	 
Retention:          	0                   	 
Location:           	hdfs://spark1:9000/user/hive/warehouse/t1	 
Table Type:         	MANAGED_TABLE       	 
Table Parameters:	 	 
	numFiles            	1                   
	numRows             	0                   
	rawDataSize         	0                   
	totalSize           	147                 
	transient_lastDdlTime	1542340647          
	 	 
# Storage Information	 	 
SerDe Library:      	org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe	 
InputFormat:        	org.apache.hadoop.mapred.TextInputFormat	 
OutputFormat:       	org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat	 
Compressed:         	No                  	 
Num Buckets:        	-1                  	 
Bucket Columns:     	[]                  	 
Sort Columns:       	[]                  	 
Storage Desc Params:	 	 
	colelction.delim    	-                   
	field.delim         	,                   
	mapkey.delim        	:                   
	serialization.format	,                   
Time taken: 0.2 seconds, Fetched: 35 row(s)

# hadoop fs -ls /user/hive/warehouse/t1
Found 1 items
-rwxrwxrwx   2 root supergroup        147 2018-11-16 11:57 /user/hive/warehouse/t1/data.log
```

# 创建外部表
```
hive> create external table t2(
    > id int,
    > name string,
    > hobby array<string>,
    > ad map<string,string>)
    > row format delimited
    > fields terminated by ','
    > collection items terminated by '-'
    > map keys terminated by ':'
    > location '/user/db/t2';

hive> desc t2;
OK
id                  	int                 	                    
name                	string              	                    
hobby               	array<string>       	                    
ad                  	map<string,string>  	                    
Time taken: 0.093 seconds, Fetched: 4 row(s)

# hadoop fs -ls /user/db
Found 1 items
drwxr-xr-x   - root supergroup          0 2018-11-16 12:02 /user/db/t2
```

# 装载数据
```
hive> load data local inpath '/usr/local/txt/data.log' overwrite into table t2;

hive> select * from t2;
OK
1	xiaoming	["book","TV","code"]	{"beijing":"chaoyang","shagnhai":"pudong"}
2	lilei	["book","code"]	{"nanjing":"jiangning","taiwan":"taibei"}
3	lihua	["music","book"]	{"heilongjiang":"haerbin"}
Time taken: 0.206 seconds, Fetched: 3 row(s)

hive> desc formatted t2;
OK
# col_name            	data_type           	comment             
	 	 
id                  	int                 	                    
name                	string              	                    
hobby               	array<string>       	                    
ad                  	map<string,string>  	                    
	 	 
# Detailed Table Information	 	 
Database:           	default             	 
Owner:              	root                	 
CreateTime:         	Fri Nov 16 12:02:01 CST 2018	 
LastAccessTime:     	UNKNOWN             	 
Retention:          	0                   	 
Location:           	hdfs://spark1:9000/user/db/t2	 
Table Type:         	EXTERNAL_TABLE      	 
Table Parameters:	 	 
	EXTERNAL            	TRUE                
	numFiles            	1                   
	totalSize           	147                 
	transient_lastDdlTime	1542347892          
	 	 
# Storage Information	 	 
SerDe Library:      	org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe	 
InputFormat:        	org.apache.hadoop.mapred.TextInputFormat	 
OutputFormat:       	org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat	 
Compressed:         	No                  	 
Num Buckets:        	-1                  	 
Bucket Columns:     	[]                  	 
Sort Columns:       	[]                  	 
Storage Desc Params:	 	 
	colelction.delim    	-                   
	field.delim         	,                   
	mapkey.delim        	:                   
	serialization.format	,                   
Time taken: 0.112 seconds, Fetched: 34 row(s)

# hadoop fs -ls /user/db/t2
Found 1 items
-rwxr-xr-x   2 root supergroup        147 2018-11-16 13:58 /user/db/t2/data.log
```

# 删除内部表和外部表
```
hive> drop table t1;
OK
Time taken: 1.814 seconds

hive> drop table t2;
OK
Time taken: 0.192 seconds

hive> desc t1;
FAILED: SemanticException [Error 10001]: Table not found t1

hive> desc t2;
FAILED: SemanticException [Error 10001]: Table not found t2

# hadoop fs -ls /user/hive/warehouse/t1
ls: `/user/hive/warehouse/t1': No such file or directory

# hadoop fs -ls /user/db/t2
Found 1 items
-rwxr-xr-x   2 root supergroup        147 2018-11-16 13:58 /user/db/t2/data.log
```

```
t1已经不存在了，t2仍然存在，所以外部表仅仅删除元数据。
```

# 重新创建外部表
```
hive> create external table t2(
    > id int,
    > name string,
    > hobby array<string>,
    > ad map<string,string>)
    > row format delimited
    > fields terminated by ','
    > collection items terminated by '-'
    > map keys terminated by ':'
    > location '/user/db/t2';
    
hive> select * from t2;
OK
1	xiaoming	["book","TV","code"]	{"beijing":"chaoyang","shagnhai":"pudong"}
2	lilei	["book","code"]	{"nanjing":"jiangning","taiwan":"taibei"}
3	lihua	["music","book"]	{"heilongjiang":"haerbin"}
Time taken: 0.206 seconds, Fetched: 3 row(s)
```

```
重新创建外部表t2，不往里面添加数据，查询结果仍然存在。
```
