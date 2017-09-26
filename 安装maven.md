#### 下载maven
http://maven.apache.org/download.cgi

#### 解压

~~~javascript
# tar -zxvf apache-maven-3.5.0-bin.tar.gz
~~~

#### 配置环境变量

~~~javascript
# vim /etc/profile
MAVEN_HOME=/usr/local/eclipse/apache-maven-3.5.0
export MAVEN_HOME
export PATH=${PATH}:${MAVEN_HOME}/bin
# source /etc/profile
~~~

#### 验证

~~~javascript
# mvn -v
Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-04T03:39:06+08:00)
Maven home: /usr/local/eclipse/apache-maven-3.5.0
Java version: 1.7.0_80, vendor: Oracle Corporation
Java home: /usr/local/java/jdk1.7.0_80/jre
Default locale: en_US, platform encoding: ANSI_X3.4-1968
OS name: "linux", version: "2.6.32-642.11.1.el6.x86_64", arch: "amd64", family: "unix"
~~~

#### 配置maven

~~~javascript
# cd /usr/local/eclipse/apache-maven-3.5.0/conf
~~~

配置本地仓库
~~~
<localRepository>/usr/local/eclipse/maven-repo</localRepository>
~~~

~~~
maven常用命令

mvn compile     编译项目，这个命令用于编译Maven工程，执行命令后会在target文件夹中的classes中生成对应的class文件。  
mvn test        编译运行单元测试，先自动进行编译，在运行所有的测试用例。  
mvn package     打包(jar or war)  
mvn install     将项目安装到本地仓库，发布生成对应的package包。  
mvn clean       清空项目，删除target文件夹，即删除生成的package包以及class等文件。  
mvn eclipse:eclipse     生成eclipse工程  
~~~

eclipse安装maven插件
~~~
1.下载m2eclipse插件
http://m2eclipse.sonatype.org/sites/m2e/

2.把features中的jar包拷贝到eclipse/features目录中；把plugins中的jar包拷贝到eclipse/plugins目录中；

3.重启eclipse
~~~

