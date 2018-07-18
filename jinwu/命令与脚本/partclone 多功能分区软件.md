## partclone 多功能分区软件

 Partclone 是由 Clonezilla 的开发者们开发的用于创建和克隆分区镜像的自由开源软件。实际上，Partclone 是 Clonezilla 所基于的工具之一。

它为用户提供了备份与恢复已用分区的工具，并与多个文件系统高度兼容，这要归功于它能够使用像 e2fslibs 这样的现有库来读取和写入分区，例如 ext2。

它最大的优点是支持各种格式，包括 ext2、ext3、ext4、hfs+、reiserfs、reiser4、btrfs、vmfs3、vmfs5、xfs、jfs、ufs、ntfs、fat（12/16/32）、exfat、f2fs 和 nilfs。

它还有许多的程序，包括 partclone.ext2（ext3＆ext4）、partclone.ntfs、partclone.exfat、partclone.hfsp 和 partclone.vmfs（v3和v5） 等等。 

**开源的免费软件，跨平台支持windows，Linux，mac，esx文件系统的备份和恢复**

功能：支持救援，克隆分区成镜像文件，将镜像文件恢复到分区，快速复制分区，支持raw格式克隆，及其功能。

Linux上安装和使用partclone。

```shell
# yum install partclone  #centos等，
# apt install partclone  #ubuntu 等。
```

语法：

```shell
partclone.[fstype] {[-c | --clone] [-r | --restore] [-b | --dev-to-dev]} {[-s | --source] source ...
```

文件系统与对应的命令：

```shell
File System             partclone.[fstype]

btrfs                   partclone.btrfs
ext2, ext3, ext4        partclone.[ext2|ext3|ext4]
reiserfs 3.5            partclone.reiserfs
reiser 4                partclone.reiser4
xfs                     partclone.xfs
ufs | ufs2              partclone.ufs
jfs                     partclone.jfs
hfs plusfs              partclone.[hfs+|hfsplus]
vmfs                    partclone.vmfs
ntfs                    partclone.ntfs
fat12, fat16, fat32     partclone.[fat12|fat16|fat32]
exfat                   partclone.exfat
minix                   partclone.minix
f2fs                    partclone.f2fs
nilfs2                  partclone.nilfs2

```

部分参数指令：

```shell
-s file, --source file	#指定源文件，file可以是镜像文件也可以是设备文件/dev/sda1  等
-o file, --output file  #指定输出的文件，file可以是镜像也可以是磁盘设备文件/dev/sda1等
-O file, --overwrite file #分区存在的话会被覆写
-c, --clone 	#保存分区到特定格式的镜像文件
-r, --restore	#从指定的镜像文件恢复到分区
-b, --dev-to-dev   #本地一个设备到另一个设备(分区)的复制
-d, --debug #显示调试信息
```



例子：

克隆分区为镜像；

```shell
# partclone.ext4 -d -c -s /dev/sda1 -o sda1.img
```

将镜像恢复到分区：

```shell
# partclone.ext4 -d -r -s sda1 -o /dev/sda1
```

分区到分区克隆：

```shell
# partclone.ext4 -d -b -s /dev/sda1 -o /dev/sdb1
```

显示镜像信息：

```shell
# partclone.info -s sda1.img
```

检查镜像：

```
partclone.chkimg -s sda1.img
```





