# 清洗数据
调用springboot的restful接口，完成MapReduce清理日志的过程。
```
http://192.168.197.107:8020/mr?input=/usr/local/txt/access_2013_05_30.log&output=hdfs://192.168.197.107:9000/user/hadoop/bbs
```

# 建立分区表
```
hive> create table bbs(ip string,atime string,url string) partitioned by (logdate string) row format delimited fields terminated by '\t';
```

# 装载数据
```
hive> load data inpath '/user/hadoop/bbs' overwrite into table bbs partition(logdate='2013_05_30');
```

# 统计PV量
页面浏览量即为PV(Page View)，是指所有用户浏览页面的总和，一个独立用户每打开一个页面就被记录 1 次。
```
hive> select count(1) as PV from bbs where logdate='2013_05_30';
```
