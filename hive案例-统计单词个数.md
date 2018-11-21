# 创建数据源文件
```
# touch /usr/local/txt/wordcount.log
# vim /usr/local/txt/wordcount.log
hello   hadoop
hello   spark
hello   hive
yellow  banana
red     apple
```
**单词之间用 \t 分割**

# 创建表
```
hive> create table wordcount(word string);

hive> desc wordcount;
OK
word                	string              	                    
Time taken: 0.051 seconds, Fetched: 1 row(s)
```

# 装载数据
```
hive> load data local inpath '/usr/local/txt/wordcount.log' overwrite into table wordcount;

hive> select * from wordcount;
OK
hello	hadoop
hello	spark
hello	hive
yellow	banana
red	apple
```

