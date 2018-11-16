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
```

# 装载数据
```
# touch /usr/local/txt/data.log

# vim /usr/local/txt/data.log
1,xiaoming,book-TV-code,beijing:chaoyang-shagnhai:pudong 
2,lilei,book-code,nanjing:jiangning-taiwan:taibei
3,lihua,music-book,heilongjiang:haerbin

hive> load data local inpath '/usr/local/txt/data.log' overwrite into table t1;

hive> select * from t1;
OK
1	xiaoming	["book","TV","code"]	{"beijing":"chaoyang","shagnhai":"pudong"}
2	lilei	["book","code"]	{"nanjing":"jiangning","taiwan":"taibei"}
3	lihua	["music","book"]	{"heilongjiang":"haerbin"}
Time taken: 2.666 seconds, Fetched: 3 row(s)
```
