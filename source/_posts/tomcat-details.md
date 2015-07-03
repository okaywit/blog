title: tomcat配置原理介绍
date: 2015-07-02 18:48:12
tags: tomcat
categories: tomcat
---

主要介绍一些tomcat日常配置相关的内容原理
<img src="http://7xk4gy.com1.z0.glb.clouddn.com/okaywit_server.jpg">

<!-- more -->
###目录介绍
* bin 脚本
* conf 配置文件 
* logs 日志
* webapps 应用

###catalina.home
	对应系统变量配置中的CATALINA_HOME，指定bin、lib目录位置。
###catalina.base
	对应系统变量配置中的CATALINA_BASE，指定conf、logs、temp、webapps、work目录位置。

###server.xml说明
1. tomcat的核心配置文件，对外提供web服务。

2. Service表示提供具体服务内容，Connector配置提供服务的监听端口；
	Engine表示提供服务的引擎，Host表示访问的资源位置，Context表示设定访问的url。


Server结构图如下：
<img src="http://7xk4gy.com1.z0.glb.clouddn.com/okaywit_server.jpg">
