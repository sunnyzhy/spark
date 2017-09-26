#### 安装phpMyAdmin
##### 安装php5
~~~javascript
yum -y update
yum -y install epel-release
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
wget https://centos6.iuscommunity.org/ius-release.rpm
rpm -Uvh ius-release*.rpm
yum -y update
yum -y install php55u php55u-opcache php55u-xml php55u-mcrypt php55u-gd php55u-devel php55u-mysql php55u-intl php55u-mbstring php55u-bcmath
~~~

如果失败，就用以下方法：
~~~javascript
# rpm -Uvh http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
# yum list --enablerepo=remi --enablerepo=remi-php56 | grep php
# yum -y install --enablerepo=remi --enablerepo=remi-php56 php php-opcache php-devel php-mbstring php-mcrypt php-mysqlnd php-phpunit-PHPUnit php-pecl-xdebug php-pecl-xhprof
# php --version
PHP 5.6.29 (cli) (built: Dec  8 2016 08:51:50) 
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
    with Xdebug v2.5.0, Copyright (c) 2002-2016, by Derick Rethans
~~~

##### 安装php7
###### 安装gcc-c++
~~~javascript
# yum -y install gcc-c++
~~~

###### 安装libxml2相关组件
~~~javascript
# yum -y install libxml2
# yum -y install libxml2-devel
~~~

###### 安装openssl
~~~javascript
# yum -y install openssl openssl-devel
~~~

###### 安装curl
~~~javascript
# yum -y install curl curl-devel
~~~

###### 安装libpng
~~~javascript
# yum -y install libpng libpng-devel
~~~

###### 安装freetype
~~~javascript
# yum -y install freetype-devel
~~~

###### 安装libxslt
~~~javascript
# yum -y install libxslt-devel
~~~

###### 安装php
http://php.net/
~~~javascript
# tar -zxvf php-7.1.9.tar.gz
# cd php-7.1.9
# ./configure --prefix=/usr/local/php --with-curl --with-freetype-dir --with-gd --with-gettext --with-iconv-dir --with-kerberos --with-libdir=lib64 --with-libxml-dir --with-MySQL --with-mysqli --with-openssl --with-pcre-regex --with-pdo-mysql --with-pdo-sqlite --with-pear --with-png-dir --with-xmlrpc --with-xsl --with-zlib --enable-fpm --enable-bcmath --enable-libxml --enable-inline-optimization --enable-gd-native-ttf --enable-mbregex --enable-mbstring --enable-opcache --enable-pcntl --enable-shmop --enable-soap --enable-sockets --enable-sysvsem --enable-xml --enable-zip
~~~

如果显示以下内容，就说明配置完成
~~~
Generating files
configure: creating ./config.status
creating main/internal_functions.c
creating main/internal_functions_cli.c
+--------------------------------------------------------------------+
| License:                                                           |
| This software is subject to the PHP License, available in this     |
| distribution in the file LICENSE.  By continuing this installation |
| process, you are bound by the terms of this license agreement.     |
| If you do not agree with the terms of this license, you must abort |
| the installation process at this point.                            |
+--------------------------------------------------------------------+

Thank you for using PHP.
~~~

~~~javascript
# make && make install
~~~

如果显示以下内容，就说明安装完成
~~~
Generating phar.php
Generating phar.phar
PEAR package PHP_Archive not installed: generated phar will require PHP's phar extension be enabled.
directorytreeiterator.inc
directorygraphiterator.inc
clicommand.inc
invertedregexiterator.inc
pharcommand.inc
phar.inc

Build complete.
Don't forget to run 'make test'.
~~~

###### 配置php
~~~javascript
# cp php.ini-development /usr/local/php/lib/php.ini
# cp -R ./sapi/fpm/php-fpm.conf /usr/local/php/etc/php-fpm.conf
# cp ./sapi/fpm/init.d.php-fpm  /etc/init.d/php-fpm
# chmod a+x /etc/init.d/php-fpm
~~~

###### 启动php
~~~javascript
# /etc/init.d/php-fpm start
[15-Sep-2017 23:55:31] WARNING: Nothing matches the include pattern '/usr/local/php/etc/php-fpm.d/*.conf' from /usr/local/php/etc/php-fpm.conf at line 125.
[15-Sep-2017 23:55:31] ERROR: No pool defined. at least one pool section must be specified in config file
[15-Sep-2017 23:55:31] ERROR: failed to post process the configuration
[15-Sep-2017 23:55:31] ERROR: FPM initialization failed
~~~

如果出现以上错误提示，就用以下方法
~~~javascript
# cd /usr/local/php/etc/php-fpm.d
# cp www.conf.default www.conf

启动成功
~~~javascript
# /etc/init.d/php-fpm start
Starting php-fpm  done
~~~

###### 更新apache
~~~javascript
# rpm -qa httpd  --查看是否安装了apache
httpd-2.2.15-55.el6.centos.2.x86_64
# yum -y update httpd  --更新apache
~~~

###### 安装phpMyAdmin
https://www.phpmyadmin.net/downloads/
~~~javascript
# tar -zxvf phpMyAdmin-4.0.10.18-all-languages.tar.gz -C /var/www/html
# cd /var/www/html
# mv phpMyAdmin-4.0.10.18-all-languages phpMyAdmin
# cd phpMyAdmin
# cp config.sample.inc.php config.inc.php
# vim config.inc.php
~~~

~~~
把 $cfg['Servers'][$i]['host'] = 'localhost';
修改为 $cfg['Servers'][$i]['host'] = '127.0.0.1';
~~~

~~~javascript
# service httpd restart
~~~

验证是否安装成功
输入http://127.0.0.1/phpMyAdmin/
