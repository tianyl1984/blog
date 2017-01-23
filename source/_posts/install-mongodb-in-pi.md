---
layout: post
title: 树莓派上安装MongoDB3
description: 如何在树莓派上安装MongoDB3
category: mongodb
---


MongoDB官方目前还没有提供arm版安装文件，可以下载源码进行编译，或使用其他人编译好的二进制程序。网络上大多资料都是2.x的版本，终于找到了3.x版本的教程。[点击这里查看](http://andyfelong.com/2015/12/mongodb-3-0-7-on-raspberry-pi-2/)

编译过程复杂，耗时太长，还好作者提供了二进制版本下载。[二进制安装地址](http://andyfelong.com/2016/01/mongodb-3-0-9-binaries-for-raspberry-pi-2-jessie/) 记录一下安装过程备用。

1.添加用户。添加一个运行mongod的用户。

	adduser mongo

2.编写配置文件mongodb.conf

	quiet = true
	dbpath = /path/to/db
	logpath = /path/to/log/logfile.log
	logappend = true
	storageEngine = mmapv1

注意：存储引擎只支持mmapv1；dbpath和logpath需要对mongo用户授权

3.编写启动service(/lib/systemd/system/mongodb.service)

	[Unit]
	Description=MongoDB
	After=network.target

	[Service]
	User=mongo
	ExecStart=/path/to/bin/mongod --quiet --config /path/to/config/mongodb.conf

	[Install]
	WantedBy=multi-user.target

需要登录认证时需加上参数，--auth

4.启动服务
	
	sudo systemctrl start mongodb

为了访问安全可以添加认证。mongo3.x已经废弃了db.addUser方法，只能使用db.createUser。[官方文档](https://docs.mongodb.org/manual/reference/method/db.createUser/#create-administrative-user-with-roles)

不出意外就可以正常使用了，由于MongoDB使用内存映射的方式，把数据文件映射到内存中，简化内存的控制，所以在32位的树莓派2理论上文件最大是2G，连接时会有提示。

在树莓派上玩玩MongodDB还是可以，正式使用还是有很多限制。
