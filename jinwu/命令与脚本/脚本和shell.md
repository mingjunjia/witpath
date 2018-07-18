## 脚本和shell

每个进程都至少有三个信道，标准输入，标准输出和标准错误。内核给每个进程都设置了这三个信道，所以进程本身不必知道这三个信道到哪里。

大多数命令都接受stdin来输入，并把输出输出到stdout，把错误输出到stderr，有了这样的约定，我们就可以把命令像积木一样串起来。

为了把stdout和stderr都重定向到同一个地方可以用>&，仅仅重定向stderr则用2>。

**shell将<、>、>>解释成指令，用于把一条命令的输入或输出重定向到一个文件，和文件关联起来。**

**要把一条命令的stdout连接到另一条命令的stdin上可以使用|符号，叫做管道。**



1、变量的定义和引用。

变量在定义的时候没有标记，但是在访问的时候需要加$符号。

```shell
$ etcdir='/etc'
$ $etcdir
/etc
```

**不要在等会两边留有空格，否则shell会误认为是条命令去执行。**

引用变量时可用花括号把变量括起来，便于阅读和区分( **${varible}** )，

单引号和双引号都表示字符串，区别是双引号括起来的变量可用进行替换。

2、常见的过滤命令

大多数过滤命令都接受在命令行上提供一个或者多个文件名作为输入。只有在一个文件名都未指定的时候，才从自己的标准输入读取数据。

2.1、cut：把行分成域

从他的输入行中选出若干部分，再打印出来。常用于提取被限定的若干域。

2.2、sort：将行排序

对输入行进行排序。

```shell
#常用选项
-b		| 忽略开头空白
-f		| 排序不区分大小写
-k		| 指定构成排序关键字的列
-n		| 按数值排序
-r		| 颠倒排序顺序，即逆序
-t		| 设定分隔符
-u		| 只输出唯一记录，重复记录只输出一次
```

2.3、uniq：重复行只打印一次

在思想上和sort -u类似，但有些sort不能模拟的选项，uniq的输入必须先排好序，因此通常放在sort之后运行。-c 累计每行出现的次数，-d 只显示重复行，-u只显示不重复行。

2.4、wc：统计行数，字数和字符数

(word count)，如果不带任何参数，会显示全部三种结果。

常用选项，-l，-w，-c。让他只输出一个数。

2.5、tee：把输入复制到2个地方

把自己的标准输入发送到标准输出，又发送到命令行上的一个文件里。

把tee作为一条执行时间很长的命令管道的最后一条命令，这是一种常见用法既输出了结果，看到执行情况，又发送到文件保存了起来。

2.6、head和tail：读取文件的开头和结尾

默认显示10行内容。可以指定要看多少行。

tail有个不错的选项-f，命令在按要求打印完之后，不会立即退出，而是等待有新行出现打印新行，**对于监视日志文件来说很有用。**可能会缓冲输出，按规律的时间间隔追加。

2.7、grep：搜索文本

搜索给他输入的文本，打印出匹配某种模式的行，正则表达式来匹配。

-c 打印匹配行数，-i 匹配时忽略大小写，-v 打印不匹配行。-l 让grep只打印匹配文件的名字，而不是匹配每一行。

```shell
$ grep -l mdadm /var/log/*
/var/log/auth.log
/var/log/syslog.0
#表明mdadm日志出现在两个不同的日志文件里。
```

**用fc命令捕获工作结果，整理后保存下来，写脚本时，要预先看到正确的结果，才做任何可能有破坏性的操作。**

3、输入和输出

echo虽然原始，但易于使用。要想对输出做出更多的控制，就需要使用printf。

read命令用于提示输入。

给一个脚本的命令行参数可以成为变量，这些变量的名字是数字，$1是第一个命令行参数，$2是第二个...，$0是调用改脚本所产生的名字。$#是提供给命令行参数的个数。$*变量保存有全部参数。这两个($#和$* *)都不算上$0。

如果脚本不带参数或者参数不正确，应该打印一段用法说明。

在bash里函数和命令很类似。用户可以在~/.bash_profile文件里定义自己的函数，然后在命令行上使用它们，就好像它们是命令一样

3.1、变量作用域

脚本里的变量是全局变量，但是函数可以用local声明语句，创建自己的局部变量。

local实际上是个命令，从执行的地方开始，创建局部变量。



3.2、流程控制

if	; then 

...

fi



if []，[]比较奇特，实际上是调用test的一种快捷方式，并不是if的句法要求。

```shell
字符串		数值		
x = y		x -eq y		x等于y
x != y		x -nq y		x不等于y
x < y		x -lt y		x小于y
x <= y		x -le y		x小于等于y
x > y		x -gt y		xd大于y
x >= y		x -ge y		x大于等于y
-n x					x不为空
-z x					x为空

# 对文件属性的取值是其出彩之处
-d file				file文件存在且是目录
-e file				file存在
-f file				file存在且是普通文件
-r file				用户有对file的读权限
-s file				file存在且不为空
-w file				用户有file文件的写权限
file1 -nt file2		file1比file2新
file1 -ot file2		file1比file2旧
```



虽然elif的形式能用，但是为了清除起见，用case是更好的方法。

case ... in

​	0) ... ;;

​	1) ... ;;

esac

**循环**

for ... 结构

for ... in ...  ; do

...

done

while 结构

while ... ; do

...

done

**python脚本示例**

```python
#!/usr/bin/python
import sys
import os

def show_usage(message, code = 1):
    print(message)
    print("%s;source_dir dest_dir"% sys.argv[0])
    sys.exit(code)
    
if (len(sys.argc) != 3):
    show_usage("2 arguments requied;you supplied %d" % (len(argc)-1))
elif not os.path.isdir(sys.argv[1]):
    show_usage("invalid source directory")
elif not os.path.isdir(sys.argv[2]):
    show_usage("invalid destination directory")
    
source, dest = sys.argv[1:3]
print("source directory is", source)
print("destination directory is", dest)
```



