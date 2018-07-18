# kvm管理工具

#### 1、libvirt

一个工具和应用程序接口，virsh,virt-manager等都在底层使用了它。主要作为连接底层hypervisor和上层应用程序的一个中间适配层

![虚拟机管理工具通过libvirt管理各种类型的虚拟机](C:\Users\yueshang\AppData\Local\Temp\1529843440417.png)



libvirt的主要功能包含主要五个部分

1. 域(domain 客户机)的管理

   包括生命周期的管理，如启动，暂停，停止。也包括对各种设备的操作，如网卡等

2. 远程节点(物理机)管理

   1. 只要物理节点上运行了libvirt这个守护进程，远程管理程序就可以连接到该节点进行管理操作，如使用ssh传输，用如下命令就可以在远程的某台管理及其机器上连接到物理节点，从而管理其上的域。

      virsh -c qemu+ssh://root@example.com/system	//example.com是节点名 qemu指的是hypervisor，ssh是连接方式

3. 存储管理

   任何运行了libvirt守护进程的主机，都可以通过libvirt来管理不同类型的存储，如创建不同格式的客户镜像(qcow2,raw.....)，挂载nfs共享。也是支持远程管理的。

4. 网络管理

   任何运行了libvirt守护进程的主机，都可以通过libvirt来管理物理的和逻辑的网络接口。包括列出现有的网络接口，配置网络接口nat网络设置，为客户机分配虚拟网口等等。

5. 提供一个稳定可靠的用户程序接口

   libvirt主要由三个部分组成，分别是应用程序接口库，一个守护进程(libvirtd)，一个默认命令管理工具(virsh)。libvirtd守护进程负责执行对域的管理工作，所以这个守护进程一定要处于运行状态。接口库提供其他管理工具(virsh,virt-managerd等)提供支持。



libvirtd守护进程的启动和停止，并不会直接影响正在运行中的客户机。libvirtd在启动或重启完成时，只要客户机的XML配置文件是存在的，libvirtd会自动加载这些客户的配置，获取他们的信息，如果客户机没有基于libvirt格式的XML文件来运行，libvirtd则不能发现他。默认配置文件在/etc/libvirt/qemu  中。

在输入设备中，tablet设备可以让光标在客户机取得绝对位置的定位。

##### 连接到本地或远程的hypervisor

使用virsh这个工具时，可以用*-c*或*--connect* 选项来指定到某个url的连接。

连接到本地：virsh -c qemu:///system		//system 指与节点的system实例相连，如果是session的话是与session实例相连。

连接到远程：virsh -c qemu+ssh://root@192.168.12.111/system



##### libvirt python API绑定

libvirt 有python绑定，使用python来编程，在使用前要安装libvirt-python软件包



#### 2、virsh

是完全在命令行文本模式下运行的用户态工具。

有两个工作模式(交互模式和非交互模式)。交互模式，在连接到相应hypervisor上，然后输入一个命令返回一个结果，直到用 *quit命令退出*；

``` shell
# virsh -c qemu+ssh://root@192.168.12.131/system
...
virsh # quit
```

非交互模式，直接在命令行中建立一个连接的url之后添加需要执行的一个或多个命令，执行完后将命令返回当前终端上，然后自动断开连接。

```shell
# virsh -c qemu+ssh://root@192.168.12.131/system "list"
```

在某个节点上直接使用virsh命令，就默认连接到本节点的hypervisor上，并进入命令交互模式。而直接使用 *virsh list* 命令，就是在连接本节点的hypervisor之后，使用的非交互模式中执行了 *list* 。



###### 2.1 域的管理

list				获取当前节点上所有域的列表

domstate<id>		获取一个域的运行状态

dominfo<id>		获取一个域的基本信息

domid<Name or UUid>	根据域名字或者UUID返回ID值

dommemstat<id>	获取一个域的内存使用情况的统计信息

setmem<id><mem-size>	设置一个域的内存大小默认单位为KB

vcpuinfo<id>		获取一个域的vCPU的基本信息

vcpuin<ID><vcpu><pCPU>	将一个域的VCPU绑定到某个物理CPU上运行

setvcpus<id><vCPU-num>	设置一个域的vcpu的个数

vncdisplay<id>	获取一个域的VNC连接ip地址和端口

create<dom.xml>	根据域的xml配置文件创建一个域

suspend<id>	暂停一个域

resume<id>	唤醒一个域

shutdown<id>		让一个域执行关机操作

reboot<id>		让一个域重启

reset<id>			强制重启一个域，相当于物理机器上的reset，可能会损坏改域的文件系统

destroy<id>		立即销毁一个域，相当于直接拔掉物理电源线，可能损坏文件系统，相当于强制关机。

save<id><file.img>		保存一个运行中的域的状态到一个文件中

restore<file.img>			从一个被保存的文件中恢复一个域的运行

migrate<id><dest_url>		讲一个域迁移到另一个域

dumpxml<id>			以XML格式转存一个域的信息到标准输出中

attach-device<id><device.xml>	向一个域中添加xml文件中的设备(热插拔)

detach-device<id><device.xml>	将xml文件中的设备从一个域中移除

console<id>		连接到一个域的控制台



###### 2.2、宿主机和hypervisor的管理

一旦建立有特权的连接(system)，virsh也可以对宿主机和hypervisor进行管理，主要是查询。

version		显示libvirt和hypervisor的版本信息

sysinfo		以xml格式打印宿主机系统的信息

nodeinfo		显示该节点的基本信息

uri			显示当前连接的URI

hostname		显示当前节点的主机名

capabilities	显示该节点宿主机和客户机的架构特性

freecell		显示当前muma单元的可用内存

nodememstats<cell>	显示该节点(某个)内存单元使用情况统计

connect<uri>		连接到URI指示的hypervisor

nodecpustats<cpu-num>		显示该节点的某个CPU使用情况的统计

qemu-attach<pid>		根据PID添加一个qemu进程到libvirt中



##### 2.3、网络的管理命令

virsh可以对节点上的网络接口和分配给域的虚拟网络进行管理。

iface-list		显示出物理主机的网络接口列表

iface-mac<if-name>	根据网络接口名称查询其对应的mac地址

iface-name<mac>		根据mac地址查询其对应的网络接口名称

iface-edit<if-name-or-uuid>	编辑一个物理主机的网络接口的xml配置文件

iface-dumpxml<if-name-or-uuid>	以xml格式转存出一个网络接口的xml文件

iface-destroy<if-name or uuid>	关闭宿主机上的一个物理网络接口

iface-bridge<if-name><bridge-name>	添加网桥,如下。

```shell
# virsh iface-bridge eth0 br0
```

net-list		列出libvirt管理的虚拟网络

net-info<net-name or uuid>		根据名称查询一个虚拟网络的基本信息

net-uuid<net-name>			根据名称查询一个虚拟网络的UUID

net-name<net-UUID>			根据UUID查询一个虚拟网络的名称

net-create<net.xml>			根据一个网络的xml配置文件创建一个虚拟网络

net-edit<net-name-or-uuid>		编辑一个网络的xml配置文件

net-dumpxml<net-name-or-uuid>	转存出一个虚拟网络的xml配置文件信息

net-destroy<net-name-or-uuid>	销毁一个虚拟网络



##### 2.4、存储池和存储卷管理

pool-list		显示出libvirt管理的存储池

pool-info<pool-name>		根据一个存储池的名字查询其基本信息

pool-uuid<pool-name>		根据存储池的名称查询其UUID

pool-create<pool.xml>		根据xml配置文件的信息创建一个存储池

pool-edit<pool-name or -uuid>	编辑一个存储池的xml配置文件

pool-destroy<pool-name or uuid>		关闭一个存储池（在libvirt可见范围内）

pool-delete<pool-name or uuid>		删除一个存储池(不可恢复)

vol-list<pool-name or uuid>		查询一个存储池中的存储卷列表

vol-name<vol-key or path>		查询一个存储卷的名称

vol-path --pool<pool><vol-name-or-key>	查询一个存储卷的路径

vol-create<vol.xml>	根据xml配置文件创建一个存储卷

vol-clone<vol-name-path><name>	克隆一个存储卷

vol-delete<vol-name or key or path>	删除一个存储卷



#### 3、virt-manager

virt-manager是虚拟机管理器(Virtual Machine Manager)这个应用程序的缩写，也是该管理工具的软件包的名称。

使用了python语言开发其应用程序部分。

内置了一个vnc客户端，可以用于连接到客户机的图形界面进行交互。

支持本地或者远程管理kvm，Xen，qemu等hypervisor上的客户机。



#### 4、virt-viewer、virt-install和virt-top

##### 4.1、virt-viewer

是Virtual Machine Viewer(虚拟机查看器)工具的软件包和命令行工具。

使用GTK-VNC作为他的显示能力。

是用于与虚拟化客户机的图形显示的轻量级的交互工具。

##### 4.2、virt-install

为虚拟客户机的安装提供了一个便捷易用的方式，可以基于文本模式和图形界面的客户机安装。

```shell
# 从已经存在的卷中建一个虚拟机，使用默认参数
# virt-install  --name demo \
			--memory 512 \
			--disk /home/user/VMs/mydisk.qcow2 \
			--vcpus 4
			--graphics vnc
			--input tablet
			--import
	
#如下创建一个名为cent的虚拟机10G，从cenos7.4.iso镜像来引导启动安装过程	
[root@jmj ~]# virt-install --name cent --memory 2048 --disk path=/u01/instance/cent.qcow2,size=10 --network network:default --cdrom /u01/image/cenos7.4.iso --os-type=linux --accelerate --vnc

#下面的示例将创建一个名为rhel6的虚拟机，其有两个虚拟CPU，安装方法为FTP，并指定了ks文件的位置，磁盘映像文件为稀疏格式，连接至物理主机上的名为brnet0的桥接网络：
# virt-install \  
   --connect qemu:///system \  
   --virt-type kvm \  
   --name rhel6 \  
   --ram 1024 \  
   --vcpus 2 \  
   --network bridge=brnet0 \  
   --disk path=/VMs/images/rhel6.img,size=120,sparse \  
   --location ftp://172.16.0.1/rhel6/dvd \  
   --extra_args “ks=http://172.16.0.1/rhel6.cfg” \  
   --os-variant rhel6 \  
   --force
   
#下面的示例将创建一个名为rhel5.8的虚拟机，磁盘映像文件为稀疏模式的格式为qcow2且总线类型为virtio，安装过程不启动图形界面（--nographics），但会启动一个串行终端将安装过程以字符形式显示在当前文本模式下，虚拟机显卡类型为cirrus： 
# virt-install \  
--connect qemu:///system \  
--virt-type kvm \   
--name rhel5.8 \   
--vcpus 2,maxvcpus=4 \  
--ram 512 \   
--disk path=/VMs/images/rhel5.8.img,size=120,format=qcow2,bus=virtio,sparse \ 
--network bridge=brnet0,model=virtio  
--nographics \  
--location ftp://172.16.0.1/pub \   
--extra-args "ks=http://172.16.0.1/class.cfg  console=ttyS0  serial" \ 
--os-variant rhel5 \  
--force  \  
--video=cirrus  
```





##### 4.3、virt-top 

是一个用于展示虚拟化客户机运行状态和资源使用率的工具，和Linux系统上常使用的top工具类似。也是使用libvirt API来获取客户机的使用情况。

```shell
# virt-top
```

