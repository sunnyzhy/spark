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

  对KV对中的value作转换
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
