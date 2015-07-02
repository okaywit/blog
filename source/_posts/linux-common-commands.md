title: "linux常用命令"
date: 2015-06-30 21:10:37
tags: "linux"
categories: "日常笔记"
---

日常使用的高频率linux命令汇总，例如：rm、tar、find、ps、unzip等。
<!-- more -->

###rm
	删除目录：rm -r 目录 (-r:递归删除该目录下文件，-f:强制删除)

###tar
	tar.gz文件：tar -xzf 文件名

###find
	查找目录： find /范围 -name '关键字' -type d
	查找文件： find /范围 -name '关键字' -print

###unzip
	解压到指定目录： unzip zip文件 -d 目录

###netstat
	显示TCP端口：netstat -tanp
	显示UDP端口：netstat -uanp

###su
	用户切换：su 用户名

###ps
	查看进程：ps -ef | grep 进程名