# map与flatMap
```
scala> val r1 = sc.parallelize(List("Hadoop", "Hive", "HDFS"))
r1: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[15] at parallelize at <console>:24

scala> r1.collect
res14: Array[String] = Array(Hadoop, Hive, HDFS)
```

- map

  map中传入的函数会对集合中的每个元素进行转换，并产生一个新的结果集合。
```
scala> val r2 = r1.map(x => x.toList)
r2: org.apache.spark.rdd.RDD[List[Char]] = MapPartitionsRDD[16] at map at <console>:28

scala> r2.collect
res12: Array[List[Char]] = Array(List(H, a, d, o, o, p), List(H, i, v, e), List(H, D, F, S))
```

- flatMap

  flatMap中传入的函数会对集合中的每个元素进行转换并返回各个元素的集合，然后flatMap把生成的多个集合扁平化成一个集合。
```
scala> val r3 = r1.flatMap(x => x.toList)
r3: org.apache.spark.rdd.RDD[Char] = MapPartitionsRDD[17] at flatMap at <console>:28

scala> r3.collect
res13: Array[Char] = Array(H, a, d, o, o, p, H, i, v, e, H, D, F, S)
```
  此示例中，可以看作是先调用map（r1中的每个元素都调用toList），生成List(Char)，再把多个Char的集合“拍扁”成为一个集合。

# map与mapValues
```
scala> val r1 = sc.parallelize(List("aaa","b","b","c"))
r1: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[18] at parallelize at <console>:24

scala> r1.collect
res15: Array[String] = Array(aaa, b, b, c)
```

- map

```
scala> val r2 = r1.map(x => (x,x.length))
r2: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[19] at map at <console>:28

scala> r2.collect
res16: Array[(String, Int)] = Array((aaa,3), (b,1), (b,1), (c,1))
```

- mapValues

  KV对中的value作转换
```
scala> val r3 = r2.mapValues(x => (x,10))
r3: org.apache.spark.rdd.RDD[(String, (Int, Int))] = MapPartitionsRDD[20] at mapValues at <console>:30

scala> r3.collect
res17: Array[(String, (Int, Int))] = Array((aaa,(3,10)), (b,(1,10)), (b,(1,10)), (c,(1,10)))
```

# reduce与reduceByKey
- reduce

  reduce将RDD中元素前两个传给输入函数，产生一个新的return值，新产生的return值与RDD中下一个元素（第三个元素）组成两个元素，再被传给输入函数，直到最后只有一个值为止。
```
scala> val r1 = sc.parallelize(1 to 10)
r1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[21] at parallelize at <console>:24

scala> r1.collect
res18: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

scala> val r2 = r1.reduce((x,y) => x + y)
r2: Int = 55
```

- reduceByKey

reduceByKey是对元素为KV对中Key相同的元素的Value进行reduce操作，因此，Key相同的多个元素的值被reduce为一个值，然后与原RDD中的Key组成一个新的KV对。
```
scala> val r1 = sc.parallelize(List((1,2),(1,3),(3,4),(3,6)))
r1: org.apache.spark.rdd.RDD[(Int, Int)] = ParallelCollectionRDD[22] at parallelize at <console>:24

scala> r1.collect
res21: Array[(Int, Int)] = Array((1,2), (1,3), (3,4), (3,6))

scala> val r2 = r1.reduceByKey((x,y) => x + y)
r2: org.apache.spark.rdd.RDD[(Int, Int)] = ShuffledRDD[23] at reduceByKey at <console>:28

scala> r2.collect
res22: Array[(Int, Int)] = Array((1,5), (3,10))
```

- reduceByKey示例

1. 按key求和
```
scala> val r1 = sc.parallelize(List(("a",2),("b",3),("a",3)))
r1: org.apache.spark.rdd.RDD[(String, Int)] = ParallelCollectionRDD[25] at parallelize at <console>:24

scala> r1.collect
res23: Array[(String, Int)] = Array((a,2), (b,3), (a,3))

scala> val r2 = r1.reduceByKey((x,y) => x + y)
r2: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[26] at reduceByKey at <console>:28

scala> r2.collect
res24: Array[(String, Int)] = Array((b,3), (a,5))
```

2. 按key求平均值
```
scala> val r1 = sc.parallelize(List(("spark",10),("hadoop",4),("hadoop",10),("spark",20)))
r1: org.apache.spark.rdd.RDD[(String, Int)] = ParallelCollectionRDD[27] at parallelize at <console>:24

scala> r1.collect
res25: Array[(String, Int)] = Array((spark,10), (hadoop,4), (hadoop,10), (spark,20))

scala> val r2 = r1.mapValues(x => (x,1))
r2: org.apache.spark.rdd.RDD[(String, (Int, Int))] = MapPartitionsRDD[28] at mapValues at <console>:28

scala> r2.collect
res26: Array[(String, (Int, Int))] = Array((spark,(10,1)), (hadoop,(4,1)), (hadoop,(10,1)), (spark,(20,1)))

scala> val r3 = r2.reduceByKey((x,y) => (x._1 + y._1, x._2 + y._2))
r3: org.apache.spark.rdd.RDD[(String, (Int, Int))] = ShuffledRDD[29] at reduceByKey at <console>:30

scala> r3.collect
res27: Array[(String, (Int, Int))] = Array((spark,(30,2)), (hadoop,(14,2)))     

scala> val r4 = r3.mapValues(x => x._1 / x._2)
r4: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[30] at mapValues at <console>:32

scala> r4.collect
res28: Array[(String, Int)] = Array((spark,15), (hadoop,7))
```
