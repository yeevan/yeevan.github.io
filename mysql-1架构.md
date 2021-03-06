# MySQL的客户端/服务器架构

## 前言
本篇算是笔记，记录和整理下自己对MySQL不熟悉的地方。

**大致逻辑**：
MySQL的服务器程序直接和我们存储的数据打交道，客户端程序连接服务器，发送增删改查的请求，服务器操作维护的数据响应请求。

MySQL服务器程序的进程也被称为MySQL数据库实例。我们启动的MySQL服务器进程的默认名称是`mysqld`，而常用的MySQL客户端进程默认名称为`mysql`。

## 安装
不论通过哪种方式安装，MySQL的服务器程序和客户端都会被安装到我们的机器上。

> 不过在Linux下使用RPM包时会有单独的服务器RPM包和客户端RPM包，需要分别安装。

## 启动服务器程序

#### mysqld
代表着mysql服务器程序，运行这个可执行文件就可以直接启动一个服务器进程。

#### mysqld_safe
* 是一个启动脚本，会间接的调用mysqld。

* 还顺便启动了另外一个监控进程，这个监控进程在服务器进程挂了的时候，可以帮助重启它。

* 会将服务器程序的出错信息和其他诊断信息重定向到某个文件中，产生出错日志。

#### mysql.server
也是一个启动脚本，会间接的调用mysqld_safe，使用时跟上start/stop参数就OK。

不过这个文件其实是一个链接文件，实际文件是../support-files/mysql.server.

#### mysqld_multi
一台计算机上可以运行多个MySQL服务器进程，这个可执行文件可以对每一个服务器进程的启动或停止进行监控。


## 启动客户端程序
格式：
	
	mysql -h主机名 -u用户名 -p密码

> 小贴士： 像 h、u、p 这样名称只有一个英文字母的参数称为短形式的参数，使用时前边需要加单短划线，像 host、user、password 这样大于一个英文字母的参数称为长形式的参数，使用时前边需要加双短划线。


## 客户端与服务器连接的过程
本质上进程间通信的过程，MySQL支持以下的通信方式

### TCP/IP
MySQL服务器默认监听3306端口。

### 命名管道和共享内存
这种通信方式是windows操作系统中的

### Unix域套接字文件
这种方式要求客户端和服务器运行在同一台类Unix的操作系统上。
MySQL服务器程序默认监听的套接字文件路径是`/tmp/mysql.sock`,客户端也默认监听这个文件。

改变文件：
 	
 	# 服务器改变套接字文件
	mysqld --socket=/tmp/a.txt
	# 客户端改变
	mysql -hlocalhost -uroot --socket=/tmp/a.txt -p
	
## 架构
![MySQL架构图](https://raw.githubusercontent.com/yeevan/yeevan.github.io/master/images/mysql/mysql_jiagou.jpeg)

### 连接管理
客户端与服务器进程建立连接，每当有一个客户端进程连接到服务器进程时，服务器进程都会创建一个`线程`来专门处理与这个客户端的交互，当该客户端退出时会与服务器断开连接，服务器会把线程缓存起来。

### 解析与优化
#### 查询缓存
如果两个查询请求在任何字符上的不同都会导致缓存不命中。请求中包含某些系统函数、用户自定义变量和函数、一些系统表也不会被缓存。

**只要该表的结构或者数据被修改，则该表的所有高速缓存查询都将无效并删除。**

PS：8.0中查询缓存已经删除了

#### 语法解析
如果查询缓存没有命中，就进入查询阶段，服务器先对文本做分析，判断语法是否正确，然后从文本中将要查询的表、各种查询条件都提取出来放到内部使用的一些数据结构上。

算是一个编译过程，涉及词法解析、语法分析、语义分析等阶段。
#### 查询优化
对SQL做优化，生成一个执行计划，表明了应该使用哪些索引进行查询，表之间的连接顺序是怎样的。

这块可以使用`explain` `profile`来查看。

#### 存储引擎
数据的存储和提取操作都封装到了这个模块里。不同的存储引擎使用不同的存储方式。

## 参考
本文主要参考小孩子写的《MySQL是怎样运行的：从根儿上理解MySQL》
想学习的同学可以通过以下方式购买，绝对超值！

![](https://raw.githubusercontent.com/yeevan/yeevan.github.io/master/images/mysql/juejinxiaoce.jpeg)