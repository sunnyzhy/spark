# 创建数据源文件
```
# touch /usr/local/txt/population.log
# vim /usr/local/txt/population.log
1 M 171
2 F 165
3 M 175
4 M 180
5 F 160
6 F 171
7 M 163
8 M 181
9 F 170
10 M 171
```
**字符之间用空格分割**

# 创建表
```
hive> create table population(id int,sex string,height float) row format delimited fields terminated by ' ';

hive> desc population;
OK
id                  	int                 	                    
sex                 	string              	                    
height              	float               	                    
Time taken: 0.078 seconds, Fetched: 3 row(s)
```

# 装载数据
```
hive> load data local inpath '/usr/local/txt/population.log' overwrite into table population;

hive> select * from population;
OK
1	M	171.0
2	F	165.0
3	M	175.0
4	M	180.0
5	F	160.0
6	F	171.0
7	M	163.0
8	M	181.0
9	F	170.0
10	M	171.0
Time taken: 0.152 seconds, Fetched: 10 row(s)
```

# 计算出男女人数，男性中的最高和最低身高，以及女性中的最高和最低身高
```
hive> select sex,count(1),max(height),min(height) from population group by sex;
OK
F	4	171.0	160.0
M	6	181.0	163.0
```
