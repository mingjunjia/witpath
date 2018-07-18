# [kvm 创建新虚拟机命令virt-install 使用说明](https://www.cnblogs.com/liang-wei/p/6732055.html)

virt-install 命令说明

*1、命令作用      建立（provision）新虚拟机   2、语法   virt-install [选项]...*

3、说明（DESCRIPTION）
   virt-install是一个使用“libvirt” hypervisor 管理库构建新虚拟机的命令行工具，此工具使用串行控制台，SDL（Simple DirectMedia Layer）图形或者VNC客户端/服务器，
   支持基于命令行和图形安装。所建立的客户机（在虚拟化中，把运行运行虚拟机服务器称为host，把虚拟机称为guest）能够配置使用一个或多个虚拟磁盘、网卡、音频设备和物理
   主机设备（USB、PCI）
   virt-install is a command line tool for provisioning new virtual machines using the "libvirt" hypervisor management library. The tool supports
   both text based & graphical installations, using serial console, SDL graphics or a VNC client/server pair. The guest can be configured to use
   one or more virtual disks, network interfaces, audio devices, and physical host devices (USB, PCI).

   安装媒介可以本地或基于NFS、HTTP、FTP服务器远程连接，基于后者，virt-install将提取必要的最小限度的文件开始安装过程，在安装过程中，允许客户机根据需要提取其他的
   文件，也支持PXE引导和导入已有的磁盘映像（此操作跳过安装阶段）。
   The installation media can be held locally or remotely on NFS, HTTP, FTP servers. In the latter case "virt-install" will fetch the minimal
   files necessary to kick off the installation process, allowing the guest to fetch the rest of the OS distribution as needed. PXE booting, and
   importing an existing disk image (thus skipping the install phase) are also supported.

   给予适合的命令行变量，“virt-install”具有完全无人值守安装的能力，这允许更容易的客户机自动化安装。本工具也支持交互模式通过提供 --prompt选项，但是这种方式只要求
   最小的必要选项。
   Given suitable command line arguments, "virt-install" is capable of running completely unattended, with the guest ’kickstarting’ itself too.
   This allows for easy automation of guest installs. An interactive mode is also available with the --prompt option, but this will only ask for
   the minimum required options.

4、选项（OPTIONS）
       大部分选项不是必须的。最小的需求是： --name  --ram ,存储选项（--disk --nodisk）以及一个安装选项。
       Most options are not required. Minimum requirements are --name, --ram, guest storage (--disk or --nodisks), and an install option.

​       -h, --help
         显示帮助信息并退出
         Show the help message and exit

​       --connect=CONNECT
         连接到一个非默认hypervisor，选择默认链接基于以下规则：
         Connect to a non-default hypervisor. The default connection is chosen based on the following rules:

​         xen If running on a host with the Xen kernel (checks against /proc/xen)

​         qemu:///system
             If running on a bare metal kernel as root (needed for KVM installs)

​         qemu:///session
             If running on a bare metal kernel as non-root

​             只有在上述默认优先级不正确时才有必要提供“--connect”参数，例如如果想要在Xen内核上使用qemu。
             It is only necessary to provide the "--connect" argument if this default prioritization is incorrect, eg if wanting to use QEMU while on
             a Xen kernel.

5、通用选项（General Options）

​       通用配置参数适用于所有类型客户机安装。
       General configuration parameters that apply to all types of guest installs.

​       -n NAME, --name=NAME
         新客户虚拟机实例名字，在连接的hypervisor已知的所有虚拟机中必须唯一，包括那些当前未活动的虚拟机。想要重新定义一个已存在的虚拟机，在运行‘virt-install’之前
         使用virsh工具关闭（‘virsh shutdown’）和删除（‘virsh undefine’）此虚拟机。
         Name of the new guest virtual machine instance. This must be unique amongst all guests known to the hypervisor on the connection, including
         those not currently active. To re-define an existing guest, use the virsh(1) tool to shut it down (’virsh shutdown’) & delete (’virsh
         undefine’) it prior to running "virt-install".

​       -r MEMORY, --ram=MEMORY
         以M为单位指定分配给虚拟机的内存大小，如果hypervisor没有足够的可用内存，它通常自动从主机[操作系统](http://lib.csdn.net/base/operatingsystem)使用的内存中获取，以满足此操作分配需要。
         Memory to allocate for guest instance in megabytes. If the hypervisor does not have enough free memory, it is usual for it to automatically
         take memory away from the host operating system to satisfy this allocation.

​       --arch=ARCH
         为虚拟机请求一个非本地CPU[架构](http://lib.csdn.net/base/architecture)，这个选项当前只对qemu客户机有效，但是不能够使用加速机制。如果忽略，在虚拟机中将使用主机CPU架构。
         Request a non-native CPU architecture for the guest virtual machine.  The option is only currently available with QEMU guests, and will not
         enable use of acceleration. If omitted, the host CPU architecture will be used in the guest.

​       -u UUID, --uuid=UUID
         虚拟机的唯一编号；如果没有指定，将生成一个随机UUID。如果指定，应当使用一个32为的十六进制数字。UUID保证跨整个数据中心甚至世界的唯一性，在手工指定
         UUID时这一点要记住。
         UUID for the guest; if none is given a random UUID will be generated. If you specify UUID, you should use a 32-digit hexadecimal number. UUID
         are intended to be unique across the entire data center, and indeed world. Bear this in mind if manually specifying a UUID

​       --vcpus=VCPUS
         虚拟机的虚拟CPU数。不是所有hypervisor都支持SMP虚拟机，在这种情况下这个变量将被忽略。
         Number of virtual cpus to configure for the guest. Not all hypervisors support SMP guests, in which case this argument will be silently
         ignored

​       --check-cpu
         检查指定的虚拟CPU数不要超过无论CPU，如果超过将返回警告信息。
         Check that the number virtual cpus requested does not exceed physical CPUs and warn if they do.

​       --cpuset=CPUSET
         设置哪个物理CPU能够被虚拟机使用。“CPUSET”是一个逗号分隔数字列表，也可以指定范围，例如：
         Set which physical cpus the guest can use. "CPUSET" is a comma separated list of numbers, which can also be specified in ranges. Example:

​             0,2,3,5     : Use processors 0,2,3 and 5  --使用0，2，3 和5 处理器
             1-3,5,6-8   : Use processors 1,2,3,5,6,7 and 8  --使用1，2，3，5，6，7，8处理器

​         如果此参数值为‘auto’，virt-install将使用NUMA（非一致性内存访问）数据试图自动确定一个优化的CPU定位。
         If the value ’auto’ is passed, virt-install attempts to automatically determine an optimal cpu pinning using NUMA data, if available.

​       --os-type=OS_TYPE
         针对一类操作系统优化虚拟机配置（例如：‘[Linux](http://lib.csdn.net/base/linux)’，‘windows’），这将试图选择最适合的ACPI与APIC设置，支持优化鼠标驱动，virtio以及通常适应其他操作系统特性。
         参见"--os-variant" 选项
         Optimize the guest configuration for a type of operating system (ex. ’linux’, ’windows’). This will attempt to pick the most suitable ACPI &
         APIC settings, optimally supported mouse drivers, virtio, and generally accommodate other operating system quirks. See "--os-variant" for
         valid options.
       --os-variant=OS_VARIANT
         针对特定操作系统变体（例如’fedora8’, ’winxp’）进一步优化虚拟机配置，这个参数是可选的并且不需要与 "--os-type"选项并用，有效值包括：
         Further optimize the guest configuration for a specific operating system variant (ex. ’fedora8’, ’winxp’). This parameter is optional, and
         does not require an "--os-type" to be specified. Valid values are:

​         linux
             debianetch
                 Debian Etch

​             debianlenny
                 Debian Lenny

​             fedora5
                 Fedora Core 5

​             fedora6
                 Fedora Core 6

​             fedora7
                 Fedora 7

​             fedora8
                 Fedora 8

​             fedora9
                 Fedora 9

​             fedora10
                 Fedora 10

​             fedora11
                 Fedora 11

​             fedora12
                 Fedora 12

​             generic24
                 Generic 2.4.x kernel
             generic26
                 Generic 2.6.x kernel

​             virtio26
                 Generic 2.6.25 or later kernel with virtio

​             rhel2.1
                 Red Hat Enterprise Linux 2.1

​             rhel3
                 Red Hat Enterprise Linux 3

​             rhel4
                 Red Hat Enterprise Linux 4

​             rhel5
                 Red Hat Enterprise Linux 5

​             rhel5.4
                 Red Hat Enterprise Linux 5.4 or later

​             rhel6
                 Red Hat Enterprise Linux 6

​             sles10
                 Suse Linux Enterprise Server

​             ubuntuhardy
                 Ubuntu 8.04 LTS (Hardy Heron)

​             ubuntuintrepid
                 Ubuntu 8.10 (Intrepid Ibex)

​             ubuntujaunty
                 Ubuntu 9.04 (Jaunty Jackalope)

​         other
             generic
                 Generic

​             msdos
                 MS-DOS

​             netware4
                 Novell Netware 4

​             netware5
                 Novell Netware 5

​             netware6
                 Novell Netware 6

​         solaris
             opensolaris
                 Sun OpenSolaris

​             solaris10
                 Sun Solaris 10

​             solaris9
                 Sun Solaris 9

​         unix
             freebsd6
                 Free BSD 6.x

​             freebsd7
                 Free BSD 7.x

​             openbsd4
                 Open BSD 4.x

​         windows
             vista
                 Microsoft Windows Vista

​             win2k
                 Microsoft Windows 2000

​             win2k3
                 Microsoft Windows 2003

​             win2k8
                 Microsoft Windows 2008

​             winxp
                 Microsoft Windows XP (x86)

​             winxp64
                 Microsoft Windows XP (x86_64)
       --host-device=HOSTDEV
         附加一个物理主机设备到客户机。HOSTDEV是随着libvirt使用的一个节点设备名（具体设备如’virsh nodedev-list’的显示的结果）
         Attach a physical host device to the guest. HOSTDEV is a node device name as used by libvirt (as shown by ’virsh nodedev-list’).

6、完全虚拟化特定选项（Full Virtualization specific options）

​       在完全虚拟化客户机安装时的特定参数。
       Parameters specific only to fully virtualized guest installs.

​       --sound
         附加一个虚拟音频设备到客户机
         Attach a virtual audio device to the guest.

​       --noapic
         覆盖操作系统类型/变体使APIC（Advanced Programmable Interrupt Controller）设置对全虚拟化无效。
         Override the OS type / variant to disables the APIC setting for fully virtualized guest.

​       --noacpi
         覆盖操作系统类型/变体使ACPI（Advanced Configuration and Power Interface）设置对全虚拟化无效。
         Override the OS type / variant to disables the ACPI setting for fully virtualized guest.

7、虚拟化类型选项（Virtualization Type options）

​       这些选项覆盖默认虚拟化类型选择。      
       Options to override the default virtualization type choices.

​       -v, --hvm
         如果在主机上全虚拟化和para 虚拟化（para-irtualization如何解释还没有定论，有人称之为半虚拟化），请求使用全虚拟化（full virtualization）。如果在
         一个没有硬件虚拟化支持的机器上连接Xen hypervisor，这个参数不可用，这个参数意指连接到一个基于qemu的hypervisor。
         Request the use of full virtualization, if both para & full virtualization are available on the host. This parameter may not be available if
         connecting to a Xen hypervisor on a machine without hardware virtualization support. This parameter is implied if connecting to a QEMU based
         hypervisor.

​       -p, --paravirt
         这个参数意指构建一个paravirtualized客户机。如何主机既支持para又支持 full虚拟化，并且既没有指定本选项也没有指定"--hvm"选项，这个选项是假定选项。
         This guest should be a paravirtualized guest. If the host supports both para & full virtualization, and neither this parameter nor the "--hvm"
         are specified, this will be assumed.

​       --accelerate
         当安装QEMU客户机时，如果支持可用KVM或KQEMU内核加速能力。除非一个客户机操作系统不兼容加速，这个选项是推荐最好加上。如果KVM和KQEMU都支持，KVM加速
         器优先使用。
         When installing a QEMU guest, make use of the KVM or KQEMU kernel acceleration capabilities if available. Use of this option is recommended
         unless a guest OS is known to be incompatible with the accelerators. The KVM accelerator is preferred over KQEMU if both are available.

8、安装方法选项（Installation Method options）

​       -c CDROM, --cdrom=CDROM
         对应全虚拟化客户机，文件或设备作为一个虚拟化CD-ROM设备使用，可以是ISO映像路径或者一个CDROM设备，它也可以是一个能够提取/访问最小引导ISO映像的URL，
         URL使用与在 "--location" 选项中说明的相同的格式。如果一个CDROM已经通过 "--disk"选项指定，并且 "--cdrom"和其他任何选项都没有指定，"--disk" cdrom
         将作为安装媒介使用。
         File or device use as a virtual CD-ROM device for fully virtualized guests.  It can be path to an ISO image, or to a CDROM device. It can also
         be a URL from which to fetch/access a minimal boot ISO image. The URLs take the same format as described for the "--location" argument. If a
         cdrom has been specified via the "--disk" option, and neither "--cdrom" nor any other install option is specified, the "--disk" cdrom is used
         as the install media.

​       -l LOCATION, --location=LOCATION
         客户虚拟机kernel+initrd 安装源。LOCATION使用以下格式：
         Installation source for guest virtual machine kernel+initrd pair.  The "LOCATION" can take one of the following forms:

​         DIRECTORY
             指向一个包含可安装发行版映像的目录。
             Path to a local directory containing an installable distribution image

​         nfs:host:/path or nfs://host/path
             指向包含可安装发行版映像的NFS服务器位置。
             An NFS server location containing an installable distribution image

​         <http://host/path>
             指向包含可安装发行版映像的http服务器位置。
             An HTTP server location containing an installable distribution image

​         <ftp://host/path>
             指向包含可安装发行版映像的FTP服务器位置。
             An FTP server location containing an installable distribution image

​         下面是指定几个特定发行版url的例子：
         Some distro specific url samples:

​         Fedora/Red Hat Based
             <http://download.fedoraproject.org/pub/fedora/linux/releases/10/Fedora/i386/os/>

​         Debian/Ubuntu
             <http://ftp.us.debian.org/debian/dists/etch/main/installer-amd64/>

​         Suse
             <http://download.opensuse.org/distribution/11.0/repo/oss/>

​         Mandriva
             <ftp://ftp.uwsg.indiana.edu/linux/mandrake/official/2009.0/i586/>

​       --pxe
         使用PXE（preboot execute environment）加载初始ramdisk 和 kernel，从而起动客户机安装过程。
         Use the PXE boot protocol to load the initial ramdisk and kernel for starting the guest installation process.

​       --import
         跳过操作系统安装过程，围绕一个存在的磁盘映像建立客户机。引导使用的设备是通过"--disk" or "--file"指定的第一个设备。
         Skip the OS installation process, and build a guest around an existing disk image. The device used for booting is the first device specified
         via "--disk" or "--file".

​       --livecd
         指定安装媒介是一个可引导操作系统CD（A live CD, live DVD, or live disc is a CD or DVD containing a bootable computer operating system），因此需要
         将虚拟机配置成永不从CDROM引导。这也许需要与"--nodisks" 标准组合使用。
         Specify that the installation media is a live CD and thus the guest needs to be configured to boot off the CDROM device permanently. It may be
         desirable to also use the "--nodisks" flag in combination.

​       -x EXTRA, --extra-args=EXTRA
         当执行从"--location"选项指定位置的客户机安装时，附加内核命令行参数到安装程序。
         Additional kernel command line arguments to pass to the installer when performing a guest install from "--location".

 9、存储配置选项（Storage Configuration）

​       --disk=DISKOPTS
         用不同的选项，指定作为客户机存储的媒介。通常的磁盘串格式是：
         Specifies media to use as storage for the guest, with various options. The general format of a disk string is

​             --disk opt1=val1,opt2=val2,...

​         要知道媒介，必须提供下面选项其中之一：
         To specify media, one of the following options is required:

​         path
             要使用的一个指向某些存在后不存在存储媒介的路径。存在的媒介可以是文件或块设备。如在远程主机安装，存在的媒介必须被共享为一个libvirt存储卷。
             A path to some storage media to use, existing or not. Existing media can be a file or block device. If installing on a remote host, the
             existing media must be shared as a libvirt storage volume.

​             指定一个不存在的路径意指试图建立一个新的存储，并且需要知道一个‘size’值。如果路径的基目录是一个在主机上的libvirt存储池，新存储将被建立为一个
             libvirt存储卷。对于远程主机，如果使用此方法，基目录需要是一个存储池。
             Specifying a non-existent path implies attempting to create the new storage, and will require specifyng a ’size’ value. If the base
             directory of the path is a libvirt storage pool on the host, the new storage will be created as a libvirt storage volume. For remote
             hosts, the base directory is required to be a storage pool if using this method.

​         pool
             一个要在其上建立新存储的已有的libvirt存储池名，需要指定一个‘size’值。
             An existing libvirt storage pool name to create new storage on. Requires specifying a ’size’ value.

​         vol 
             要使用的一个已有的libvirt存储卷，指定格式类似’poolname/volname’
             An existing libvirt storage volume to use. This is specified as ’poolname/volname’.

10、其他可用选项（Other available options）

​         device
             磁盘设备类型。取值是’cdrom’, ’disk’, or ’floppy’，默认为 ’disk’。如果’cdrom’作为指定值并且没有选择安装方法，cdrom将被作为安装媒介。
             Disk device type. Value can be ’cdrom’, ’disk’, or ’floppy’. Default is ’disk’. If a ’cdrom’ is specified, and no install method is
             chosen, the cdrom is used as the install media.

​         bus 
             磁盘总线类型，取值是’ide’, ’scsi’,’usb’, ’virtio’ 或 ’xen’，由于不是所有的hypervisor对所有总线类型都支持，因此默认值为依赖于所使用的hypervisor。
             Disk bus type. Value can be ’ide’, ’scsi’, ’usb’, ’virtio’ or ’xen’.  The default is hypervisor dependent since not all hypervisors
             support all bus types.

​         perms
             磁盘权限，取值为’rw’ (读/写), ’ro’ (只读), or ’sh’ (共享 读/写)，默认值为’rw'
             Disk permissions. Value can be ’rw’ (Read/Write), ’ro’ (Readonly), or ’sh’ (Shared Read/Write). Default is ’rw’

​        size
             以GB为单位的新建存储大小。
             size (in GB) to use if creating new storage

​         sparse
             指定建立的存储是否跳过完全分配。取值为 ’true’ 或 ’false’。
             whether to skip fully allocating newly created storage. Value is ’true’ or ’false’. Default is ’true’ (do not fully allocate).
             --所谓的完全分配是指在建立文件后即分配给其规定的所有空间，所谓的sparse是指根据使用情况逐渐增加空间。
             初始时对客户机虚拟磁盘采用全分配策略（sparse=false）通常在客户机内部通过提供更快的安装时间获得平衡。因此在主机文件系统可能被填满时推荐使用
             此选项以确保高性能和避免I/O错误。
             The initial time taken to fully-allocate the guest virtual disk (spare=false) will be usually by balanced by faster install times inside
             the guest. Thus use of this option is recommended to ensure consistently high performance and to avoid I/O errors in the guest should the
             host filesystem fill up.

​         cache
             使用缓存模式，，主机页面缓存提供内存缓存。此选项取值包括’none’, ’writethrough’, 或 ’writeback’， ’writethrough’提供读缓存，’writeback’提供
             读和写缓存。
             The cache mode to be used. The host pagecache provides cache memory.  The cache value can be ’none’, ’writethrough’, or ’writeback’.
             ’writethrough’ provides read caching. ’writeback’ provides read and write caching.
         参加例子一节中的一些使用。这个选项屏蔽 "--file", "--file-size"和 "--nonsparse"选项。
         See the examples section for some uses. This option deprecates "--file", "--file-size", and "--nonsparse".

​       -f DISKFILE, --file=DISKFILE
         指向作为客户机虚拟磁盘后台存储的文件、磁盘分区或逻辑卷。这个选项与"--disk"选项指定一个即可。
         Path to the file, disk partition, or logical volume to use as the backing store for the guest’s virtual disk. This option is deprecated in
         favor of "--disk".

​       -s DISKSIZE, --file-size=DISKSIZE
         作为客户机虚拟磁盘的文件大小。这个选项不能与"--disk"选项同时使用。
         Size of the file to create for the guest virtual disk. This is deprecated in favor of "--disk".

​       --nonsparse
         指定在建立存储时机分配全部空间。这个选项不能与"--disk"选项同时使用。
         Fully allocate the storage when creating. This is deprecated in favort of "--disk"

​       --nodisks
         请求一个没有任何本地磁盘存储的虚拟机，典型应用在运行’Live CD’映像或安装到网络存储（iSCSI或NFS root）时。
         Request a virtual machine without any local disk storage, typically used for running ’Live CD’ images or installing to network storage (iSCSI
         or NFS root).
12、网络配置选项（Networking Configuration）

​       -w NETWORK, --network=NETWORK
         连接客户机到主机网络。"NETWORK"可采用一下任何一种值：
         Connect the guest to the host network. The value for "NETWORK" can take one of 3 formats:

​         bridge:BRIDGE
             连接到主机上名称为"BRIDGE"的桥接设备。如果主机具有静态网络配置和客户机需要与局域网进行全面的入站出站连接时使用此选项。在客户机使用在线迁移时也
             使用此选项。
             Connect to a bridge device in the host called "BRIDGE". Use this option if the host has static networking config & the guest requires full
             outbound and inbound connectivity  to/from the LAN. Also use this if live migration will be used with this guest.

​         network:NAME
             连接到主机上名称为"NAME"的虚拟网络。虚拟网络可以使用"virsh"命令行工具列出、建立和删除。未经修改的“libvirt”安装通常有一个名字为“default”的虚拟
             网络。在主机使用动态网络或无线网时使用虚拟网络。任何一个连接活动时客户机将通过地址转换将连接请求转到局域网。
             Connect to a virtual network in the host called "NAME". Virtual networks can be listed, created, deleted using the "virsh" command line
             tool. In an unmodified install of "libvirt" there is usually a virtual network with a name of "default". Use a virtual network if the host
             has dynamic networking (eg NetworkManager), or using wireless. The guest will be NATed to the LAN by whichever connection is active.

​         user
             使用SLIRP连接到局域网。只有没有特权的用户运行一个QEMU客户机时才使用本选项。这种方法从网络地址转换（NAT）提供了非常有限的方式。
             Connect to the LAN using SLIRP. Only use this if running a QEMU guest as an unprivileged user. This provides a very limited form of NAT.

​         如果忽略此选项，将在客户机中建立一个单网络接口卡（NIC），如果在主机中有一个与物理网卡绑定的桥接设备，将用此设备进行网络连接。做不到这一点，被成之为
         "default"的虚拟网络将被使用。这个选项可以被指定多次从而设置多个网卡。
         If this option is omitted a single NIC will be created in the guest. If there is a bridge device in the host with a physical interface
         enslaved, that will be used for connectivity. Failing that, the virtual network called "default" will be used. This option can be specified
         multiple times to setup more than one NIC.

​       -b BRIDGE, --bridge=BRIDGE
         指定连接客户机网卡的桥接设备。这个参数不能与 "--network"选项共同使用。指定
         Bridge device to connect the guest NIC to. This parameter is deprecated in favour of the "--network" parameter.

​       -m MAC, --mac=MAC
         指定客户机网卡物理地址；如果忽略这个参数或者指定了值"RANDOM"，将随机产生一个适当的地址。对应基于Xen的虚拟机，物理地址中最先的3对必须是’00:16:3e’，
         而QEMU或KVM虚拟机必须是’54:52:00’。
         Fixed MAC address for the guest; If this parameter is omitted, or the value "RANDOM" is specified a suitable address will be randomly
         generated. For Xen virtual machines it is required that the first 3 pairs in the MAC address be the sequence ’00:16:3e’, while for QEMU or KVM
         virtual machines it must be ’54:52:00’.

​       --nonetworks
         请求一个没有任何网卡的虚拟机。
         Request a virtual machine without any network interfaces.

13、图形化配置（Graphics Configuration）

​       如果没有指定图形选项，在DISPLAY环境变量已经设置的情况下，"virt-install" 将默认使用--vnc选项，否则将使用--nographics选项。
       If no graphics option is specified, "virt-install" will default to --vnc if the DISPLAY environment variable is set, otherwise --nographics is
       used.

​       --vnc
         在客户机中设置一个虚拟控制台并且将其导出为一个VNC服务。除非"--vncport" 参数也已提供，VNC服务将运行在5900或其之上第一个未用的端口号。实际的VNC显示
         可以使用"virsh"的"vncdisplay"命令（或者使用virt-viewer处理这个细节）。
         Setup a virtual console in the guest and export it as a VNC server in the host. Unless the "--vncport" parameter is also provided, the VNC
         server will run on the first free port number at 5900 or above. The actual VNC display allocated can be obtained using the "vncdisplay"
         command to "virsh" (or virt-viewer(1) can be used which handles this detail for the use).

​       --vncport=VNCPORT
         为客户机VNC控制台请求一个永久、静态的指定端口号。当其他客户机自动选择端口号时不鼓励使用此选项，因为可能产生冲突。
         Request a permanent, statically assigned port number for the guest VNC console. Use of this option is discouraged as other guests may
         automatically choose to run on this port causing a clash.

​       --sdl
         在客户机中设置一个虚拟控制台并且在主机中显示一个SDL窗口来呈现输出。如果SDL窗口被关闭，客户机将被无条件终止。
         Setup a virtual console in the guest and display an SDL window in the host to render the output. If the SDL window is closed the guest may be
         unconditionally terminated.

​       --nographics
         指定没有控制台被分配给客户机。全虚拟化客户机（Xen FV或者QEMU/KVM）将需要在客户机第一个串口有一个文本控制台配置（这可以通过--extra-args选项实现）。
         Xen PV将自动进行设置。命令’virsh console NAME’被用来连接串行设备。
         No graphical console will be allocated for the guest. Fully virtualized guests (Xen FV or QEmu/KVM) will need to have a text console
         configured on the first serial port in the guest (this can be done via the --extra-args option). Xen PV will set this up automatically. The
         command ’virsh console NAME’ can be used to connect to the serial device.

​       --noautoconsole
         使用本选项指定不自动试图连接到客户机控制台。默认行为是调用一个VNC客户端显示图形控制台，或者运行 "virsh" "console"命令显示文本控制台。
         Don’t automatically try to connect to the guest console. The default behaviour is to launch a VNC client to display the graphical console, or
         to run the "virsh" "console" command to display the text console. Use of this parameter will disable this behaviour.

​       -k KEYMAP, --keymap=KEYMAP
         请求将虚拟VNC控制台配置为非英语键盘布局。
         Request that the virtual VNC console be configured to run with a non-English keyboard layout.
14、Miscellaneous Options

​       -d, --debug
         在安装过程中，打印调试信息到终端。即使忽略此选项，调试信息也保存在当前用户home目录下的.virtinst/virt-install.log文件中。
         Print debugging information to the terminal when running the install process.  The debugging information is also stored in
         "$HOME/.virtinst/virt-install.log" even if this parameter is omitted.

​       --noreboot
         防止域（虚拟机）在安装完成后自动重启。
         Prevent the domain from automatically rebooting after the install has completed.

​       --wait=WAIT
         设置以分钟为单位的等待虚拟机完成其安装的时间。没有这个的选项，virt-install将等待控制台关闭（不必要指示客户机已经关闭），或者在--noautoconsole选项
         指定的情况下，简单地开始安装并退出。任何负值将使virt-install无限等待，0值将触发与--noauotoconsole选项相同的结果。如果超出时间限制，virt-install只是
         简单的退出，保留虚拟机在其当前状态。
         Amount of time to wait (in minutes) for a VM to complete its install.  Without this option, virt-install will wait for the console to close
         (not neccessarily indicating the guest has shutdown), or in the case of --noautoconsole, simply kick off the install and exit. Any negative
         value will make virt-install wait indefinitely, a value of 0 triggers the same results as noautoconsole. If the time limit is exceeded, virt-
         install simply exits, leaving the virtual machine in its current state.

​       --force
         防止交互式提示。如果预期的提示为是/否，总是回答是。对应任何其他提示，应用将退出。
         Prevent interactive prompts. If the intended prompt was a yes/no prompt, always say yes. For any other prompts, the application will exit.

​       --prompt
         提供交互模式，提示选择和输入建立虚拟机必要的信息。默认情况下提示功能是关闭的。
         Specifically enable prompting for required information. Default prompting is off (as of virtinst 0.400.0)
15、例子（EXAMPLES）

​       Install a KVM guest, creating a new storage file, virtual networking, booting from the host CDROM, using VNC server/viewer

​         # virt-install \
              --connect qemu:///system \
              --name demo \
              --ram 500 \
              --disk path=/var/lib/libvirt/images/demo.img,size=5 \
              --network network:default \
              --accelerate \
              --vnc \
              --cdrom /dev/cdrom

​       Install a Fedora 9 KVM guest, using LVM partition, virtual networking, booting from PXE, using VNC server/viewer

​         # virt-install \
              --connect qemu:///system \
              --name demo \
              --ram 500 \
              --disk path=/dev/HostVG/DemoVM \
              --network network:default \
              --accelerate \
              --vnc \
              --os-variant fedora9

​       Install a QEMU guest, with a real partition, for a different architecture using SDL graphics, using a remote kernel and initrd pair:

​         # virt-install \
              --connect qemu:///system \
              --name demo \
              --ram 500 \
              --disk path=/dev/hdc \
              --network bridge:eth1 \
              --arch ppc64 \
              --arch ppc64 \
              --sdl \
              --location <http://download.fedora.redhat.com/pub/fedora/linux/core/6/x86_64/os/>

​       Run a Live CD image under Xen fullyvirt, in diskless environment

​         # virt-install \
              --hvm \
              --name demo \
              --ram 500 \
              --nodisks \
              --livecd \
              --vnc \
              --cdrom /root/fedora7live.iso

​       Install a paravirtualized Xen guest, 500 MB of RAM, a 5 GB of disk, and Fedora Core 6 from a web server, in text-only mode, with old style
       --file options:

​         # virt-install \
              --paravirt \
              --name demo \
              --ram 500 \
              --file /var/lib/xen/images/demo.img \
              --file-size 6 \
              --nographics \
              --location <http://download.fedora.redhat.com/pub/fedora/linux/core/6/x86_64/os/>

​       Create a guest from an existing disk image ’mydisk.img’ using defaults for the rest of the options.

​         # virt-install \
              --name demo
              --ram 512
              --disk path=/home/user/VMs/mydisk.img
              --import

 

例子2：

virt-install --name=liangwietest --location=/root/CentOS-6.8-x86_64-bin-DVD1.iso  --ram 1024 --vcpus=2 --disk path=/root/10.40.1.211.qcow2,size=20,format=qcow2 --accelerate --vnc --vncport=5922 --vnclisten=0.0.0.0  --noautoconsole --network bridge=virbr0

 

virbr0：bridge的名字，可以通过brctl show查看