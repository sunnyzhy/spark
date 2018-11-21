# 创建数据源文件
```
# touch /usr/local/txt/wordcount.log
# vim /usr/local/txt/wordcount.log
hello hadoop
hello spark
hello hive
yellow hive
red hadoop
```
**单词之间用空格分割**

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

# 统计单词个数
```
hive> select w, count(*) from (select explode(split(word,'\t')) w from wordcount) wc group by w;
OK
hadoop	2
hello	3
hive	2
red	1
spark	1
yellow	1
```

# split函数
字符串分割函数

语法：
split(str, regex) - Splits str around occurances that match regex

返回值: 
array

示例：
```
hive> select split(word,' ') from wordcount;
OK
["hello","hadoop"]
["hello","spark"]
["hello","hive"]
["yellow","hive"]
["red","hadoop"]
```

# explode函数
hive explode函数可以将一个array或者map展开，其中explode(array)使得结果中将array列表里的每个元素生成一行；explode(map)使得结果中将map里的每一对元素作为一行，key为一列，value为一列。

语法：
explode(a) - separates the elements of array a into multiple rows, or the elements of a map into multiple rows and columns

示例：
```
hive> select explode(split(word,' ')) from wordcount;
OK
hello
hadoop
hello
spark
hello
hive
yellow
hive
red
hadoop
```
