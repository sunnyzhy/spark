# 下载
下载地址http://www.scala-lang.org/download/

# 解压
```
# cd /usr/local/scala
# tar -zxvf scala-2.12.4.tgz
```

# 配置环境变量
```
# vim /etc/profile
export SCALA_HOME=/usr/local/scala/scala-2.12.4
export PATH=$PATH:${SCALA_HOME}/bin

# source /etc/profile
```

# 测试
```
# scala -version
Scala code runner version 2.12.4 -- Copyright 2002-2017, LAMP/EPFL and Lightbend, Inc.
```

# 退出scala命令行
  ctr + c
