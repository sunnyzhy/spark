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
页面浏览量即为PV(Page View)，是指所有用户浏览页面的总和，一个独立用户每打开一个页面就被记录 1 次。这里，我们只需要统计日志中的记录个数即可。
```
hive> select count(1) as PV from bbs where logdate='2013_05_30';
```

# 统计注册用户数
统计出日志中访问的URL是member.php?mod=register的he总和
```
hive> select count(1) from bbs where logdate='2013_05_30' and instr(url,'member.php?mod=register')>0;
```

# 统计独立IP数
一天之内，访问网站的不同独立 IP 个数总和。其中同一IP无论访问了几个页面，独立IP 数均为1。因此，这里我们只需要统计日志中处理的独立IP数即可。
```
hive> select count(distinct ip) from bbs where logdate='2013_05_30';
```

# 统计跳出用户数
只浏览了一个页面便离开了网站的访问次数，即只浏览了一个页面便不再访问的访问次数。这里，我们可以通过用户的IP进行分组，如果分组后的记录数只有一条，那么即为跳出用户。将这些用户的数量相加，就得出了跳出用户数。
```
hive> select count(1) from(select count(ip) as times from bbs where logdate='2013_05_30' group by ip having times=1)e;
```

**跳出率**是指只浏览了一个页面便离开了网站的访问次数占总的访问次数的百分比，即 **只浏览了一个页面的访问次数 / 全部的访问次数汇总** 。这里，我们可以将这里得出的 **跳出用户数/PV数** 即可得到跳出率。
