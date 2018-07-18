## centos7安装svn服务

1、安装

```shell
# yum install subversion -y
```

2、创建版本库

```shell
# svnadmin create /u01/svn/repo1
```

如果路径不存在，要先创建好目录。在另一个系统上他自己建创建了目录，我自己的上面却不行，可能是版本的问题吧。

创建好后可以看到原来空的/u01/svn/repo1目录下多出了这些文件：

```shell
# ls
conf  db  format  hooks  locks  README.txt
```

3、配置svn

在/u01/svn/repo1下

配置conf下的authz文件

在[groups]添加需要的组 格式：用户组名=用户名  多个用户同属一组用逗号分隔

我添加了admin组,这个组有2个成员admin和test。devel组有一个用户jmj：

```shell
[groups]
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe
admin = admin,test
devel = jmj
```

在[/foo/bar]下添加用户组的权限，这是组权限，下面还有添加版本库的用户权限。

```shell
# [/foo/bar]
# harry = rw
# &joe = r
# * =
[/]
@admin = rw	#admin组的用户具有可读写的权限

在下面还有这一行# [repository:/baz/fuz]的下面的内容
我不知道有什么用，只写上面的就ok了，我加了一些东西和没加没有区别，有没有都一样
```

devel组的我没有设置。

**注意：这里的权限设置，如果groups哪里没有定义就在这里设置权限，会报错。**

**svn提示:Invalid authz configuration**

我就在这卡了，没有test组却设了:@test = rw



配置conf/passwd文件

在[users]下添加用户名和密码

格式 user = userpassword

```shell
[users]
# harry = harryssecret
# sally = sallyssecret
admin = handdba	#用户admin的密码是handdba
test = handdba
```

在conf/svnserve.conf

取消以下项目的注释

```shell
anon-access=none  #未鉴定及匿名用户的权限
auth-access=write #认证过的用户访问版本库的权限
password-db=passwd #指定用户名口令文件，即当前目录下的passwd文件
authz-db=authz #指定权限配置文件名，即当前目录下的authz文件，我已经在上面设置了这两个文件了
realm=repo1	#指定版本库的认证域，即在登录时提示的认证域名称
```

修改/root/.subversion/servers

取消store-plaintext-passwords=yes的注释并修改=yes  #这句有什么用处暂时不知道

确认安装openssl与openssl-devel	#这两个包与设置svn是有什么关系，我还不知道。



---

启用svn，有2种方式，访问的时候也相应不同

方式1、  非全路径启动

svnserver -d -r /u01/svn

这里注意，我在上面设置本地仓库是/u01/svn/repo1,但是启动的时候不是全路径的启动，

访问时这样访问  svn://192.168.198.130/repo1   不要把全路径加上，启动时路径是/u01/svn，表示这个就是根了。

如果这样访问：svn://192.168.198.130/u01/svn/repo1则意味着svn服务器上的仓库是/u01/svn/u01/svn/repo1，实际上没有这个目录，会报错说这个目录不存在。

方式2、  全路径启动

```shell
# svnserve -d -r /u01/svn/repo1		#-d 表示deamon，-r表示root的意思
```

全路径启动时，访问svn就不能在ip后面加其他东西了。

是这样的访问：svn://192.168.198.130/

不能加如：svn://192.168.198.130/repo1  这样的访问了，会报没有这个目录。因为确实没有这个目录，这个访问方式代表的是svn服务启动的目录+repo1目录的路径

(/u01/svn/repo1+repo1)，在我这里代表的就是/u01/svn/repo1/repo1。



4、两种启动方式

全路径启动和非全路径启动的差别是什么或者优点是什么呢

例子如下：

我在/u01/svn/下建了两个版本库

/u01/svn/repo1和/u01/svn/repo2

以非全路径启动

```shell
# svnserve -d -r /u01/svn/
```

我访问的时候可以：

```
svn://192.168.198.130/repo1		#访问repo1库
svn://192.168.198.130/repo2		#访问repo2库
```

我可以很容易的切换去多个库，不需要再服务器上去启动。

---

如果是全路径启动

```shell
# svnserve -d -r /u01/svn/repo1
```

我们要去访问repo1这个库，

```shell
svn://192.168.198.130/	#访问repo1库
```

少输入了repo1

但是我们要去访问repo2库时，就必须到svn服务器上关闭了repo1再开启repo2，然后才能去访问repo2库，其他的也类似。

结论：非全路径启动可以方便的切换到其他库，全路径如果要切换到其他库就需要重新启动svn服务，所以非全路径的启动更方便一些，如果只有一个库的话就无所谓了。

5、其他

1、我以为上传的文件会在仓库里面原件保留，我弄个文件commit上去没看见，我想他做了某种处理吧。

2、我在仓库repo2下又建了一个目录test，然后启动，

```shell
# svnserve -d -r /u01/svn/repo2/test
```

但是发现无法访问，应该是不可以吧，只能在仓库目录及之前的目录启动。

3、某些教程后面会有一个import语句，这个和库的配置是否成功没有什么关系，和配好了在文件夹了新建内容commit是一样的效果。