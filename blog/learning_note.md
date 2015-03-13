Shell 学习笔记：
========================================

1. 运行的时候，Bash 创建一个自身的拷贝因此一个新的进程就被创建。子进程和父进程拥有一样的环境，唯一个区别只有进程号。
这个过程被称作内核空间创建进程（Forking）。在内核空间创建进程后，子进程的地址空间被新进程数据覆盖。
此步骤通过exec 系统调用完成

2. 建立在子shell 中的变量，函数和别名等只有子shell 使用。当那个shell 退出，父shell 重新得到控制的时候，所有的东西都
被清空，脚本对shell 状态所作的改变耶全部清除。

3. Bash 的内建命令source 和sh 的.命令是相同的。这里脚本不需要可执行权限。命令在当前shell
力执行，所以任何对环境的改变，将在脚本结束时同样起作用。(source命令会保留对环境的修改，一直保留，不像之前子shell会的
修改会在其退出时失效)

4. Bash 中双引号和单引号的意义不同。如下面的例子，单引号会直接解析为字符串，而双引号则会引用该变量的值
 t = 12
 echo '$t' ## 输出$t，认为它是一个字符串
 echo "$t" ## 输出12，因为双引号内，会直接引用该变量的值
 另外shell中的比较运算符，-eq 和 == 用于不同的对象，前者只能用于整数的比较，而后者则用于字符串的比较。

5. 使用posix标准的shell：
	+ 增加设置： set -o posix
	+ 使用选项启动：bash --posix

6. 交互式和非交互式shell的区别：


7. 未赋值是变量默认只是空字符串，变量只能通过unset来销毁
8. 日志，电源管理，命令和邮件等，都是由初始化脚本执行来启动相关的服务的。该脚本在/etc/rc.d/init.d或者/etc/init.d下







***

Shell 学习笔记
============================

1. 字符串中双引号和单引号的区别？
双引号可以引用特殊的转移字符，比如$等等，而单引号则表示一个完全的字符串引用，禁用来这种符号扩展特性。单引号会直接解析为字符串，而双引号则会引用该变量的值

	t = 12

	echo '$t' ## 输出$t，认为它是一个字符串

	echo "$t" ## 输出12，因为双引号内，会直接引用该变量的值









***

最牛B的Linux Shell命令
==================================

###引言


Shell作为Unix系操作系统当中最有魅力且不可或缺的组件，经过数十载的洗礼不仅没有被淘汰，而且愈加变得成熟稳健，究其原因，大概因为它是个非常稳固的粘合剂，能够把大量功能强大的组件任意配搭，总能很好很快地完成用户的任务。

本文的一些命令很可能看起来是“雕虫小技”，我们只好仰慕一下Shell大牛了，但是有些细节我会稍加发掘加以说明，遇到有趣的地方希望能博您一笑了。

###1.**以sudo运行上条命令**

　　$ sudo !!

　　大家应该都知sudo，不解释。但通常出现的情况是，敲完命令执行后报错才发现忘了sudo。这时候，新手用户就会：按上箭头，按左箭头，盯着光标回到开始处，输入sudo，回车；高手用户就蛋定多了，按Ctrl-p，按Ctrl-a，输入sudo，回车。

　　这里介绍这个是天外飞仙级别的，对，就直接sudo !!。

　　当然这几种解决方式效果是完全一样的，只是款不一样，嗯，不解释。

　　两个感叹号其实是bash的一个特性，称为事件引用符（event designators）。!!其实相当于!-1，引用前一条命令，当然也可以!-2，!-50。默认情况下bash会在~/.bash_history文件内记录用户执行的最近500条命令，history命令可以显示这些命令。

　　关于事件引用符的更多用法可以深入阅读The Definitive Guide to Bash Command Line History。


###**2.以HTTP方式共享当前文件夹的文件**


　　$ python -m SimpleHTTPServer

　　这命令启动了Python的SimpleHTTPServer模块，考虑到Python在绝大多数的Linux发行版当中都默认安装，所以这个命令很可能是最简单的跨平台传文件的方法。

　　命令执行后将在本机8000端口开放HTTP服务，在其他能访问本机的机器的浏览器打开ttp://ip:8000即打开一个目录列表，点击即可下载。

###**3.在以普通用户打开的vim当中保存一个root用户文件**

　　:w !sudo tee %

　　这题目读起来纠结，其实是很常见的，常常忘记了sudo就直接用vim编辑/etc内的文件，（不过也不一定，vim发现保存的文件无法保存时候会提示）等编辑好了，保存时候才发现没权限。曲线方法是先保存个临时文件，退出后再sudo cp回去。不过实际上在vim里面可以直接完成这个过程的，命令就是如此。

　　查阅vim的文档（输入:help :w），会提到命令:w!{cmd}，让vim执行一个外部命令{cmd}，然后把当前缓冲区的内容从stdin传入。

　　tee是一个把stdin保存到文件的小工具。

　　而%，是vim当中一个只读寄存器的名字，总保存着当前编辑文件的文件路径。

　　所以执行这个命令，就相当于从vim外部修改了当前编辑的文件，好完工。

###**4.切换回上一个目录**

　　$ cd -

　　应该不少人都知道这个，横杆-代表上一个目录的路径。

　　实际上cd -就是cd $OLDPWD的简写，bash的固定变量$OLDPWD总保存着之前一个目录的路径。

　　相对地，$PWD总保存着当前目录的路径。这些变量在编写shell脚本时候相当有用。

###**5.替换上一条命令中的一个短语**

　　$ ^foo^bar^

　　又是另外一个事件引用符（event designator），可以把上一条命令当中的foo替换成bar。

　　在需要重复运行调试一道长长的命令，需要测试某个参数时候，用这个命令会比较实用；但多数人会首先选择按上箭头提出上道命令，再移动光标去修改某参数，这样更直观，但效率上就不够使用引用符高，而且在脚本中用这个方法可以简化很多。

　　这道命令的原始样式应该是这样的：

　　!!:s/foo/bar/

　　本文一开始介绍过!!，后面的一段大家应该很熟悉，vim、sed的替换操作都是这样的语法。

　　关于事件引用符的更多用法可以深入阅读The Definitive Guide to Bash Command Line History

##总结

1. sudo执行上一条命令： sudo !!（非常有用）
2. 以sudo来保存当前文件　　:w !sudo tee %
3. 进入到上一个目录中，cd - （有时候两个目录切换）
4. 替换上条命令中的某个字符 ^foo^bar^(替换foo为bar)
5. 利用python建立http服务器，python -m SimpleHTTPServer


***











