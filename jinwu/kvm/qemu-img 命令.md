## qemu-img 命令

是qemu的磁盘管理工具。

基本用法：

*qemu-img command [command options]*

支持的命令分为如下几种。

1. checkout [-f fmt] filename

   对磁盘文件进行一致性检查，查找镜像文件中的错误。

   ```shell
   # qemu-img check mysystem.qcow2
   ```

2. create [-f fmt][-o options] filename [size]

创建fmt格式，大小为size，文件名为filename的镜像文件。根据fmt的不同，还可以添加不同的选项附加对该文件的设置。可以使用*-o ?* 来查询某种格式文件支持哪些选项。选项backing_file用来指定其后端镜像，那么这个创建的镜像文件仅记录与后端镜像文件的差异性(应该就是快照了)，后端镜像不会被修改，除非使用commit去提交这些改动。这时size不是必须的，默认为后端镜像的大小。

```shell
# 查询qcow2格式支持的选项
]# qemu-img create -f qcow2 -o ?

#创建有backing_file的qcow2格式镜像
]# qemu-img create -f qcow2 -b rhe16.img rhe16.qcow2
或者
]# qemu-img create -f qcow2 -o backing_file=rhe16.img rhe16.qcow2
我想应该可以作为一个备份类似的，但是发现不是，我在正常的系统下建立了一个新卷指定了backing_file，然后开启并修改系统(删除boot分区)，使之不能正常启动，关机后，删除创建的卷，再开机任然不行。
我在机器中建立了一个1G的文件，但是查看卷的信息发现没有任何变化disk的大小，目前没有发现有什么用backing_file


#创建没有backing_file的10G大小的qcow2格式镜像。
]# qemu-img create -f qcow2 ubuntu.qcow2 10G

```

3、commit [-f fmt] filename

提交filename文件中的更改到后端支持镜像文件。

4、convert (-c) (-f fmt) (-O output_fmt) [-o options] filename filename2 [...] output_name

将fmt格式的filename根据options选项转换为格式output_fmt名为output_filename的镜像文件。输入格式可以自动检测，输出格式默认会被转换为raw文件格式。默认稀疏方式存储。-c 表示压缩qcow2和qcow才支持。

```shell
]# qemu-img convert -O qcow2 my.vmdk mykvm.qcow2
```

5、info [-f fmt] filename

展示filename镜像文件的信息。



6、snapshot [-l | -a snapshot | -c snapshot | -d snapshot]filename

*-l* 选项表示查询并列出镜像文件中的所有快照。

*-a* 让镜像文件使用某个快照。

*-c* 表示创建一个快照

*-d* 删除一个快照

快照的使用方法，在需要时创建一个快照。保存当前的系统状态。

```shell
]# qemu-img snapshot -c snap1 ss.qcow2
```

在系统出问题或其他情况下需要恢复到快照时的状态时，应用相对应的快照。

```shell
]# qemu-img snapshot -a snap1 ss.qcow2
```

7、resize filename [+ | -]size

改变镜像文件的大小。+ -表示增加或者减少镜像大小，size支持K,M,G,T等单位。缩小之前需要在客户机中保证其中文件系统有空余空间，否则会丢失数据，qcow2格式不支持缩小镜像的操作。