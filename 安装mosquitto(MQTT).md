# 官网
http://mosquitto.org/download/

# 安装
~~~
# cd /etc/yum.repos.d

# vim mosquitto.repo
[home_oojah_mqtt]
name=mqtt (CentOS_CentOS-7)
type=rpm-md
baseurl=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/
gpgcheck=1
gpgkey=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/repodata/repomd.xml.key
enabled=1

# yum search all mosquitto

# yum -y install mosquitto-clients.x86_64  mosquitto.x86_64
~~~

# 启动
~~~
# systemctl start mosquitto
~~~

# 停止
~~~
# systemctl stop mosquitto
~~~

# 订阅消息
~~~
# mosquitto_sub -h 192.168.x.x -p 1883 -t Topic
~~~

# 发布消息
~~~
# mosquitto_pub -h 192.168.x.x -p 1883 -t Topic -m "hello world"
hello world
~~~
