```
scala> val books = List("Hadoop", "Hive", "HDFS")
books: List[String] = List(Hadoop, Hive, HDFS)
```

- map

  map中传入的函数会应用到集合中的每个元素，并产生一个结果集合。
```
scala> books.map(s => s.toList)
res0: List[List[Char]] = List(List(H, a, d, o, o, p), List(H, i, v, e), List(H, D, F, S))
```

- flatMap

  flatMap中传入的函数对每个输入都会返回一个集合（而不是一个元素），然后flatMap把生成的多个集合扁平化成一个集合。
```
scala> books.flatMap(s => s.toList)
res1: List[Char] = List(H, a, d, o, o, p, H, i, v, e, H, D, F, S)
```
  此示例中，可以看作是先调用map（books中的每个元素都调用toList），生成List(Char)，再把多个Char的集合“拍扁”成为一个集合。
