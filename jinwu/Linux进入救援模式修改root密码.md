## Linux进入救援模式修改root密码

1、开机进入引导界面，在内核选择界面按字母 **e**,进入内核模式编辑。

![选择内核进入编辑界面](C:\Users\yueshang\AppData\Local\Temp\1530246980561.png)

2、进入Linux内核模式后，找到Linux16开头那一行，在最后面加上rd.break,再按住Ctrl+x，启动到救援模式。

![加上rd.break](C:\Users\yueshang\AppData\Local\Temp\1530247215123.png)

3、执行如下代码

```shell
# mount -o remount rw /sysroot	#挂载根目录
# chroot /sysroot	#切换到根目录
# LANG=C
# passwd
# touch /.autorelable #让SELinux生效，如果不执行，修改的密码不生效
# exit	#退出sh-4.2# 模式，才能使用reboot命令
# reboot
```

![修改root密码](C:\Users\yueshang\AppData\Local\Temp\1530247611021.png)

