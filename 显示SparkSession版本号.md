# pom.xml
```
<dependency>
	<groupId>org.apache.spark</groupId>
	<artifactId>spark-core_2.11</artifactId>
	<version>2.2.1</version>
	<exclusions>
		<exclusion>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
		</exclusion>
		<exclusion>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
		</exclusion>
	</exclusions>
</dependency>
<dependency>
	<groupId>org.apache.spark</groupId>
	<artifactId>spark-sql_2.11</artifactId>
	<version>2.2.1</version>
</dependency>
```

# java
```
      SparkSession sparkSession = SparkSession
      .builder()
      .appName("SparkWithSpring")
      .master("spark://192.168.253.107:7077")
      .getOrCreate();
      System.out.println("Spark Version: " + sparkSession.version());
```
  Spark Version: 2.2.1
