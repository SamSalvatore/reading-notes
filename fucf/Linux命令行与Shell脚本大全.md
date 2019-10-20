[TOC]
# Linux命令行与Shell脚本大全

## 第一章 初识Linux Shell

### 1.1 什么是Linux
Linux可分为以下四个部分
* Linux内核
* GNU工具
* 图像化桌面环境
* 应用软件

![-w604](media/15701526271100.jpg)

#### 深入探究Linux内核
Linux系统的核心是内核。内核控制着计算机系统上的所有硬件和软件，在必要时分配硬件，并根据需要执行软件。

内核主要负责以下四种功能：
* 系统内存管理
* 软件程序管理
* 硬件设备管理
* 文件系统管理

![-w664](media/15701531338152.jpg)


- 系统内存管理

	- 管理服务器上的可用物理内存

	- 创建和管理虚拟内存

		- 通过硬盘上的存储空间实现
		  这块区域称为交换空间

		- 内存存储单元按组划分为很多块（Page页面）。内核维护一个内存页面表

- 软件程序管理

	- 内核创建了第一个进程（init进程）来启动系统上的所有其他进程

	- 一些Linux发型版使用一个表来管理在系统开机时
	  要自动启动的进程

		- /etc/init.d

		- /etc/rxX.d

		- /etc/inittab

	- 5个启动运行级

		- 1:只启动基本的系统进程以及一个控制台终端进程。
		  通常用于紧急文件系统维护

		- 3：标准的启动运行级

		- 5：启动图形化的X Window系统

- 硬件设备管理

	- 任何Linux系统需要与之通信的设备，都需要在内核代码中加入其驱动程序代码

	- 插入设备驱动代码

		- 编辑进内核的设备驱动代码

		- 可插入内核的设备驱动模块

	- 内核模块

		- 允许将驱动代码插入到运行中的内核而无需重新编译内核

	- 设备文件

		- 字符型设备文件

			- 每次只能处理一个字符的设备。如终端

		- 块设备文件

			- 每次处理大块数据的设备，如硬盘

		- 网络设备文件

			- 采用数据包发送和接收数据的设备，如网卡

	- 节点

		- 与设备的所有通信都通过设备节点完成。

		- 每个节点都有唯一的数值对 供Linux内核标识它

- 文件系统管理

	- Linux内核支持通过不同类型的文件系统从硬盘中读取数据

#### GNU工具

- 执行一些标准功能，比如控制文件和程序

	- 核心GNU工具

		- 处理文件

		- 操作文本

		- 管理进程

	- Shell

		- 命令提示符

		- 输入文本命令，解释命令，然后在内核中执行

#### 图形化桌面环境

#### 应用软件

### 1.2 Linux发行版

#### 完整的核心Linux发行版

- 内核、一个或多个图形化桌面环境以及预编译好的几乎所有能见到的Linux应用

- Red Hat、Debian等

#### 特定用途的发行版

- 仅包含主流发行版中一小部分用于某种特定用途的应用程序

- Ubuntu、Centos等

#### LiveCD测试发行版

- 无需将Linux安装到硬盘就能体验Linux 的发行版。

## 第2章 走进Shell

## 第4章 更多的bash shell命令

### 4.1 PS 命令
进程（process）：当程序运行在系统上时，我们称之为进程。

`ps`:显示运行在当前控制台下的属于当前用户的进程

```
[root@iZ2zeb7nht7zwkd4k8tdquZ ~]# ps
PID TTY          TIME CMD
10729 pts/0    00:00:00 bash
10744 pts/0    00:00:00 ps
```
其中
* `PID`:程序的进程ID
* `TTY`:运行在哪个终端
* `TIME`:进程已用的CPU时间


`ps -ef`: 查看系统上运行的所有进程. 

```shell
ps -ef | more
UID   PID  PPID   C STIME   TTY           TIME CMD
0     1     0   0 四10上午 ??         3:19.35 /sbin/launchd
0    41     1   0 四10上午 ??         0:09.53 /usr/sbin/syslogd
0    42     1   0 四10上午 ??         0:04.12 /usr/libexec/UserEventAgent (System)
```
`-e`参数制定显示所有运行在系统上的进程
`-f`参数则扩展了输出

其中
* UID：启动这些进程的用户
* PID:进程的进程ID
* PPID:父进程的进程号（如果该进程是由另一个进程启动的）
* C:进程生命周期中的CPU利用率
* STIME：进程启动时的系统时间
* TTY:进程启动时的终端设备
* TIME:运行进程需要的累计CPU时间
* CMD:启动的程序名称😂

### 4.2 top命令
`ps`命令显示某个特定时间点的信息，`top`命令则**实时显示**进程信息

Load Avg: 最近一分钟、五分钟和最近15分钟的平均负载。值越大说明系统的负载越高。

### 4.3 结束进程
在`Linux`中,进程之间通过信号来通信，进程的信号就是预定好的一个消息，进程能识别它并决定忽略还是做出反应。

| 信号 | 名称  | 描述 |
|----|-----|----|
| 1  | HUP | 挂起 |
|  2  | INT    |  中断  |
|  3  |   QUIT  |   结束运行 |
| 9   |  KILL   |  无条件终止  |
|   11 |  SEVG   |  段错误  |
|  15  |  TERM   |  尽可能终止  |
|  17  |   STOP  |   无条件停止运行，但不终止 |
|   18 |  TSIP   |  停止或暂停，但继续在后台运行  |
| 19   |  CONT   |  在STOP或TSTP之后恢复执行  |

在Linux上有两个命令可以向运行中的进程发出进程信号
* `kill`命令: 通过进程ID（PID）给进程发信号。默认发送的为`TERM`信号。

### 4.4 df 命令
`df`命令: 查看设备上还有多少磁盘空间
`df -h`命令: 将输出中的磁盘空间按照用户易读的形式显示
`df`: `Disk free` 空余硬盘

```shell
➜  ~ df -h
Filesystem      Size   Used  Avail Capacity iused               ifree %iused  Mounted on
/dev/disk1s1   233Gi  125Gi  104Gi    55% 1220276 9223372036853555531    0%   /
devfs          188Ki  188Ki    0Bi   100%     650                   0  100%   /dev
/dev/disk1s4   233Gi  3.0Gi  104Gi     3%       3 9223372036854775804    0%   /private/var/vm
map -hosts       0Bi    0Bi    0Bi   100%       0                   0  100%   /net
map auto_home    0Bi    0Bi    0Bi   100%       0                   0  100%   /home
```

### 4.5 du 命令
`du`命令: 显示当期目录下的所有文件、目录和子目录的磁盘使用情况。

`du` 全称 `Disk usauge` 硬盘使用率

`du -h`: 按用户易读的格式输出大小

```shell
➜  certificate du -h *
484K	30125021298231.pdf
484K	30125046178411.pdf
484K	30125049514821.pdf
484K	30125049621531.pdf
484K	30125061446231.pdf
484K	30125230319511.pdf
484K	30125261735531.pdf
484K	30125277387411.pdf
484K	30125288120821.pdf
484K	30125458397431.pdf
484K	30125464708131.pdf
484K	30125624162631.pdf
484K	30125656095431.pdf
484K	30125677615631.pdf
484K	30125684626631.pdf
484K	30125820971531.pdf
484K	30125831929621.pdf
484K	30125837380921.pdf
484K	30125882823921.pdf
484K	30125886599631.pdf
➜  certificate du -h
9.5M
```

### 4.6 sort 命令
`sort`: sort命令是对数据进行排序的。 默认情况下，sort命令按照会话指定的默认语言的排序规则对文本文件中的数据行排序。

```shell
➜  Desktop cat test.txt
aa
bb
dd
cc
➜  Desktop sort test.txt
aa
bb
cc
dd
```

常用参数：
* `-n`: 按字符串数值来排序
* `-r`: 反序排序
* `-t`: 指定一个用来分键位置的字符,默认是tab分隔符
* `-k[n,m]`:按照指定的字段范围排序。从第n个字段开始，到第m个字（默认到行尾）
* `-f`:忽略大小写
* `-b`:忽略每行前面的空白部分
* `-u`:删除重复行。就是uniq命令

### 4.7 grep 命令
命令格式:
`grep [options] pattern [file]`

命令作用为在输入或指定的文件中查找包含匹配指定模式的字符的行。grep的输出就是包含了匹配模式的行。

**在指定文本中寻找符合条件的文本行 grep**

```shell
# 输出仅会包含指定信息的文本行
grep 'match_pattern' file_name
```

**输出文本行和行数 grep -n**

```shell
# 会在文本行前面加上行数
grep -n 'match_pattern' file_name
```

仅输出文本行行数 grep -c

```shell
仅输出文本行行数的和
grep -c 'match_pattern' file_name
```

**输出文本行及其上下相关文本 grep -A -B -C**
```shell
# 之后5行
grep -A 5 'match_pattern' file_name
  
# 之前5行
grep -B 5 'match_pattern' file_name

# 前5行&后5行
grep -C 5 'match_pattern' file_name
```

**标记匹配的文本行 grep --color=auto**

```shell
# 前5行后5行并标记符合条件的文本
grep -n -C 5 'match_pattern' file_name  --color=auto 
```
**忽略大小写 grep -i**
```shell
grep -r 'aaa'  -i
```

**以递归的方式查找符合条件的文件 grep -r**
```shell
# path为空则默认当前目录
grep -r 'match_pattern'  path  

# 查找当前文件夹及子文件夹下所有符合条件的文本及文本行
grep -r 'aaa' 
```

**排除指定文本 grep -v**
```shell
grep -v 'match_pattern' file_name
```

## 第五章 理解shell

### 5.1 shell的种类
shell的基本功能
* 命令解释器
* 程序设计语言

shell常用的种类：
* ash 资源少，用起来不方便
* bash 默认shell
* ksh
* csh 即tcsh
* zch linux最强大shell之一




### 5.2 Shell 的外建命令
外部命令
* 存在于bash shell 之外的程序
* 当外部命令执行时，会创建出一个包含全新环境的子进程
* 父进程和子进程之间可以通过信号来进行沟通

### 5.3 Shell 的内建命令
* 内建命令不需要子进程来执行，他们已经和`shell`编译成了一体
* 可以通过`type`命令来了解某个命令是否是内建的
* 内建命令的执行速度更快、效率更高


### 5.4 history
>history命令可以查看执行过的历史命令

命令格式:

```shell
[root@shell ~]# history [-n]
[root@shell ~]# history [-c]
[root@shell ~]# history [-raw] historyfiles
```

* `n`：数字，列出最近执行的n个指令
* `-n`：数字，列出最近执行的n个指令 
* 注:网上有资料说显示最近n条命令用的命令为`history n`, 我在自己电脑上试了下为`history -n`

历史指令可以结合以下指令配合：
* `!number`：执行第number个指令
* `!command`：由最近的指令向前搜寻指令串开头为command的指令，并执行
* `!!`：执行上一个指令

### 5.5 alias
>Linux alias 命令用来设置指令的别名，对一些较长的命令进行简化。使用 alias 时，必须使用单引号将原来的命令包含，防止特殊字符导致错误。

设置别名

```shell
# alias 别名='原命令 -选项/参数'
alias ll='ls -lt'
```

查看已经设置的别名列表

```shell
alias -p
```

删除别名
```shell
# unalias 别名
unalias ll
```


>alias 命令只作用于当次登入的操作。如果想每次登入都能使用这些命令的别名，则可以把相应的 alias 命令存放在 ~/.bashrc 文件中。
打开~/.bashrc 文件，输入要设置的 alias 命令，保存，然后运行
source ~/.bashrc


## 第六章 使用Linux环境变量

### 6.1 什么是环境变量
