[TOC]

# 运行命令行工具
```
# hbase shell

hbase(main):001:0>
```

# 查询服务器状态
```
> status
1 active master, 0 backup masters, 1 servers, 0 dead, 2.0000 average load
```

# 查询hbase版本
```
> version
1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017
```

# 列出所有的表
```
> list
TABLE                                                                           
0 row(s) in 0.0790 seconds
```

# 创建表
```
> create 'student','id','name','birth','address'
```

# 获得表的描述
```
> describe 'student'
Table student is ENABLED                                                        
student                                                                         
COLUMN FAMILIES DESCRIPTION                                                     
{NAME => 'address', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false',
 KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER',
 COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => 
'65536', REPLICATION_SCOPE => '0'}                                              
{NAME => 'birth', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', K
EEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', C
OMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '6
5536', REPLICATION_SCOPE => '0'}                                                
{NAME => 'id', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP
_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMP
RESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '6553
6', REPLICATION_SCOPE => '0'}                                                   
{NAME => 'name', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KE
EP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', CO
MPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65
536', REPLICATION_SCOPE => '0'}                                                 
4 row(s) in 0.0640 seconds
```

# 删除列族
```
> disable 'student'

> alter 'student',{NAME=>'address',METHOD=>'delete'}

> enable 'student'
```

# 查看表是否存在
```
> exists 'student'
Table student does exist
```

# 判断表是否为‘enable’
```
> is_enabled 'student'
true
```

# 判断表是否为‘disable’
```
> is_disabled 'student'
false
```

# 删除表
```
> disable 'student'

> drop 'student'
```

# 添加记录
```
> put 'student','stu','id',1

> put 'student','stu','name','tom'

> put 'student','stu','birth:year',1990

> put 'student','stu','birth:month',1

> put 'student','stu','birth:day',1

> put 'student','stu','address:contry','china'

> put 'student','stu','address:province','hubei'

> put 'student','stu','address:city','wuhan'
```

# 全表扫描
```
> scan 'student'
ROW                   COLUMN+CELL                                               
 stu                  column=address:city, timestamp=1543815995654, value=wuhan 
 stu                  column=address:contry, timestamp=1543815950557, value=chin
                      a                                                         
 stu                  column=address:province, timestamp=1543815986639, value=hu
                      bei                                                       
 stu                  column=birth:day, timestamp=1543816106649, value=1        
 stu                  column=birth:month, timestamp=1543816100714, value=1      
 stu                  column=birth:year, timestamp=1543816091475, value=1990    
 stu                  column=id:, timestamp=1543816035653, value=1              
 stu                  column=name:, timestamp=1543816049017, value=tom          
1 row(s) in 0.0480 seconds
```

#  查询一行的所有数据
```
> get 'student','stu'
COLUMN                CELL                                                      
 address:city         timestamp=1543815995654, value=wuhan                      
 address:contry       timestamp=1543815950557, value=china                      
 address:province     timestamp=1543815986639, value=hubei                      
 birth:day            timestamp=1543816106649, value=1                          
 birth:month          timestamp=1543816100714, value=1                          
 birth:year           timestamp=1543816091475, value=1990                       
 id:                  timestamp=1543816035653, value=1                          
 name:                timestamp=1543816049017, value=tom                        
8 row(s) in 0.0620 seconds
```

# 查询指定行，指定列族的所有数据
```
> get 'student','stu','address'
COLUMN                CELL                                                      
 address:city         timestamp=1543815995654, value=wuhan                      
 address:contry       timestamp=1543815950557, value=china                      
 address:province     timestamp=1543815986639, value=hubei                      
3 row(s) in 0.0340 seconds
```

# 查询指定行，指定列族，指定列的所有数据
```
> get 'student','stu','address:city'
COLUMN                CELL                                                      
 address:city         timestamp=1543815995654, value=wuhan                      
1 row(s) in 0.0640 seconds
```

# 更新记录
```
> put 'student','stu','birth:day',2
```

# 删除指定行，指定列族，指定列的数据
```
> delete 'student','stu','address:city'
```

# 删除整行
```
> deleteall 'student','stu'
```

 # 查询表中有多少行
 ```
 > count 'student'
1 row(s) in 0.1010 seconds
 ```
 
 # 清空表
 ```
 > truncate 'student'
Truncating 'student' table (it may take a while):
 - Disabling table...
 - Truncating table...
0 row(s) in 6.4110 seconds
 ```
