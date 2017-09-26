#### 下载JDK1.7
http://www.oracle.com/
~~~
Downloads -> Java for Developers -> Java Archive -> Download ->  Java SE 7
~~~

#### 解压
~~~javascript
# tar -zxvf /..目录/jdk-7u80-linux-x64.tar.gz -C /usr/local/java
~~~

#### 配置环境变量
~~~javascript
# vim /etc/profile
~~~

在 pathmunge () { 的上方添加以下内容:
~~~
export JAVA_HOME=/usr/local/java/jdk1.7.0_80
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
~~~

~~~javascript
# source /etc/profile
~~~

#### 测试
~~~javascript
# java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
~~~