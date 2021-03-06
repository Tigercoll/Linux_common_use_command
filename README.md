---
title: Linux常用命令
categories: Linux
tag: ['Linux']
---

记录常用命令

## 查看当前使用的shell

```bash
echo $SHELL
```



## 查看本机已安装的shell

```bash
cat /etc/shells
```



## pstree

将所有行程以树状图显示，树状图将会以 pid (如果有指定) 或是以 init 这个基本行程为根 (root)，如果有指定使用者 id，则树状图会只显示该使用者所拥有的行程。



## netstat

netstat 命令用于显示网络状态。

常用用于显示端口是否被占用

```bash
netstat -tunlp | grep 端口号

# -t (tcp) 仅显示tcp相关选项
# -u (udp)仅显示udp相关选项
# -n 拒绝显示别名，能显示数字的全部转化为数字
# -l 仅列出在Listen(监听)的服务状态
# -p 显示建立相关链接的程序名
```



## tail

tail 命令可用于查看文件的内容，有一个常用的参数 **-f** 常用于查阅正在改变的日志文件。

- -n<行数> 显示文件的尾部 n 行内容
- -f 循环读取

```bash
tail -f filename
# 但是如果用vim修改后无法获取变化
# 通过inode追踪文件，如果文件的inode发生了变化，将会使tail -f失效。
# 使用tail -F替代tail -f
# 还可以接 grep 过滤
```



## AWK

处理文本文件的语言，是一个强大的文本分析工具。

```bash
awk '{[pattern] action}' {filenames}   # 行匹配语句 awk '' 只能用单引号
```

```bash
cat 1.txt 
1 2 3 4
a b c d 
q w e r

#############################################
# 取出第一列
awk '{print $1}' 1.txt
1
a
q

# 多列的话用,号隔开
awk '{print $1,$3}' 1.txt
1 3
a c
q e
```

```bash
awk -F  #-F相当于内置变量FS, 指定分割字符
```

```bash
cat 1.txt 
1, 2, 3, 4
a, b, c, d 
q, w, e, r

# 默认用空格隔开
awk  '{print $1,$3}' 1.txt 
1, 3,
a, c,
q, e,

# 指定用,号分割
awk -F, '{print $1,$3}' 1.txt
1  3
a  c
q  e
```

这些知识基本使用,如需更高级用法请自行google



## 查看系统在线用户

```bash
w
 11:32:00 up 11 min,  3 users,  load average: 1.45, 1.00, 0.59
USER     TTY      来自           LOGIN@   IDLE   JCPU   PCPU WHAT
tiger    :0       :0               11:22   ?xdm?   5:25   0.01s /usr/lib/gdm3/g
tiger    tty5     -                11:31    7.00s  0.21s  0.19s -zsh
tiger    tty6     -                11:31   16.00s  0.18s  0.15s -zsh

# 或者
who   
tiger    :0           2021-05-17 11:22 (:0)
tiger    tty5         2021-05-17 11:31
tiger    tty6         2021-05-17 11:31
```



## 只查看当前终端的可以使用如下两个命令

```bash
# tty 和 who am i
tty 
/dev/pts/0

who am i
tiger    pts/1        2021-05-17 11:41 (127.0.0.1)
```



## pkill掉自己不适用的终端

```bash
pkill -9 -t pts/1
```



## 文件描述符

```bash
# 查看本机可以打开的文件描述符数量
ulimit -n
1024
# 修改文件描述符数量
ulimit -n 65536

# 单台服务器器最⼤大连接数
# ⽂文件描述符: 限制⽂文件打开数量量 (⼀一切皆⽂文件)
# 内核限制: net.core.somaxconn
# 内存限制
# 修改⽂文件描述符: ulimit -n 65535
```



## 查询可用内存

```bash
# -m 以兆为单位，-g 以GB 为单位
free -g
              总计         已用        空闲      共享    缓冲/缓存    可用
内存：          15           9           0      0       4           4

```



## Linux查看物理CPU个数、核数、逻辑CPU个数

```bash
# 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
# 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

# 查看物理CPU个数
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

# 查看每个物理CPU中core的个数(即核数)
cat /proc/cpuinfo| grep "cpu cores"| uniq

# 查看逻辑CPU的个数
cat /proc/cpuinfo| grep "processor"| wc -l
```



## rsync 是一个常用的 Linux 应用程序，用于文件同步。

```bash
rsync 参数 源文件 目标文件
# 一般参数为avzhP
```

参数

```bash
-v, --verbose 详细模式输出
-q, --quiet 精简输出模式
-c, --checksum 打开校验开关，强制对文件传输进行校验
-a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于 -rlptgoD
-r, --recursive 对子目录以递归模式处理
-R, --relative 使用相对路径信息
-b, --backup 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用 --suffix 选项来指定不同的备份文件前缀。
--backup-dir 将备份文件（如~filename) 存放在在目录下。
-suffix=SUFFIX 定义备份文件前缀
-u, --update 仅仅进行更新，也就是跳过所有已经存在于 DST，并且文件时间晚于要备份的文件。（不覆盖更新的文件）
-l, --links 保留软链结
-L, --copy-links 想对待常规文件一样处理软链结
--copy-unsafe-links 仅仅拷贝指向 SRC 路径目录树以外的链结
--safe-links 忽略指向 SRC 路径目录树以外的链结
-H, --hard-links 保留硬链结
-p, --perms 保持文件权限
-o, --owner 保持文件属主信息
-g, --group 保持文件属组信息
-D, --devices 保持设备文件信息
-t, --times 保持文件时间信息
-S, --sparse 对稀疏文件进行特殊处理以节省 DST 的空间
-n, --dry-run 现实哪些文件将被传输
-W, --whole-file 拷贝文件，不进行增量检测
-x, --one-file-system 不要跨越文件系统边界
-B, --block-size=SIZE 检验算法使用的块尺寸，默认是 700 字节
-e, --rsh=COMMAND 指定使用 rsh、ssh 方式进行数据同步
--rsync-path=PATH 指定远程服务器上的 rsync 命令所在路径信息
-C, --cvs-exclude 使用和 CVS 一样的方法自动忽略文件，用来排除那些不希望传输的文件
--existing 仅仅更新那些已经存在于 DST 的文件，而不备份那些新创建的文件
--delete 删除那些 DST 中 SRC 没有的文件
--delete-excluded 同样删除接收端那些被该选项指定排除的文件
--delete-after 传输结束以后再删除
--ignore-errors 及时出现 IO 错误也进行删除
--max-delete=NUM 最多删除 NUM 个文件
--partial 保留那些因故没有完全传输的文件，以是加快随后的再次传输
--force 强制删除目录，即使不为空
--numeric-ids 不将数字的用户和组 ID 匹配为用户名和组名
--timeout=TIME IP 超时时间，单位为秒
-I, --ignore-times 不跳过那些有同样的时间和长度的文件
--size-only 当决定是否要备份文件时，仅仅察看文件大小而不考虑文件时间
--modify-window=NUM 决定文件是否时间相同时使用的时间戳窗口，默认为 0
-T --temp-dir=DIR 在 DIR 中创建临时文件
--compare-dest=DIR 同样比较 DIR 中的文件来决定是否需要备份
-P 等同于 --partial
--progress 显示备份过程
-z, --compress 对备份的文件在传输时进行压缩处理
--exclude=PATTERN 指定排除不需要传输的文件模式
--include=PATTERN 指定不排除而需要传输的文件模式
--exclude-from=FILE 排除 FILE 中指定模式的文件
--include-from=FILE 不排除 FILE 指定模式匹配的文件
--version 打印版本信息
```

## 解压gz结尾压缩包

```bash
tar -xzvf test.tar.gz 
# 参数
-x或--extract或--get 从备份文件中还原文件。
-z或--gzip或--ungzip 通过gzip指令处理备份文件。
-v或--verbose 显示指令执行过程。
-f<备份文件>或--file=<备份文件> 指定备份文件。
```

## watch

```bash
# 每隔多少秒查看该命令结果
watch -n 1 命令
```

## 不间断重启

```bash
# 生产环境中为了不影响业务
kill -HUB pid
```

## du

du（是 disk usage 的简称）用来显示目录或文件的大小，查找文件和目录的磁盘使用情况的命令。du 命令在与各种选项一起使用时能以多种格式提供结果。

语法：`du [-abcDhHklmsSx] [-L <符号连接>][-X <文件>][--block-size][--exclude=<目录或文件>] [--max-depth=<目录层数>][--help][--version][目录或文件]`

参数:

```bash
-a或-all 显示目录中个别文件的大小。
-b或-bytes 显示目录或文件大小时，以byte为单位。 
-c或--total 除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和。 
-D或--dereference-args 显示指定符号连接的源文件大小。 
-h或--human-readable 以K，M，G为单位，提高信息的可读性。
-H或--si 与-h参数相同，但是K，M，G是以1000为换算单位。 
-k或--kilobytes 以1024 bytes为单位。 
-l或--count-links 重复计算硬件连接的文件。 
-L<符号连接>或--dereference<符号连接> 显示选项中所指定符号连接的源文件大小。 
-m或--megabytes 以1MB为单位。 
-s或--summarize 仅显示总计。 
-S或--separate-dirs 显示个别目录的大小时，并不含其子目录的大小。 
-x或--one-file-xystem 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。 
-X<文件>或--exclude-from=<文件> 在<文件>指定目录或文件。 
--exclude=<目录或文件> 略过指定的目录或文件。 
--max-depth=<目录层数> 超过指定层数的目录后，予以忽略。
--help 显示帮助。 
--version 显示版本信息。
```

一般使用

```bash
# 一般用于查看文件大小
du -h 文件名
```

## find

Linux中的Find(查找)命令是在Linux系统中最重要并且更有用的命令之一。Find命令主要用于指定匹配文件条件的参数查找或者定位文件和目录的列表。Find命令能够被使用基于各种各样的条件，例如permissions（权限），users（用户），groups（组），file type（文件类型），date（日期），size（大小）等等其它可能的条件。

```bash
find [查找范围] [查找条件表达式]

-name：按名称查找；根据目标文件的名称进行查找，允许使用“*”及“?”通配符；
-size：按文件大小查找；一般使用“+”、“-”号设置超过或小于指定的大小作为查找条件。常用的容量单位包括kB（注意k是小写）、MB、GB；
-user：按文件属主查找；
-type：按文件类型查找；类型指的是普通文件（f）、目录（d）、块设备文件（b）、字符设备文件（c）等。
```

## VIM操作

### Vim替换

```bash
# :{作用范围}s/{目标}/{替换}/{替换标志}

# 当前行
:s/foo/bar/g

# 全文
:%s/foo/bar/g

# 当前行.与接下来两行+2：
:.,+2s/foo/bar/g
```

#### 命令模式：

```shell
G - 光标移动到文章末尾
500G - 光标移动500行
gg - 光标移动到行首
30gg - 光标移动到30行
h - 前
j - 下
k - 上
l - 右
Ctrl + y - 向上一行
Ctrl + e - 向上一页
Ctrl + f - 向下一行
Ctrl + b - 向上一页
0 - 光标移动到行首
$ - 光标移动到行尾
w - 光标移动到下一个单词
dd - 删除一行
100dd - 删除100行
yy - 复制一行
100yy - 复制100行
p - 粘贴
10p - 粘贴10次
u - 撤销
Ctrl + r - 恢复撤销
```

#### 多文件操作

```bash
:ls - 查看所有打开的文件
：b 数字 - 制定显示哪个文件
：vs - 垂直拆分窗口
：ps - 水平拆分窗口
Ctrl + w *2 - 切换窗口 
：qa -  关闭全部窗口
：wqa - 保存并退出全部窗口
```

## Nginx 启动报 [emerg] bind() to 0.0.0.0:XXXX failed (13: Permission denied)错误处理

第一种：端口小于1024的情况：

```bash
[emerg] bind() to 0.0.0.0:80 failed (13: Permission denied)
```

原因是1024以下端口启动时需要root权限，所以sudo nginx即可。

第二种：端口大于1024的情况：

```bash
[emerg] bind() to 0.0.0.0:8380 failed (13: Permission denied)
```

这种情况，需要如下操作：

首先，查看http允许访问的端口：

```bash
# 执行semanage port -l | grep http_port_t
http_port_t                    tcp      1433, 2433, 80, 81, 443, 488, 8008, 8009, 8443, 9000
```

其次，将要启动的端口加入到如上端口列表中

```bash
semanage port -a -t http_port_t  -p tcp 要添加的端口号
```

## semanage命令

**semanage命令**是用来查询与修改SELinux默认目录的安全上下文。SELinux的策略与规则管理相关命令：[seinfo](http://man.linuxde.net/seinfo)命令、[sesearch](http://man.linuxde.net/sesearch)命令、[getsebool](http://man.linuxde.net/getsebool)命令、[setsebool](http://man.linuxde.net/setsebool)命令、semanage命令。

```
semanage {login|user|port|interface|fcontext|translation} -l
semanage fcontext -{a|d|m} [-frst] file_spec


-l：查询。
fcontext：主要用在安全上下文方面。
-a：增加，你可以增加一些目录的默认安全上下文类型设置。
-m：修改。
-d：删除。
```

## nmap 网络探测

```bash
nmap -p 1-65535 -T4 -A -v 你的域名或者地址

# -p 指定端口
# -v 显示扫描过程
# -T4 针对TCP端口进制动态扫描延迟超过10ms
# -A 全面扫描
```

## systemctl

查看linux上运行哪些服务

以下选项可根据类型列出单元：

```bash
systemctl list-units --type service
```

以下选项可帮助您根据状态列出单位，输出与前例类似但更直截了当：

```bash
systemctl list-unit-files --type service
```

运行以下命令以查看指定服务的状态：

```bash
systemctl | grep apache2
# 或者
systemctl status apache2
```

使用以下命令查看系统启动时会被启用的服务列表：

```bash
systemctl list-unit-files | grep enabled
```











