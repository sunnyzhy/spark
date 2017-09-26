#### 关闭VMware的DHCP

~~~
1).打开VMware的 编辑 -> 虚拟网络编辑器 -> 选择VMnet8 -> 不勾选“使用本地DHCP服务将IP地址分配给虚拟机”

2).打开NAT设置，查看网关IP，如192.168.237.2

3).打开VMware的 虚拟机 -> 设置 -> 网络适配器 -> NAT模式
~~~

#### 设置静态IP

~~~javascript
# vim /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=hadoop1
#NTPSERVERARGS=iburst
GATEWAY=192.168.237.2

# vim /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
HWADDR=00:0C:29:94:2C:33
TYPE=Ethernet
UUID=726e8d76-c9d6-41a9-9b8d-03c203a6e724
ONBOOT=yes
NM_CONTROLLED=yes
#BOOTPROTO=dhcp
BOOTPROTO=static
IPADDR=192.168.237.107
BROADCAST=192.168.237.255
GATEWAY=192.168.237.2
NETMASK=255.255.255.0
IPV6INIT=yes
ARPCHECK=no
DNS1=8.8.8.8
~~~

		注意：这里的DNS1是必须要设置的否则无法进行域名解析。

~~~javascript
# vim /etc/resolv.conf
nameserver 8.8.8.8
~~~

其实这一步可以省掉，上面设置了DNS Server的地址后系统会自动修改这个配置文件。

~~~javascript
# service network restart
~~~
