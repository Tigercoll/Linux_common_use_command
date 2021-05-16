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