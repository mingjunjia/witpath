## openstack上出现的一些问题



###### 1、重启后虚拟机的网络不通

用对应的工号登录openstack上，进入对应虚拟机的控制台。

检查发现ip没有，路由表也为空，网卡为关闭状态。重启后也是同样症状。

openstack上的虚拟机的ip是*dhcp*形式分配，现在dhcp失败，只好采用手动设置网络。

![问题虚拟机](C:\Users\yueshang\AppData\Local\Temp\1530003671227.png)

在数量页可以看到，原来的ip为：172.29.1.17

从其他的虚拟机中知道route为：172.29.1.1	dns为：192.168.211.103

打开网页配置文件：

```shell
[root@hadoop003 ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

设置参数如下：

```shell
DEVICE="eth0"
BOOTPROTO="static"
IPV6INIT="yes"
MTU="1500"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="5200c8cb-aace-4850-9807-1dc868f9a1cf"
IPADDR="172.29.1.17"
NETMASK="255.255.255.0"
GATEWAY="172.29.1.1"
DNS1="192.168.211.103"
```

只需添加ip，netmask，route，dns和修改BOOTPROTO的值为static即可。

重启网络：

```shell
[root@hadoop003 ~]# service network restart
```

已经可以ping通外网，但是还是不可以ssh连接上。



连接时报错：

```shell
[yueshang.LAPTOP-7QFEQV1O] ➤ ssh root@192.168.11.189
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```

应该是sshd的配置有些问题，查到PubkeyAuthentication yes   和PasswordAuthentication no的值都要为yes，修改之后，可以成功连上。

```shell
[root@hadoop003 ~]# vi /etc/ssh/sshd_config
```



